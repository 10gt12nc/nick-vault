# Decision Notes - GSC seamless withdraw / deposit / rollback / cancel

## 1. Split endpoint vs transfer endpoint

已確認：

- split endpoint 包含 `/withdraw`、`/deposit`、`/rollback`、`/cancel`。
- `/transfer` 是另一條已完成 Step 5 的整合式 flow。
- commit history 有「實測結果是投派整合，需串接 `/transfer` API」線索。

Owner 判斷：

- 面試時可以拿 split endpoint 對照 `/transfer`，說明 provider API 演進。
- 但不能宣稱 split endpoint 是目前 production 主線，除非補到 provider route / deploy / traffic evidence。

## 2. Idempotency 放在哪一層

Adapter 層已做：

- withdraw：`betId + step 1` duplicate check。
- deposit：要求 step 1，並檢查 `betId + action + step 2`。
- rollback / cancel：要求 step 1，並檢查 `betId + action + step 3`。

不足：

- Mongo evidence 是 gameserver success 後才寫。
- 若 gameserver success 但 Mongo insert fail，adapter 層下次 retry 仍可能看不到已處理狀態。

建議：

- wallet mutation boundary 也要有 durable idempotency。
- key 至少是 provider + betId + action / step；若 provider transaction id 穩定，也要納入。

## 3. Rollback / cancel 語意

已確認：

- rollback 送 `GSC_REFUND`，`gscType = 3`，reason `10509`。
- cancel 送 `GSC_OTHER`，`gscType = 4`，reason `10510`。
- rollback / cancel 都寫 `third_transaction_gsc` step `"3"`，靠 action 區分。

待決策：

- cancel 是否應視為退款、取消、other credit，或只是 provider 特殊驗證。
- rollback 與 cancel 是否互斥。
- deposit 後是否允許 rollback / cancel。

## 4. Mongo step evidence

Mongo 適合作：

- callback audit。
- transaction evidence。
- reconciliation join point。
- provider retry 查詢輔助。

Mongo 不應單獨作：

- wallet source of truth。
- exactly-once 保證。
- 唯一防 double pay / double refund 的 guard。

## 5. Observability

本 flow 至少需要觀測：

- 每個 endpoint 的 provider response code。
- `betId + action + step` duplicate count。
- gameserver success but Mongo insert fail。
- Mongo step 1 missing but deposit / rollback arrived。
- Redis game mapping miss / center route miss。
- GSC_OTHER / cancel 的交易數量與後續對帳結果。

## 6. Step 4 面試轉譯重點

正式面試不只背 route，而是要講出三層邊界：

- Adapter boundary：驗簽、Redis route、Mongo step evidence。
- Wallet boundary：gameserver `GSCTransferInOutJob` 與 `PlayerData.modifyAndGetCoinGSC`。
- Reconciliation boundary：Mongo transaction、currency / reel log、provider statement 與後續 repair / audit。

面試時的 owner stance：

- 承認 current code 的 Mongo guard 有價值，但不是 final idempotency。
- 先追 production route，確認 split endpoint 是否仍是主線。
- 如果要改善，先把 idempotency guard 放到 wallet mutation boundary，再補 outbox / audit / reconciliation。
- 不把未確認的 production usage 或 Nick contribution 講成已確認。

## 下一步

```text
iwin third_games_api gsc-seamless-withdraw-deposit-cancel Step 5
```
