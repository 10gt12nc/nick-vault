# Interview Notes: coin-flow-batch-projection

## 面試主軸

這條 flow 適合拿來講：「報表 projection 不是交易 source of truth，但因為它接近 money / game result，仍然要用 money correctness 的標準看 checkpoint、重跑與一致性。」

證據層級：專案存在 / code-backed。不能說是 Nick 真實開發或主導。

## 口語開場

> 我分析過一條遊戲金幣流水 / 玩家行為的 batch projection。它每 5 分鐘從 `log_reel`、`log_jackpot`、`log_taxt` 三張每日分表增量拉資料，透過 Redis last id checkpoint 控制進度，再寫 MySQL user behaviour 與 Mongo coin flow。這條不是 wallet source of truth，但因為報表會影響查帳和營運判斷，所以我會用 money correctness 的方式看它。

## 深入講法

```text
這條 flow 的成功邊界其實不在某一個 SQL。

source 是三張每日分表，各自有 checkpoint。
下游有兩種 projection：
1. MySQL user_game_behaviour 是累加 upsert。
2. Mongo log_coin_flow 是先 delete 再 insert。

Redis 還保存 LastCoinFlowResult，作為同一天的累計基準。

所以如果 checkpoint 推進了，但 MySQL / Mongo / Redis 有任一段失敗，就可能造成漏算、重複累加或報表短暫缺資料。

我會用 source -> checkpoint -> MySQL projection -> Mongo projection -> Redis last result 的順序排查，而不是只看 job 有沒有成功。
```

## STAR 素材

### Situation

某日某 channel / game 報表數字異常，可能是投注額偏低、玩家數不對、或 Mongo coin flow 缺資料。

### Task

判斷問題落在 source log、checkpoint、projection 寫入、custom date replay 還是跨日 finish。

### Action

1. 查 task log：`BiTaskStatus:CoinFlow` 是 success、running 還是 exception。
2. 查 Redis `LastPullMaxId`：看 `log_reel`、`log_jackpot`、`log_taxt` 哪張表卡住。
3. 查 Redis `PullOverStatus`：看是否某 channel / table 沒完成導致 date 不 finish。
4. 對 source 分表查 `id > checkpoint` 是否還有資料。
5. 查 `user_game_behaviour_{yyyy_m}` 是否已累加該 date。
6. 查 Mongo `log_coin_flow_{gameId}` 是否有 channel / cps / date projection。
7. 若需要重跑，走 custom date replay，不直接重設 checkpoint。

### Result

可以定位成 source 延遲、checkpoint 卡住、MySQL 重複累加、Mongo delete+insert 空窗或 Redis last result 遺失，避免用手動改報表掩蓋真正資料邊界問題。

## 面試官追問與回答

### Q1：這條是不是金流？

不是。它不改玩家錢包，不做扣款 / 派彩。它是 money-adjacent reporting projection。

### Q2：如果 checkpoint 遺失會怎樣？

可能從 0 重拉。Mongo coin flow 可能被重新覆蓋，但 MySQL `user_game_behaviour` 是累加 upsert，會有重複累加風險。所以不能直接把 Redis 清掉當成重跑，應走 custom date replay。

### Q3：為什麼 Mongo delete+insert 有風險？

因為 remove 和 insert 不是同一個 transaction。delete 成功、insert 前掛掉，該 channel / cps / date 的 projection 會缺資料。更穩的做法是 deterministic key upsert，或 staging + version switch。

### Q4：跨日為什麼要多等 11 分鐘？

這是保護 source writer 在零點附近延遲補昨天資料。它是經驗型窗口；如果實際 writer delay 超過窗口，仍可能漏資料，所以應補 lag metrics 與 late data replay SOP。

### Q5：如果你 owner 這條 flow，第一步做什麼？

先補觀測性與 runbook：

- 每張 source table 的 checkpoint、processed count、lag。
- 每次 Mongo delete / insert count。
- 每個 channel / table 的 pull over status。
- custom date replay SOP。

先讓事故能定位、能恢復，再談重構 checkpoint / upsert。

### Q6：這可以寫履歷嗎？

目前不建議。它是 code-backed 分析素材，缺 Nick direct evidence。可以面試講分析能力，但不能寫成真實開發成果。

## Lead / Architect 追問

### 如果要重構成更穩的 batch，你怎麼設計？

我會用 staged projection + checkpoint commit 的方向：

1. 每批 source record 有 batch id。
2. 先寫 staging projection。
3. 驗證 MySQL / Mongo 寫入 count 與 source count。
4. 成功後再更新 checkpoint。
5. 最後切換 active version 或 upsert deterministic key。

但我不會一開始就大改，因為這條可能是既有 BI job；第一步會補觀測性與 replay runbook，降低事故恢復成本。

### 如果 business 要近即時報表，又要完全正確？

要先談 trade-off：

- 近即時：保留增量 checkpoint，但要接受 replay / late data correction。
- 完全正確：每日結束後可做 full reconciliation / 重算。
- 折衷：白天增量、日終對帳重建或校正。

## 可用句子

- 這條 flow 的重點不是會寫 batch，而是 batch 的成功邊界到底在哪裡。
- 我會先分清 source of truth 與 projection，避免把報表錯誤誤判成錢包錯誤。
- MySQL 累加 upsert 與 Mongo delete+insert 的 idempotency 不一樣，重跑策略不能混在一起看。
- 沒有 direct contribution evidence 前，我只把這條當 code-backed 面試分析，不寫成履歷成果。
