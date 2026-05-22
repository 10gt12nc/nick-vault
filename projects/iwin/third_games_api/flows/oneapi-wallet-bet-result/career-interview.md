# OneAPI / PG bet_result Career Interview

完成狀態：Step 3 已建立；下一步 Step 4 轉面試 case。

證據層級：`專案存在 / code-backed`、`分析素材 / learning-only`。`third_games_api` 本 repo 的 OneAPI adapter 目前沒有 Nick / `10gt12nc` direct evidence；下游 `iwin_gameserver` 的 PG bet_result / PGTransferInOut 有 Nick / `10gt12nc` direct commits，但歸屬 `iwin_gameserver`。

## 面試主軸

這條 flow 可以包成「第三方 provider bet_result callback 的 idempotency 與 wallet boundary」。

短版說法：

```text
我分析過 OneAPI / PG bet_result 這條 third-party game callback。Provider 打 /wallet/bet_result 進來後，adapter 會用 HMAC-SHA256 驗簽，檢查 transactionId 是否已在 Mongo transaction 裡處理過，沒有處理過才把 bet / win / jackpot / turnover 轉成 gameserver PGTRANSFERINOUT command。真正錢包異動在 gameserver，adapter 只負責轉換、路由和 Mongo audit。
```

## 可講的技術點

- HMAC-SHA256 可以驗證 body contract，但不等於 replay 防護。
- `transactionId` 是 adapter 層 duplicate key；duplicate 命中時回既有 balance。
- `betId` 用於查 balance 與 END 前置檢查，但是否足以表示一個 round / bet lifecycle 需看 provider contract。
- gameserver `PGTRANSFERINOUT` 才是真正 wallet mutation boundary。
- Mongo `third_transaction_oneapi` 是 audit / duplicate projection，不是 wallet ledger。
- `gameserver success -> Mongo insert` 中間有 failure window。

## 三分鐘講法

這條 flow 是第三方遊戲 provider 回傳投注結果的流程。OneAPI 會把 `traceId`、`transactionId`、`betId`、`roundId`、投注額、派彩、jackpot、有效投注和 `resultType` 打到 `/wallet/bet_result`。

Adapter 第一件事是重新用固定欄位順序組 JSON，透過 HMAC-SHA256 驗證 `X-Signature`。接著檢查幣別、玩家、`END` 是否已有前置 betId，以及 `transactionId` 是否已存在。若 transaction 已存在，就從 Mongo 查 balance 回 `SC_OK`，避免再送 gameserver。

新交易會把金額乘內部倍率，計算 `addMoney`，用 Redis mapping 找 `gameId` 和 `center_http`，再呼叫 gameserver 的 `PGTRANSFERINOUT`。gameserver 端才會查玩家、排入 game pool、呼叫 wallet method 改錢，並產生 currency / reel / bet log side effect。

我會特別看 failure window：現在 duplicate guard 在 adapter Mongo，但 Mongo 是 gameserver 成功後才寫。如果 gameserver 改錢成功但 Mongo 沒寫，provider retry 可能再次進入 gameserver。所以比較完整的設計會把 idempotency guard 放在 wallet mutation 前，或先落 durable request state，再推進錢包異動與 audit。

## 履歷建議

目前不建議新增 `third_games_api` standalone 履歷 bullet。

可作為面試補充：

```text
分析過第三方遊戲 provider bet_result callback，能拆解 HMAC 驗簽、transactionId duplicate guard、gameserver wallet mutation、Mongo audit 與 retry / reconciliation failure window。
```

這句只能作面試素材，不應直接放正式履歷主成果。

## Claim Boundary

- 可面試講：code-backed / 分析過。
- 可放履歷：目前不放 `third_games_api` standalone 成果。
- 可補強其他 project：下游 PGTransferInOut direct commits 已屬 `iwin_gameserver` contribution claim。
- 不可誇大：不說 Nick 開發 OneAPI adapter、不說 Nick 是 provider integration owner、不說已完成 exactly-once。

## 下一步

```text
iwin third_games_api oneapi-wallet-bet-result Step 4
```
