# claim-boundary

## Step 5 claim 判斷

本 flow 目前是：

- `專案存在 / code-backed`
- `分析素材 / learning-only`
- Nick 個人貢獻：未確認到可放正式履歷的直接 evidence

## 可以使用

- 面試時作為人工修復 / 補償邊界案例。
- 說明 payment 人工審核不是單純改狀態，而是可能牽涉上分、退款、統計與通知。
- 說明 direct repair 與正式審核 API 的差異。
- 說明 `PROCESSING` unknown 不應直接退款或改成功。

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

## Step 5 結論

- 可作 Senior Backend / Owner 面試分析素材。
- 不更新正式履歷 / 自傳。
- 已完成最後 claim gate；目前沒有 Nick 本人 MR / ticket / production issue / 本人確認，path-specific commit 也不足以支撐人工審核 / 補單 / 修單 claim。
- 已找到的 `03c28e3`、`6539d7a` 只能支撐共享建單 / 提款 insert consistency 相關保守說法，不延伸到本 flow ownership。

## 下一步

```text
iwin payment contribution claim consolidation
```

## 履歷 claim 分層（2026-05-18 KB 對齊）

- 可放履歷：目前不單獨升級成本 flow 的真實開發成果；但不得否定 Nick 在 `payment` 的整體實際開發經驗。若放履歷，應先併入 project-level payment contribution consolidation。
- 可面試講：code-backed / 分析過。可用本 flow 說明 money correctness、狀態轉移、冪等、retry、補償、人工修復或 runtime config consistency。
- 不可誇大：不得把本 flow 寫成 Nick 主導完整 payment / wallet owner、設計整套金流架構、解決全部對帳或 production incident，除非後續補到本人 MR / ticket / production issue / 本人確認與重要 diff。
