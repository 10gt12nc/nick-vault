# online-payment-data-cleaning career-interview

完成狀態：Step 3。

證據層級：專案存在 / code-backed。這條 flow 目前沒有足夠 Nick / `10gt12nc` direct path evidence 可寫成真實開發過；面試時只能說「我分析過 / code-backed 梳理過」，不能說「我開發 / 主導」。

## 一句話版本

這是一條 payment order reporting projection：從成功的 `payment_order_{yyyy_m}` 讀充值 / 提現訂單，整理成 Mongo 支付分布、MySQL `economic_data_day_log`，再供 `daily_economic_data_total` 計算 profit、付費率、ARPU / ARPPU 等營運指標。

## 30 秒版本

我分析過一條充值 / 提現資料清洗 batch。它不是金流 callback 或錢包交易核心，而是從 payment order 月表讀成功訂單，用最後修改時間當資料日，按線上、線下、快捷、代理、首充、新註冊與提現分類，產生 BI / economic reporting projection。

這條 flow 的重點是 reporting correctness。Mongo 指標是 insert 型 projection，MySQL economic day log 是 delete+insert，Redis 又用 hash 做 distinct uid 去重；所以重跑與失敗恢復不能只看 job 成功，要看 source order、資料日、Redis 去重、Mongo / MySQL 寫入與下游 daily total 是否一致。

## 3 分鐘版本

這條 flow 在 `game_job` 裡由 Quartz 每 8 分鐘觸發。`OnlinePaymentDataJobQuartz` 設定 `BiTaskStatus:OnlinePayment`，交給 `BiJobBase` 管 task date、custom date、Redis task status 與 task log。

核心 job 會讀 `payment_order_{yyyy_m}`，條件是 `status=0` 且 `last_modify_time` 落在當前資料日。充值只吃幾種 reason：線上充值、快捷支付、代理充值，以及特定 GM / 補單類型；提現會用 `actual_out_money` 當統計金額。

輸出分三層：

- Mongo `online_*` 月 collection：支付金額、人數、次數、trade type / online type 等細分指標。
- MySQL `economic_data_day_log`：每日經濟資料，會被 `DailyEconomicDataTotalJob` 讀走。
- MySQL `payment_amount`、`first_payment_amount`、`payment_amount_agent`：充值金額區間分布。

我會把風險分成四類：

1. 資料日用 `last_modify_time`，人工補單或 callback late update 會改變報表歸屬日期。
2. Mongo 是 insert 型 projection，重跑可能留下重複指標。
3. `economic_data_day_log` 先 delete date 再 insert，失敗時下游每日經濟總表可能讀到空窗。
4. Redis distinct uid key 只有 2 天 TTL，重跑時間拉長後，人數去重語意要重新確認。

所以這條 flow 適合講 payment reporting / reconciliation view，而不是講自己 owner 了 payment source of truth。

## STAR 素材：每日支付數字異常排查

Situation：營運看到某天某 channel / cps 的充值金額、提現金額、首充人數或 ARPU 不合理。

Task：定位問題是 payment source order、資料日、projection 重跑、Redis 去重、還是下游 daily total。

Action：

1. 查 `BiTaskStatus:OnlinePayment` task log，確認 job 是否成功或仍在 running。
2. 查 `payment_order_{yyyy_m}` 中 `status=0` 且 `last_modify_time` 為該日的充值 / 提現訂單量。
3. 對照 reason / tradeType / onlineType，確認訂單是否被 job 分類邏輯納入。
4. 查 Redis distinct uid key 是否存在，避免人數重算時被舊 key 或 TTL 影響。
5. 查 Mongo `online_*` collection 是否有重複 date / channel / cps / minute 指標。
6. 查 `economic_data_day_log` 是否已重建，再查 `daily_economic_data_total` 是否吃到最新資料。
7. 若要重跑，先定義清理範圍：Mongo 指標、economic day log、amount distribution 與 Redis distinct key 要一起看。

Result：可以把問題拆成 source、classification、dedupe、projection、downstream 五個邊界，避免把 reporting 異常誤判成 provider callback 或錢包交易錯誤。

## 可面試講

- payment order 不是直接等於報表，要經過資料日、狀態、reason / tradeType / onlineType 的分類。
- reporting projection 也要看 idempotency，尤其 Mongo insert 與 MySQL delete+insert 的語意不同。
- Redis distinct uid 可以解決同日多筆訂單的人數去重，但會引入 TTL / replay 邊界。
- `DailyEconomicDataTotalJob` 依賴 `economic_data_day_log`，所以 upstream payment cleaning 的空窗會傳到 profit、pay rate、ARPU / ARPPU。

## 不可誇大

- 不說 Nick 開發這條 flow。
- 不說 Nick 主導 payment reporting / reconciliation。
- 不說這條是 payment provider callback、錢包上分或提款出款核心。
- 不說已驗證 production enable。
- 不寫任何量化改善。

## 下一步

```text
iwin game_job online-payment-data-cleaning Step 4
```
