# coupon-redeem-credit-grant interview material

更新時間：2026-05-15
對應 Step：Step 4 面試案例
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## Step 4 前檢查

| 文件 | 狀態 | 判斷 |
| --- | --- | --- |
| `flow.md` | 可沿用 | 已有白話導讀、Code 分層、架構圖、流程圖、failure window 與 owner decision |
| `materials/evidence.md` | 可沿用 | 已記錄 `game_api` / `iwin_gameserver` fetch 狀態、code path、branch diff 與 DB schema |
| `materials/decision-notes.md` | 可沿用 | 已整理 DB unique、deterministic idempotency key、state machine、Redis lock 與 reconciliation |
| `materials/claim-boundary.md` | 本輪補強 | Step 4 需補面試可說 / 不可說 |
| `career-interview.md` | 本輪重寫 | 轉成 30 秒 / 2 分鐘 / 5 分鐘 / STAR / 反問面試官 |

Step 4 不更新正式履歷 master，不新增 `真實開發過` claim。

## 面試主軸

這條 flow 最適合被包裝成：

> 跨系統 reward / coupon money side effect 的一致性案例。

不要包裝成：

> 我做了一套 coupon 系統。

核心要講的是：`game_api` 只負責 orchestration，真正玩家錢包與打碼要求在 `iwin_gameserver`；local DB record、wallet mutation、bet target side effect 不在同一個 transaction。

## 一句話版本

優惠券兌換不是單純發 bonus，而是 `game_api` 驗證 coupon 與玩家資格後，跨到 gameserver 做上分和打碼要求，最後再寫本地 record；真正的 owner 風險是 idempotency、partial success 和 reconciliation。

## 30 秒版本

我分析過 `game_api` 的 coupon redeem flow。入口是 `GET /coupons/redeem`，它會驗 token、查 coupon setting、查 coupon record 防同 uid / 同 IP 重複使用，再呼叫 `iwin_gameserver` 做 `DEPOSIT` 上分，接著用 `SET_BET_TARGET_COUPON` 加打碼要求，最後才寫本地 `coupon_record` 和更新 `used_count`。

這條 flow 最值得談的是：它跨 Redis、MySQL、GM HTTP command、玩家錢包和打碼狀態，不是一個本地 DB transaction。我的 owner 建議會是 DB unique constraint、下游 idempotency key、pending state machine 和 reconciliation。

## 2 分鐘版本

這條 flow 的業務目標是讓玩家輸入 coupon code 後領到 bonus，同時系統要加上對應 wagering requirement。正常流程是 API 先驗證登入 token，從 Redis 找 uid，再查 `coupon_setting` 和 `coupon_record`，確認 coupon 有效、玩家沒領過、同 IP 沒超過限制。

資格通過後，`game_api` 送兩個 GM command 到 `iwin_gameserver`。第一個 `DEPOSIT` 會修改玩家大廳餘額，第二個 `SET_BET_TARGET_COUPON` 會增加提款前要完成的打碼量。最後 API 才寫 `coupon_record`，並把 `coupon_setting.used_count` 加一。

這裡的風險不是查詢邏輯，而是 side effect ordering。現有順序是先改下游錢包，再改下游打碼，最後才寫本地 record。任何中間失敗都可能造成 partial success。例如玩家拿到錢但沒有打碼要求，或玩家已拿到錢但本地查不到 record，導致重試可能再次上分。

我會先補 idempotency 底線：`coupon_record(coupon_code, log_user_id)` 要有 unique constraint；deposit 的 bill no 要是 deterministic idempotency key；Redis lock 只能降低重複提交，不是最終保證。再來才是 state machine 和 reconciliation。

## 5 分鐘深講版

### 1. 先定義 source of truth

這條 flow 有多個 source / projection：

- `coupon_setting`：coupon 是否存在、有效期、金額、打碼量、適用 user type。
- `coupon_record`：本地兌換紀錄與重複兌換判斷。
- Redis `loginToken:{openId}`：登入狀態。
- `iwin_gameserver` / `PlayerData`：玩家錢包餘額與打碼要求。
- `coupon_setting.used_count`：營運統計 projection，不應比 `coupon_record` 更權威。

### 2. 再看 transaction boundary

`game_api` local DB、Redis、GM command、gameserver wallet mutation 都是不同邊界。就算在 `game_api` 加 `@Transactional`，也不能 rollback gameserver 已經完成的上分或打碼要求。

### 3. 找 failure window

重要窗口：

- `DEPOSIT` 成功，`SET_BET_TARGET_COUPON` 失敗。
- 兩個 GM command 成功，`coupon_record` insert 失敗。
- `coupon_record` insert 成功，`used_count` update 失敗。
- 同 uid 同 coupon 併發請求都在 insert 前查不到 record。
- Redis lock 過期或 finally delete 誤刪新 lock。

### 4. 最後講 owner decision

優先順序：

1. DB unique constraint：`coupon_record(coupon_code, log_user_id)`。
2. deterministic idempotency key：coupon code + uid + purpose，並確認 gameserver duplicate bill no 語意。
3. durable state machine：pending、deposited、bet target set、success、failed。
4. Redis lock 修正：短 TTL、唯一 value、compare-and-delete。
5. reconciliation：coupon record、wallet log、bet target log、used_count。

## Senior 常見追問

### Q：為什麼 `@Transactional` 不夠？

因為主要 money side effect 是外部 GM command。`@Transactional` 只能保護 `game_api` 本地 DB，不能 rollback gameserver 已完成的 wallet mutation。

### Q：如果只加 Redis lock，可以嗎？

不夠。Redis lock 是防短時間重複提交的流量 / UX mitigation，不是資料正確性的底線。lock 可能過期、可能被誤刪，也不能讓下游 deposit idempotent。

### Q：DB unique 放哪裡？

放在 `coupon_record(coupon_code, log_user_id)`。這是同 uid 同 coupon 只能成功一次的本地資料底線。duplicate insert 應回 already redeemed 或既有結果，不應再送 GM command。

### Q：下游 idempotency key 怎麼設計？

不能每次 UUID。應該讓同一 business operation 對應同一 key，例如 coupon code + uid + deposit purpose，並確認 gameserver 對同 bill no 重送是回既有結果，而不是再次加錢。

### Q：如果 deposit 成功但 bet target 失敗？

要留下 durable 狀態，例如 `DEPOSITED_BET_TARGET_FAILED`。後續可以重送 bet target 或人工處理。不要只靠 exception log，因為客服 / 對帳需要查出卡在哪一段。

### Q：`used_count` 和 `coupon_record` 不一致怎麼辦？

`used_count` 應視為 projection。對帳時以 `coupon_record` 和下游 wallet / bet target log 為主，`used_count` 可以重建或定期校正。

## Lead / Architect 延伸追問

- GM command timeout 時，如何判斷 gameserver 是否已 apply？
- HTTP OK 對 client 的語意是「request accepted」還是「bonus + bet target + record 全部完成」？
- 是否要把 GM side effect 包成 outbox event？
- 若 coupon campaign 有總量上限，如何避免 oversell？
- 同 IP 限制如果走 NAT / proxy，是否會誤傷正常玩家？
- Redis lock 和 DB unique constraint 的責任邊界怎麼分？
- 對帳報表應由誰擁有：game_api、gameserver、BI 還是營運後台？

## 面試官可能聽到的亮點

- 能分清楚 orchestration layer 與 wallet source of truth。
- 不把 Redis lock 誇大成完整分散式交易。
- 知道 local transaction 無法覆蓋外部 side effect。
- 能提出 state machine 和 reconciliation，而不是只說 try-catch。
- 能保守標示 evidence：目前是 code-backed analysis，不是本人實作 claim。

## 不該說的話

- 「我做過這個 coupon 系統。」
- 「我修了 double redeem bug。」
- 「這個 lock 已經完全防雙領。」
- 「我知道 production 一定部署 k3s branch。」
- 「我負責完整 reward / coupon owner。」

## Step 4 結論

本 flow 已可作為 Senior / Owner 面試案例。它的面試價值在於：

- money correctness。
- transaction boundary。
- idempotency。
- partial success。
- reconciliation。

但目前仍不更新正式履歷 / 自傳。下一步應做 Step 5：只判斷是否能形成安全履歷 bullet，預期仍會因缺 Nick 本人 evidence 而保守不放正式履歷。
