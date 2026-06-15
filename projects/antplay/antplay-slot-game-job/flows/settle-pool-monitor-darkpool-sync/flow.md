# settle-pool-monitor-darkpool-sync

日期: 2026-05-25
Step 4 補充日期: 2026-05-25
Step 5 補充日期: 2026-05-25

## 0. 閱讀定位

### Flow 類型與閱讀定位

- Flow 類型: Data / Risk Projection Flow
- 所屬大系統: AntPlay dark pool / settle pool monitor sync
- 面試用途: 輔助差異化 case / risk projection
- 閱讀方式: 先看 settlement event 如何同步 risk / dark pool state，再看 DB / alert 邊界。
- 不要期待: 這不是完整風控平台，也不是 Nick direct development claim。

- Flow 中文名稱: settle pool monitor / dark pool Redis -> DB sync。
- Flow slug: `settle-pool-monitor-darkpool-sync`。
- 完成狀態: Step 5 claim gate 完成。
- 證據層級: 專案存在 / code-backed / analysis-first。current code 技術價值高，但主路徑 current blame 幾乎是 Arnold，早期基礎有 Eliot；目前沒有 Nick / `10gt12nc` direct path evidence，不能寫成 Nick 真實開發或主導 settle pool / risk / jackpot owner。
- 本 flow 類型: Kafka settlement event -> risk / dark pool monitor projection -> DB / alert。
- 是否只確認到入口: 否，已確認 consumer、分群、handler、DB repository、reset flag sync 與 path-specific history；未掃 admin / monitoring 查詢畫面與 game-api producer 最新 contract。

Source repo 遠端狀態: 先前同 repo `git fetch --all --prune` 已因內網不可達失敗；依 KB 不反覆重試。local `master` / local `origin/master` 都在 `d847357`，ahead / behind `0 / 0`，但未確認最新遠端，本文件依本地 refs / 本地 working tree 保守分析。

## 1. 白話導讀

這條 flow 是把每一批已結算投注 event，整理成「目前各類池子的投注量、派彩金額、玩家控制差額」。

上游 `settled_bets` event 裡有多筆 `BetRecord`。Job 會先把它們依類型分群：一般 spin、free spin、jackpot、活動 spin、活動 free spin、單點玩家控制。每一群會算出 batch total bet / total win，再寫進 `settled_pool`。另外，如果 Redis 出現 reset flag，job 會先從 Redis 讀目前 dark pool key，把 snapshot 同步到 DB。

它不是下注交易 source of truth，也不是完整風控平台。它更像 runtime 後面的 monitor / projection：用結算後資料和 dark pool snapshot 做比對，發現池子差異或 max win 超過預期時送 alert。

壞掉時，最直覺會有幾種風險:

- Kafka event 重送造成 `settled_pool` 重複 increment。
- reset flag 被刪掉後，sync DB 中途失敗，後續不再自動補。
- `settled_pool` 被 truncate / rebuild 時有 partial save 風險。
- `additional_info` 缺失時，該筆 bet 不會納入計算。
- jackpot 單位是 hao，一般池是 cent，單位錯會讓 alert 和 max win 判斷失真。
- player control 差額用 win - bet 累加，若 compare type / success 判斷錯，可能誤判控制完成狀態。

## 2. 初中階 Code 分層對照

| 分層 | Code / 資料 | 作用 | 本輪判斷 |
| --- | --- | --- | --- |
| Event source | Kafka `settled_bets` | 已結算下注批次 | consumer 已確認；producer 最新 contract 未掃 |
| Payload VO | `SettleBetsVo` | 包 `batch_id` 與 bet list | 已確認 |
| Domain model | `BetRecord` + `AdditionalInfoVO` | bet / win / type / jackpot / dark pool before value / player control config | 已確認 |
| Consumer | `SettlePoolMonitorConsumerService#consumeLatest` | 讀 event、先 sync reset flag、再 mainExecute | 已確認 |
| Grouping | `GroupSettleTypeRecord#groupBySettleType` | 依 bet type / buy free / jackpot / activity / player control 分群 | 已確認 |
| Handler | `MainHandler` | 計 batch total bet / win、寫 `settled_pool`、送 alert | 已確認 |
| Key builder | `MainMethod` + `RedisKeysUtil` | 產生 normal / free / jackpot / activity / player-control Redis key | 已確認 |
| Persistence | `SettledPoolRepositoryImpl` | 查 key、insert / increment、reset snapshot saveAll | 已確認 |
| Reset sync | `SyncDbFromRedis#syncData` | Redis reset flag 存在時，從 Redis 讀 snapshot 並重建 DB | 已確認 |
| Alert | `MonitorAlertUtil` | maxWin / darkpool diff alert | 已確認 wrapper call；下游通知未掃 |
| Admin / monitoring | admin 查詢 settled pool | 未掃 | 不作完整後台 / 風控平台 claim |

## 3. 最小架構圖

```text
antplay-slot-game-api / settlement producer (待確認最新)
  -> Kafka topic: settled_bets
  -> SettlePoolMonitorConsumerService
  -> SyncDbFromRedis if antplay:settlePool:reset exists
  -> GroupSettleTypeRecord
  -> MainHandler
  -> SettledPoolRepository
  -> settled_pool / settled_pool_bak
  -> MonitorAlertUtil
```

## 4. 正常流程圖

```text
settled_bets event
  -> parse SettleBetsVo
  -> syncIfResetFlagPresent
  -> read jackpot games
  -> group records by settle type
  -> normal spin/free/jackpot groups
  -> activity spin/free groups
  -> player control groups
  -> calculate batch total bet / win
  -> save increment into settled_pool
  -> compare maxWin / before dark pool
  -> send alert if threshold exceeded
```

## 5. 正常流程逐步說明

1. `SettlePoolMonitorConsumerService` 只有在 `spring.kafka.consumer.task.monitor=true` 時啟用。
2. Listener 消費 `settled_bets`，groupId 是 `monitor`，concurrency 讀 topic4 consumer。
3. Consumer 先把 Kafka value 反序列化成 `SettleBetsVo`。
4. 每次處理前呼叫 `syncIfResetFlagPresent()`；如果 Redis 有 `antplay:settlePool:reset`，`SyncDbFromRedis#syncData` 會啟動 DB 重建流程。
5. 讀 jackpot game set，交給 `GroupSettleTypeRecord` 分群。
6. `GroupSettleTypeRecord` 先 parse `BetRecord#additionalInfo`；如果 `additional_info` 是 null，production 會 log error 並跳過該筆。
7. `BET_TYPE_SINGLE_POINT_CONTROL` 會依 `agentId + currency + account + playerControlConfigId` 分到 player control。
8. `BET_TYPE_ACTIVITY` 會依 `agentId + activityId + game + currency` 分到 activity spin 或 activity free。
9. `BET_TYPE_NORMAL` 會依 `agentId + game + currency` 分到 normal spin / free；如果是 jackpot game，還會額外分到 normal jackpot。
10. `MainHandler` 對每個 group 計算 batch total bet / total win。
11. 一般池會將 total bet / total win increment 到 `settled_pool`；jackpot 會用 jackpot 比例與 hao 單位。
12. Activity 池用 voucher bet 作 total bet，依 activity id / game / currency 建 key。
13. Player control 只寫 diff amount，計算方式是 `batchTotalWin - batchTotalBet`。
14. 寫入後，handler 用 latest DB value、batch value、RTP cache 與 `additional_info` 的 before dark pool value 做 maxWin / diff alert 判斷。
15. 若超過 threshold，呼叫 `MonitorAlertUtil#sendAlert`。

## 6. Reset Flag Sync 流程

```text
Redis key antplay:settlePool:reset exists
  -> delete reset flag first
  -> load enabled agents and games
  -> read normal spin/free/jackpot dark pool keys from Redis
  -> read active activity dark pool keys from Redis
  -> read active player control diff keys from Redis
  -> backup current settled_pool into settled_pool_bak
  -> truncate settled_pool
  -> insert snapshot rows
```

這段是本 flow 的高風險點。它目前先刪 reset flag，再讀 Redis / DB / activity / player control，最後 `saveAll`。`saveAll` 會先 backup、truncate，再逐筆 insert。若中途 exception 或 process crash，需要靠人工重設 reset flag 或外部 runbook 補救；本 repo 未看到 durable sync job state。

## 7. 業務問題

這條 flow 解的是「結算後，營運 / 風控要看到各類池子的累積狀態與異常差異」。

它的價值在於:

- 把 runtime dark pool / activity / jackpot / player control 的資料投影到可查詢 DB。
- 對 max win 可能超出池可承受範圍的情境發 alert。
- 對 settlement pool 與 dark pool before value 差異過大的情境發 alert。
- 讓後續 admin / monitoring 可以看 pool 狀態。

它不是:

- 下注派彩 source of truth。
- 完整 RTP 策略。
- 完整 jackpot owner。
- 完整即時風控平台。

## 8. DB / Redis / MQ / 外部 API

| 類型 | 已確認 | 說明 |
| --- | --- | --- |
| MQ in | Kafka `settled_bets` | monitor consumer 消費 settlement event |
| Redis | normal / free / jackpot / activity / player-control dark pool keys | reset sync 會讀 Redis snapshot |
| Redis | `antplay:settlePool:reset` | reset flag；存在就觸發 sync |
| DB | `settled_pool` | projection / snapshot table |
| DB | `settled_pool_bak` | `saveAll` 會先 backup current table |
| DB | `ag_player_control` | player control active config / success check |
| DB | activity config / agent / game | reset sync enumerates active data |
| Alert | `MonitorAlertUtil` | max win / diff alert |
| External API | 無直接 HTTP | 本 flow 是 Kafka + DB / Redis / alert |

## 9. 資料狀態與 State Transition

```text
BetRecord settled
  -> grouped into settle pool bucket
  -> batch total bet / win calculated
  -> DB row incremented
  -> alert comparison evaluated
```

Reset sync:

```text
Redis dark pool state
  -> reset flag detected
  -> reset flag deleted
  -> current DB backed up
  -> current DB truncated
  -> snapshot rows inserted
```

重要 source of truth 邊界:

- `BetRecord` 是已結算事件輸入。
- Redis dark pool 是 runtime 池子狀態。
- `settled_pool` 是 monitor / projection / snapshot，不是下注交易真相。
- `additional_info.beforeDp*` 是投注前 snapshot，用來做差異檢查；若 upstream 沒填，monitor 無法完整比對。

## 10. Failure Window / Consistency

| 風險 | 現況 | Senior / Owner 解讀 |
| --- | --- | --- |
| Kafka event 重送 | `saveOneIncrement` 以 DB current value 加 batch value，未見 event id 去重 | 重送可能重複累加；需要 batch id / bet id idempotency 或 reconciliation |
| reset flag 先刪 | `syncData` 一開始刪 `SETTLE_POOL_RESET` | 後續失敗可能不再自動 sync；需要 durable sync state 或失敗後重設 flag |
| `saveAll` truncate rebuild | 先 backup、truncate，再 insert | partial insert / crash window 高；需要 transaction 驗證與 restore / retry runbook |
| busy guard 註解 | `AtomicBoolean busy` 存在但 compareAndSet 被註解 | 多 consumer / 多 instance 同時遇到 reset flag 時，依 Redis delete 競態避免部分重入，但不是明確 distributed lock |
| `additional_info` null | production log error 後 return | 該筆不進 pool 計算；需要 upstream contract / missing metric |
| jackpot 單位 | jackpot total bet / win 使用 hao，其他多為 cent | alert / maxWin 比較要很小心，面試不能混用單位 |
| player control success | 超過門檻後只檢查 `isSuccess`，失敗 log error | 未見自動關閉或補償；只能說 monitor / diagnosis，不說完整控制治理 |
| `getOneValue` ignore type | `findByKey` 只依 agentId + key 查，未用 type filter | 若 key 唯一性足夠可行；若 key collision，type 不參與查詢會有風險 |

## 11. Retry / Compensation / Reconciliation

已確認:

- Consumer catch exception 後 log error，不 rethrow。
- `saveOneIncrement` 是 DB insert or update increment，沒有 event-level idempotency。
- Reset sync 有 backup table，但本 repo 未看到 restore flow。
- `SyncDbFromRedis` 對 activity / player control 子段 catch 後繼續，但整體 `saveAll` exception 會被 consumer catch。
- Alert 有 `SETTLED_POOL_MAXWIN` 與 `SETTLED_POOL_DIFF`。

待確認:

- Kafka listener ack / error handler 對此 consumer 的實際 offset commit 行為。
- `settled_pool` 是否有 DB unique key / DDL 和 backup restore runbook。
- Admin / monitoring 如何查 `settled_pool` 與 alert。
- 是否有 reconciliation job 以 bet record 重建 settle pool。
- 上游 `additional_info` 是否保證 before dark pool / jackpot / player control 欄位存在。

Owner 建議:

- 對 event projection 補 deterministic idempotency key，例如 batch id + bet id + pool type。
- Reset sync 不應只靠 delete flag；可增加 sync job id、狀態表、running lock、failure retry。
- `saveAll` truncate 前後要有 row count / checksum / restore path。
- Alert 應補 metric：generated groups、skipped additional_info、increment success、sync rows、sync failure。
- 單位 cent / hao 要在 code 與文件上明確標示。

## 12. Senior / Owner 設計取捨

| 決策點 | 現況 | 好處 | 風險 |
| --- | --- | --- | --- |
| 結算後做 projection | `settled_bets` -> monitor consumer | 不阻塞 runtime bet path | event replay 會影響 projection 正確性 |
| DB increment | `saveOneIncrement` | 寫法直接、可累加 batch | 缺 event idempotency |
| Reset from Redis | reset flag 觸發 snapshot | 可用 runtime Redis 修復 DB projection | sync 本身需要 lock / retry / restore |
| Jackpot 另拆 | jackpot group 額外計算 | 可分 normal pool 與 jackpot pool | 同一 normal bet 可能同時進 normal / jackpot bucket，面試要說清楚 |
| Activity 用 voucher bet | activity pool 用 `voucherBet` | 符合活動投放成本語意 | 若 voucherBet 單位或空值不一致會造成池差 |
| Player control diff | `totalWin - totalBet` | 可看單玩家控制差額 | success / status governance 不在本 flow |

## 13. Step 4 正式面試 Case

### 面試定位

這條 flow 面試時要定位成「settlement event 後的 monitor projection / Redis DB sync case」，不是下注結算、錢包、完整風控或完整 jackpot 平台。

安全主軸:

```text
settled_bets -> group by pool type -> settled_pool increment -> reset from Redis -> alert
```

### 30 秒版本

我有分析過 AntPlay job 裡的 settle pool monitor / dark pool sync flow。它消費 `settled_bets`，把 bet record 依 normal spin、free spin、jackpot、activity、player control 分群，計算 batch total bet / win 後 increment 到 `settled_pool`；如果 Redis 有 reset flag，還會從 Redis dark pool snapshot 重建 DB。這條我會保守講成 code-backed analysis，不是我的 direct implementation；面試重點會放在 Kafka replay idempotency、Redis / DB reset sync、truncate rebuild failure window、cent / hao 單位與 alert 邊界。

### 90 秒版本

這條 flow 是 settlement 事件後的 monitor projection。`SettlePoolMonitorConsumerService` 消費 `settled_bets` 後，會先檢查 Redis reset flag；如果有，就由 `SyncDbFromRedis` 讀 normal、activity、player control 等 dark pool key，backup `settled_pool`、truncate current table，再把 snapshot insert 回 DB。接著 consumer 會把本批 bet records 依 pool type 分群，透過 `MainHandler` 計算 batch total bet / total win，呼叫 repository 做 increment。

Senior 要看的不是「有沒有寫 DB」，而是這個 projection 在 failure 下能不能被信任。current `saveOneIncrement` 沒有看到 batch id / bet id 去重，所以 Kafka event replay 可能 double count。reset sync 先刪 flag，再做 backup / truncate / insert，中途失敗就需要人工 retry / restore runbook。jackpot pool 用 hao，一般 pool 多是 cent，單位混用會讓 maxWin / diff alert 失真。這些都是我會主動講的 owner trade-off。

### 3 分鐘版本

我可以講一條 AntPlay job 裡的 settle pool monitor / dark pool sync flow。這條 flow 不是下注或派彩 source of truth，而是 settlement event 後的 monitor projection。上游結算完成後送 `settled_bets`，job consumer 讀 `SettleBetsVo` 裡的一批 `BetRecord`。

第一段是 reset sync。consumer 每次處理前會檢查 Redis reset flag。flag 存在時，`SyncDbFromRedis` 先刪 flag，再枚舉 enabled agent、game、active activity、active player control，從 Redis 讀 normal spin、free spin、jackpot、activity、player control diff 等 key。接著 repository 會 backup current `settled_pool` 到 `settled_pool_bak`，truncate current table，再 insert snapshot rows。這裡的 owner 風險是 flag 先刪、table truncate、逐筆 insert，中途 exception 或 process crash 會讓 monitor DB 可能不完整，而且本 repo 沒看到 durable sync state 或 restore command evidence。

第二段是 event projection。`GroupSettleTypeRecord` 會 parse `additional_info`，依 bet type 分成 normal、activity、player control。normal bet 又會分 spin / free；如果是 jackpot game，還會額外進 jackpot bucket。`MainHandler` 會計 batch total bet / win，對 normal / activity 寫 total bet 和 total win，對 player control 寫 diff amount，也就是 `totalWin - totalBet`。一般池和 activity 多是 cent，jackpot 會轉 hao，所以面試時我會主動說單位邊界，不能把 cent / hao 混在同一個 alert 判斷。

可靠性上，這條 flow 最大的問題是它是 projection。Kafka event 如果 replay，而 DB 是 increment，沒有 deterministic event idempotency key，就會 double count。reset sync 內有 `AtomicBoolean busy`，但 compare-and-set guard 被註解，跨 instance 更不是 distributed lock；目前主要靠 Redis flag delete 降低重入，不是完整同步治理。`additional_info` null 時 production 只 log error 並跳過該筆，也會造成 projection 漏算。

所以我會把這條定位成 code-backed analysis case。它很適合展示我能從 Senior Backend 角度分析 event-driven projection、Redis / DB consistency、reset / rebuild、alert、reconciliation 與 owner decision；但我不會說這是我真實開發或我主導完整風控平台，因為 current path evidence 主要是 Arnold / Eliot。

### STAR

| 面向 | 內容 |
| --- | --- |
| Situation | AntPlay slot job 需要把 settlement event 後的 pool 狀態投影到 DB，並用 dark pool / settle pool 差異和 maxWin 做異常提醒。 |
| Task | 研讀 settle pool monitor flow，釐清 Kafka event、Redis dark pool、DB snapshot、alert 與 failure window。 |
| Action | 追 `SettlePoolMonitorConsumerService`、`GroupSettleTypeRecord`、`MainHandler`、`SyncDbFromRedis`、`SettledPoolRepositoryImpl`，整理 grouping、increment、reset sync、單位邊界與 claim boundary。 |
| Result | 形成 code-backed 面試 case，可回答 event projection idempotency、reset sync partial failure、Redis / DB consistency、reconciliation 與不可誇大邊界。 |

### Senior 追問短答

#### Q1: 這條 flow 的 source of truth 是什麼？

下注與派彩 source of truth 仍是 bet record / wallet / settlement；`settled_pool` 是 settlement event 後的 monitor projection，用來查 pool 狀態和 alert，不能反推交易一定正確。

#### Q2: Kafka event replay 會怎樣？

current code 以 `saveOneIncrement` 將 batch delta 累加到 DB，未見 processed event table 或 `batchId + betId + poolType` 去重。若同一 event replay，就可能 double count。改善方向是 deterministic idempotency key、processed-event table，或可重建 projection 的 reconciliation job。

#### Q3: reset sync 最大 failure window 是什麼？

`SyncDbFromRedis` 先刪 reset flag，再讀 Redis snapshot，最後 backup / truncate / insert DB。若刪 flag 後中途失敗，後續不一定自動重跑；若 truncate 後 insert 失敗，DB 可能 partial。Owner 要補 sync state、distributed lock、staging table、checksum 與 restore / retry runbook。

#### Q4: `AtomicBoolean busy` 有解決並發嗎？

沒有完整解決。code 裡 `busy` 存在，但 compare-and-set 和 release 被註解；就算啟用也只管 single JVM，不是 distributed lock。多 instance 場景仍需要 Redis lock / DB lock / sync state。

#### Q5: 為什麼 cent / hao 是面試重點？

normal / activity 多是 cent，jackpot 轉 hao。maxWin / diff alert 如果混用單位，可能誤報或漏報。Senior 要主動問清每個 key 的 unit，並讓 log、dashboard、threshold 都標明單位。

#### Q6: `additional_info` null 會怎樣？

production log error 後跳過該筆，projection 會漏算。這代表 upstream contract 很重要，應補 missing metric / alert，並確認 game-api producer 是否保證 before dark pool、jackpot amount、player control config 等欄位。

### Lead / Architect 追問

| 追問 | 回答方向 |
| --- | --- |
| 如果只能先改一件事？ | 先補 event-level idempotency 或 processed-event table，避免 replay double count。 |
| reset sync 怎麼設計更安全？ | pending / running / failed / done state、distributed lock、staging table rebuild、row count / checksum、restore command。 |
| 要不要 outbox？ | 這是 consumer projection，不是 producer side 事件發送；重點是 consumer idempotency、DLQ / replay 與 reconciliation，不一定先談 outbox。 |
| 怎麼做 reconciliation？ | 從 bet record 按同樣 grouping 重算 expected pool，和 `settled_pool` / Redis snapshot 比對，輸出差異與修復建議。 |
| 怎麼觀測？ | skipped additional_info、group count、increment success/failure、reset rows、sync failure、alert count、unit-specific threshold dashboard。 |
| 怎麼處理多 instance？ | 不靠 local `AtomicBoolean`；使用 Redis lock with TTL 或 DB state lock，並讓 sync operation idempotent。 |

### 面試常見陷阱

| 陷阱 | 避免方式 |
| --- | --- |
| 說成交易 source of truth | 改說 monitor projection，交易真相回 bet / wallet / settlement |
| 說成自己開發 | 改說 code-backed analysis；未找到 Nick direct path evidence |
| 只講流程不講風險 | 主動講 replay、reset partial failure、unit、additional_info |
| 說已經 exactly-once | 改說 current code 未見 event idempotency，需補設計 |
| 說完整風控 / jackpot owner | 改說 settle pool / dark pool monitor flow 分析 |

## 14. 面試 / 履歷邊界摘要

可面試講:

- 這是一條 code-backed 的 Kafka settlement projection / dark pool monitor flow。
- 我能讀懂它如何把 normal / free / jackpot / activity / player control 分群，如何累加 `settled_pool`，以及 reset flag 如何從 Redis rebuild DB。
- Senior 重點是 event replay idempotency、reset sync partial failure、Redis / DB consistency、cent / hao 單位、alert 與 runbook。

履歷要保守:

- 本 flow 不建議單獨寫入正式履歷。
- 可作 `antplay-slot-game-job` project-level 「settle pool / dark pool monitor code-backed analysis」補充素材。
- 目前不能說 Nick 真實開發此 flow。

不能誇大:

- 不說主導完整 settle pool / dark pool / risk platform。
- 不說主導完整 jackpot owner。
- 不說已建立可靠 replay / idempotency / reconciliation。
- 不把 Arnold / Eliot 的 commits 說成 Nick direct contribution。
- 不直接更新 `05 / 08`。

## 15. 本次實際掃描範圍

Vault:

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `projects/antplay/antplay-slot-game-job/README.md`
- `projects/antplay/antplay-slot-game-job/step1-candidate-flows.md`
- `projects/antplay/antplay-slot-game-job/step2-flow-comparison.md`
- `projects/antplay/antplay-slot-game-job/contribution-claim-consolidation.md`
- `projects/antplay/antplay-slot-game-api/flows/runtime-rtp-darkpool-player-control/*` 作上下游背景參考

Source code:

- `SettlePoolMonitorConsumerService`
- `GroupSettleTypeRecord`
- `MainHandler`
- `MainMethod`
- `SyncDbFromRedis`
- `SettledPoolRepository` / `SettledPoolRepositoryImpl`
- `SettledPool`
- `RedisKeysUtil` / `RedisKey`
- `SettlePoolContainer` / `SettlePoolKeyType`
- `AdditionalInfoVO`
- `PlayerControlServiceImpl` / `PlayerControlRepository`
- `application.yml` monitor toggle / topic config only

Git history:

- Path-specific log for settle pool package, `SettledPoolRepositoryImpl`, `SettledPool`, Redis key utilities, `AdditionalInfoVO`。
- Selected important commits: `909170d`, `9929cf2`, `5aceb24`, `854c00d`, `54b2a52`, `d026f37`, `76e5b76`, `fa6d9a0`, `266d730`, `e5b5d83`, `a6fec13`, `a25367b`。
- Current blame summary: core settle pool files are Arnold-heavy; early base includes Eliot; no Nick / `10gt12nc` direct path-specific evidence found.

未掃 / 待確認:

- 上游 `settled_bets` producer 最新 contract。
- Admin / monitoring 查詢 `settled_pool` 的下游。
- live Redis / DB schema / DB unique key / production alert config。
- Kafka listener ack / DLT / retry runtime behavior。
- Level 3 逐檔逐 commit diff。

## 16. Step 5 Claim Gate 結論

這條 flow 已完成 Step 5 claim gate。它是 AntPlay job repo 裡技術含量高的 code-backed flow，可用來練 Senior Backend 對 event projection、Redis / DB consistency、reset sync、alert、risk monitor 的分析能力。

Step 5 重新確認後，claim boundary 維持保守：

- `git log --all --author='10gt12nc|Nick|nick' -- <settle pool paths>` 沒有找到 direct path-specific commit。
- `beta-dev-bigWin-Nick` / `kafka_Nick` 對此 path 沒有修改紀錄。
- `origin/settle-pool` 主要是 Eliot / Arnold；`origin/risk-mng` 只看到早期 Eliot settle pool context。
- current blame 顯示 core files 仍是 Arnold-heavy，早期 base 有 Eliot。
- 因此本 flow 只能作 `專案存在 / code-backed / analysis-first`，不能標 `真實開發過`。

可面試講：能分析 `settled_bets` -> pool grouping -> `settled_pool` increment -> Redis reset snapshot -> alert 的 projection consistency、reset sync partial failure、unit boundary 與 reconciliation 改善。

不可履歷主寫：不回填 `05 / 08`，不單獨寫成正式履歷 bullet，不說 Nick 主導 settle pool / dark pool / risk / jackpot / player control platform。

Rank 5 `db-partition-job-report-routing Step 5` 已完成，project-level `contribution claim consolidation refresh` 也已完成。若 Nick 要把這批 job repo 素材回填到通用履歷 / 自傳，下一步做 `rolling resume package`；目前已完成的 job project-level consolidation 可沿用。

後續 rolling resume package 已完成；目前沒有預設下一步。
