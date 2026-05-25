# Interview Notes - settle-pool-monitor-darkpool-sync

日期: 2026-05-25
Step 5 補充日期: 2026-05-25

## Step 5 狀態

本檔是 `settle-pool-monitor-darkpool-sync` 的正式面試稿，已完成 Step 5 claim gate。這條 flow 是 code-backed / analysis-first case，不是 Nick direct implementation；面試價值在於 event projection、Redis / DB consistency、reset sync 與 owner decision。

## 面試主軸

```text
settled_bets -> group by pool type -> settled_pool increment -> reset sync from Redis -> alert
```

這條 flow 要講的是 projection consistency，不是交易正確性本身。安全說法是「我能讀懂並分析這條 flow 的可靠性邊界」，不是「我主導 settle pool / risk platform」。

## 可講問題

### Q1: 為什麼這條不是 source of truth？

因為下注 / 派彩結果要回到 bet record / wallet / settlement。`settled_pool` 是 settlement event 後的 monitor projection，用來看 pool 狀態和 alert。

### Q2: Kafka event 重送會怎樣？

current `saveOneIncrement` 以 DB current value 加 batch value，未見 batch id / bet id 去重。若同一 event 重送，可能 double count。

### Q3: Reset sync 最大風險是什麼？

`SyncDbFromRedis` 先刪 reset flag，再從 Redis 讀 snapshot，最後 `saveAll` backup / truncate / insert。如果中途失敗，flag 已消失，DB 可能是 partial state，需要 retry / restore runbook。

### Q4: 為什麼單位很重要？

normal / activity 多用 cent，jackpot 轉 hao。maxWin / diff alert 如果混用單位，就會誤報或漏報。

### Q5: 為什麼這條不能放履歷主成果？

因為本輪 path-specific history 與 blame 顯示 core current implementation 主要是 Arnold / Eliot，沒有 Nick direct commit。可作 code-backed analysis，不可說真實開發。

## 30 秒版本

我有分析過 AntPlay job 裡的 settle pool monitor / dark pool sync flow。它消費 `settled_bets`，把 bet record 依 normal spin、free spin、jackpot、activity、player control 分群，計算 batch total bet / win 後 increment 到 `settled_pool`；如果 Redis 有 reset flag，還會從 Redis dark pool snapshot 重建 DB。這條我會保守講成 code-backed analysis，不是我的 direct implementation；面試重點會放在 Kafka replay idempotency、Redis / DB reset sync、truncate rebuild failure window、cent / hao 單位與 alert 邊界。

## 90 秒版本

這條 flow 是 settlement 事件後的 monitor projection。`SettlePoolMonitorConsumerService` 消費 `settled_bets` 後，會先檢查 Redis reset flag；如果有，就由 `SyncDbFromRedis` 讀 normal、activity、player control 等 dark pool key，backup `settled_pool`、truncate current table，再把 snapshot insert 回 DB。接著 consumer 會把本批 bet records 依 pool type 分群，透過 `MainHandler` 計算 batch total bet / total win，呼叫 repository 做 increment。

Senior 要看的不是「有沒有寫 DB」，而是這個 projection 在 failure 下能不能被信任。current `saveOneIncrement` 沒有看到 batch id / bet id 去重，所以 Kafka event replay 可能 double count。reset sync 先刪 flag，再做 backup / truncate / insert，中途失敗就需要人工 retry / restore runbook。jackpot pool 用 hao，一般 pool 多是 cent，單位混用會讓 maxWin / diff alert 失真。這些都是我會主動講的 owner trade-off。

## 3 分鐘版本

我可以講一條 AntPlay job 裡的 settle pool monitor / dark pool sync flow。這條 flow 不是下注或派彩 source of truth，而是 settlement event 後的 monitor projection。上游結算完成後送 `settled_bets`，job consumer 讀 `SettleBetsVo` 裡的一批 `BetRecord`。

第一段是 reset sync。consumer 每次處理前會檢查 Redis reset flag。flag 存在時，`SyncDbFromRedis` 先刪 flag，再枚舉 enabled agent、game、active activity、active player control，從 Redis 讀 normal spin、free spin、jackpot、activity、player control diff 等 key。接著 repository 會 backup current `settled_pool` 到 `settled_pool_bak`，truncate current table，再 insert snapshot rows。這裡的 owner 風險是 flag 先刪、table truncate、逐筆 insert，中途 exception 或 process crash 會讓 monitor DB 可能不完整，而且本 repo 沒看到 durable sync state 或 restore command evidence。

第二段是 event projection。`GroupSettleTypeRecord` 會 parse `additional_info`，依 bet type 分成 normal、activity、player control。normal bet 又會分 spin / free；如果是 jackpot game，還會額外進 jackpot bucket。`MainHandler` 會計 batch total bet / win，對 normal / activity 寫 total bet 和 total win，對 player control 寫 diff amount，也就是 `totalWin - totalBet`。一般池和 activity 多是 cent，jackpot 會轉 hao，所以面試時我會主動說單位邊界，不能把 cent / hao 混在同一個 alert 判斷。

可靠性上，這條 flow 最大的問題是它是 projection。Kafka event 如果 replay，而 DB 是 increment，沒有 deterministic event idempotency key，就會 double count。reset sync 內有 `AtomicBoolean busy`，但 compare-and-set guard 被註解，跨 instance 更不是 distributed lock；目前主要靠 Redis flag delete 降低重入，不是完整同步治理。`additional_info` null 時 production 只 log error 並跳過該筆，也會造成 projection 漏算。

所以我會把這條定位成 code-backed analysis case。它很適合展示我能從 Senior Backend 角度分析 event-driven projection、Redis / DB consistency、reset / rebuild、alert、reconciliation 與 owner decision；但我不會說這是我真實開發或我主導完整風控平台，因為 current path evidence 主要是 Arnold / Eliot。

## STAR

| 面向 | 內容 |
| --- | --- |
| Situation | AntPlay slot job 需要把 settlement event 後的 pool 狀態投影到 DB，並用 dark pool / settle pool 差異和 maxWin 做異常提醒。 |
| Task | 研讀 settle pool monitor flow，釐清 Kafka event、Redis dark pool、DB snapshot、alert 與 failure window。 |
| Action | 追 `SettlePoolMonitorConsumerService`、`GroupSettleTypeRecord`、`MainHandler`、`SyncDbFromRedis`、`SettledPoolRepositoryImpl`，整理 grouping、increment、reset sync、單位邊界與 claim boundary。 |
| Result | 形成 code-backed 面試 case，可回答 event projection idempotency、reset sync partial failure、Redis / DB consistency、reconciliation 與不可誇大邊界。 |

## Failure Scenarios

| 情境 | 面試回答重點 |
| --- | --- |
| event replay | `settled_pool` increment 缺 event idempotency，可能 double count |
| reset sync crash | flag 先刪、DB truncate / insert 中斷，需要 runbook |
| busy guard disabled | `AtomicBoolean` 存在但註解，缺明確 distributed lock |
| additional_info null | production log error and skip，projection 可能漏算 |
| jackpot unit mismatch | jackpot hao vs normal cent，alert 可能失真 |
| admin / alert 未掃 | 只能說 producer side / repository side，不能宣稱完整 monitoring |

## Senior 追問短答

### Q1: 這條 flow 的 source of truth 是什麼？

下注與派彩 source of truth 仍是 bet record / wallet / settlement；`settled_pool` 是 settlement event 後的 monitor projection，用來查 pool 狀態和 alert，不能反推交易一定正確。

### Q2: Kafka event replay 會怎樣？

current code 以 `saveOneIncrement` 將 batch delta 累加到 DB，未見 processed event table 或 `batchId + betId + poolType` 去重。若同一 event replay，就可能 double count。改善方向是 deterministic idempotency key、processed-event table，或可重建 projection 的 reconciliation job。

### Q3: reset sync 最大 failure window 是什麼？

`SyncDbFromRedis` 先刪 reset flag，再讀 Redis snapshot，最後 backup / truncate / insert DB。若刪 flag 後中途失敗，後續不一定自動重跑；若 truncate 後 insert 失敗，DB 可能 partial。Owner 要補 sync state、distributed lock、staging table、checksum 與 restore / retry runbook。

### Q4: `AtomicBoolean busy` 有解決並發嗎？

沒有完整解決。code 裡 `busy` 存在，但 compare-and-set 和 release 被註解；就算啟用也只管 single JVM，不是 distributed lock。多 instance 場景仍需要 Redis lock / DB lock / sync state。

### Q5: `additional_info` null 會怎樣？

production log error 後跳過該筆，projection 會漏算。這代表 upstream contract 很重要，應補 missing metric / alert，並確認 producer 是否保證 before dark pool、jackpot amount、player control config 等欄位。

## Lead / Architect 追問方向

| 追問 | 回答方向 |
| --- | --- |
| 如何讓 projection replay-safe？ | batch id / bet id / pool type deterministic key，或 event processed table |
| reset sync 怎麼設計更安全？ | 狀態表、distributed lock、idempotent rebuild、staging table、checksum、restore runbook |
| 需要 outbox 嗎？ | 這是 consumer projection，重點是 processed-event idempotency 與 reconciliation，不一定是 outbox |
| 怎麼做 reconciliation？ | 從 bet record 按同樣 grouping 重算，和 `settled_pool` 比對差異 |
| 怎麼觀測？ | skipped additional_info、group count、increment count、reset rows、alert count、sync failure metric |

## 面試常見陷阱

| 陷阱 | 避免方式 |
| --- | --- |
| 說成交易 source of truth | 改說 monitor projection，交易真相回 bet / wallet / settlement |
| 說成自己開發 | 改說 code-backed analysis；未找到 Nick direct path evidence |
| 只講流程不講風險 | 主動講 replay、reset partial failure、unit、additional_info |
| 說已經 exactly-once | 改說 current code 未見 event idempotency，需補設計 |
| 說完整風控 / jackpot owner | 改說 settle pool / dark pool monitor flow 分析 |

## 可用反問

- 你們的 projection 是允許 eventual consistency，還是要求和交易帳完全一致？
- Kafka replay / DLT 重放時，你們怎麼避免 derived table double count？
- Redis snapshot rebuild 有沒有 staging table、checksum 或 restore runbook？
- alert threshold 的單位和 source of truth 是誰定義的？
- 多 instance consumer 同時看到 reset flag 時，你們用什麼 lock / state 防重？

## Step 5 結論

Step 5 已完成 claim gate。這條 flow 可以當 Senior Backend 的 code-backed analysis case，用來講 event projection consistency、reset sync partial failure、Redis / DB source boundary、單位治理、observability 與 reconciliation。它不回填正式履歷主成果；面試時要主動說明這是分析過的 code-backed flow，不是 Nick direct implementation。
