# third-party-record-mongo-backup career-interview

## 一句話版本

這是一條第三方遊戲紀錄 Mongo retention flow：把 Antplay / GSC 的 provider log 與 transaction 文件從 active collection 分批搬到 backup collection，再清掉過期 backup。面試價值在 copy-then-delete 的一致性、idempotency、retention policy 與 observability。

證據層級：專案存在 / code-backed；Nick / `10gt12nc` 貢獻待 Step 5 claim gate 判斷。

## 可面試講

我會把這類 flow 當成資料保留與稽核安全題，而不是單純排程清資料。

正常流程是 Quartz 觸發 job，`BiJobBase` 管 Redis task state 與 task log，實際 job 查 `createdTime < today - 14 days` 的 Mongo 文件，分批 insert 到 `{collection}_job_bak`，再用原文件 `_id` 刪除 active collection。最後再刪 backup 裡超過 30 天的資料。

我會特別看四個點：

- copy 和 delete 不在同一個 transaction，失敗時要選擇寧可 duplicate 還是可能丟資料。
- 重跑時 backup insert 要能 idempotent，否則 delete 失敗後下一輪可能重複備份或遇到 duplicate key。
- delete count 必須和 batch size 對帳，否則只 log count 但繼續跑會讓資料狀態不透明。
- retention policy 要和客服查詢、provider dispute、audit 需求對齊，不能只是 code 裡 14 天 / 30 天 hard-code。

## Senior 版回答

這條 job 的安全順序是先 copy 再 delete，方向是對的，因為 retention job 比較怕資料被硬刪後沒有 backup。但目前看不到跨 collection transaction，也沒有持久化 run id / copied count / deleted count reconciliation，所以它的可靠性主要依賴 retry 時不會造成壞狀態。

如果讓我 owner，我會先確認 backup collection 是否保留原 `_id` 且有 unique constraint，然後把 insert 改成 duplicate-safe bulk upsert 或至少能處理 duplicate key。每個 batch 要驗證 copy 成功筆數、delete 成功筆數、剩餘筆數，遇到不一致就讓 task log failure 並告警。這樣即使 insert 成功但 delete 失敗，下輪也只是 retry，不會產生不可控 duplicate。

另外我會把 `createdTime` 的語意釐清：它是 provider event time、third_games_api receive time，還是資料寫入時間。因為 retention 用的是 `createdTime`，如果補寫舊單或時間型別不一致，會影響什麼資料被搬走或刪掉。

## 可放履歷

目前不建議單獨放正式履歷。

原因：

- Step 3 已 code-backed，但尚未完成 Step 5 claim gate。
- `10gt12nc` 有 GSC 分批查詢與 batch size 調整 commit，屬於值得保留的直接參與線索，但還需要 Step 5 判斷能否寫成局部真實開發。
- 不能把這條 flow 包裝成 Nick 主導完整第三方遊戲 audit retention。

## 可作面試素材

- Batch retention job 的 copy-then-delete failure window。
- Mongo active / backup collection 的 idempotent move。
- Retention policy 與 audit / dispute 查詢需求。
- Quartz `@DisallowConcurrentExecution` 只能處理單 scheduler job overlap，distributed / manual overlap 仍需確認。
- `createdTime` 型別與語意會直接影響清理正確性。

## 不可誇大

- 不說 Nick 主導 Antplay / GSC backup job。
- 不說 Nick 負責第三方遊戲完整交易鏈路。
- 不說已驗證 production cron 啟用狀態。
- 不說有量化改善 storage、查帳時間、正確率。

## 下一步

```text
iwin game_job third-party-record-mongo-backup Step 4
```
