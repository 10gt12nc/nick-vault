# Decision Notes: coin-flow-batch-projection

## 決策題 1：增量 checkpoint vs 每次全量重算

現況：

- 每 5 分鐘依 Redis `LastPullMaxId` 從三張每日 source table 增量拉資料。
- Custom date replay 才會先清舊 projection。

優點：

- 不需要每次掃整天 source log。
- 能持續把最新 log 投影到 BI / Mongo。

代價：

- checkpoint 推進後，下游寫入失敗會產生漏投影風險。
- Redis 遺失或人工重設會造成 replay 語意不穩。
- MySQL 累加 upsert 對重拉不安全。

Owner 判斷：

- 若目標是 near-real-time 報表，增量合理。
- 若目標是最強正確性，應提供可控 replay：先清 projection、再從 source 全量重算指定 date，最後切換完成狀態。

## 決策題 2：Mongo delete+insert vs upsert replace

現況：

- `saveCoinFlowData()` 先 remove `channel+cps+dateYmd`，再 insert。

優點：

- 實作直觀。
- 可覆蓋同一天同 channel / cps 的舊 projection。

代價：

- remove 和 insert 中間有空窗。
- insert 失敗時資料會消失。
- 沒有明確 version / batch id，難以觀察哪次投影產生資料。

較穩方案：

- 用 deterministic unique key 做 upsert replace。
- 或寫入 staging collection，再以 version / ready flag 切換。

## 決策題 3：MySQL user behaviour 累加 upsert

現況：

- `user_game_behaviour_{yyyy_m}` 用 duplicate key update 做累加。

優點：

- 每批 delta 寫入快。
- 不需要每輪重新計算玩家行為。

代價：

- replay 同一批資料會重複累加。
- checkpoint 與 DB 寫入不是 transaction。
- 依賴 table unique key 正確定義。

Owner 判斷：

- Incremental delta 型 projection 必須把 checkpoint 視為交易邊界。
- 若要讓重跑安全，重跑入口必須先清指定 date 的 projection，或改成以 source record id 去重。

## 決策題 4：跨日多等 11 分鐘

現況：

- 拉不到資料且已跨日後，還要等零點後約 11 分鐘才標記 source table pull over。

優點：

- 避免零點瞬間 source writer 還在補昨天資料造成漏算。

代價：

- 11 分鐘是業務假設，不一定覆蓋所有 writer delay。
- 延遲超過窗口仍可能漏資料。
- 所有 channel / table 都要 pull over 才能 finish，單點卡住會拖住整個 date。

建議 owner 追問：

- production writer 延遲 P95 / P99 是多少？
- 卡住時是否有可觀測告警？
- 是否允許 late data repair / replay？
