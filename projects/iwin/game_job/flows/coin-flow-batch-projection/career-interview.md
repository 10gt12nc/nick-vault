# coin-flow-batch-projection career-interview

## 定位

本文件是 `coin-flow-batch-projection` 的 Step 3 初版面試素材。Step 4 時再轉成完整 30 秒 / 3 分鐘 / STAR / Lead 追問版本。

證據層級：專案存在 / code-backed。尚未可寫正式履歷。

## 30 秒口語版

我分析過一條遊戲金幣流水與玩家行為的 batch projection。它不是錢包交易 source of truth，而是從 `log_reel`、`log_jackpot`、`log_taxt` 三種每日分表增量拉資料，靠 Redis 保存每張表的 last id checkpoint，再寫入 MySQL `user_game_behaviour` 與 Mongo `log_coin_flow` 報表資料。

這條 flow 的重點是 checkpoint consistency。MySQL 是累加式 upsert，Mongo 是 delete+insert projection，Redis 又保存當日累計結果，所以重跑與失敗恢復不能只看單一 DB，要把 checkpoint、projection 寫入、跨日 finish 一起看。

## 3 分鐘技術版

這條 flow 在 `game_job` 裡由 Quartz 每 5 分鐘觸發。`CoinFlowJobQuartz` 設定 daily task 與 30 分鐘 timeout，進到 `BiJobBase` 之後會決定跑昨天或今天，再用 Redis task state 防止同一日期重疊執行。

核心 job 會逐 channel 處理三個來源：

- `log_reel_{yyyy_m_d}`：戰績、下注、派彩。
- `log_jackpot_{yyyy_m_d}`：Jackpot / 暗池。
- `log_taxt_{yyyy_m_d}`：服務費 / 稅費。

每張表各自有 `LastPullMaxId`，查詢語句是 `id > checkpoint order by id asc limit 2000`。拉到資料後，job 依 gameId 分派到不同 coin flow model 計算房間、投注、輸贏、抽水、暗池與玩家數。

輸出分兩層：

- `user_game_behaviour_{yyyy_m}` 用 `insert ... on duplicate key update` 累加玩家行為。
- Mongo `log_coin_flow_{gameId}` 對同 channel / cps / date 先刪再 insert。

我會把風險分成三類看：

1. Checkpoint 先推進但 projection 寫入失敗，會造成漏算。
2. Replay 重拉時 MySQL 累加 upsert 可能重複，但 Mongo projection 又比較像覆蓋，兩邊 idempotency 不一致。
3. 跨日切換靠「零點後再多等 11 分鐘」與所有 channel / 三張表 pull over status，若 source writer 延遲或某 channel 卡住，會影響當日 finish。

## 可面試講

- Incremental batch 怎麼設 checkpoint。
- 多 source table 同步到同一個 projection 時，如何判斷 source of truth。
- Redis 作 checkpoint / 暫存累計結果的風險。
- MySQL 累加式 upsert 與 Mongo delete+insert 的 idempotency 差異。
- Custom date replay 為什麼要先清舊資料。
- 跨日 catch-up window 為什麼要保守設計。

## 不可誇大

- 不能說 Nick 開發或主導這條 flow。
- 不能說這是正式金流 / wallet source of truth。
- 不能說已驗證 production enable。
- 不能寫任何量化改善。

## Step 4 要補

- STAR case：報表 projection 漏算 / 重複累加時如何定位。
- Lead 追問：如果要重構，如何把 checkpoint 與 projection 寫入做成可恢復流程。
- Trade-off：全量重算、增量 checkpoint、event-driven consumer 三種方案比較。
