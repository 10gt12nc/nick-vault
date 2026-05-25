# Interview Notes - activity-accumulated-bet-voucher

日期: 2026-05-25

## Step 5 狀態

本檔是 `activity-accumulated-bet-voucher` 的正式面試稿，已完成 Step 5 claim gate。這條 flow 是 code-backed reward correctness case，不是 Nick 主開發 claim。

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

## 30 秒版本

我看過 AntPlay job 裡的活動累計投注送 voucher flow。它消費 `settled_bets`，依活動設定過濾 agent、活動時間與幣別，把玩家投注額累加到 Redis；達 daily 或 period 門檻後透過 `BetVoucherService` 發 free spin / voucher。這條 case 我會拿來講 Kafka 重送、Redis awarded count、DB voucher count 與重複發放邊界；但我會保守說這是 code-backed 分析素材，不說是我主導開發。

## 90 秒版本

這條 flow 是 settlement event 後的 reward projection。上游送出 `settled_bets` 後，job consumer 會找 type=1、status=1 的活動，檢查 bet record 的 agent、activity period 與 currency，再把同玩家、同代理、同幣別的投注額累加到 Redis。

它有 daily 與 period 兩類邏輯。Daily 是當日達標後發一次 voucher，靠 Redis daily awarded key 擋重複發。Period 是活動期間累積，依門檻倍數計算應發數，除了 Redis awarded count，還會查 DB voucher count，避免 Redis 狀態不準時重複發。

這個 case 最值得講的是 reward correctness。Kafka 重送可能讓 Redis accumulate 重複增加；voucher 寫成功但 Redis awarded key 失敗，Daily 可能重複發；DB count 查詢失敗回 0，Period 則有 fail-open 風險。我會把 owner 改善放在 deterministic idempotency key、reward ledger / reconciliation、Daily DB guard，以及明確的 fail-open / fail-closed decision。

## 3 分鐘版本

我可以分享一條 AntPlay job 的活動累計投注送 voucher flow。這條不是下注主交易，而是 settlement event 後的 reward projection。上游結算完成後會送 `settled_bets` Kafka event，event 裡有一批 `BetRecord`。job consumer 讀到後，會找 type=1、status=1 的活動設定，然後檢查每筆 bet record 的 agentId、下注時間是否在活動期間、currency 是否有對應的 daily 或 accumulated bet 設定。

current code 會先做 event 內聚合，用 `playerId + currency + agentId` 當 group key，把同一個 Kafka message 裡同玩家、同代理、同幣別的 bet amount 累加，然後對 matched activities 跑 daily、period1、period2 三種邏輯。這樣可以避免同一批 event 裡多筆 bet record 重複打 Redis / voucher service；但我也會注意 group key 沒包含 activityId，如果同批事件中不同 bet 對應不同 activity set，這是需要再確認的邊界。

Daily flow 會用 Redis 存當日累計投注額，key 裡有 agent、activity、player、currency、date。每次事件會先 increment 累計值，達標且 daily awarded key 不存在時，呼叫 `BetVoucherService` 發 voucher，再寫 awarded key，TTL 到隔天 0 點。這裡的風險是，如果 voucher 寫 DB 成功但 Redis awarded key 失敗，Daily path 沒看到 DB count guard，後面可能重複發。

Period flow 則更像累積型 reward。它把 Redis TTL 設到活動結束，依 `accumulatedBet / threshold` 算出應發倍數，再用 Redis awarded count 扣掉已發數。比較好的地方是，它還會查 DB voucher count，避免 Redis 不準造成 duplicate reward。但這裡也有 owner decision: DB count 查詢失敗時目前回 0，這是 fail-open，代表比較不容易漏發，但可能多發。

如果讓我設計改善，我會先補 DB 層的 idempotency key，例如 agentId、activityId、playerId、currency、reward type、day 或 threshold multiple。再補 reward ledger / reconciliation，讓 source bet、Redis accumulated、BetVoucher DB count 能對起來。最後要定義 daily timezone 與 replay semantics，因為 activity eligibility 用 bet time，但 daily key 用 consumer now，跨日 replay 可能有邊界。

這個 case 我會保守說是 code-backed 分析素材。Nick 的 evidence 主要是 merge，不是主要 implementation，所以我不會說我主導活動功能；我會講我能用這條 flow 分析 reward idempotency 與 Redis / DB consistency。

## STAR

| 面向 | 內容 |
| --- | --- |
| Situation | 活動累計投注需要根據 settlement event 自動發 voucher / free spin。 |
| Task | 梳理 reward projection 的防重與一致性風險。 |
| Action | 追 consumer、Redis key、daily / period rules、DB voucher count guard 與 commits / blame。 |
| Result | 形成可面試的 reward correctness case，但 claim 維持 code-backed，不誇大成 Nick 主開發。 |

## 面試常見陷阱

| 陷阱 | 避免方式 |
| --- | --- |
| 說自己開發整個活動功能 | 改說 code-backed 分析 / 接觸 / merge evidence |
| 說 Redis 可以保證不重複發 | Redis 只能作 guard，不是完整 transaction guarantee |
| 忽略 DB count 查詢失敗回 0 | 要主動指出 fail-open 風險 |
| 把 voucher 當錢包交易 | voucher 是 reward evidence，不是 wallet source of truth |
| 忽略跨日 replay | Daily key 用 consumer now，bet time / timezone 需確認 |

## 可用反問

- 你們活動 reward 是 fail-open 還是 fail-closed？
- Voucher 發放有沒有 deterministic idempotency key？
- 活動設定上線前有沒有 dry run / impact preview？
- Redis projection 和 voucher DB 有沒有定期 reconciliation？
- 跨日 event replay 時，daily reward 以 bet time 還是 process time 為準？

## Step 5 結論

Step 5 已追 claim gate。`BetVoucherService` 下游 implementation、DB unique key 與 transaction boundary 不在本 repo；current flow 每次發券產生新的 UUID `refId`，不能證明 deterministic idempotency。Nick 有 `62fa93f` merge evidence，但 current implementation 主要是 Gill，後續 Arnold / Eliot；因此本 flow 只作 code-backed 面試素材與 project-level supporting evidence，不單獨放正式履歷，也不直接更新 `05 / 08`。
