# settle-pool-monitor-darkpool-sync Career / Interview

日期: 2026-05-25
Step 5 補充日期: 2026-05-25

## Evidence Level

- 證據層級: 專案存在 / code-backed / analysis-first。
- Nick evidence: 本輪未找到 Nick / `10gt12nc` 對 settle pool monitor 主路徑的 direct path-specific commit。
- Current behavior evidence: `SettlePoolMonitorConsumerService`、`GroupSettleTypeRecord`、`MainHandler`、`SyncDbFromRedis`、`SettledPoolRepositoryImpl` 可完整追到。
- 完成狀態: Step 5 claim gate 完成。
- 本文件是 flow-level 素材，不是 project-level final consolidation。正式履歷仍以 contribution consolidation / rolling resume package 為準。

## 履歷保守 Bullet

不建議單獨放正式履歷。

可作面試或 project-level 補充素材:

- 研讀 AntPlay slot job 的 settle pool / dark pool monitor flow，能說明 settled bet event 如何分群、累加 `settled_pool`，以及 Redis reset flag 如何觸發 DB snapshot rebuild。

如果一定要放在經驗描述，只能併入更大的 `antplay-slot-game-job` code-backed 分析，不寫成真實開發:

- 具備 AntPlay slot job 中 Kafka settlement projection、Redis / DB consistency、dark pool monitor 與 alert flow 的 code-backed 分析經驗。

## 面試定位

這條 flow 適合被問到「你怎麼看非同步 projection、Redis / DB consistency、風控監控資料」時使用。

安全定位:

```text
settled_bets -> grouping -> settled_pool increment -> reset sync -> alert
```

不安全定位:

```text
我主導完整風控 / dark pool / jackpot 平台
```

## 面試 30 秒版本

我有分析過 AntPlay job 裡的 settle pool monitor / dark pool sync flow。它消費 `settled_bets`，把 bet record 依 normal spin、free spin、jackpot、activity、player control 分群，計算 batch total bet / win 後 increment 到 `settled_pool`；如果 Redis 有 reset flag，還會從 Redis dark pool snapshot 重建 DB。這條我會保守講成 code-backed analysis，不是我的 direct implementation；面試重點會放在 Kafka replay idempotency、Redis / DB reset sync、truncate rebuild failure window、cent / hao 單位與 alert 邊界。

## 面試 90 秒版本

這條 flow 是 settlement event 後的 monitor / projection。`SettlePoolMonitorConsumerService` 會消費 `settled_bets`，先檢查 Redis reset flag；如果有 reset flag，就從 Redis 讀 normal、activity、player control 等 dark pool key，把 snapshot 重建到 `settled_pool`。之後才把這批 bet records 分群，依不同 pool type 計算 batch total bet / total win，透過 repository 寫入 DB。

Senior 要看的不是單純「有沒有寫 DB」，而是 consistency。像 Kafka event 重送時，current `saveOneIncrement` 沒有 event-level idempotency，可能重複累加；reset sync 先刪 flag 再做 backup / truncate / insert，中途失敗會需要 runbook 補救；jackpot pool 用 hao，一般 pool 多用 cent，單位如果混掉，alert 判斷會失真。

這條我不會說是我開發，因為 current path evidence 主要是 Arnold / Eliot。我會把它當成我能讀懂並分析的高價值後端案例。

## 面試 3 分鐘版本

我可以講一條 AntPlay job 裡的 settle pool monitor / dark pool sync flow。它不是下注結算 source of truth，而是 settlement event 後的 monitor projection。上游結算完成後送 `settled_bets`，job consumer 端會讀 `SettleBetsVo`，拿到一批 `BetRecord`。

第一件事是 reset sync。如果 Redis 裡有 `antplay:settlePool:reset`，`SyncDbFromRedis` 會先刪掉 reset flag，然後枚舉 enabled agent、game、active activity、active player control，從 Redis 讀 normal spin、free spin、jackpot、activity、player control diff 等 key。接著 repository 會先把 current `settled_pool` backup 到 `settled_pool_bak`，truncate current table，再把 Redis snapshot insert 回去。這段 owner 要注意：flag 先刪、DB truncate、逐筆 insert，中間任何失敗都要有 retry / restore runbook，不然 monitor DB 可能短時間不完整。

第二段是 event projection。`GroupSettleTypeRecord` 依 bet type 分群：normal 會分 normal spin / free，jackpot game 會額外進 jackpot bucket；activity 依 activityId / game / currency 分 activity spin / free；player control 依 agent / currency / account / configId 分群。`MainHandler` 會計 batch total bet / win，寫入 `settled_pool`。一般池和 activity 池多是 cent，jackpot 池會轉成 hao；這是面試要主動講清楚的單位風險。

可靠性上，這條 flow 最大的問題是它是 projection，不是交易真相。Kafka event 如果重送，而 DB 寫入只是 increment，沒有 batch id 或 bet id 去重，就可能 double count。reset sync 如果多 instance 同時跑，code 雖有 `AtomicBoolean busy` 但 guard 被註解，主要靠 Redis flag delete 避免重複，這不是完整 distributed lock。還有 `additional_info` 如果缺失，production 只 log error 並跳過該筆，會造成 projection 漏算。

所以我會把這條定位成 code-backed analysis case。它很適合展示 Senior Backend 對 event-driven projection、Redis / DB consistency、reset / rebuild、alert 與 reconciliation 的理解，但我不會把它包裝成我主導完整風控平台。

## STAR

| 面向 | 內容 |
| --- | --- |
| Situation | AntPlay slot job 需要把 settlement event 後的 pool 狀態投影到 DB，並在 dark pool / settle pool 差異異常時 alert。 |
| Task | 研讀 settle pool monitor flow，釐清 Kafka event、Redis dark pool、DB snapshot、alert 與 failure window。 |
| Action | 追 `SettlePoolMonitorConsumerService`、`GroupSettleTypeRecord`、`MainHandler`、`SyncDbFromRedis`、`SettledPoolRepositoryImpl`，整理 grouping、increment、reset sync 與 claim boundary。 |
| Result | 形成 code-backed 面試素材，可說明 event projection 的 idempotency、reset sync partial failure、Redis / DB consistency 與不可誇大邊界。 |

## Senior 追問短答

### Q1: 這條 flow 的 source of truth 是什麼？

下注與派彩 source of truth 仍是 bet record / wallet / settlement；`settled_pool` 是 settlement event 後的 monitor projection，用來查 pool 狀態和 alert。

### Q2: replay-safe 要怎麼做？

current code 是 increment projection，未見 event idempotency。可補 `batchId + betId + poolType` processed-event table，或讓 projection 可從 bet record 重算，再用 reconciliation 修正差異。

### Q3: reset sync 怎麼改安全？

不要只靠刪 Redis flag。應加 sync 狀態、distributed lock、staging table rebuild、row count / checksum 驗證、restore / retry runbook。

### Q4: 為什麼這條不放正式履歷？

因為本輪 path-specific log / blame 沒有找到 Nick direct evidence；可作 code-backed analysis 和面試素材，但不說真實開發或主導。

## 可說

- 這是 code-backed analysis，不是 Nick direct implementation。
- 可講 settled bet event 如何分成 normal / free / jackpot / activity / player control。
- 可講 `settled_pool` increment 的 replay 風險。
- 可講 reset flag -> Redis snapshot -> DB rebuild 的 partial failure 風險。
- 可講 cent / hao 單位、additional_info missing、alert threshold 與 runbook。

## 不可誇大

- 不說真實開發過此 flow。
- 不說主導完整 settle pool / dark pool / risk platform。
- 不說主導 jackpot / player control policy。
- 不說已確認 production runbook / reconciliation / DLT。
- 不直接更新 `05 / 08`。

## Step 5 Claim Gate

Step 5 已完成 claim gate；仍維持 code-backed / analysis-first，不升級履歷 claim。重新掃 Nick / `10gt12nc` author log、Nick 命名 branch、`origin/settle-pool` / `origin/risk-mng` path log 與 current blame 後，沒有找到 Nick direct path-specific evidence。這條只能作 interview-only supporting case，不回填 `05 / 08`，不寫成真實開發或主導 settle pool / risk。
