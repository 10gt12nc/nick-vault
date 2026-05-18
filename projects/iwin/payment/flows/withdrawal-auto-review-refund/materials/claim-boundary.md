# claim-boundary

## 目前結論

本 flow 目前只可作為 `專案存在 / code-backed` 與 `分析素材 / learning-only`。

Step 4 已完成面試 case，但這不等於履歷 claim 可以升級。Step 5 才檢查是否更新正式履歷 / 自傳。

不更新：

- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

原因：

- 沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認。
- 沒有確認 Nick 是 payment owner。
- 還沒完成 Step 4 failure / consistency 深挖。
- 下游 `billNo` 去重、DB unique key、reconciliation 還是待確認。

## 可寫入面試素材

可以保守說：

- 「我分析過 iwin payment 的玩家提款、自動審核 / 自動出款與失敗退款 flow。」
- 「這條 flow 涉及 `payment_order` 狀態、game lobby 扣分 / 退款、provider 代付、callback、MQ retry 與人工審核 fallback。」
- 「我會用它討論 money correctness、idempotency、retry、compensation、reconciliation。」

## 需要 Nick 確認後才可升級

若 Nick 確認曾參與，可升級成：

- `真實開發過`
- `真實排查過`
- `真實修復過`
- `真實 owner / maintenance 經驗`

需要 evidence：

- MR / PR link。
- ticket / issue。
- commit author。
- production incident / postmortem。
- Nick 口頭確認具體做過哪一段。

## 禁止 claim

- 禁止寫「主導 iwin 自動出款」。
- 禁止寫「設計金流提款一致性架構」。
- 禁止寫「解決重複退款問題」。
- 禁止寫「負責 payment owner」。
- 禁止寫「建立完整 reconciliation 機制」。
- 禁止寫「保證不會重複上下分」。

## 下一步

```text
iwin payment withdrawal-auto-review-refund Step 5
```
