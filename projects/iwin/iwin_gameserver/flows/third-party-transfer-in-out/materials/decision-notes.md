# third-party-transfer-in-out Decision Notes

更新時間：2026-05-15
狀態：Step 3 owner decision 初稿

## Decision 1：OK response 的語意

現況觀察：

- `modifyCoin()` 成功後立即回 HTTP OK。
- `afterEvent()`、`sendReelToLog()`、`sendBetLog()` 在 OK 後執行。

Owner 判斷：

- 目前 OK 比較像「wallet mutation 成功」，不是「所有 downstream side effect 完成」。
- 文件與 upstream contract 應明確定義這件事，否則 adapter retry / reconciliation 會誤判。

## Decision 2：idempotency guard 應在 wallet mutation 前

現況觀察：

- `transactionId` / `betId` 被帶到 log。
- Step 3 未看到 wallet mutation 前的查重 guard。

Owner 判斷：

- 防重不能只靠 log writer。
- 建議以 provider + event type + transaction id / bet id 建立處理紀錄，在變更玩家餘額前判斷是否已完成。
- duplicate callback 應回既有結果，而不是再次修改餘額。

## Decision 3：log 補償不要依賴人工 grep

現況觀察：

- log server 使用 cache 批次 insert / update。
- gameserver 與 log server 不在同一 transaction。

Owner 判斷：

- 短期：建立 reconciliation report，比對 provider statement、adapter request、currency log、log_reel。
- 中期：建立 outbox 或 durable event，讓 wallet success 後的 log event 可重試。
- 長期：讓事件狀態可查，例如 `wallet_applied`、`reel_logged`、`bet_record_logged`。

## Decision 4：GSC split path 與 PG / AP integrated path 要分開說

現況觀察：

- GSC bet / settle / refund 分不同 command，log 層有 insert / update。
- PG / AP 投派整合以單一 transfer in/out command 表達。

Owner 判斷：

- 面試或文件不要把三個 provider 混成一條完全相同流程。
- 共通層是 wallet mutation、response ordering、log side effect；差異層是 command shape 與 log insert/update 策略。

## Decision 5：Step 4 先做面試案例，不急著更新履歷

原因：

- Step 3 已能支撐 technical discussion。
- 但 Nick 本人 evidence 尚未補齊。
- 正式履歷如果現在寫，很容易變成過度 claim。
