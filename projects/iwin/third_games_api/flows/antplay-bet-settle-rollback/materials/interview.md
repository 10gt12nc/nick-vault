# Interview Notes - Antplay bet / settle / rollback

## 30 秒草稿

這條 flow 是 Antplay 舊版三段式錢包 callback：bet 扣款、settle 派彩、rollback 退款。adapter 會做 MD5 驗簽、Redis 遊戲與 center routing，然後呼叫 gameserver 的 `ANTPLAY_BET / SETTLE / REFUND`，成功後寫 Mongo log / transaction。風險是 gameserver 已改錢但 Mongo step 沒寫成功時，provider retry 可能造成重複扣款、派彩或退款，所以真正的 idempotency 應該放在 wallet mutation boundary 或 durable transaction state。

## 90 秒草稿

Antplay 舊 flow 不是整合式投派，而是三個 endpoint。`bet` 先驗簽、查餘額、找 gameId 與 center_http，送 gameserver 扣款，成功後寫 Mongo step 1。`settle` 和 `rollback` 會先查 step 1 是否存在，再從 Mongo 讀原始 bet / betTime，分別送 gameserver 派彩或退款，成功後寫 step 2 / step 3。

這裡的 owner 風險是狀態紀錄在 adapter 層，而且寫在 gameserver 成功之後。如果 gameserver 成功但 Mongo 寫失敗，下一次 provider retry 時 adapter 會看不到成功 evidence，可能再次送 gameserver。更穩的設計是用 provider + betId + step 作冪等 key，把防重放在錢包改動邊界，或先落 pending transaction，再用狀態機推進。

## 3 分鐘待 Step 4 補

Step 4 再補完整 STAR、追問與反問。

## Senior 追問清單

- 如果 `bet` 成功但 step 1 沒寫，`settle` 來了怎麼辦？
- retry 的 key 是 `sign`、`betId`、還是 provider transaction id？
- gameserver success 但 adapter timeout，provider retry 怎麼保證 deterministic response？
- `settle` / `rollback` 是否互斥？如果 settle 後 rollback 又來怎麼處理？
- Mongo log 和 transaction evidence 的責任要怎麼切？
- 新 `/bet_settle-new` 可能改善了什麼？又帶來什麼新風險？

## 保守邊界

- 這是 code-backed 分析素材，不是 Nick 在 `third_games_api` 的正式履歷成果。
- 若要談 Nick 實際開發 evidence，應回到 `iwin_gameserver` project claim。
