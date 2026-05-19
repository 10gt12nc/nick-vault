# Interview Notes: coin-flow-batch-projection

## 面試主軸

這條 flow 適合拿來講：「報表 projection 不是交易 source of truth，但因為它接近 money / game result，仍然要用 money correctness 的標準看 checkpoint、重跑與一致性。」

## 常見追問

### Q1：這條是不是金流？

不是。它不改玩家錢包，不做扣款 / 派彩。它讀遊戲 log，產生 BI / reporting projection。

保守回答：

> 我會把它定位成 money-adjacent projection。它不是 source of truth，但它會影響營運查帳、RTP、玩家行為與報表，所以不能用一般 CRUD 報表的標準看。

### Q2：為什麼 checkpoint 會危險？

因為 checkpoint 是「我已處理到哪個 source id」的承諾。若 checkpoint 推進後，下游 MySQL / Mongo / Redis 寫入沒有完整成功，下次 job 會跳過那批 source record。

### Q3：這條 flow 的 idempotency 怎麼看？

要分三層：

- MySQL `user_game_behaviour` 是累加 upsert，重拉會重複。
- Mongo `log_coin_flow` 是 delete+insert，比較像 replace，但有空窗。
- Redis `LastCoinFlowResult` 是當日累積基準，遺失會影響下一輪。

所以不能簡單說它 idempotent；它是靠 checkpoint 避免重複，而不是每個 write 自身都可重入。

### Q4：如果 production 報表少了一段資料，你怎麼查？

1. 先查 task log，確認 `BiTaskStatus:CoinFlow` 是否成功、是否有 exception。
2. 查 Redis task key 的 `LastPullMaxId` 與 `PullOverStatus`，確認卡在哪個 channel / source table。
3. 對比 source `log_reel` / `log_jackpot` / `log_taxt` 是否有 id 大於 checkpoint 的資料。
4. 查 `user_game_behaviour_{yyyy_m}` 是否已累加該日期。
5. 查 Mongo `log_coin_flow_{gameId}` 是否有 channel / cps / date projection。
6. 若 checkpoint 已前進但 projection 缺資料，優先走 custom date replay，不手動改單表。

### Q5：如果你要改善，會改哪裡？

保守改善順序：

1. 補 observable：每張 source table 的 last id、processed count、Mongo delete / insert count、lag、pull over status。
2. 補 replay runbook：指定 date 先清 projection，再從 0 重建。
3. 將 Mongo delete+insert 改成 deterministic key upsert 或 staging + version switch。
4. 將 checkpoint 更新延後到所有 projection 寫入成功之後，並記錄 batch id。

## 可用一句話

這條 flow 的重點不是「會寫 batch」，而是「batch 的成功邊界到底在哪裡」。
