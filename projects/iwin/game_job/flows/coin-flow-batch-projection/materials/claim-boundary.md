# Claim Boundary: coin-flow-batch-projection

## Step 3 判定

目前判定：不更新正式履歷 / 自傳。

證據層級：專案存在 / code-backed。

原因：

- 已深讀 `CoinFlowJob` 核心 path，可作面試分析素材。
- path-specific history 沒有足夠 Nick / `10gt12nc` direct commit 可以證明 Nick 真實開發這條 flow。
- 目前沒有 Nick 本人確認、MR、ticket、production issue 可補強。

## 可說

- 我分析過一條遊戲金幣流水 / 玩家行為 batch projection。
- 這條 flow 從 `log_reel`、`log_jackpot`、`log_taxt` 增量拉資料，透過 Redis checkpoint 生成 MySQL / Mongo projection。
- 我能說明 checkpoint consistency、custom date replay、跨日 finish、MySQL 累加 upsert 與 Mongo delete+insert 的 failure window。

## 不可說

- 我開發這條 flow。
- 我主導金幣流水清算。
- 我負責完整遊戲金流 / 錢包 / 派彩。
- 我改善了報表正確率或效能 X%。
- production 實際啟用狀態已確認。

## Step 5 前需要補的 evidence

- Nick 本人確認是否實際參與過 coin flow。
- `10gt12nc` 在 coin flow direct path 的 commit / branch / MR / ticket。
- production issue 或修復紀錄。
- app_bi / BI 查詢端是否把此 projection 用於重要報表。
