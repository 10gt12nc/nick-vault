# coupon-redeem-credit-grant：保守面試素材

更新時間：2026-05-15
對應 Step：Step 4 面試案例
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 面試定位

這份不是正式履歷，也不是 Nick 已實作 claim。它是 code-backed 的 Senior / Owner 面試分析素材：用一條真實存在的優惠券兌換上分 flow，練習怎麼談跨系統 money side effect、idempotency、transaction boundary、partial success、reconciliation 與不可誇大的邊界。

可用語氣：

- 「我分析過一條優惠券兌換上分 flow。」
- 「我會把風險拆成 coupon record、玩家錢包、打碼要求與對帳。」
- 「如果我是 owner，我會優先補 DB unique、下游 idempotency key、狀態機與 reconciliation。」

避免語氣：

- 「我主導設計 coupon 兌換系統。」
- 「我修掉 production 雙領問題。」
- 「我設計了分散式鎖。」
- 「我讓錯帳率下降多少。」

## 30 秒版本

我分析過 `game_api` 的優惠券兌換上分 flow。它不是單純查 coupon 再寫 record，而是 API 先驗 token、coupon 資格、同 uid / 同 IP 限制，接著送 GM command 到 `iwin_gameserver` 幫玩家上分，再送第二個 GM command 設定打碼要求，最後才寫 `coupon_record` 和更新 `used_count`。

我會把面試重點放在三個風險：第一，`main` 上是 check-then-insert，DB schema 也不是 unique key，併發下可能雙領；第二，deposit 成功但 bet target 或本地 record 失敗，會造成 partial success；第三，`coupon_record`、wallet log、bet target log、`used_count` 之間需要 reconciliation。

## 2 分鐘版本

這條 flow 的入口是 `GET /coupons/redeem`。Controller 只負責拿 coupon code 和 IP，主要邏輯在 service。Service 會從 Authorization token 解出 openId，再到 Redis 的 `loginToken:{openId}` 驗證 token 是否仍有效，取得 uid。

接著它查 `coupon_setting`，確認 coupon 啟用且未過期；查 `coupon_record`，確認同 uid 沒用過、同 IP 沒超過 3 個帳號；再查 `log_user`，取得玩家的 accountId、centerId、充值次數、最後充值時間與 channel，依 user type 判斷是否符合資格。

資格通過後，真正的 money side effect 不是在 `game_api` 本地完成，而是透過 `GmIntfcComponent` 送到 `iwin_gameserver`。第一個 command 是 `DEPOSIT`，會走 `HttpNewBill` / `NewBillJob` 修改玩家大廳餘額；第二個 command 是 `SET_BET_TARGET_COUPON`，會呼叫 `PlayerData.addWithDrawSpinNeeds` 增加提款前的打碼要求。兩個 command 成功後，`game_api` 才 insert `coupon_record`，再把 `coupon_setting.used_count` 加一。

這裡最值得談的是 transaction boundary：Redis、MySQL、GM HTTP command、玩家錢包和打碼狀態都不在同一個 transaction。`@Transactional` 只能保護本地 DB，不能 rollback 已經送到 gameserver 的上分。因此我會先用 DB unique constraint 和 deterministic idempotency key 固住「同 uid 同 coupon 只成功一次」，再用 pending / deposited / bet target set / success / failed 這類狀態機，讓 partial success 可查、可補償。

## 5 分鐘版本

我會先把系統切成四段：玩家 API、coupon local state、gameserver wallet、wagering / bet target。

玩家 API 的責任是驗證請求與資格。它從 request 拿 coupon code 和 IP，從 Authorization token 透過 Redis login token 找 uid。這一段失敗通常沒有 money side effect，風險相對低。

Coupon local state 的責任是判斷 coupon 是否有效、玩家是否已領過、同 IP 是否超過限制。`coupon_setting` 是設定來源，`coupon_record` 是本地兌換紀錄。但目前 Step 3 evidence 看到 `coupon_record(coupon_code, log_user_id)` 在 dump schema 裡是普通 index，不是 unique key，所以 `main` 上的同 uid 同 coupon 限制仍是先查後寫。這在併發下不能當硬保證。

Gameserver wallet 的責任是實際上分。`game_api` 組 `DEPOSIT` command，帶 `accountId`、`userLayer`、`value`、`type=0`、`billNos`、`reason=124`、`centerId`，再透過 `GmIntfcComponent` HTTP POST 到 center。下游 `iwin_gameserver` 會進 `HttpService#onDeposit`，建立 `HttpNewBill` / `NewBillJob` 修改玩家大廳餘額。

Wagering / bet target 的責任是提款限制。第二個 command `SET_BET_TARGET_COUPON` 會在 gameserver 找玩家並呼叫 `PlayerData.addWithDrawSpinNeeds`，把 coupon 對應的打碼要求寫進玩家狀態。這代表 bonus 和 wagering requirement 是兩個外部 side effect，中間可能只成功一個。

所以 owner risk 是 response 與 side effect ordering。現有順序是先 deposit、再 set bet target、最後才寫本地 coupon record。這不一定代表錯，但 contract 要講清楚：如果 deposit 成功、bet target 失敗，玩家已拿到 bonus 但沒有打碼要求；如果兩個 GM command 成功、本地 record insert 失敗，玩家可能重試並再次上分；如果 record 成功但 used_count 失敗，營運統計會漂移。

如果我要改善，我不會只加 Redis lock。`origin/k3s` 已有 `coupon:lock:{uid}:{couponCode}` 的方向，但 Redis lock 只能降低短時間重複提交，不能替代資料正確性底線。第一步我會在 DB 層加 `coupon_record(coupon_code, log_user_id)` unique constraint。第二步我會讓 `billNos` 變 deterministic idempotency key，並確認下游是否以 bill no 去重。第三步把兌換改成 durable state machine，先寫 pending，再依序轉 deposited、bet target set、success 或 failed。第四步建立 reconciliation，比對 coupon record、reason 124 的 wallet log、coupon bet target log 與 used_count。

## STAR 版本

Situation：

- 優惠券兌換會同時影響玩家餘額、提款打碼要求、本地兌換紀錄與營運統計。任何一段不一致，都可能造成雙領、發錢未記錄、bonus 沒有 wagering requirement 或客服查帳困難。

Task：

- 不是只看 API 是否回 success，而是追整條 flow 的 source of truth、transaction boundary、idempotency guard、partial success window 與對帳方式。

Action：

- 先從 `CouponRedeemController#redeem` 定位入口。
- 追 `CouponRedeemServiceImpl#redeemCoupon` 的 token、coupon setting、coupon record、log user 資格檢查。
- 對照 `CouponRecordDao.xml`、`CouponSettingDao.xml` 與 DB dump，確認 `coupon_record(coupon_code, log_user_id)` 不是 unique key。
- 追 `GmIntfcComponent` 送出的 `DEPOSIT` 與 `SET_BET_TARGET_COUPON`。
- 到 `iwin_gameserver` 追 `HttpService#onDeposit`、`HttpNewBill`、`NewBillJob`、`setPlayerBetTargetCoupon` 與 `PlayerData.addWithDrawSpinNeeds`。
- 比較 `origin/main` 與 `origin/k3s` 的 Redis lock diff，判斷它是 mitigation，不是完整 idempotency guarantee。

Result：

- 形成一個可面試討論的 owner analysis case：同 uid 同 coupon 的 DB unique、下游 idempotency key、pending state machine、partial success 補償與 reconciliation 是核心風險。
- 目前仍只屬於 `專案存在 / code-backed` 與 `分析素材 / learning-only`，不能寫成 Nick 主導成果。

## 面試回答草稿

如果被問「你會怎麼看一個 coupon 兌換上分 flow？」可以回答：

> 我會先找 source of truth。Coupon 是否有效在 `coupon_setting`，是否領過在 `coupon_record`，但真正的玩家錢包和打碼要求在 gameserver。這代表它不是一個本地 transaction 能解決的 CRUD，而是 API orchestration 加外部 money side effect。

如果被問「這裡最容易出什麼問題？」可以回答：

> 最大風險是 partial success 和重複提交。比如 deposit 成功、bet target 失敗，玩家拿到 bonus 但沒有 wagering requirement；或者兩個 GM command 都成功，但本地 coupon record insert 失敗，玩家重試時 API 可能還以為沒領過。再加上 main branch 的同 uid 檢查是先查後寫，DB dump 也不是 unique key，所以併發下可能雙領。

如果被問「Redis lock 不是已經解了嗎？」可以回答：

> Redis lock 是 mitigation，不是資料正確性的最後防線。它可以降低短時間重複提交，但 lock 可能過期、可能被錯誤刪除，也不能保證下游 GM command idempotent。對 money flow，我還是會把底線放在 DB unique constraint 和下游 idempotency key。

如果被問「你會怎麼改？」可以回答：

> 第一，`coupon_record(coupon_code, log_user_id)` 要加 unique constraint。第二，deposit 的 bill no 要改成由 uid + coupon code 派生的 deterministic idempotency key，並確認 gameserver 是否去重。第三，先寫 pending record，再執行 deposit 和 bet target，狀態轉成 deposited、bet target set、success 或 failed。第四，建立 reconciliation，比對 coupon record、wallet log、bet target log 和 used_count。

## 反問面試官

可以用這些問題展現 owner 視角：

- 你們 coupon / reward flow 的「成功」定義是 wallet applied，還是所有本地 record / wagering requirement 都完成？
- 下游 deposit 是否支援 idempotency key？如果同 bill no 重送會回原結果還是再加一次？
- coupon record 是否有 DB unique constraint？如果 duplicate insert 發生，要回 success、already redeemed 還是 fail？
- Redis lock 的 TTL、value 與 delete 是否有 compare-and-delete？
- 你們有沒有 reconciliation，可以比對 reward record、wallet log、wagering log 與營運統計？

## 目前不能講

- 不能說我主導 coupon 兌換系統。
- 不能說我修過 production 雙領 bug。
- 不能說我設計或部署了 Redis lock。
- 不能說已確認 production 使用 `origin/k3s`。
- 不能說有改善百分比或 owner 權限。

## Step 5 待補

- 判斷是否能形成履歷 bullet。
- 若沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認，正式履歷應暫不放。
- 若 Nick 補到本人 evidence，再重新評估是否能標成 `真實開發過`，並保守描述實際角色。
