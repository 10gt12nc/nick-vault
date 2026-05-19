# third-party-record-mongo-backup claim-boundary

完成狀態：Step 5。

## Evidence level

目前：局部真實開發過 + code-backed。

可確認 claim：

- `10gt12nc` 在 2025-05-05 有 GSC backup 分批查詢相關 commit，且 commit 已存在於 `main` / `origin/main` / `origin/k3s`：
  - `d11b1f4`：`fix  gsc 改分批查詢`
  - `bf92773`：`fix  gsc 改分批查詢 10000`
- `d11b1f4` 直接修改 `ThirdLogGscJob`、`ThirdTransactionGscJob`、GSC VO 與 quartz config，把 GSC backup 從一次查全量改成分批查詢。
- `bf92773` 直接修改兩支 GSC job 的 batch size。

Step 5 判斷：

- 可寫成「參與 GSC 第三方遊戲紀錄 Mongo 備份 job 的分批查詢與批次大小調整」。
- 可放入 `game_job` / 批次處理 / Mongo 大量資料處理的保守履歷素材。
- 不足以寫成完整第三方遊戲紀錄備份 owner，也不足以涵蓋 Antplay / Oneapi / retention policy owner。

## 可面試講

- 分析過第三方遊戲 provider Mongo log / transaction retention job。
- 能說明 copy-then-delete 的 partial failure、idempotency、duplicate backup、delete count reconciliation。
- 能提出 owner 級改善：duplicate-safe write、run id、copied / deleted count 對帳、retention policy 對齊。
- 能保守說實際參與過 GSC backup job 的分批查詢與 batch size 調整，這是局部直接 commit evidence。

## 可保守放履歷

可放正式履歷，但建議放在 `game_job` / 批次處理綜合 bullet 中，不要單獨放大成一整個專案成果：

```text
參與 GSC 第三方遊戲紀錄 Mongo 備份 job 的分批查詢與批次大小調整，降低單次批次處理風險，並支援 provider log / transaction retention。
```

更保守履歷版：

```text
參與遊戲報表與紀錄保留相關 batch 維護，包含每日遊戲資料彙總，以及 GSC 第三方遊戲紀錄 Mongo 備份 job 的分批查詢與批次大小調整。
```

## 不可寫

- 主導第三方遊戲紀錄備份系統。
- 負責 Antplay / GSC 完整 audit retention policy。
- 設計完整 Mongo backup 架構。
- 改善 storage 成本 X%。
- 改善查帳效率 X%。
- 確保資料零遺失。
- GSC / Antplay / Oneapi 全 provider retention owner。
- 已驗證 production cron 啟用狀態。

## 已補 evidence

- `d11b1f4` / `bf92773` diff 細節已讀。
- branch contains 已確認兩個 commit 都在 `main`、`origin/main`、`origin/k3s`。
- path-specific author log 已確認 GSC backup path 只有這兩個 `10gt12nc` direct commits；後續 `b993395` by arnold 把 GSC transaction batch size 改為 2500。

## 仍待確認

- 是否有 MR / ticket / production issue 可補強背景。
- Nick 本人是否要補充當時需求來源與負責範圍。
- GSC job 在 production 實際啟用狀態；main config 目前仍顯示 GSC disabled。
