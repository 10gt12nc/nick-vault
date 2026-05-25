# proxy-user-data-report-projection

日期: 2026-05-22

Step 5 補充日期: 2026-05-25

## 0. 閱讀定位

- Flow 中文名稱: 代理玩家報表 projection / summary。
- Flow slug: `proxy-user-data-report-projection`。
- 完成狀態: Step 5 claim gate 完成。
- 證據層級: 真實開發過 + code-backed；`10gt12nc` 在 `#386`、`#590`、`#702`、`fix ag_report_player` 有 direct commits，可回填 project-level contribution consolidation；單條 flow Step 5 不直接更新 05 / 08。
- 本 flow 類型: Kafka event projection + Quartz report summary / cleanup。
- 是否只確認到入口: 否，已確認 consumer、service、repository、Quartz job、主要 SQL 與 path-specific history；上游 producer 與 live DB constraint 未完整掃，標為待確認。

Source repo 遠端狀態: `git fetch --all --prune` 失敗一次；依 KB 不反覆重試。local `master` / local `origin/master` 都在 `d847357`，ahead / behind `0 / 0`，但未確認最新遠端，本文件依本地 refs / 本地 working tree 保守分析。

## 1. 白話導讀

這條 flow 的目的，是把玩家下注結算事件轉成代理 / 玩家 / 日期 / 幣別維度的報表資料。

正常情境是:

1. 遊戲結算後，上游把一批 `BetRecord` 包成 `SettleBetsVo` 丟到 Kafka `settled_bets` topic。
2. `ProxyUserDataConsumerService` 消費事件，依 `agentId + playerId + dataDay + currency` 累加下注筆數、下注額、總贏分與 profit。
3. 系統查 DB 裡已存在的報表 row，沒有就 insert，有且新累積筆數較大就 update。
4. 每天 02:00 的 `ReportAgentPlayerJob` 會把三天前以前的日報表彙總到 `dataDay = 0` 的 summary row。
5. 匯總後把舊日報表備份到 `ag_report_player_bak`，再從 `ag_report_player` 刪除舊 row。

使用者通常不會直接觸發這條 flow；它服務的是後台 / 營運 / 代理報表查詢。成功後，報表查詢可以看到按代理、玩家、日期、幣別累計的下注與輸贏資料。失敗時，最直覺會壞在報表數字不準、幣別混在一起、summary 重跑重複加總，或 backup / delete 做到一半導致資料需要人工核對。

## 2. 初中階 Code 分層對照

| 分層 | Code / 資料 | 作用 | 本輪判斷 |
| --- | --- | --- | --- |
| Event source | Kafka `settled_bets` | 結算後下注批次事件 | producer 端未完整掃，先視為上游事件來源 |
| Payload VO | `SettleBetsVo` | 包 `batch_id` 與 `List<BetRecord>` | 已確認 |
| Domain model | `BetRecord` | 單筆下注結算資料，含 `agentId / playerId / account / bet / totalWin / currency` | 已確認 |
| Consumer | `ProxyUserDataConsumerService` | 消費 Kafka，聚合報表 row，呼叫 service 寫 DB | 已確認 |
| In-memory state | `ConcurrentHashMap reportMap` | 暫存 `agentId_playerId_dataDay_currency` 累計值 | 已確認；有長生命週期風險 |
| Service | `ReportAgentPlayerServiceImpl` | 薄 service，轉呼叫 custom repository | 已確認 |
| Repository | `ReportAgentPlayerRepositoryCustomImpl` / `ReportAgentPlayerRepositoryInternal` | 查舊 row、batch insert / update、summary / backup / delete | 已確認 |
| Table | `ag_report_player` / `ag_report_player_bak` | 報表主表與備份表 | 已確認 table name；constraint / index 未完整掃 |
| Quartz | `ReportAgentPlayerJob` / `JobOptions` | 每天 02:00 彙總三天前以前資料 | 已確認 |

## 3. 最小架構圖

```text
antplay-slot-game-api / upstream settle
  -> Kafka topic: settled_bets
  -> ProxyUserDataConsumerService
  -> ReportAgentPlayerService
  -> ReportAgentPlayerRepositoryCustomImpl / Internal
  -> ag_report_player

Quartz 02:00 ReportAgentPlayerJob
  -> summary old daily rows into dataDay = 0
  -> ag_report_player_bak
  -> delete old daily rows from ag_report_player
```

## 4. 正常流程圖

```text
settled_bets event
  -> parse SettleBetsVo
  -> choose agentId from first bet
  -> accumulate valid BetRecord by agent / player / midnight / currency
  -> find old report rows
  -> split addPlayers / updatePlayers
  -> batchInsert / batchUpdate ag_report_player

daily 02:00
  -> list enabled agents
  -> list players by agent
  -> summary rows data_day < threeDaysBefore and data_day > 0
  -> insert or update dataDay = 0 summary row by currency
  -> backup old rows
  -> delete old rows
```

## 5. 正常流程逐步說明

### 5.1 Kafka projection

`ProxyUserDataConsumerService` 只有在 `spring.kafka.consumer.task.proxy-user-data=true` 時啟用，listener 消費 `settled_bets`，groupId 是 `proxy_user_data`，concurrency 來自設定值。

consumer 把 `record.value()` 反序列化成 `SettleBetsVo`，再拿第一筆 bet 的 `agentId` 當作本批主要 agent。它會呼叫 `consumeProxyUserDataServiceInternal(agentId, bets)`，若內部發現同批 bets 有不同 agent，外層再把其他 agent 分組後重跑。

內部聚合時，會跳過 `playerId / agentId / game` 為 null 的資料。有效資料會轉成:

- `dataDay`: `DateTimeUtil.betTimeCovertToMidnight(time)`。
- `betAmount`: `betRecord.getBet()`。
- `totalWin`: `betRecord.getTotalWin()`。
- `profit`: `bet - totalWin`。
- `currency`: null 時轉成空字串。
- key: `agentId + "_" + playerId + "_" + dataDay + "_" + currency`。

之後用 `reportMap.compute` 累加 bet count / amount / win / profit。寫 DB 前，consumer 會把整個 `reportMap.values()` 當作 `newPlayers`，查 `findOldPlayers`，再按同一組 key 判斷 insert 或 update。

### 5.2 DB write

`ReportAgentPlayerRepositoryCustomImpl` 先按 agent 分組，再交給 `ReportAgentPlayerRepositoryInternal` 寫 `ag_report_player`。

`batchInsert` 寫入 `agent_id, player_id, account, data_day, bet_count, bet_amount, total_win, profit, create_at, currency`。

`batchUpdate` 用 `agent_id + player_id + data_day + currency` 定位 row，直接把目前累計值覆寫到 `bet_count / bet_amount / total_win / profit / update_at`。

`findOldPlayers` 用 `(agent_id, player_id, data_day, currency) IN (...)` 查舊資料。current code 會固定以 800 筆為 batch，不足 800 時用第一筆 padding，避免動態 SQL placeholder 過長或不固定。

### 5.3 Quartz summary / cleanup

`JobOptions` 註冊 `reportAgentPlayerJob`，cron 是每天 02:00。

`ReportAgentPlayerJob` 有 `@DisallowConcurrentExecution`，避免同一 Quartz job 同時跑兩份。job 會遍歷 enabled agents，再遍歷 agent 底下玩家，計算三天前 midnight。

每個玩家會做:

1. `summaryReport(threeDaysBefore, playerId, account, agentId)`：查 `data_day < threeDaysBefore` 且 `data_day > 0` 的日報表，按 `player_id / account / currency` group。
2. 對每個 currency 的 summary result，查 `dataDay = 0` 的 summary row。
3. 沒有舊 summary 就 insert，並把 `dataDay` 設成 `0L`。
4. 有舊 summary 就用加總方式 update。
5. `backupReport` 把三天前以前資料 insert 到 `ag_report_player_bak`。
6. `deleteReport` 從 `ag_report_player` 刪除三天前以前資料。

job 對單一 player 包 try / catch，某玩家失敗會記 log 後繼續下一個玩家。

## 6. 業務問題

這條 flow 解的是「玩家結算資料如何轉成代理報表」。

下注結算本身通常是交易主流程；報表 projection 是 derived data。derived report 不應該成為錢包餘額或結算正確性的 source of truth，但它會影響營運查帳、代理對帳與客服判斷。

所以它的核心不是「不能掉一筆就資金錯」，而是:

- 報表 key 要切對，不能把不同代理、不同玩家、不同日期、不同幣別混在一起。
- projection 可以重跑或修復時，要知道 source 是誰。
- summary / backup / delete 要避免重複加總或漏備份。
- 觀測性要能回答哪個 agent / player / day / currency 出問題。

## 7. 系統位置與入口

主要入口:

- `src/main/java/com/ps/domain/kafka/service/ProxyUserDataConsumerService.java`
- `src/main/java/com/ps/quartz/job/impl/ReportAgentPlayerJob.java`

主要 service / repository:

- `src/main/java/com/ps/domain/kafka/service/ReportAgentPlayerService.java`
- `src/main/java/com/ps/domain/kafka/service/impl/ReportAgentPlayerServiceImpl.java`
- `src/main/java/com/ps/domain/kafka/gameData/dao/impl/ReportAgentPlayerRepositoryCustomImpl.java`
- `src/main/java/com/ps/domain/kafka/gameData/dao/impl/internal/ReportAgentPlayerRepositoryInternal.java`

主要 model:

- `src/main/java/com/ps/quartz/log/vo/SettleBetsVo.java`
- `src/main/java/com/ps/domain/game/slot/data/entity/BetRecord.java`
- `src/main/java/com/ps/domain/kafka/gameData/entity/ReportAgentPlayer.java`

設定入口:

- `application.yml` 有 `spring.kafka.consumer.task.proxy-user-data` 開關與 `settled_bets` topic 設定；本文件不記錄環境細節。
- `JobOptions` 註冊 `reportAgentPlayerJob` 於每天 02:00 執行。

## 8. DB / Redis / MQ / 外部 API

| 類型 | 已確認 | 說明 |
| --- | --- | --- |
| MQ | Kafka `settled_bets` | consumer 已確認；producer 端未完整掃 |
| DB | `ag_report_player` | projection 主表 |
| DB | `ag_report_player_bak` | summary 後備份表 |
| Redis | 無直接使用 | 本 flow 未看到 Redis 參與 |
| 外部 API | 無直接呼叫 | 主要是內部 event / job / DB projection |

## 9. 資料狀態與 state transition

```text
BetRecord event
  -> valid / invalid
  -> in-memory accumulated ReportAgentPlayer
  -> DB daily row
  -> DB summary row dataDay = 0
  -> backup row
  -> old daily row deleted
```

狀態語意:

- `BetRecord`: 上游結算事件中的單筆下注結果。
- `ReportAgentPlayer(dataDay > 0)`: 某日、某代理、某玩家、某幣別的日報表 row。
- `ReportAgentPlayer(dataDay = 0)`: 該代理 / 玩家 / 幣別的歷史 summary row。
- `ag_report_player_bak`: 被 summary 後的舊日報表備份。

`currency` 是重要維度。`#590` 顯示報表後來補了 currency 拆分；`#702` 顯示 key 重複修正時把 agentId 放回 oldMap / key 判斷，避免跨代理 key collision。

## 10. Failure Window / Consistency

### 10.1 consumer 反序列化或處理失敗

consumer 外層 catch 只記 log。Step 5 補掃 `KafkaConfig` 後，確認 default listener factory 有 `DefaultErrorHandler`、`FixedBackOff(1000L, 3)` 與 dead-letter-topic 設定 evidence。

保守結論: 可以說 source code 有基本 retry / dead-letter-topic 設計；但不能宣稱這條 flow 有 exactly-once、完整 replay architecture、DLQ 消費治理或補數 runbook。

### 10.2 `reportMap` 長生命週期

`reportMap` 是 service field，並且 current code 沒看到定期 clear。每次事件後 `newPlayers = new ArrayList<>(reportMap.values())`，代表新事件會帶著過去累積過的所有 key 一起查舊資料與判斷 update。

這種設計能讓 process 內累積值逐步覆寫 DB，但風險是:

- process 跑久後 map 可能變大。
- consumer concurrency 下，多個 listener thread 共享同一個 map。
- restart 後 map 歸零，是否能完全靠上游 replay / DB old row 補齊，要再確認。
- 如果某次 DB write 部分失敗，map 仍保留累計值，下次可能嘗試再寫，但這不是完整 transaction guarantee。

### 10.3 mixed agent batch

consumer 預設拿第一筆 bet 的 agentId 當本批 agent。若內部遇到不同 agentId，會回傳一個 exclude id，再由外層按其他 agent 分組重跑。

這段邏輯可視為處理同一 Kafka message 混多代理的防呆，但命名與排除語意不直觀。面試說法應保守: 「有看到混 agent 分組補處理邏輯，但需要 Step 4 / Step 5 再確認是否完全覆蓋所有混批情境。」

### 10.4 insert / update 邏輯

insert / update 是以 DB old row 是否存在與 `report.betCount > old.betCount` 判斷。update 是覆寫累計值，不是 `+ diff`。

優點:

- 若同一 JVM map 保留累計值，update 比單純增量較不容易因重送直接 double add。

風險:

- 如果 map 與 DB old row 不一致，`betCount` 比較可能跳過某些修復。
- DB unique key / index 未在 source scan 中找到，不能保證 concurrent insert 不會重複 row。
- `findOldPlayers` padding 800 筆能穩定 SQL placeholder，但可能增加重複查詢，需要看 DB plan / index。

### 10.5 summary / backup / delete partial failure

Quartz job 對每個 player 的流程是 summary -> insert / update summary row -> backup -> delete。repository methods 各自有 transaction annotation，但 job 層沒有看到把整段包成同一 transaction。

可能 failure window:

- summary row 已加總，但 backup 失敗。
- backup 成功，但 delete 失敗，下一次 job 可能再次 summary。
- delete 成功但 backup 或 summary 實際有 partial issue，需要查備份表與 source event。
- 某玩家失敗後 job 繼續下一玩家，所以整體 job 完成不代表所有玩家都成功。

這是 Step 4 很適合轉成面試題的地方: derived report 的 cleanup 不該影響下注主流程，但 owner 要設計 reconciliation 與 rerun 邊界。

## 11. Retry / Compensation / Reconciliation

本輪已確認:

- consumer catch exception 有 log。
- job catch per player exception 有 log。
- summary 使用三天前以前資料，保留最近三天日報表。
- 舊日報表有 backup table。

本輪未確認:

- Kafka manual replay、DLQ 消費治理與補數 runbook。
- 報表重建 command。
- DB unique constraint / index。
- backup table 去重策略。
- report dashboard 是否有 reconciliation 指標。

保守建議:

- 把 `ag_report_player` 視為 derived projection。
- 若 production 要更 owner-grade，應補「以 source bet record 或 Kafka replay 重建 report」的 runbook。
- summary / backup / delete 應有 job execution id、處理數、失敗 agent/player/day/currency、重跑策略。
- 若 DB constraint 允許，應以 `agent_id + player_id + data_day + currency` 做唯一性防線或明確 upsert。

## 12. Senior / Owner 設計取捨

| 決策點 | 現況 | 好處 | 風險 / 待補 |
| --- | --- | --- | --- |
| 用 Kafka projection 做報表 | `settled_bets` -> report table | 不阻塞下注主流程，報表可非同步更新 | 需要 replay / reconciliation |
| key 加上 currency | `agentId + playerId + dataDay + currency` | 避免多幣別報表混算 | 舊資料 migration / null currency 語意要清楚 |
| oldMap key 加上 agentId | `#702` 修正 | 避免不同代理同 player/day/currency collision | DB unique key 未確認 |
| in-memory map 累加後覆寫 DB | `reportMap` + batch update | process 內可累積，重送時不直接加 diff | 長生命週期、restart、concurrency、memory |
| summary row 用 `dataDay=0` | 歷史 summary 折疊 | 查詢舊資料可少掃日 row | summary 重跑重複加總要防 |
| 三天前才 summary | 保留近期日資料 | 近期報表可細查，舊資料縮量 | 三天窗口是否符合業務對帳要確認 |
| backup 後 delete | 主表瘦身 | 查詢效能與儲存治理 | backup/delete partial failure |

## 13. Lead / Architect 追問

1. `ag_report_player` 的 source of truth 是 Kafka event、`BetRecord` table，還是 report table 本身？
2. 如果 consumer 寫 DB 成功但 offset commit 失敗，重送會不會 double count？
3. `reportMap` 為什麼是 service field？怎麼控制 memory 與 concurrency？
4. summary row update 是加總，若 backup 成功但 delete 失敗，下次 job 會不會再加一次？
5. `currency` 從無到有時，舊資料 migration 怎麼處理？
6. `agent_id + player_id + data_day + currency` 有沒有 DB unique key？
7. 如果某 agent 的某玩家連續失敗，job 觀測性怎麼讓 on-call 找到？
8. 要如何重建某天某代理報表？

## 14. 面試 / 履歷邊界摘要

可作面試:

- Kafka event projection 到報表 DB。
- 報表 key design: agent / player / day / currency。
- batch insert / update 與 old row 比對。
- Quartz summary / backup / delete 的 failure window。
- derived report 的 source-of-truth / reconciliation 設計。

履歷可保守寫:

- 參與 AntPlay slot job / event processing 維護，處理 Kafka `settled_bets` 報表 projection、代理玩家日報表聚合、currency 維度與 Quartz summary / backup / cleanup 類資料治理。

不能誇大:

- 不寫完整 Kafka platform owner。
- 不寫 exactly-once / replay architecture owner；也不把基本 retry / dead-letter-topic 設定包裝成完整 DLQ 治理。
- 不寫完整 BI / report platform owner。
- 不寫完整金流或下注結算 source-of-truth owner。

## 15. Step 5 Claim Gate 結論

這條 flow 是 `antplay-slot-game-job` 目前最適合先講的 Senior Backend case，原因是 evidence 集中、production path 完整，而且有清楚的 consistency / failure window 題材。

Step 5 已完成 claim gate:

- 可回填 project-level `antplay-slot-game-job` contribution consolidation，作為 Kafka report projection / Quartz summary / report key correctness 的直接 evidence。
- 可面試講「結算 event -> derived report projection -> daily summary / backup / delete」的 correctness、failure window 與 owner 改善方向。
- 可保守寫入 project-level 素材池，但不由單條 flow 直接更新 `05 / 08`。
- 不可誇大為完整 Kafka platform、完整 BI/report platform、完整 exactly-once / replay architecture owner。

2026-05-25 後續進度: 同 project 第二順位 `activity-accumulated-bet-voucher Step 5` 已完成，第三順位 `big-win-notification Step 5` 也已完成，第四順位 `settle-pool-monitor-darkpool-sync Step 4` 已完成。下一步若延續 `antplay-slot-game-job`，應做 `settle-pool-monitor-darkpool-sync Step 5`，而不是插隊做 domain-level 大地圖。大地圖要在 active flow 收口後、且 Nick 要求總結或 domain 已累積足夠代表 flows 時再處理。
