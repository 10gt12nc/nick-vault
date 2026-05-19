# Decision Notes: online-payment-data-cleaning

完成狀態：Step 3。

用途：整理這條 payment reporting projection 的 owner decision、取捨與面試 framing。

## 決策題 1：資料日用 `last_modify_time`

現況：

- `queryPaymentOrders()` 用 `DATE_FORMAT(last_modify_time,'%Y-%m-%d') = date`。
- 不是用 `create_time`，也不是 provider callback event time。

優點：

- 補單、審核、callback 最終成功後才進入報表。
- 對營運「當天成功入帳 / 出款」視角可能較合理。

代價：

- 昨天建立、今天成功的訂單會進今天。
- 人工修單或 late callback 可能改變資料日。
- 若 downstream 用另一種資料日，對帳會不一致。

Owner 追問：

- payment repo 裡成功時間是否就是 `last_modify_time`？
- 充值、提現、人工補單是否都應用同一資料日？
- 報表要看「建立日」還是「成功日」？

## 決策題 2：全日重掃 vs 持久 checkpoint

現況：

- job 每輪 `lastOrderId=0`，用分頁掃當日成功訂單。
- 沒有保存 last processed id。

優點：

- 不怕上一輪漏掉 late update；只要 source 狀態變成功，下一輪會被看見。
- 不需要維護 checkpoint consistency。

代價：

- 每 8 分鐘掃全日成功訂單，資料量越大成本越高。
- Mongo insert 型 projection 需要處理重複，否則全日重掃會留下多筆 snapshot。
- Redis distinct uid key 會在本日多輪間保留，和全日重掃一起決定人數語意。

Owner 判斷：

- 若這是「近即時 snapshot」，應明確用 replace / versioned snapshot。
- 若這是「append time series」，查詢端要按 minute 或 latest snapshot 聚合，避免重複。

## 決策題 3：Mongo insert projection

現況：

- `OnlineHandlerServerBase.savePayData()` / `savePayTypeData()` 對 Mongo collection 直接 insert。
- collection 名稱是 `online_*_{yyyy_m}`。

風險：

- 同 date / channel / cps / minute 重跑可能有多筆。
- job 失敗後重跑沒有明確 delete / upsert / version switch。
- 查詢端若沒有取 latest 或按 minute 正確聚合，數字可能重複。

較穩方案：

- 若要保存 snapshot history，加入 run id / minute / generatedAt 並讓查詢端明確取最新。
- 若只要當日最新值，用 deterministic key upsert。
- 若要重跑指定日期，先清指定 date 的 Mongo projection，再重建。

## 決策題 4：MySQL economic day log delete+insert

現況：

- `saveEconomicData(date)` 先 `deleteEconomicData(date)`，再 insert `dataMap`。

優點：

- 重跑指定 date 時可重建整日 economic summary。

代價：

- delete 成功、insert 前掛掉會造成下游空窗。
- delete 只按 date，不按 channel / cps，單 channel 重算也會清掉全日資料。
- 下游 `DailyEconomicDataTotalJob` 若剛好讀到空窗，會產生錯誤 daily total。

較穩方案：

- staging table + version switch。
- delete / insert 包 transaction。
- 至少補 delete count、insert count、channel+cps count 對帳。

## 決策題 5：Redis distinct uid

現況：

- 人數去重用 Redis hash，TTL 2 天。
- key 維度依 service / channel / cps / date / tradeType / onlineType 不同。

優點：

- 同一玩家多筆訂單不會重複算人數。
- 比每輪 SQL `count distinct` 彈性高。

代價：

- Redis 是暫存 state，custom date replay 超過 TTL 後語意會不同。
- DB projection 失敗但 Redis key 成功寫入，重跑時人數可能仍依舊 key 計算。
- 如果同一玩家訂單被修正分類，舊 key 可能仍殘留到 TTL 到期。

Owner 判斷：

- 若目標是可重算報表，人數去重最好能從 source SQL deterministic 重算。
- 若用 Redis，replay SOP 要包含清理對應 date / service key。

## Step 3 後仍不可升級的點

- 不可寫 Nick 真實開發這條 flow，因 direct path 未見 Nick / `10gt12nc` commit。
- 不可說這條是 payment callback / wallet source of truth。
- 不可說 production 已驗證啟用，因 main config disabled。
- 不可寫改善支付報表正確率、查帳時間或效能 X%。
