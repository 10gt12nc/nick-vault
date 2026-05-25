# Interview Notes - activity-accumulated-bet-voucher

日期: 2026-05-25

## Step 3 狀態

本檔先放 Step 3 的面試素材草稿。正式 30 秒 / 90 秒 / 3 分鐘講法與追問答案要在 Step 4 整理。

## 面試主軸

這條 flow 的主軸是 reward correctness:

```text
settled_bets -> activity eligibility -> Redis accumulated bet -> BetVoucher issue -> duplicate reward guard
```

不要講成 Nick 主導活動系統。安全說法是「我用這條 code-backed flow 分析 reward projection 的一致性風險」。

## 可講問題

### Q1: 這條 flow 為什麼需要 Redis？

它要累計玩家在 daily / period 規則下的投注額，Redis 適合做快速累計與 TTL。Daily 到隔天 0 點過期，Period 到活動結束過期。

### Q2: 怎麼避免重複發 voucher？

Daily 用 Redis awarded key 擋當日重複發。Period 用 Redis awarded count 算還應發多少，再查 DB voucher count 作第二層 guard。但這還不是完整 idempotency，因為 `addVoucher` 的唯一鍵與 transaction boundary 未確認。

### Q3: Kafka 重送會怎樣？

重送可能讓 Redis accumulate 被重複 increment。Daily 若 awarded key 已存在可避免再發，但累計值仍會變大；Period 依 thresholdMultiple，仍要靠 Redis awarded count 與 DB count 擋重複發。完整做法應該補 event id / bet id 去重或 voucher idempotency key。

### Q4: 最危險 failure window 是什麼？

`addVoucher` 成功但 Redis awarded key 更新失敗，下一次 event 可能再次發；或 DB count 查詢失敗回 0，Period 可能低估已發次數。這兩個都會導致 duplicate reward 風險。

### Q5: 這條 flow 的 source of truth 是什麼？

投注 source 應該回到 settlement event / bet record；reward 發放 evidence 應該回到 BetVoucher DB。Redis 是 projection / guard state，不應是唯一 source of truth。

## 面試常見陷阱

| 陷阱 | 避免方式 |
| --- | --- |
| 說自己開發整個活動功能 | 改說 code-backed 分析 / 接觸 / merge evidence |
| 說 Redis 可以保證不重複發 | Redis 只能作 guard，不是完整 transaction guarantee |
| 忽略 DB count 查詢失敗回 0 | 要主動指出 fail-open 風險 |
| 把 voucher 當錢包交易 | voucher 是 reward evidence，不是 wallet source of truth |
| 忽略跨日 replay | Daily key 用 consumer now，bet time / timezone 需確認 |

## Step 4 應補

- STAR 案例。
- 30 秒 / 90 秒 / 3 分鐘版本。
- Senior / Lead 追問短答。
- Owner 改善方向: idempotency key、outbox / reward ledger、reconciliation、fail closed / fail open decision。
