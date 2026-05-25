# big-win-notification

日期: 2026-05-25

Step 4 補充日期: 2026-05-25

Step 5 補充日期: 2026-05-25

## 0. 閱讀定位

- Flow 中文名稱: 中大獎通知 / push user message。
- Flow slug: `big-win-notification`。
- 完成狀態: Step 5 claim gate 完成。
- 證據層級: 真實開發過 + code-backed；`10gt12nc` 在 `#303` 有 direct commits 建立 big-win consumer 與小數格式修正，current code 後續有 Gill / Arnold / Eliot 修改。Step 5 已確認可作 project-level supporting evidence，但不能升級成完整 push platform claim。
- 本 flow 類型: Kafka settlement event -> derived notification -> push user topic。
- 是否只確認到入口: 否，已確認 consumer、payload model、game translation cache、producer wrapper、feature toggle、path-specific history 與 `antplay-push` websocket bridge；上游 producer 與實際遊戲前端未確認最新，不作完整上下游 claim。

Source repo 遠端狀態: 先前同 repo `git fetch --all --prune` 已因內網不可達失敗；依 KB 不反覆重試。local `master` / local `origin/master` 都在 `d847357`，ahead / behind `0 / 0`，但未確認最新遠端，本文件依本地 refs / 本地 working tree 保守分析。

## 1. 白話導讀

這條 flow 的目的，是在玩家某次結算後贏分達到投注額一定倍數時，產生「中大獎」通知，送到 push user topic，讓前端或通知服務可以廣播給對應代理的玩家。

它不是下注結算主流程，也不是錢包或獎金 source of truth。它是 settlement event 之後的 derived notification。成功後，系統會產生一筆包含玩家遮罩名稱、遊戲代碼、遊戲顯示名稱、幣別、金額與 channel 的 JSON message，送到 `push_user` topic。失敗時，最直覺會壞在:

- 該通知沒送出去。
- 遊戲翻譯缺漏導致通知被跳過。
- 玩家名稱遮罩或 full name 使用不當造成隱私風險。
- Kafka producer 失敗只記 log，沒有補償或重送治理。
- event 重送時重複推通知。

## 2. 初中階 Code 分層對照

| 分層 | Code / 資料 | 作用 | 本輪判斷 |
| --- | --- | --- | --- |
| Event source | Kafka `settled_bets` | 上游結算完成後的一批 bet records | consumer 已確認；producer 端未確認最新 |
| Payload VO | `SettleBetsVo` | 包 `batch_id` 與 `List<BetRecord>` | 已確認 |
| Domain model | `BetRecord` | 下注結算資料，含 `agentId / game / account / bet / voucherBet / totalWin / currency` | 已確認 |
| Consumer | `BigWinConsumerService#consumeBigWin` | 判斷中大獎、組 push message | 已確認 |
| Cache | `GameDataCache` | 每 60 秒載入 agents、game translations、message templates | 已確認 |
| Translation | `TransGames` | 依 game code + lang 找遊戲名稱 | 已確認 |
| Producer | `KafkaProducerService#sendMessage` | 用 `KafkaTemplate` 非同步送到 push user topic | 已確認 wrapper；下游 consumer 未掃 |
| Config | `application.yml` / `@ConditionalOnProperty` | `spring.kafka.consumer.task.big-win` 控制 consumer 啟用 | 已確認 current config 為 true；不記錄環境敏感值 |
| Privacy | `maskPlayerName` | 將玩家名稱遮罩後放入通知 | 已確認 current code 同時也放 `fullPlayerName`，有隱私邊界 |

## 3. 最小架構圖

```text
antplay-slot-game-api / upstream settle (待確認最新)
  -> Kafka topic: settled_bets
  -> BigWinConsumerService
  -> GameDataCache / TransGames
  -> KafkaProducerService
  -> Kafka topic: push_user
  -> antplay-push websocket bridge (已掃本地 refs)
  -> frontend notification rendering (未掃)
```

## 4. 正常流程圖

```text
settled_bets event
  -> parse SettleBetsVo
  -> for each BetRecord
  -> read bet + voucherBet + totalWin
  -> if totalWin >= (bet + voucherBet) * 10
  -> find agent
  -> choose translation language by currency
  -> find TransGames name by game code
  -> build pushUserData JSON
  -> mask player name
  -> send to push_user topic
  -> collect bet ids by agent
```

## 5. 正常流程逐步說明

1. `BigWinConsumerService` 只有在 `spring.kafka.consumer.task.big-win=true` 時啟用。
2. Listener 消費 `settled_bets`，groupId 是 `big_win`，concurrency 由 topic3 consumer 設定控制。
3. Consumer 把 Kafka value 反序列化成 `SettleBetsVo`，取得 `List<BetRecord>`。
4. 每筆 bet record 讀 `bet`、`voucherBet` 與 `totalWin`。
5. 若 `totalWin >= (bet + voucherBet) * 10`，視為中大獎候選。
6. 依 `agentId` 到 `GameDataCache#getAllAgents()` 找對應 agent。
7. current code 依 currency 決定翻譯語系: CNY 使用 `cn`，其他幣別使用 `en`。
8. 從 `GameDataCache#getAllTransGames()` 找同語系、同 game code 的 `TransGames#name`。
9. 若找不到遊戲名稱，記 error 並跳過該 bet record，不發通知。
10. 組 `pushUserData`: `_id`、`type=3`、`channel=agent_{agentId}_all`、`data`。
11. `data` 包含 `playerName`、`gameCode`、`fullPlayerName`、`enName`、`prize`、`currency`。
12. 呼叫 `KafkaProducerService#sendMessage(pushUserTopic, 0, pushUserData)` 非同步送出。
13. 主要 try/catch 結束後，另呼叫 `BetIdPersistence#collectBetIdsByAgent(betRecordList)`；Step 5 已確認它是 per-agent 最大 bet id / request id 進度紀錄，不是通知去重 evidence。

## 6. 業務問題

這條 flow 解的是「玩家中大獎時，要即時或近即時廣播通知」。

它的業務價值偏營運與玩家體驗，不是交易正確性本身:

- 促進遊戲熱度與社群感。
- 讓玩家看到某代理下有人中大獎。
- 對前端顯示文案、遊戲名稱、幣別與金額格式敏感。
- 錯了通常不是錢包錯，而是通知漏發、重複發、顯示錯、隱私外洩或誤導玩家。

## 7. 系統位置與入口

主要入口:

- `src/main/java/com/ps/domain/kafka/service/BigWinConsumerService.java`

主要輔助:

- `src/main/java/com/ps/domain/kafka/service/KafkaProducerService.java`
- `src/main/java/com/ps/domain/kafka/gameData/job/GameDataCache.java`
- `src/main/java/com/ps/domain/kafka/gameData/entity/TransGames.java`
- `src/main/java/com/ps/domain/kafka/gameData/dao/TransGameRepository.java`
- `src/main/java/com/ps/domain/kafka/gameData/dao/AgentsRepository.java`
- `src/main/java/com/ps/domain/game/slot/data/entity/BetRecord.java`
- `src/main/java/com/ps/quartz/log/vo/SettleBetsVo.java`
- `src/main/resources/application.yml`

未掃 / 待確認:

- 上游 `settled_bets` producer 最新 contract。
- 下游 `antplay-push` websocket bridge 已掃本地 refs；實際 frontend rendering 未掃。
- `BetIdPersistence#collectBetIdsByAgent` 已確認不是通知去重；完整 id 進度用途不展開。

## 8. DB / Redis / MQ / 外部 API

| 類型 | 已確認 | 說明 |
| --- | --- | --- |
| MQ in | Kafka `settled_bets` | big-win consumer 消費 settled bet event |
| MQ out | Kafka `push_user` | producer wrapper 非同步送通知 message |
| DB read | `AgentsRepository` | `GameDataCache` 讀 agents |
| DB read | `TransGameRepository` | `GameDataCache` 讀 game translations |
| DB read | `TransMessageTemplateRepository` | current code message template 已移到前端，cache 仍會讀 |
| Redis | 未確認 | 本 flow 主路徑未看到 Redis state |
| External API | 無直接呼叫 | 下游通知是 Kafka topic，不是 HTTP API |

## 9. 資料狀態與 state transition

```text
BetRecord settled
  -> big-win condition matched / not matched
  -> translation found / missing
  -> pushUserData built
  -> Kafka producer accepted send future
  -> async callback success / failure log
```

狀態語意:

- `BetRecord`: 上游結算結果，是判斷通知的輸入，不是通知本身的 source of truth。
- `pushUserData`: derived notification payload，包含 UUID `_id`；Step 5 補查下游 `antplay-push` 後，未看到用 `_id` 做去重的 evidence。
- `GameDataCache`: translation / agent cache，current code 每 60 秒更新。
- `push_user`: 下游通知通道；已掃到 `antplay-push` bridge 會轉送 websocket topic，但實際前端 rendering / delivery 未確認，不能宣稱通知一定抵達玩家。

## 10. Failure Window / Consistency

### 10.1 Kafka event 重送造成重複通知

本 flow 會為每次符合條件的 record 產生新的 UUID `_id`。若 `settled_bets` event 被重送，current repo 未看到 deterministic idempotency key，例如 bet id + agent id + notification type。這代表重送時可能產生另一筆通知。

### 10.2 Producer 非同步失敗只記 log

`KafkaProducerService#sendMessage` 使用 `KafkaTemplate#send` 回傳 `CompletableFuture`，`whenComplete` 成功或失敗只記 log。呼叫端不等待 send 完成，也沒有把 failure 重新丟出給 consumer。這對通知類 flow 可接受度可能較高，但 owner 要知道這是 at-most-observed / best-effort notification，不是強一致事件。

### 10.3 缺翻譯直接跳過

若 `TransGames` 找不到對應 game code / lang，current code 記 error 並 `continue`。這避免送出空遊戲名稱，但會漏通知。這是產品取捨: 寧可不發，還是用 game code fallback。

### 10.4 玩家隱私

current code 會放遮罩後的 `playerName`，但也放 `fullPlayerName`。Step 5 補查到 `antplay-push` 會把 JSON 原樣轉送 websocket topic，未見過濾 `fullPlayerName`。如果前端不該拿完整帳號，這是隱私與資料最小化風險。

### 10.5 金額單位與門檻

current code 用 `totalWin / 100.0` 格式化成兩位小數，並以 `(bet + voucherBet) * 10` 判斷大獎。這比初版 `detail.totalBet * 10` 更接近 current model，但仍要確認 `bet / voucherBet / totalWin` 單位一致，及 voucher bet 是否應納入門檻。

### 10.6 Cache stale

`GameDataCache` 每 60 秒更新 agents / translations。新增或修正遊戲翻譯後，最多可能有短時間 stale window；如果 cache load 失敗的行為未加保護，也可能影響通知產生。

## 11. Retry / Compensation / Reconciliation

本輪已確認:

- Kafka producer send 是 async callback log。
- push message 有 UUID `_id`，每次通知重新產生，不是 deterministic notification key。
- big-win consumer 外層 catch exception，不重新 throw。
- 缺翻譯時跳過通知。
- `BetIdPersistence#collectBetIdsByAgent` 會依 agent 保存目前最大 bet id 到 `ag_id_store` 類 id store，並另有 `collectOthers` 掃 request / transfer / transaction table 最新 id；沒有證據顯示它支援 big-win notification 去重。
- `antplay-push` 的 `WspushKafkaListener#listenPush` 會消費 `push_user`，取 `channel` 和 `_id` log 後把 JSON 原樣送到 `/topic/{channel}`；本地 code 未看到 `_id` 去重或 `fullPlayerName` 過濾。

本輪未確認:

- 下游 `push_user` consumer 是否有 retry / DLQ / idempotency。
- 更完整前端 rendering / permission 邊界；本輪只掃到 `antplay-push` websocket bridge，未掃實際遊戲前端畫面。
- 是否有補推通知工具或營運查詢。
- Kafka listener ack / error handler 對本 consumer 的實際 offset commit 行為。

Owner 建議:

- 對 notification payload 補 deterministic idempotency key，例如 `betRecord.id + notificationType`。
- Producer failure 至少要有 metric / alert，重要通知可落 durable outbox。
- 缺翻譯可以分等級: game code fallback + alert，比直接漏通知更可追。
- 明確定義通知隱私資料，避免 full account 被廣播到不需要的下游。

## 12. Senior / Owner 設計取捨

| 決策點 | 現況 | 好處 | 風險 / 待補 |
| --- | --- | --- | --- |
| 用 settled event 衍生通知 | `settled_bets` -> `BigWinConsumerService` | 不阻塞下注主流程 | Event 重送可能重複通知 |
| 非同步送 push topic | `KafkaProducerService#sendMessage` | 呼叫端快速返回 | send failure 只記 log，補償未確認 |
| 缺翻譯跳過 | 找不到 `TransGames` 就 continue | 避免送空名稱 | 漏通知，需要 alert / fallback |
| 玩家名稱遮罩 | `maskPlayerName` | 降低公開顯示風險 | 同 payload 仍帶 `fullPlayerName` |
| 幣別決定語系 | CNY -> cn，其他 -> en | 簡單規則 | agent language 被註解，不一定符合多語系需求 |
| UUID `_id` | 每次通知生成新 UUID；下游 `antplay-push` 只取出 log / route | 可追單筆 message | 不適合做重送去重 key，未見下游 dedupe |
| Bet id collection | 保存 per-agent 最大 bet / request id | 可支援 id 進度追蹤 | 不等於通知補償 / 去重 |

## 13. Lead / Architect 追問

1. 這條通知是 best-effort 還是保證送達？
2. `settled_bets` 重送時會不會重複推中大獎？
3. Producer send failure 只 log，營運怎麼知道通知漏了？
4. `fullPlayerName` 是否應該出現在 push payload？
5. 缺遊戲翻譯時應該跳過，還是用 game code fallback？
6. 幣別決定語系是否符合 agent / player language？
7. `(bet + voucherBet) * 10` 是產品規格，還是 current implementation 的技術假設？
8. 下游 push user consumer 有沒有 DLT / retry / idempotency？

## 14. 面試 / 履歷邊界摘要

可作面試:

- Kafka settlement event 之後的 derived notification flow。
- 通知不是交易 source of truth，偏 best-effort / UX。
- 玩家名稱遮罩、翻譯 fallback、金額格式、重複通知與 producer failure。
- 初版 direct commits 與 current behavior 被後續修正的差異。

履歷要保守:

- 可作 `antplay-slot-game-job` project-level 「big-win notification / Kafka derived event」supporting evidence。
- 可以說參與中大獎通知的 event consumer / push message 組裝與格式調整。
- Step 5 不直接更新 `05 / 08`，也不單獨新增正式履歷 bullet。

不能誇大:

- 不說完整 push platform owner。
- 不說通知 exactly-once / guaranteed delivery。
- 不說完整 jackpot / bonus owner。
- 不把 Arnold / Gill / Eliot 後續 current behavior 全部說成 Nick 完成。

## 15. Step 5 Claim Gate 結論

這條 flow 已完成 Level 2 Step 3 學習包、Step 4 面試 case 與 Step 5 claim gate。它比 `activity-accumulated-bet-voucher` 風險低、scope 小，而且 Nick direct evidence 更乾淨：`#303` 直接新增 `BigWinConsumerService` 與小數格式修正。current code 後續由 Gill / Arnold / Eliot 補玩家遮罩、currency / translation、`totalWin` 判斷與 id collection，因此正式說法要主動講「初版參與 + current behavior code-backed 分析」，不要說完整通知平台 owner。

Step 5 補查後結論:

- 可回填 project-level `antplay-slot-game-job` consolidation，作為 big-win notification / Kafka derived event supporting evidence。
- 不單獨更新 `05 / 08`，因為正式履歷仍以 project-level consolidation 與 rolling resume package 為準。
- 可以保守說參與中大獎通知初版、金額格式修正，並能用 current code 講 privacy / translation / producer failure / replay duplicate。
- `_id` 每次 UUID，`BetIdPersistence` 是 id 進度紀錄，`antplay-push` 下游未見去重；不能宣稱通知 exactly-once、guaranteed delivery 或完整 push platform owner。
- 下游 websocket bridge 會把 JSON 原樣送到 channel，未見 `fullPlayerName` 過濾；因此面試要主動指出 privacy 最小化風險。

2026-05-25 後續進度: Rank 4 `settle-pool-monitor-darkpool-sync Step 5` 已完成，Rank 5 `db-partition-job-report-routing Step 3` 已完成。下一步若延續 `antplay-slot-game-job` Flow Track，可做 `db-partition-job-report-routing Step 4`。settle pool 是 code-backed analysis-first flow，不應直接包裝成 Nick 主導的 settle pool / risk owner。
