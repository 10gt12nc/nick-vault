# coupon-redeem-credit-grant decision notes

更新時間：2026-05-15
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## Decision 1：Idempotency 底線放在 DB，不只放在 Redis lock

現況：

- `main` 先查 `coupon_record(coupon_code, log_user_id)`，查不到才往下做。
- dump schema 顯示 `coupon_record(coupon_code, log_user_id)` 是普通 index。
- `origin/k3s` 增加 Redis lock，但仍是 application-level guard。

建議：

- 新增 `UNIQUE KEY uk_coupon_record_coupon_user (coupon_code, log_user_id)`。
- insert 遇 duplicate 時回「已兌換」。
- Redis lock 可以保留作為減少短時間重複提交的 UX / load protection，但不是資料正確性的唯一底線。

理由：

- check-then-insert 在併發下不可靠。
- Redis lock 有 TTL、刪除與網路問題。
- DB unique constraint 是最接近 coupon record source of truth 的硬防線。

## Decision 2：billNos 應該可重試，而不是每次 UUID

現況：

- `CouponRedeemServiceImpl` 對 `DEPOSIT` 參數使用 `UUIDUtils.getUUID()`。
- 如果 GM command 成功但本地寫 record 失敗，重試會產生新的 bill no。

建議：

- 用 deterministic idempotency key，例如 `coupon:{couponCode}:uid:{uid}:deposit`。
- 確認下游 `DEPOSIT` 是否以 bill no 去重。
- 若下游目前不去重，需在下游帳本或 log 層補 unique constraint / duplicate handling。

理由：

- money side effect 的 retry 必須可安全重送。
- 每次 UUID 只能追蹤單次請求，不能表示同一 business operation。

## Decision 3：用狀態機取代「下游成功後才寫 record」

現況：

1. 呼叫 `DEPOSIT`。
2. 呼叫 `SET_BET_TARGET_COUPON`。
3. insert `coupon_record`。
4. update `used_count`。

建議：

- 先 insert `coupon_record`，狀態為 `PENDING`。
- `DEPOSIT` 成功後更新為 `DEPOSITED`。
- `SET_BET_TARGET_COUPON` 成功後更新為 `BET_TARGET_SET` 或 `SUCCESS`。
- 失敗則保留 `FAILED_DEPOSIT`、`FAILED_BET_TARGET`、`FAILED_RECORD_UPDATE` 等狀態。

理由：

- 下游 side effect 一旦成功，本地需要 durable anchor 可查。
- partial success 不是 exception log 能解決的，需要可對帳的狀態。

## Decision 4：Redis lock 若保留，需 compare-and-delete

現況：

- `origin/k3s` 用 `setIfAbsent(lockKey, "1", 10_000L)`。
- finally 直接 `redisService.delete(lockKey)`。
- `RedisServiceImpl#setIfAbsent` 使用 Redis `SET ... NX EX expire`。

建議：

- lock value 改為 request UUID。
- delete 時用 Lua compare value 再 delete。
- TTL 明確以秒或毫秒命名，例如 `lockTtlSeconds`。
- TTL 不宜過長；若 `10_000L` 目標是 10 秒，需改成符合 `EX` 的秒數。

理由：

- 直接 delete 可能刪掉另一個 request 後來取得的新 lock。
- TTL 單位不明會造成誤阻擋或保護不足。

## Decision 5：`used_count` 不應單獨作為準確統計

現況：

- SQL 是 `used_count = IFNULL(used_count, 0) + 1`。
- 沒有看到 max count 或條件式 update。
- 若 record 成功但 count 失敗，統計會少。

建議：

- 若 `used_count` 只是顯示值，可由 `coupon_record` 聚合或定期 reconcile。
- 若未來有總量上限，需條件式 update，例如 `used_count < max_count`，且要與 record insert 放在可控制的 transaction / state flow。

理由：

- counter 是 projection，不應比 record 更權威。
- money / reward flow 需要能從 event / record 重建狀態。

## Decision 6：補償與對帳要跨四個來源

建議 reconciliation 視角：

| 對象 | 查核目的 |
| --- | --- |
| `coupon_record` | 誰應該拿到 coupon |
| wallet / bill log with reason `124` | 誰真的拿到錢 |
| bet target log / `SpinBetTargetConst.COUPON` | 誰真的被加打碼要求 |
| `coupon_setting.used_count` | 營運統計是否漂移 |

判斷原則：

- 有 wallet log 無 coupon record：高風險，可能是 record failure 或手動補單，需人工確認。
- 有 coupon record 無 wallet log：可能下游失敗，需補發或標失敗。
- 有 wallet log 無 bet target：玩家拿 bonus 但沒有 wagering requirement，需優先修復。
- used_count 與 record count 不一致：統計漂移，可用 record 重建。
