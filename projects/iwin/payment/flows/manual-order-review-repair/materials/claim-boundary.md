# claim-boundary

## Step 3 claim 判斷

本 flow 目前是：

- `專案存在 / code-backed`
- `分析素材 / learning-only`
- Nick 個人貢獻：待確認

## 可以使用

- 面試時作為人工修復 / 補償邊界案例。
- 說明 payment 人工審核不是單純改狀態，而是可能牽涉上分、退款、統計與通知。
- 說明 direct repair 與正式審核 API 的差異。

## 不可使用

- 不放入正式履歷。
- 不寫 Nick 主導人工審核 / 補單 / 修單。
- 不寫 Nick 設計 reconciliation。
- 不寫已確認 end-to-end exactly-once。

## Evidence 判斷

已找到 Nick 相關但只能間接使用：

- `03c28e3`：payment 建單 id copy 修正，和補單 / 建單共用風險相關。
- `6539d7a`：withdraw insert id copy 修正，和提款建單一致性相關。

未找到：

- `10gt12nc` 直接修改 `PayTypeServiceImpl#oderView`。
- `10gt12nc` 直接修改 `PayTypeServiceImpl#gameRecharge` 主邏輯。
- `10gt12nc` 直接修改 app_bi `bill_check` / `repairOrderService`。

## 下一步

```text
iwin payment manual-order-review-repair Step 4
```
