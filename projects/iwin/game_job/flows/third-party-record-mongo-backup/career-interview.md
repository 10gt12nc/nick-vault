# third-party-record-mongo-backup career-interview

完成狀態：Step 5。

證據層級：局部真實開發過 + code-backed；`10gt12nc` 有 GSC 分批查詢與 batch size 調整 commits。

## 一句話版本

這是一條第三方遊戲紀錄 Mongo retention flow：把 Antplay / GSC 的 provider log 與 transaction 文件從 active collection 分批搬到 backup collection，再清掉過期 backup。面試價值在 copy-then-delete 的一致性、idempotency、retention policy 與 observability。

## 30 秒版本

我會把它當成「資料保留與查帳安全」來講。表面上只是 Quartz job 清 Mongo，但真正的風險是它先把 14 天以前的 provider log / transaction insert 到 backup collection，再從 active collection delete。這不是同一個 transaction，所以要設計成寧可重複、不可丟資料，而且 retry 時要 idempotent。面試時我會強調 copy-then-delete 的 failure window、backup duplicate、防重跑、delete count 對帳與 retention policy。

## 3 分鐘版本

這條 flow 在 `game_job`，上游是 `third_games_api` 寫入 Antplay / GSC 的 Mongo active collections。Quartz 觸發後，`BiJobBase` 先處理 Redis task state 與 task log，真正的 job 會查 `createdTime < today - 14 days` 的文件，分批 insert 到 `{collection}_job_bak`，再用 `_id` 從 origin collection 刪掉。最後會把 backup collection 裡超過 30 天的資料 hard delete。

我會先說它不是 wallet source of truth，而是 provider log / transaction audit retention。這很重要，因為不能把這種 backup job 說成交易正確性的最終帳本；它更像客服查詢、provider dispute、事故排查時的證據資料。

Senior 角度，我會抓三個風險：

第一，copy 和 delete 不是 atomic。insert backup 成功但 delete origin 失敗時，active 和 backup 會同時存在同一批資料。這比丟資料安全，但會帶來 duplicate 與重跑問題。

第二，重跑 idempotency 不明。backup collection 是否保留原 `_id`、是否有 unique index、duplicate key 時是整批失敗還是部分成功，這些會決定重跑是安全 retry 還是產生第二份 backup。

第三，observability 不夠。現在有 log backup count 和 delete count，但我沒有看到持久化的 job run id、copied count、deleted count、remaining count。若 delete count 小於 batch size，只靠 log 很難在事故後確認哪一批沒搬乾淨。

如果由我 owner，我會先定義 retention policy，再把 backup write 做成 duplicate-safe，並補每批 reconciliation。對 audit 類資料，我的 decision 會是寧可 duplicate，不可丟資料；但 duplicate 要能被查詢端或 reconciliation 去重。

## STAR 版本

Situation：第三方遊戲 provider log / transaction 寫在 Mongo active collection，資料量會持續成長，系統需要保留近期查詢能力，同時把較舊資料搬到 backup collection。

Task：讓 retention job 能分批搬移舊資料，避免一次拉全量造成記憶體與長時間操作風險，並降低 copy-then-delete 的資料不一致風險。

Action：從 code-backed 分析來看，GSC 曾由 `10gt12nc` commit 把原本一次查出所有符合條件的資料，改成 `while` loop + `limit(BATCH_SIZE)` 分批查詢、逐批 insert backup、逐批用 `_id` delete origin；後續又調整 batch size。Step 5 已確認這能支撐局部真實開發 claim。

Result：可面試講成 batch retention / partial failure / idempotent retry 題；正式履歷可保守寫「參與 GSC 第三方遊戲紀錄 Mongo 備份 job 的分批查詢與批次大小調整」，但不可擴大成完整 retention owner。

## 可面試講

我會把這類 flow 當成資料保留與稽核安全題，而不是單純排程清資料。

正常流程是 Quartz 觸發 job，`BiJobBase` 管 Redis task state 與 task log，實際 job 查 `createdTime < today - 14 days` 的 Mongo 文件，分批 insert 到 `{collection}_job_bak`，再用原文件 `_id` 刪除 active collection。最後再刪 backup 裡超過 30 天的資料。

我會特別看四個點：

- copy 和 delete 不在同一個 transaction，失敗時要選擇寧可 duplicate 還是可能丟資料。
- 重跑時 backup insert 要能 idempotent，否則 delete 失敗後下一輪可能重複備份或遇到 duplicate key。
- delete count 必須和 batch size 對帳，否則只 log count 但繼續跑會讓資料狀態不透明。
- retention policy 要和客服查詢、provider dispute、audit 需求對齊，不能只是 code 裡 14 天 / 30 天 hard-code。
- `createdTime` 的型別與語意會直接影響清理窗口，因為 backup / delete 都靠它判斷資料年齡。

## Senior 版回答

這條 job 的安全順序是先 copy 再 delete，方向是對的，因為 retention job 比較怕資料被硬刪後沒有 backup。但目前看不到跨 collection transaction，也沒有持久化 run id / copied count / deleted count reconciliation，所以它的可靠性主要依賴 retry 時不會造成壞狀態。

如果讓我 owner，我會先確認 backup collection 是否保留原 `_id` 且有 unique constraint，然後把 insert 改成 duplicate-safe bulk upsert 或至少能處理 duplicate key。每個 batch 要驗證 copy 成功筆數、delete 成功筆數、剩餘筆數，遇到不一致就讓 task log failure 並告警。這樣即使 insert 成功但 delete 失敗，下輪也只是 retry，不會產生不可控 duplicate。

另外我會把 `createdTime` 的語意釐清：它是 provider event time、third_games_api receive time，還是資料寫入時間。因為 retention 用的是 `createdTime`，如果補寫舊單或時間型別不一致，會影響什麼資料被搬走或刪掉。

## Lead / Architect 追問準備

Q：為什麼不直接 Mongo transaction？

A：可以評估，但要先看 Mongo deployment、collection 是否同 DB、transaction 成本與現有 Spring MongoTemplate 使用方式。Retention job 常見 decision 是先保守成 copy-before-delete，再用 idempotent backup、unique key、reconciliation 與告警控風險。若 audit 要求非常高，才考慮 transaction 或兩階段狀態機。

Q：delete 失敗後怎麼辦？

A：下輪 retry 前提是 backup insert duplicate-safe。否則 delete 失敗後 active 留著，下輪再 insert backup 會變 duplicate 或 duplicate key failure。我的做法是 backup 以 source `_id` 唯一，insert 用 upsert / bulk unordered 或明確處理 duplicate，然後 delete count 不一致就保留 failed run。

Q：backup 超過 30 天 hard delete 會不會太短？

A：不能只從 code 判斷。要對齊 provider dispute、客服查詢、法遵 / audit、storage 成本。如果 dispute 週期比 30 天長，就要延長、冷存、或至少在刪除前有可查的 retention decision。

Q：這條和交易正確性有什麼關係？

A：它不是 wallet correctness 的 source of truth，但它是 troubleshooting / audit evidence。真正交易正確性要看 gameserver / provider adapter / wallet ledger；這條是查問題時能不能保留 provider log 與 transaction evidence。

## 可放履歷

可以放正式履歷，但不建議單獨放大成一整條主力成果；應併入 `game_job` / batch / Mongo 大量資料處理經驗。

原因：

- Step 5 已確認 `10gt12nc` 有 GSC 分批查詢與 batch size 調整 direct commits。
- 這是局部真實開發，不是完整系統 owner。
- 不能把這條 flow 包裝成 Nick 主導完整第三方遊戲 audit retention。

保守履歷句：

```text
參與遊戲報表與紀錄保留相關 batch 維護，包含每日遊戲資料彙總，以及 GSC 第三方遊戲紀錄 Mongo 備份 job 的分批查詢與批次大小調整。
```

## 可作面試素材

- Batch retention job 的 copy-then-delete failure window。
- Mongo active / backup collection 的 idempotent move。
- Retention policy 與 audit / dispute 查詢需求。
- Quartz `@DisallowConcurrentExecution` 只能處理單 scheduler job overlap，distributed / manual overlap 仍需確認。
- `createdTime` 型別與語意會直接影響清理正確性。
- 大資料量 job 從一次查全量改成分批查詢的可靠性取捨。

## 不可誇大

- 不說 Nick 主導 Antplay / GSC backup job。
- 不說 Nick 負責第三方遊戲完整交易鏈路。
- 不說已驗證 production cron 啟用狀態。
- 不說有量化改善 storage、查帳時間、正確率。

## 下一步

```text
iwin game_job coin-flow-batch-projection Step 5
```
