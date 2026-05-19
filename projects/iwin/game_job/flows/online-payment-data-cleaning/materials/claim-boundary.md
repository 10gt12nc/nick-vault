# Claim Boundary: online-payment-data-cleaning

## Step 5 最終判定

目前判定：不更新正式履歷 / 自傳。

證據層級：專案存在 / code-backed。

原因：

- 已深讀 `OnlinePaymentDataJob` 核心 path，確認它是 payment reporting projection。
- Step 4 已整理成可面試的 code-backed case，但不是 direct contribution evidence。
- path-specific history 沒有看到 Nick / `10gt12nc` direct commit 可以證明 Nick 真實開發這條 flow。
- Step 5 補查 direct path history、Nick / `10gt12nc` author commits、keyword history 與 production config 線索後，仍沒有 Nick 本人確認、MR、ticket、production issue 可補強。

## 可說

- 我分析過一條充值 / 提現資料清洗與每日經濟資料 projection。
- 這條 flow 從 `payment_order_{yyyy_m}` 成功訂單產出 Mongo payment metrics、MySQL `economic_data_day_log` 與金額分布表。
- 我能說明資料日、Redis distinct uid、Mongo insert projection、MySQL delete+insert 與 downstream daily total 的一致性風險。
- 若面試官追問 owner decision，我可以提出 observability、replay SOP、deterministic projection、staging / snapshot 的改進方向。

## 不可說

- 我開發這條 flow。
- 我主導 payment reporting / reconciliation。
- 我負責 provider callback、payment source of truth、錢包上分或提款出款。
- 我改善了支付報表正確率或效能 X%。
- production 實際啟用狀態已確認。

## 若未來要升級，需要補的 evidence

- Nick 本人確認是否實際參與過 online payment data cleaning。
- `10gt12nc` 在 direct path 的 commit / branch / MR / ticket。
- `payment` repo 是否有對應 source order 寫入 / 修復 commits，可把本 flow 和 payment source 串成更完整 case。
- app_bi / BI 查詢端是否使用 Mongo `online_*` 或 `daily_economic_data_total`，以及是否有重複 / replay 風險。

## 正式履歷 / 自傳判定

不更新 `senior-owner-playbook/05-resume-master-zh.md` 與 `senior-owner-playbook/08-application-autobiography-zh.md`。

原因：這條 flow 很適合面試說明 money reporting projection，但目前 evidence 只支持「我分析過 / code-backed」，不支持「我開發 / 參與維護」。若 Nick 未來補充本人確認，可改判為「本人確認 + code-backed，待 commit / ticket 補強」，但仍不能寫成主導完整 payment owner。
