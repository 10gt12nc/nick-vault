# OneAPI / PG bet_result Career Interview

完成狀態：Step 5 已完成，單條 flow claim gate 已收斂；後續 `antplay-bet-settle-rollback` 也已完成 Step 5，目前下一步回同 project 做 `gsc-seamless-withdraw-deposit-cancel Step 5`。

證據層級：`專案存在 / code-backed`、`分析素材 / learning-only`。`third_games_api` 本 repo 的 OneAPI adapter 目前沒有 Nick / `10gt12nc` direct evidence；下游 `iwin_gameserver` 的 PG bet_result / PGTransferInOut 有 Nick / `10gt12nc` direct commits，但歸屬 `iwin_gameserver`。

## 面試主軸

這條 flow 可以包成「第三方 provider bet_result callback 的 idempotency 與 wallet boundary」。

## 30 秒說法

```text
我分析過 OneAPI / PG bet_result 這條第三方遊戲 callback。Provider 打 /wallet/bet_result 進來後，adapter 會先用 HMAC-SHA256 驗 request body，再用 transactionId 查 Mongo duplicate evidence；新交易才會把 bet / win / jackpot / turnover 轉成 gameserver PGTRANSFERINOUT command。真正錢包異動在 gameserver，所以這條 flow 的重點是 retry / idempotency boundary，而不是只有簽章驗證。
```

## 可講的技術點

- HMAC-SHA256 可以驗證 body contract，但不等於 replay 防護。
- `transactionId` 是 adapter 層 duplicate key；duplicate 命中時回既有 balance。
- `betId` 用於查 balance 與 END 前置檢查，但是否足以表示一個 round / bet lifecycle 需看 provider contract。
- gameserver `PGTRANSFERINOUT` 才是真正 wallet mutation boundary。
- Mongo `third_transaction_oneapi` 是 audit / duplicate projection，不是 wallet ledger。
- `gameserver success -> Mongo insert` 中間有 failure window。

## 90 秒說法

```text
這條 flow 的系統分界很清楚：third_games_api 是 provider-facing adapter，負責驗簽、欄位轉換、Redis route、Mongo audit；iwin_gameserver 才是 wallet source of truth。

正常情境下，OneAPI 帶 transactionId、betId、roundId、resultType 和金額進來。Adapter 驗 HMAC、檢查幣別和玩家、確認 transactionId 是否已處理。若 duplicate，就回 Mongo 裡那筆交易完成後的 balance；若是新交易，就算 addMoney，送 gameserver PGTRANSFERINOUT 去修改玩家餘額。

我會特別盯 gameserver 成功後才寫 Mongo 這個順序。它代表 Mongo duplicate guard 只能擋住已寫入 audit 的 retry；如果 wallet 已成功但 audit 未寫，provider retry 仍可能再次進 wallet boundary。
```

## 3 分鐘講法

這條 flow 是第三方遊戲 provider 回傳投注結果的流程。OneAPI 會把 `traceId`、`transactionId`、`betId`、`roundId`、投注額、派彩、jackpot、有效投注和 `resultType` 打到 `/wallet/bet_result`。

Adapter 第一件事是重新用固定欄位順序組 JSON，透過 HMAC-SHA256 驗證 `X-Signature`。接著檢查幣別、玩家、`END` 是否已有前置 betId，以及 `transactionId` 是否已存在。若 transaction 已存在，就從 Mongo 查 balance 回 `SC_OK`，避免再送 gameserver。

新交易會把金額乘內部倍率，計算 `addMoney`，用 Redis mapping 找 `gameId` 和 `center_http`，再呼叫 gameserver 的 `PGTRANSFERINOUT`。gameserver 端才會查玩家、排入 game pool、呼叫 wallet method 改錢，並產生 currency / reel / bet log side effect。

我會特別看 failure window：現在 duplicate guard 在 adapter Mongo，但 Mongo 是 gameserver 成功後才寫。如果 gameserver 改錢成功但 Mongo 沒寫，provider retry 可能再次進入 gameserver。所以比較完整的設計會把 idempotency guard 放在 wallet mutation 前，或先落 durable request state，再推進錢包異動與 audit。

## STAR 講法

Situation：第三方遊戲 provider callback 會直接影響玩家餘額，且 provider timeout / retry 是常態。

Task：需要拆清楚 adapter 驗簽、duplicate guard、gameserver wallet mutation 與 Mongo audit 的責任邊界，避免把 audit projection 誤當帳本。

Action：我沿 `POST /wallet/bet_result` 往下追 HMAC 驗簽、`transactionId + step` duplicate check、金額倍率與 `addMoney` 計算、Redis `center_http` 路由、gameserver `PGTRANSFERINOUT`、wallet mutation job、Mongo `third_log_oneapi` / `third_transaction_oneapi` 寫入順序。

Result：這條 flow 可以被面試講成「adapter duplicate guard 不等於 money-safe idempotency」。最危險 window 是 gameserver 成功但 Mongo 未寫；改善方向是把 idempotency guard 移到 wallet mutation boundary，或先建立 durable request state，再用 reconciliation 對 provider / adapter / gameserver 三層資料。

## 常見追問

### HMAC 驗簽是不是就安全？

不是。HMAC 只能證明 request body 和 shared secret 對得上，不代表防 replay。同一份合法 request 重送時，signature 仍然會通過。

### duplicate 時為什麼回 Mongo balance？

這是 replay response，不是即時餘額查詢。Provider retry 通常期待同一 transaction 的處理結果；但要講清楚 Mongo balance 只是 adapter transaction evidence，不是 gameserver source of truth。

### 最應該補在哪裡？

補在 wallet mutation 前。`PGTRANSFERINOUT` 或更底層 wallet method 應能用 provider transaction key 判斷「這筆 money event 是否已處理」，避免 adapter audit 還沒寫時 retry 造成 double apply。

### `END` 找不到 betId 直接錯，合理嗎？

要看 provider contract。如果 provider 保證 event order，這是合理防呆；如果 provider 可能 out-of-order，就需要 pending state、retry 或 repair 流程，而不是直接把合法補結算擋掉。

## 履歷建議

目前不建議新增 `third_games_api` standalone 履歷 bullet。

可作為面試補充：

```text
分析過第三方遊戲 provider bet_result callback，能拆解 HMAC 驗簽、transactionId duplicate guard、gameserver wallet mutation、Mongo audit 與 retry / reconciliation failure window。
```

這句只能作面試素材，不應直接放正式履歷主成果。

Step 5 最終判斷：

- 可面試講：是，code-backed / 分析過。
- 可放正式履歷：否，不新增 `third_games_api` standalone bullet。
- 可回填 project consolidation：是，回填為 OneAPI flow Step 5 已完成、仍維持 interview-only。
- 可回填 `iwin_gameserver`：下游 PGTransferInOut direct evidence 已由 `iwin_gameserver` consolidation 承接，不在本 flow 重複升級。

## Claim Boundary

- 可面試講：code-backed / 分析過。
- 可放履歷：目前不放 `third_games_api` standalone 成果。
- 可補強其他 project：下游 PGTransferInOut direct commits 已屬 `iwin_gameserver` contribution claim。
- 不可誇大：不說 Nick 開發 OneAPI adapter、不說 Nick 是 provider integration owner、不說已完成 exactly-once。

## 下一步

```text
iwin third_games_api gsc-seamless-withdraw-deposit-cancel Step 5
```
