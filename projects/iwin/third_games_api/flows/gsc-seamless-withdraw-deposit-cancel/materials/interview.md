# Interview - GSC seamless withdraw / deposit / rollback / cancel

完成狀態：Step 4 已完成，正式面試 case 已建立；下一步做 Step 5 單條 flow claim gate。

## 30 秒說法

GSC split endpoint flow 分成 withdraw、deposit、rollback、cancel。Adapter 會驗 sign、查 Redis mapping / center route、用 Mongo step 1 / 2 / 3 做前置與 duplicate 判斷，再送 gameserver `GSC_BET / GSC_SETTLE / GSC_REFUND / GSC_OTHER`。真正改錢在 gameserver，所以 Mongo 只能當 audit evidence，不能當 wallet source of truth。

## 90 秒說法

這條 flow 像傳統三段式狀態機：withdraw 成功後留下 step 1，deposit 必須先看到 step 1 才能派彩，rollback / cancel 也必須先看到 step 1 才能回滾或取消。Adapter 對 deposit 檢查 `betId + action + step 2`，對 rollback / cancel 檢查 `betId + action + step 3`。

風險是 transaction boundary。adapter 是 gameserver 成功後才寫 Mongo。如果 gameserver 已經扣款或加款，但 Mongo 寫失敗或 adapter timeout，provider retry 時可能看不到 step evidence。這種 flow 的最終冪等要放在 wallet mutation boundary，而不是只靠 adapter Mongo。

## 3 分鐘說法

GSC split endpoint 可以當成一個第三方遊戲 wallet callback 狀態機來講。它不是一支 `/transfer` 解所有事情，而是四支 endpoint：withdraw 扣款、deposit 派彩、rollback 退款、cancel 走 other / cancel 語意。adapter 會做 MD5 sign、currency、member、Redis game mapping 和 center route，然後根據 endpoint 呼叫 gameserver `GSC_BET / GSC_SETTLE / GSC_REFUND / GSC_OTHER`。

我會先講狀態：withdraw 成功後是 step 1；deposit 必須先看到 step 1，再寫 step 2；rollback / cancel 也要求 step 1，然後寫 step 3。這讓流程很好理解，但也有風險：step evidence 是 Mongo 後寫，不是最終錢包真相。

最大的 failure window 是 gameserver 成功但 Mongo 沒寫成。`GSC_BET` 已扣款但 step 1 沒寫，provider retry 時可能再次扣款，或 deposit / rollback 來了卻查不到 step 1。`GSC_SETTLE`、`GSC_REFUND`、`GSC_OTHER` 也一樣。這種設計如果只靠 adapter Mongo 查重是不夠的，真正的 idempotency 要靠近 wallet mutation boundary。

Owner 改善我會先做三件事：第一，確認 provider contract 和 production routing，因為 `/transfer` 可能才是後來主線；第二，在 wallet mutation 前建立 durable idempotency key，至少包含 provider + betId + action / step；第三，補 observability，例如 gameserver success but Mongo fail、step 1 missing、duplicate hit、Redis route miss、cancel / rollback step 3 數量。這樣面試時可以講出 transaction correctness，而不是只描述 API。

## Senior 追問清單

- deposit 先到、withdraw 還沒寫 step 1，怎麼處理？
- rollback / cancel 是否能在 deposit 後發生？
- cancel 和 rollback 都寫 step 3，互斥規則是什麼？
- `sign` 如果包含 requestTime，是否適合當 transaction idempotency key？
- gameserver success 但 Mongo insert fail，provider retry 會怎麼收斂？
- split endpoint 和 `/transfer` 整合式 endpoint 哪個是 production 主線？

## Lead / Architect 追問與回答

### Q1：如果只能修一個點，你會先修哪裡？

先修 wallet mutation boundary 前的 idempotency。因為 Mongo 後寫 evidence 失敗時，provider retry 的 double deduct / double settle 風險最大。先把 provider + betId + action / step 固化成 durable guard，再用 Mongo / log 做 audit。

### Q2：Mongo unique index 能不能解決問題？

只能解決一部分。Mongo unique index 可以避免 adapter evidence 重複寫，但如果 gameserver 已經成功、Mongo 還沒寫或寫失敗，下一次 retry 還是可能再次打到 gameserver。所以 unique index 要有，但不能取代 wallet boundary idempotency。

### Q3：rollback / cancel 都是 step 3，怎麼避免語意混淆？

step 3 只能表示「withdraw 後的補償 / 結束類事件」，不能單獨表示狀態。需要 action、downstream command、provider transaction type 一起判斷，並確認 deposit 後是否允許 rollback / cancel、cancel 是否等價退款或只是 other。

### Q4：split endpoint 和 transfer endpoint 並存時，怎麼做 owner 判斷？

先確認 production route / traffic / provider contract。如果 `/transfer` 是新主線，split endpoint 應標成 legacy / compatibility，監控與文件要避免維運誤判。面試時也要保守說「我分析過 split endpoint 風險」，不說它一定是當前主線。

### Q5：觀測指標怎麼設計？

至少要看 endpoint response code、duplicate hit、gameserver success but Mongo fail、step 1 missing、Redis mapping / center route miss、`GSC_OTHER` 數量、rollback / cancel after deposit 的異常比例。這些指標能支撐客服查單、對帳與事故 RCA。

## STAR 版本

Situation：GSC split endpoint 將投注、派彩、rollback、cancel 拆成多支 callback，provider retry 或 out-of-order 會直接影響玩家餘額。

Task：要判斷 adapter Mongo step evidence、gameserver wallet mutation、provider retry 三者的責任邊界。

Action：我把四支 endpoint 對到 Mongo step 1 / 2 / 3 與 gameserver command，整理 duplicate guard、failure window、cancel / rollback 語意與 `/transfer` 主線待確認邊界。

Result：形成一個可面試的 third-party wallet callback case，可講 money correctness、idempotency、reconciliation 與 observability；但不包裝成 Nick 的正式開發成果。

## 保守邊界

- 這是 code-backed 分析素材，不是 Nick 在 `third_games_api` 的正式履歷成果。
- 若要談 Nick 實際開發 evidence，應回到 `iwin_gameserver` project claim。
- 本 Step 4 不更新正式履歷 / 自傳。

## 下一步

```text
iwin third_games_api gsc-seamless-withdraw-deposit-cancel Step 5
```
