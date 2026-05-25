# Decision Notes - activity-accumulated-bet-voucher

日期: 2026-05-25

## 1. Redis Accumulate vs DB Reward Evidence

Redis 適合做「目前累計多少」與「是否已發過」的快速 guard，但 reward 是否真的發出去，應以 DB voucher record 作 evidence。這條 flow 的 Period path 有 DB count guard，是比純 Redis 更保守的方向；Daily path 尚未看到 DB count guard。

## 2. Fail Open / Fail Closed

`BetVoucherUtils#getDatabaseAwardedCount` 查 DB 失敗時回 0。這是 fail open：系統傾向繼續發獎，避免漏發，但會增加重複發放風險。Owner 要依活動成本與客服策略決定:

- fail open: 玩家體驗較好，但可能多發 reward。
- fail closed: 避免多發，但可能漏發，需要補發工具。

目前 code 沒看到明確 decision note 或補償流程，所以面試只能說「我會把這列為 owner decision」。

## 3. Idempotency Key

理想 idempotency key 不應只靠 UUID refId，因為 UUID 每次嘗試都不同。比較合理的是:

```text
agentId + activityId + playerId + currency + rewardType + day/period + thresholdMultiple
```

這樣 Kafka 重送、consumer crash retry 或 Redis awarded key 遺失時，DB 仍能擋 duplicate voucher。

## 4. Time Boundary

Daily 使用 consumer current date，activity eligibility 使用 betRecord time。若 event delay 或 replay 跨日，這兩個時間來源可能不一致。Owner 要先確認產品語意:

- 以投注發生日算 daily reward。
- 以消費處理日算 daily reward。
- 以特定業務 timezone 算 daily reward。

沒有 spec 前不能說 current behavior 一定正確。

## 5. Interview Owner Improvement

若面試官問「你會怎麼改」，優先順序:

1. 補 DB idempotency key / unique constraint。
2. Daily path 補 DB awarded count guard 或 durable reward ledger。
3. DB count 查詢失敗不要默默回 0，至少要告警並明確 fail policy。
4. 加 reconciliation: source bet amount、Redis accumulated、BetVoucher DB count。
5. 明確 daily timezone / replay semantics。
