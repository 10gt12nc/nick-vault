# coupon-redeem-credit-grant interview material

更新時間：2026-05-15
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 面試開場

我會把這條優惠券兌換 flow 當成 money-related orchestration 來看。它不是只查 coupon 再寫 record，而是跨了 token、Redis、MySQL、GM command、玩家錢包與打碼要求。

最重要的問題是：玩家拿到錢、加上打碼、本地記錄成功，這三件事不是同一個 transaction。只要其中一段失敗，就要能知道目前停在哪裡，並且重試不能造成雙上分。

## 架構講法

入口是 `GET /coupons/redeem`。API 先驗 Authorization token，從 Redis 的 login token 找 uid。再查 coupon setting，確認 coupon 啟用且未過期。接著查 coupon record，確認同 uid 沒用過、同 IP 沒超過限制。玩家資格從 `log_user` 讀，例如充值次數與最後充值時間。

資格通過後，API 送兩個 GM command。第一個 `DEPOSIT` 到遊戲中心加錢，第二個 `SET_BET_TARGET_COUPON` 增加提款前的打碼要求。最後才寫 `coupon_record` 和更新 `used_count`。

## Failure Window 講法

最大的 failure window 是：

- deposit 成功，但 bet target 失敗：玩家拿到 bonus，但 wagering requirement 沒加上。
- deposit 和 bet target 都成功，但 coupon record insert 失敗：玩家已拿到錢，但本地看起來還沒兌換，可能重試。
- coupon record 成功，但 used_count 更新失敗：實際兌換和統計不一致。
- main branch 上同 uid 同 coupon 是先查後寫，DB schema 也不是 unique key，所以併發下可能雙領。

## 改善方案講法

我會分三層處理。

第一層是 idempotency。`coupon_record(coupon_code, log_user_id)` 要有 unique constraint，避免同 uid 同 coupon 真的寫出兩筆。下游 bill no 也要 deterministic，讓同一 business operation 重試時不會變成新的加錢請求。

第二層是狀態機。不要等兩個 GM command 都成功後才寫 record，而是先寫 pending，再依序轉 deposited、bet target set、success。這樣每個 partial success 都有 durable 狀態可以追。

第三層是 reconciliation。固定比對 coupon record、wallet log、bet target log、used_count。尤其是 wallet 有 reason 124 但沒有 coupon record、或有 coupon record 但沒有 wallet log 的情況，要能列出來處理。

## 常見追問

### Q：為什麼 Redis lock 不夠？

Redis lock 可以減少短時間重複提交，但它不是資料正確性的最後防線。lock 可能過期、可能誤刪，也不能保證下游不重複上分。真正要靠 DB unique constraint 和下游 idempotency。

### Q：為什麼不直接把整段包 transaction？

因為 GM command 是外部系統 side effect，本地 transaction rollback 不會把玩家錢包 rollback。這種 flow 要靠狀態機、idempotency 和補償，而不是只靠 DB transaction。

### Q：如果只能先修一件事，你會修哪個？

先加 `coupon_record(coupon_code, log_user_id)` unique constraint，並讓 duplicate insert 回「已兌換」。這是同 uid 同 coupon 不能雙領的最低保護。接著才處理 deterministic bill no 和狀態機。

### Q：如何驗證修正有效？

我會測：

- 同 uid 同 coupon 併發送多次，只能成功一次。
- deposit 成功後故意讓 record insert 失敗，系統能留下可對帳狀態。
- deposit 成功、bet target 失敗，補償流程能辨識並重送或人工處理。
- used_count 可由 coupon_record 重建或 reconcile。

## 不該說的話

- 「我主導開發這套 coupon 系統。」
- 「我解決了 production 雙領事故。」
- 「我設計完整分散式交易。」
- 「這條 flow 已經沒有風險。」

目前只能說「我分析過 code-backed flow，能清楚拆出一致性風險與改善方案」。
