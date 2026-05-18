# claim-boundary

## 目前結論

本 flow 目前只可作為 `專案存在 / code-backed` 與 `分析素材 / learning-only`。

Step 4 已轉成面試 case，但這不等於履歷 claim 可以升級。正式履歷 / 自傳不更新，等 Step 5 claim gate 做最後判斷。

不更新：

- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

原因：

- 沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認。
- 沒有確認 Nick 是 payment provider integration owner。
- 已完成 Step 4 面試 case，但尚未完成 Step 5 claim gate。
- DB unique key、provider idempotency、自動 reconciliation 還是待確認。

## 可寫入面試素材

可以保守說：

- 「我分析過 iwin payment 的充值建單與 provider request flow。」
- 「這條 flow 涉及 `payment_order`、商戶設定、provider request、簽章、金額單位、callback 與查單補償。」
- 「我會用它討論 provider integration 的 transaction boundary、timeout、idempotency、reconciliation。」

## 需要 Nick 確認後才可升級

若 Nick 確認曾參與，可升級成：

- `真實開發過`
- `真實排查過`
- `真實修復過`
- `真實 provider integration 維護經驗`

需要 evidence：

- MR / PR link。
- ticket / issue。
- commit author。
- production incident / postmortem。
- Nick 口頭確認具體做過哪一段。

## 禁止 claim

- 禁止寫「主導 iwin 三方金流串接」。
- 禁止寫「設計 provider request 架構」。
- 禁止寫「對接 Pay4z / NimTestPay / CashPay」。
- 禁止寫「解決 provider request timeout 對帳」。
- 禁止寫「建立完整 reconciliation 機制」。
- 禁止寫「確認所有 provider idempotent」。

## 下一步

```text
iwin payment payment-order-provider-request Step 5
```
