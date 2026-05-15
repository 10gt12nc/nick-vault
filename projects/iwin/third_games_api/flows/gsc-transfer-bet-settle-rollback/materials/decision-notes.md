# Decision Notes：GSC transfer bet / settle / rollback

## 1. Source of truth

建議決策：錢包 source of truth 應該是 gameserver wallet ledger，而不是 `third_games_api` Mongo。

理由：

- `third_games_api` 呼叫 gameserver 後才寫 Mongo。
- 真正扣加錢在 `PGTransferInOutJob` / `GamePlayer.modifyAndGetCoinPG`。
- Mongo 更適合作為 callback evidence、query support、reconciliation join point。

## 2. Idempotency 放在哪一層

保守建議：adapter 層與 wallet mutation 層都應該有 idempotency，但最終保護要在 wallet mutation 層。

原因：

- Provider retry / timeout 最終會造成重送同一個 provider transaction。
- Adapter 層的 Mongo insert 可能晚於 wallet mutation，不能只靠 Mongo 判斷。
- 若 gameserver 已成功但 `third_games_api` 回 provider error，下一次 retry 仍必須回到同一個結果，而不是再次扣加。

理想 key：

- provider `transactionId`
- `wager_code` / `betId`
- player id / account id
- action / transaction type

## 3. Rollback 語意

目前 code-backed 事實：

- `action == ROLLBACK` 時，`third_games_api` 不呼叫 gameserver。
- response balance 由 `request amount + current balance` 算出。
- 仍會寫 `third_log_gsc` 與 `third_transaction_gsc`。

Owner 必須決定：

- 這是 provider spec 要求的 validation response，還是真正漏掉 wallet compensation？
- 如果是 validation response，命名與 monitoring 要讓維運不要誤解。
- 如果應該是 wallet rollback，必須補 reversal transaction，而不是只回算 balance。

## 4. Mongo 寫入失敗

現有 boundary：

- gameserver 成功後才寫 Mongo。
- Mongo insert exception 可能使 API 外層 catch 回 provider server error，但 wallet 已成功。

建議：

- wallet success 後的 audit write failure 應進入可重試 repair path。
- response policy 要明確：是否回 success 並補寫 audit，或回 error 讓 provider retry。
- 若回 error，wallet 層 idempotency 必須先完成。

## 5. Transactions 多筆處理

目前 code 顯示：

- request 內 loop 多筆 transactions，但只保留最後一筆進後續 gameserver call。
- duplicate check 只檢查 action 是否重複，不是 transaction id 是否重複。

建議：

- 若 provider spec 只允許單筆，request validation 應明確限制。
- 若 spec 允許多筆，應逐筆處理、逐筆 idempotency、逐筆 response 或整批 transaction policy。

## 6. Observability

必要觀察點：

- provider transaction id / wager code
- gameserver command id / wallet mutation result
- Mongo audit insert result
- response status to provider
- rollback branch count
- Redis mapping miss count
- gameserver success but Mongo failure count

面試可講的 owner 視角：

> 第三方錢包整合最怕的是「provider 以為失敗、內部其實成功」或「內部錢包成功、報表 evidence 失敗」。所以我會先把 source of truth、idempotency key、audit repair、reconciliation join key 定義清楚，再談 API response。
