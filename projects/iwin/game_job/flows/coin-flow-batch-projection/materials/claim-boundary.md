# Claim Boundary: coin-flow-batch-projection

## Step 5 判定

最終判定：不更新正式履歷 / 自傳。

證據層級：專案存在 / code-backed。

原因：

- 已深讀 `CoinFlowJob` 核心 path，並已轉成 Step 4 正式面試 case。
- Step 5 已再次查核 source repo 狀態與 path-specific history；`CoinFlowJob.java` / coin flow model / mapper path 未看到足夠 Nick / `10gt12nc` direct ownership evidence。
- `10gt12nc` 在相鄰 mapper / coin flow 相關 path 可見的 commit 主要是 daily summary 類調整，不能外推成 Nick 真實開發 `coin-flow-batch-projection`。
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

## 正式履歷 / 自傳

本輪不更新：

- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

原因是 evidence 只能支撐「code-backed 分析過」，不足以支撐「真實開發過」或「參與開發 / 維護」。

## 未來若要升級 claim 需要補的 evidence

- Nick 本人確認是否實際參與過 coin flow。
- `10gt12nc` 在 coin flow direct path 的 commit / branch / MR / ticket。
- production issue 或修復紀錄。
- app_bi / BI 查詢端是否把此 projection 用於重要報表。

## 最終用法

這條保留為 code-backed 面試 case，展示 batch projection、checkpoint consistency、replay 與 observability 分析能力；不放正式履歷，不包裝成 Nick 主導或真實開發成果。
