# provider-callback-bet-settle-to-mq Decision Notes

## 核心設計判斷

這條 flow 的 owner decision 不在「callback endpoint 能不能接到」，而在「成功 callback 如何可靠地變成 bet record」。目前 code 顯示它採用的是：

```text
provider callback -> merchant callback success -> MQ -> downstream consumer idempotency
```

這比較接近 eventual consistency，不是 exactly-once。

## 為什麼用 MQ

合理推論：

- callback request 不應塞完整 bet record 入庫與 quota update。
- provider callback 可能高頻，MQ 可削峰。
- 下游 bet record persistence 可以獨立擴充和重試。
- connector-api 和 admin-api 職責分離：connector 做 provider gateway，admin 做資料落地 / report / quota。

待確認：

- 是否有明確性能或事故背景。
- MQ 失敗是否有 alert / replay。

## Idempotency 邊界

現有關鍵：

- producer 不保證 exactly-once。
- consumer 用 providerBetId / id / ptDay / agentId / currency 等欄位做 duplicate boundary。
- `BetRecord` entity 有 provider / provider_bet_id / currency 相關 unique boundary。

風險：

- AntPlay / DerPlay 來源欄位不同，producer 和 consumer 若 key 口徑不同，去重會失效。
- `id` 和 `providerBetId` fallback 規則要一致。
- callback 重送與 job sync 補資料可能打到同一筆資料。

## Amount / currency trade-off

AntPlay DTO 已是 BigDecimal 欄位；DerPlay callback message 註解顯示部分 amount 是分。current history 有多次 amount scaling 修正，代表這不是 trivial mapping。

Owner 視角要追：

- provider amount unit。
- downstream DB amount unit。
- status 定義。
- currency default 是否合理。
- precision / rounding 是否一致。

## 目前不應誇大的地方

- 沒看到完整 transactional outbox。
- 沒確認 listener retry / DLQ。
- producer publish exception 目前 catch log，不代表 callback 成功時 MQ 一定可靠入列。
- consumer quota update failure 不阻擋 bet record write，這是設計取捨，但也代表 quota eventual consistency 要另外觀測。

## Step 4 可延伸追問

- 為什麼 callback 成功才送 MQ？
- MQ publish 失敗怎麼補？
- callback 重送怎麼防重？
- providerBetId、id、currency、ptDay 誰當 duplicate key？
- DerPlay amount 為什麼要 normalize？
- consumer 寫 DB 成功但 quota update 失敗怎麼看？

## Step 5 owner 結論

這條 flow 的可用 owner thinking 是「我知道 callback 成功不等於資料落地成功，必須拆 producer publish、queue delivery、consumer idempotency、DB persistence 與 quota update 來看」。這能放進面試與 project-level provider connector / MQ claim。

不能升級成「我設計完整可靠投遞架構」。目前 evidence 仍缺：

- transactional outbox 或 durable replay 的實作證據。
- listener retry / DLQ / ack policy 的確認。
- monitoring / alert / incident ticket。
- quota update failure 的完整補償邊界。

因此 Step 5 最終口徑是：可講 provider callback / MQ eventual consistency 的維護與風險判斷；不講完整 exactly-once / reconciliation / callback platform owner。
