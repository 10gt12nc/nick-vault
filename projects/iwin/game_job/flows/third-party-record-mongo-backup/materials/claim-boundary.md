# third-party-record-mongo-backup claim-boundary

## Evidence level

目前：專案存在 / code-backed。

可保留線索：

- `10gt12nc` 在 2025-05-05 有 GSC backup 分批查詢相關 commit：
  - `d11b1f4`：`fix  gsc 改分批查詢`
  - `bf92773`：`fix  gsc 改分批查詢 10000`
- 這代表 Nick / `10gt12nc` 對 GSC backup job 有局部直接 commit evidence。

仍待 Step 5 判斷：

- 是否能寫成「參與 GSC 第三方紀錄 Mongo 備份 job 的分批查詢與批次大小調整」。
- 是否有 MR / ticket / production issue / 本人確認可補強。
- 是否只是一次修正，不能升級成完整 flow owner。

## 可面試講

- 分析過第三方遊戲 provider Mongo log / transaction retention job。
- 能說明 copy-then-delete 的 partial failure、idempotency、duplicate backup、delete count reconciliation。
- 能提出 owner 級改善：duplicate-safe write、run id、copied / deleted count 對帳、retention policy 對齊。

## 可保守放履歷的條件

只有 Step 5 通過後，才考慮以下保守句：

```text
參與 GSC 第三方遊戲紀錄 Mongo 備份 job 的分批查詢與批次大小調整，降低單次批次處理風險，並支援 provider log / transaction retention。
```

這句目前還不能放正式履歷，因為 Step 3 尚未完成 claim gate。

## 不可寫

- 主導第三方遊戲紀錄備份系統。
- 負責 Antplay / GSC 完整 audit retention policy。
- 設計完整 Mongo backup 架構。
- 改善 storage 成本 X%。
- 改善查帳效率 X%。
- 確保資料零遺失。

## Step 5 要補的 evidence

- `10gt12nc` 兩個 commit 的 diff 細節。
- 是否有相關 branch / MR / ticket。
- Nick 本人是否確認當時負責範圍。
- GSC job 在 production 實際啟用狀態。
- 是否有後續 bugfix 或 rollback。
