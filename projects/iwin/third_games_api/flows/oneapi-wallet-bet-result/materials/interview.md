# Interview：OneAPI / PG bet_result

## 3 分鐘講法

OneAPI `bet_result` 是第三方遊戲 provider 回傳投注結果的 callback。Provider 會帶 `traceId`、`transactionId`、`betId`、`roundId`、投注額、派彩、jackpot、有效投注和 `resultType` 到 `/wallet/bet_result`。

Adapter 會先用固定欄位順序重建 JSON，再用 HMAC-SHA256 驗 `X-Signature`。驗簽過後，它會檢查幣別、玩家、`END` 是否已有前置 betId，以及 `transactionId` 是否已經處理過。若 `transactionId` 已存在，就用 Mongo 的 balance 回 `SC_OK`，避免再送 gameserver。

新交易會把 provider 金額轉成內部倍率，算出 `addMoney`，用 Redis mapping 找 internal gameId 和 gameserver `center_http`，再送 `PGTRANSFERINOUT`。真正的錢包加扣在 gameserver 的 `PGTransferInOutJob`，後面還會帶出 currency log、戰績與打碼 log。

這條 flow 的 Senior 重點是 idempotency boundary。現在 duplicate guard 在 adapter Mongo，但 Mongo 是 gameserver 成功後才寫。如果 gameserver 改錢成功但 Mongo 沒寫，provider retry 可能再次送進 gameserver。所以 owner 會追問：防重是否應該放在 wallet mutation 前、如何建立 reconciliation、以及 provider `transactionId` / `betId` 的唯一語意是什麼。

## 常見追問

### HMAC 驗簽解決了什麼？

它能確認 request body 沒被改、來源知道 secret。它不解決 replay，因為同一份 body 和 signature 重送仍會通過。

### Duplicate guard 在哪裡？

在 `third_games_api` adapter 層查 Mongo `third_transaction_oneapi`，條件是 `transactionId + step = 1`。命中後回 `SC_OK` 和 Mongo balance。

### 為什麼這還有風險？

因為 Mongo transaction 是 gameserver 成功後才寫。如果 gameserver 已改錢，但 adapter crash 或 Mongo insert 失敗，下一次 retry 查不到 transactionId，可能再次送 gameserver。

### `transactionId` 和 `betId` 怎麼選？

要看 provider contract。若 `transactionId` 是每個 provider event 唯一，就適合當 idempotency key；若 `betId` 下會有多個 event，就不能只用 betId。保守做法會把 provider、event type、transaction id 組成 key。

### `END` 查不到 betId 回錯代表什麼？

系統假設 END 之前一定已有同 betId 的 step 1 transaction。若 provider 可能 out-of-order，這會把合法補結算擋下；如果 provider 不會 out-of-order，這是合理保護。

### Mongo 是不是帳本？

不是最終帳本。它是 adapter audit / duplicate projection。真正錢包狀態在 gameserver wallet / currency log。

### 你會怎麼改善？

我會先確認 provider unique key contract，然後把 idempotency guard 往 wallet mutation boundary 移，至少在 `PGTRANSFERINOUT` 修改玩家餘額前判斷已處理狀態。再補 reconciliation，把 provider statement、adapter Mongo、gameserver currency log、reel log 對起來。

## 可講 / 不可講

可講：

- 分析過 OneAPI bet_result callback。
- 能拆 HMAC、transactionId duplicate、gameserver `PGTRANSFERINOUT`、Mongo audit 和 failure window。
- 知道 adapter 層和 wallet source of truth 的分界。

不可講：

- Nick 開發 OneAPI adapter。
- Nick 主導 `third_games_api`。
- 已建立完整 exactly-once。
- 已修復 OneAPI production 錯帳。

## 下一步

```text
iwin third_games_api oneapi-wallet-bet-result Step 4
```
