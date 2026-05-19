# Interview Notes: online-payment-data-cleaning

## 面試主軸

這條 flow 適合拿來講：「payment order 進入 BI / economic reporting 之後，報表正確性仍然要看資料日、重跑、去重與下游一致性。」

證據層級：專案存在 / code-backed。不能說是 Nick 真實開發或主導。

## 口語開場

我分析過一條充值 / 提現資料清洗 batch。它不是 provider callback，也不是錢包 source of truth，而是從成功的 `payment_order_{yyyy_m}` 讀資料，整理成 Mongo payment metrics、MySQL `economic_data_day_log`，再提供給每日經濟總表計算 pay、withdraw、profit、ARPU、ARPPU。

我會把它定位成 money reporting projection。它不直接處理交易，但如果資料日、重跑、去重或 projection 寫入出錯，營運看到的 payment / withdraw / profit 指標就會錯。

## 深入講法

```text
這條 flow 的重點是 reporting projection 的成功邊界。

source 是 payment_order 月表，資料日用 last_modify_time。
job 每輪會掃當日成功訂單，依 reason / tradeType / onlineType 分類。

下游有三種語意：
1. Mongo online_* 指標是 insert 型 projection。
2. MySQL economic_data_day_log 是 delete date 後 insert。
3. payment_amount 類表是 duplicate key update。

另外人數去重不是直接 SQL count distinct，而是靠 Redis hash 記 uid。

所以重跑時要一起看 source order、Redis 去重 key、Mongo 重複、MySQL 空窗、DailyEconomicDataTotalJob 是否吃到正確資料。
```

## STAR 素材

### Situation

某天某 channel / cps 的充值金額、提現金額、首充人數或每日經濟總表 profit / ARPU 數字異常。

### Task

判斷問題在 source order、資料日、分類規則、Redis 去重、projection 寫入或 downstream daily total。

### Action

1. 查 `BiTaskStatus:OnlinePayment` task log，確認 job 成功、失敗或卡住。
2. 對 `payment_order_{yyyy_m}` 查 `status=0` 且 `last_modify_time` 為該日的訂單。
3. 依 reason / tradeType / onlineType 檢查是否被 job 納入分類。
4. 查 Redis distinct uid key，確認人數去重是否受 TTL 或舊 key 影響。
5. 查 Mongo `online_*` 是否有同 date / channel / cps / minute 重複資料。
6. 查 `economic_data_day_log` 是否完整，再查 `daily_economic_data_total` 是否已重算。
7. 若要 replay，定義要清的 projection 範圍，不只重跑 job。

### Result

能把 payment reporting 問題切成 source、classification、dedupe、projection、downstream 五段；避免把 BI 數字異常直接歸因成 provider 或 wallet 錯。

## 面試官追問與回答

### Q1：這條是不是金流？

不是。它讀成功訂單產生報表，不做 provider callback、不改訂單狀態、不改錢包。它靠近 money reporting，但不是 payment source of truth。

### Q2：為什麼資料日用 `last_modify_time` 有風險？

因為它代表最後修改日。late callback、人工補單或修單可能把原本建立於昨天的訂單算到今天。這不一定錯，但要和 business 定義對齊。

### Q3：這條 flow idempotent 嗎？

不能簡單說 idempotent。MySQL amount distribution 是 upsert，`economic_data_day_log` 是 delete+insert，Mongo 指標是 insert，Redis 又保存 distinct uid。每一段重跑語意不同。

### Q4：如果你 owner，第一步補什麼？

先補 observability 與 replay SOP：

- source order count、pay / withdraw count。
- Mongo insert count / duplicate count。
- `economic_data_day_log` delete count / insert count。
- Redis distinct key 清理規則。
- downstream daily total 重算順序。

### Q5：如果要讓這條 replay-safe，你會怎麼設計？

我會把 projection 做成 deterministic：

- 先定義資料日是成功日、修改日或 provider event day。
- Mongo 以 date + channel + cps + metric 維度 upsert，或在 replay 前清理完整 scope。
- `economic_data_day_log` 用 staging table 或 versioned snapshot，避免 delete 後 insert 的空窗被下游讀到。
- 人數去重避免只依賴短 TTL Redis key，必要時由 source order 或 staging 結果重建。
- replay SOP 明確規定 upstream cleaning、daily total 重算與 BI 查詢 cache 的順序。

### Q6：怎麼避免把 reporting 異常誤判成交易異常？

先分 source order 與 projection。若 `payment_order_{yyyy_m}` 的成功訂單正確，但 Mongo / `economic_data_day_log` / `daily_economic_data_total` 不一致，那是 reporting projection 問題；若 source order 本身狀態、成功時間或金額就錯，才往 payment repo / provider callback / wallet transaction 查。

### Q7：這可以寫履歷嗎？

目前不建議。這條缺 Nick direct evidence，只能作 code-backed 面試分析素材。正式履歷要等 Step 5 claim gate，或補到本人確認、commit、MR、ticket、production issue。

## 可用句子

- 這條不是 payment truth，但它會影響 money reporting。
- 我會先分清成功訂單、報表資料日、projection 寫入與每日總表，避免把報表錯誤誤判成交易錯誤。
- Mongo insert、MySQL delete+insert、MySQL upsert、Redis 去重這四種 state 語意不同，重跑策略不能混在一起看。
- 如果要做 replay-safe，我會先定義資料日與 projection 唯一鍵，再讓下游只讀完整 snapshot。
- 沒有 direct contribution evidence 前，我只把它當 code-backed 分析，不寫成履歷成果。
