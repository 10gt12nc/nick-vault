# coin-flow-batch-projection career-interview

## 定位

本文件是 `iwin game_job coin-flow-batch-projection Step 4` 的正式面試 case；Step 5 claim gate 已完成。

證據層級：專案存在 / code-backed。Step 5 已確認這條 flow 目前沒有足夠 Nick / `10gt12nc` direct path evidence 可寫成真實開發過；面試時只能說「我分析過 / code-backed 梳理過」，不能說「我開發 / 主導」。

## 30 秒摘要

我分析過一條遊戲金幣流水與玩家行為的 batch projection。它不是錢包交易 source of truth，而是從 `log_reel`、`log_jackpot`、`log_taxt` 三種每日分表增量拉資料，靠 Redis 保存每張表的 last id checkpoint，再寫入 MySQL `user_game_behaviour` 與 Mongo `log_coin_flow` 報表資料。

這條 flow 的重點不是單純會跑 batch，而是 checkpoint consistency。MySQL 是累加式 upsert，Mongo 是 delete+insert projection，Redis 又保存當日累計結果；所以重跑與失敗恢復不能只看單一 DB，要一起看 checkpoint、projection 寫入、跨日 finish 與 custom date replay。

## 3 分鐘版本

這條 flow 在 `game_job` 裡由 Quartz 觸發。`CoinFlowJobQuartz` 設定 daily task 與 30 分鐘 timeout，進到 `BiJobBase` 之後會決定跑昨天或今天，再用 Redis task state 防止同一日期重疊執行。

核心 job 會逐 channel 處理三個來源：

- `log_reel_{yyyy_m_d}`：戰績、下注、派彩。
- `log_jackpot_{yyyy_m_d}`：Jackpot / 暗池變化。
- `log_taxt_{yyyy_m_d}`：服務費 / 稅費。

每張表都有自己的 `LastPullMaxId`，查詢語句是 `id > checkpoint order by id asc limit 2000`。拉到資料後，job 依 gameId 分派到不同 coin flow model，計算房間、投注、輸贏、抽水、暗池與玩家數。

輸出分兩層：

- `user_game_behaviour_{yyyy_m}` 用 `insert ... on duplicate key update` 累加玩家行為。
- Mongo `log_coin_flow_{gameId}` 對同 channel / cps / date 先刪再 insert。

我會把風險分成三類：

1. Checkpoint 先推進但 projection 寫入失敗，可能漏算。
2. Replay 重拉時，MySQL 累加 upsert 可能重複，但 Mongo projection 又比較像覆蓋，兩邊 idempotency 不一致。
3. 跨日切換靠零點後多等 11 分鐘與所有 channel / 三張表 pull over status；如果 source writer 延遲或某個 channel 卡住，會影響當日 finish。

所以這條 flow 的 owner 重點是把「成功邊界」定清楚：source 拉取、MySQL 累加、Mongo replace-like projection、Redis last result、Redis checkpoint 哪一步成功才算一批成功。

## STAR Case：報表漏算 / 重複累加排查

### Situation

營運或 BI 發現某一天某個 channel / game 的報表數字不對，可能是投注額偏低、玩家數不對，或 Mongo coin flow 某段資料缺失。

### Task

目標不是直接手改報表，而是判斷是 source log 缺資料、checkpoint 卡住、projection 寫入失敗，還是 replay 重複累加。

### Action

1. 先看 `BiTaskStatus:CoinFlow` 的 task log，確認 job 是否成功、是否 exception、是否仍 running。
2. 查 Redis `LastPullMaxId` 與 `PullOverStatus`，定位是哪個 channel、哪張 source table 停住。
3. 對 source 分表查 `id > checkpoint` 是否還有資料，確認是 source writer 延遲還是 job 未處理。
4. 查 `user_game_behaviour_{yyyy_m}`，確認該 date / channel 的玩家行為是否已累加。
5. 查 Mongo `log_coin_flow_{gameId}`，確認同 channel / cps / date projection 是否存在。
6. 如果 checkpoint 已前進但 projection 缺資料，優先走 custom date replay：先清指定日期的 user behaviour 與 Mongo projection，再從 source 重建，不手動補單一 projection。

### Result

這樣可以把問題拆成 source、checkpoint、MySQL projection、Mongo projection、Redis last result 五個邊界。即使沒有 production incident evidence，也能展示我看 batch correctness 時會先找成功邊界，而不是只看某個 SQL 或某個 class。

## 面試官可能追問

### Q1：這條是不是金流？

不是。它不改玩家錢包，也不做扣款 / 派彩。它讀遊戲 log，產生 BI / reporting projection。

可以這樣答：

> 我會把它定位成 money-adjacent projection。它不是 source of truth，但它會影響營運查帳、RTP、玩家行為與報表，所以不能用一般 CRUD 報表的標準看。

### Q2：為什麼 checkpoint 會危險？

checkpoint 是「我已處理到哪個 source id」的承諾。若 checkpoint 推進後，MySQL / Mongo / Redis 其中一段沒有完整成功，下次 job 會跳過那批 source record。

### Q3：這條 flow idempotent 嗎？

不能簡單說 idempotent。要分三層：

- MySQL `user_game_behaviour` 是累加 upsert，重拉同一批會重複。
- Mongo `log_coin_flow` 是 delete+insert，接近 replace，但有空窗。
- Redis `LastCoinFlowResult` 是當日累積基準，遺失會影響下一輪。

所以它更像「checkpoint-idempotent」，不是 write 本身 idempotent。

### Q4：custom date replay 為什麼比較安全？

因為 custom date 會先呼叫 `clearLastData()`，刪掉該日 `user_game_behaviour` 與 Mongo `log_coin_flow`，再清 Redis task key，讓指定日期從乾淨狀態重建。這比在 production 直接重設 checkpoint 安全，因為累加式 MySQL projection 不適合直接重拉同一批。

### Q5：如果你要改設計，優先改哪裡？

我會先補觀測性與 runbook，不會第一步就大重構：

1. 補每張 source table 的 last id、processed count、lag、pull over status。
2. 補 Mongo delete / insert count 與失敗告警。
3. 明確定義 replay SOP：指定 date 先清 projection，再從 0 重建。
4. 再考慮把 Mongo delete+insert 改成 deterministic key upsert，或 staging + version switch。
5. 如果要進一步強化，再把 checkpoint 更新延後到所有 projection 寫入成功後，並記 batch id。

### Q6：這是你主導的嗎？

保守回答：

> 這條我目前只能說是 code-backed 分析過，不會說是我主導或開發。我的重點是能把 batch projection 的資料流、checkpoint、replay 與 failure window 講清楚。如果要放履歷，需要再補 Nick 本人的 MR / ticket / commit 或本人確認。

## 可以展現的 Senior 能力

- 能分清楚 source of truth 與 reporting projection。
- 能看出 checkpoint 是 batch 的交易邊界。
- 能拆 MySQL、Mongo、Redis 三種寫入語意的 idempotency 差異。
- 能設計 custom date replay / runbook，而不是手動補資料。
- 能從 production support 角度補 observability：lag、last id、processed count、delete / insert count、pull over status。
- 能保守表達 evidence，不把 code-backed 分析包裝成實際開發。

## 不能誇大的地方

- 不說 Nick 開發這條 flow。
- 不說 Nick 主導金幣流水清算。
- 不說這是正式金流或 wallet source of truth。
- 不說已驗證 production enable。
- 不寫任何量化改善。

## 對應履歷 bullet

目前不建議寫進正式履歷。

可先放在面試準備材料的保守說法：

```text
Code-backed 分析遊戲金幣流水 / 玩家行為 batch projection，梳理 Redis checkpoint、多來源 log 增量投影、MySQL 累加 upsert、Mongo delete+insert 與 custom date replay 的一致性風險。
```

這句不是正式履歷 bullet；Step 5 已判定不更新正式履歷 / 自傳。

## 下一步建議

只推薦一件事：

```text
iwin game_api agent-bonus-receive-transfer Step 5
```

原因：

- `coin-flow-batch-projection` 已完成 Step 5 claim gate，正式履歷 / 自傳不更新。
- `game_job contribution claim consolidation` 已完成，下一步回到 `game_api` 未完成的代表 flow。
