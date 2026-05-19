# Claim Boundary: online-payment-data-cleaning

## Step 3 判定

目前判定：不更新正式履歷 / 自傳。

證據層級：專案存在 / code-backed。

原因：

- 已深讀 `OnlinePaymentDataJob` 核心 path，確認它是 payment reporting projection。
- path-specific history 沒有看到 Nick / `10gt12nc` direct commit 可以證明 Nick 真實開發這條 flow。
- 目前沒有 Nick 本人確認、MR、ticket、production issue 可補強。

## 可說

- 我分析過一條充值 / 提現資料清洗與每日經濟資料 projection。
- 這條 flow 從 `payment_order_{yyyy_m}` 成功訂單產出 Mongo payment metrics、MySQL `economic_data_day_log` 與金額分布表。
- 我能說明資料日、Redis distinct uid、Mongo insert projection、MySQL delete+insert 與 downstream daily total 的一致性風險。

## 不可說

- 我開發這條 flow。
- 我主導 payment reporting / reconciliation。
- 我負責 provider callback、payment source of truth、錢包上分或提款出款。
- 我改善了支付報表正確率或效能 X%。
- production 實際啟用狀態已確認。

## Step 4 前需要補的 evidence

- Nick 本人確認是否實際參與過 online payment data cleaning。
- `10gt12nc` 在 direct path 的 commit / branch / MR / ticket。
- `payment` repo 是否有對應 source order 寫入 / 修復 commits，可把本 flow 和 payment source 串成更完整 case。
- app_bi / BI 查詢端是否使用 Mongo `online_*` 或 `daily_economic_data_total`，以及是否有重複 / replay 風險。

## Step 4 初步預期

Step 4 可把這條轉成保守面試 case，主題是「不是 payment truth，但會影響 money reporting 的 projection correctness」。若沒有新增 evidence，Step 5 大概率不更新正式履歷 / 自傳。
