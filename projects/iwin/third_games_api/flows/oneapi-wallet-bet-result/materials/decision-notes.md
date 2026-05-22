# Decision Notes：OneAPI / PG bet_result

## Decision 1：HMAC 是 contract check，不是 idempotency

HMAC-SHA256 可以證明 request body 與 secret 對得上，但同一份合法 request 重送時，signature 仍然合法。因此 replay / retry 必須靠 transaction key 與狀態機處理。

Owner 問法：

- body canonicalization 是否穩定？
- `X-Signature` 是否涵蓋全部影響 money 的欄位？
- 是否有 timestamp / nonce / replay window？

## Decision 2：Duplicate guard 放 adapter 層不等於 money-safe

目前 `third_games_api` 先查 Mongo `third_transaction_oneapi` 的 `transactionId + step = 1`。這能擋住「已成功寫 Mongo 後的 retry」。

但更危險的 window 是：

```text
gameserver wallet success
-> adapter crash / Mongo insert fail / response timeout
-> provider retry
-> Mongo 查不到 transactionId
-> wallet 可能再次 mutation
```

因此 money-safe idempotency 應靠近 wallet mutation boundary，或至少有 pending / success durable state。

## Decision 3：Balance response 要區分來源

Duplicate 時回的是 Mongo `third_transaction_oneapi` 的 balance，不一定等於當下 gameserver 餘額。

這不一定是錯，因為 provider retry 常要求回同一筆交易完成後的結果；但面試要講清楚這是 replay response，不是即時 balance query。

## Decision 4：`resultType` 是狀態機，不只是 if-else

目前 code 明確處理：

- `WIN`
- `BET_WIN`
- `BET_LOSE`
- `LOSE`
- `END` 的前置 betId 檢查

待確認：

- provider 是否還有其他 resultType。
- `END` 是否一定不需要再改錢。
- out-of-order event 是否可能發生。

如果 resultType contract 不清，`addMoney` 可能被算成 0 或漏處理。

## Decision 5：Audit / Reconciliation 要跨三層

要真正對帳，不能只看 Mongo：

1. Provider statement：provider transaction / round result。
2. Adapter Mongo：`third_log_oneapi` / `third_transaction_oneapi`。
3. Gameserver：currency log、reel log、bet log、玩家餘額。

缺任何一層，都只能說有 audit evidence，不能說有完整 reconciliation。

## 下一步

```text
iwin third_games_api oneapi-wallet-bet-result Step 4
```
