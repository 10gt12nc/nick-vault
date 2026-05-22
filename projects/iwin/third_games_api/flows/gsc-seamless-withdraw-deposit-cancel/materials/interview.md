# Interview - GSC seamless withdraw / deposit / rollback / cancel

完成狀態：Step 3 已完成，下一步做 Step 4 正式面試 case。

## 30 秒草稿

GSC split endpoint flow 分成 withdraw、deposit、rollback、cancel。Adapter 會驗 sign、查 Redis mapping / center route、用 Mongo step 1 / 2 / 3 做前置與 duplicate 判斷，再送 gameserver `GSC_BET / GSC_SETTLE / GSC_REFUND / GSC_OTHER`。真正改錢在 gameserver，所以 Mongo 只能當 audit evidence，不能當 wallet source of truth。

## 90 秒草稿

這條 flow 像傳統三段式狀態機：withdraw 成功後留下 step 1，deposit 必須先看到 step 1 才能派彩，rollback / cancel 也必須先看到 step 1 才能回滾或取消。Adapter 對 deposit 檢查 `betId + action + step 2`，對 rollback / cancel 檢查 `betId + action + step 3`。

風險是 transaction boundary。adapter 是 gameserver 成功後才寫 Mongo。如果 gameserver 已經扣款或加款，但 Mongo 寫失敗或 adapter timeout，provider retry 時可能看不到 step evidence。這種 flow 的最終冪等要放在 wallet mutation boundary，而不是只靠 adapter Mongo。

## Senior 追問清單

- deposit 先到、withdraw 還沒寫 step 1，怎麼處理？
- rollback / cancel 是否能在 deposit 後發生？
- cancel 和 rollback 都寫 step 3，互斥規則是什麼？
- `sign` 如果包含 requestTime，是否適合當 transaction idempotency key？
- gameserver success 但 Mongo insert fail，provider retry 會怎麼收斂？
- split endpoint 和 `/transfer` 整合式 endpoint 哪個是 production 主線？

## 保守邊界

- 這是 code-backed 分析素材，不是 Nick 在 `third_games_api` 的正式履歷成果。
- 若要談 Nick 實際開發 evidence，應回到 `iwin_gameserver` project claim。
- 本 Step 3 不更新正式履歷 / 自傳。

## 下一步

```text
iwin third_games_api gsc-seamless-withdraw-deposit-cancel Step 4
```
