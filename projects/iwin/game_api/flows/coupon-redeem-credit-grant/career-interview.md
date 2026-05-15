# coupon-redeem-credit-grant career interview

更新時間：2026-05-15
證據層級：專案存在 / code-backed；Nick 貢獻待確認
用途：Step 3 後的保守面試素材，不是正式履歷版本

## 保守定位

這個案例目前只能說成：

> 我分析過一條優惠券兌換上分 flow，裡面跨了 API、Redis token、MySQL coupon 設定與兌換紀錄、GM command、遊戲中心玩家餘額與打碼要求。重點不是單一 CRUD，而是錢包 side effect 和本地紀錄之間的一致性、idempotency 與補償設計。

不能說成：

- 我主導設計 coupon 兌換系統。
- 我修掉雙領問題。
- 我完成分散式鎖改造。
- 我讓錯帳率下降多少。

## 30 秒版本

這條 flow 是玩家用 coupon code 兌換 bonus。API 會先驗 token、coupon 是否有效、同 uid / 同 IP 是否已達限制，再呼叫遊戲中心做上分，接著設定提款前打碼要求，最後才寫入 `coupon_record` 和更新 `used_count`。

我會把風險切成三塊：第一是同一玩家併發重送造成 check-then-insert race；第二是上分成功但打碼或本地 record 失敗的 partial success；第三是本地 `used_count` 和真實兌換紀錄、下游錢包 log 可能漂移。解法上，我會優先用 DB unique constraint 和 deterministic idempotency key 固住「同 uid 同 coupon 只成功一次」，再用 state machine / reconciliation 處理跨系統 side effect。

## 3 分鐘版本

這條 flow 的入口是 `GET /coupons/redeem`。Controller 只負責拿 coupon code 和 IP，主要邏輯在 service。Service 先從 Authorization token 解出 openId，再到 Redis 用 `loginToken:{openId}` 驗證 token 是否仍有效，取得 uid。

接著它會查 `coupon_setting` 確認 coupon 啟用且未過期，查 `coupon_record` 確認同 uid 沒用過，也查同 IP 的使用筆數是否小於 3。玩家資格則從 `log_user` 取得，例如充值次數、最後充值時間、accountId、centerId。

真正的 money side effect 不是在 `game_api` 本地完成，而是透過 `GmIntfcComponent` 送兩個 GM command。第一個是 `DEPOSIT`，把 coupon 金額加到玩家大廳餘額；第二個是 `SET_BET_TARGET_COUPON`，在遊戲中心加上 coupon 對應的打碼要求。下游 `iwin_gameserver` 裡可以看到 `DEPOSIT` 走 `HttpNewBill` / `NewBillJob` 修改玩家金額，`SET_BET_TARGET_COUPON` 則呼叫 `PlayerData.addWithDrawSpinNeeds`。

這裡的核心風險是 transaction boundary。Redis、MySQL、GM HTTP command、玩家錢包和打碼資料不在同一個 transaction 裡，而且順序是先做下游上分和打碼，最後才寫本地 `coupon_record`。所以如果下游上分成功但本地 record insert 失敗，玩家可能已拿到錢，但 API 這邊查不到兌換紀錄，重試時可能再次上分。

我會優先做幾件事：第一，在 `coupon_record(coupon_code, log_user_id)` 建 unique constraint，讓同 uid 同 coupon 的唯一性由 DB 保證；第二，讓下游 bill no 變成可重試的 deterministic idempotency key，而不是每次 UUID；第三，把本地 record 改成 pending / deposited / bet_target_set / success / failed 這類狀態機；第四，建立 reconciliation，固定比對 coupon record、wallet log、bet target log 和 used_count。

## 可回答的 Senior 問題

### 為什麼不能只加 `@Transactional`？

因為 `@Transactional` 只能涵蓋 `game_api` 本地 DB。這條 flow 的主要 side effect 是下游 GM command 修改玩家錢包與打碼要求，這些不能被本地 DB transaction rollback。

### Redis lock 能不能解決雙領？

可以降低短時間重複提交，但不能當唯一保證。lock 可能過期、可能被錯誤刪除，也不能保證下游重試 idempotent。真正的底線應該是 DB unique constraint 加下游 idempotency key。

### 為什麼 `coupon_record` 要先寫 pending？

因為一旦下游 side effect 成功，本地就需要有 durable record 可以追蹤狀態。先寫 pending 可以讓 retry / 補償有根據，也能避免「玩家拿到錢，但本地完全沒有紀錄」。

### 如果 deposit 成功但 bet target 失敗怎麼辦？

不要吞掉。應該保留狀態，例如 `DEPOSITED_BET_TARGET_FAILED`，讓補償流程能重送 bet target 或人工處理。這比直接回 generic fail 更可追。

## 可放入口語素材

- 「這不是單純發 coupon，而是跨系統 money side effect 的一致性問題。」
- 「我會先找 source of truth：coupon 設定、兌換紀錄、玩家錢包、打碼要求各在哪裡。」
- 「我不會只靠 application lock，因為鎖是短期保護，資料正確性底線要放在 unique constraint 和 idempotency key。」
- 「外部 side effect 成功後，本地狀態一定要能追，不然補償和客服查帳會失去錨點。」

## 履歷邊界

目前不更新正式履歷。若未來 Nick 補到本人 evidence，可再進 Step 5，評估是否能保守改寫成「參與 coupon 兌換一致性 / 防重複提交風險分析與改善」。
