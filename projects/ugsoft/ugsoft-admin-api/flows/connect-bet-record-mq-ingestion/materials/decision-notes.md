# Decision Notes - connect-bet-record-mq-ingestion

## 1. 為什麼用 MQ

Provider callback / job sync 若直接同步寫入所有下游，容易讓 provider request latency、DB 寫入、quota update、report update 綁在一起。MQ 把 producer 和 consumer 拆開：

- 上游 connector 負責 provider normalize / publish。
- 下游 admin-api consumer 負責入庫與衍生事件。
- provider callback 不必等待 quota update。

代價是要面對 retry、duplicate、DLQ、observability 與 eventual consistency。

## 2. Source of truth

本 flow 的 source of truth 應該是 `pt_bet_record`，不是 quota Redis view。Quota remaining 是衍生 view，可重建、可延遲，但 bet record 入庫錯了會影響報表、查詢與後續修復。

## 3. Idempotency key

目前可以觀察到三層 key：

| 層 | key | 風險 |
| --- | --- | --- |
| connector sync | `providerBetId|currency` | 避免同批或 DB 已存在資料重送 MQ。 |
| admin consumer query | `ptDay + agentId + providerBetId` | 最新 code 查重 boundary。 |
| entity unique | `ptDay + agentId + provider + providerBetId + currency` | 可能是 DB 最終防線，但 production schema 待確認。 |

這三者要能解釋清楚，不能面試時只說「有查重所以沒問題」。

## 4. Fire-and-forget quota update

save bet record 後 publish quota update 是非同步衍生事件。它的 owner trade-off：

- 優點：bet record 寫入不被 quota 系統阻塞。
- 缺點：publish 失敗時，quota view 不更新。
- 補強：outbox、publisher confirm、重送 job、由 `pt_bet_record` rebuild quota、DLQ replay、metric alert。

## 5. Listener exception handling

listener catch exception 後不重拋，可能讓 broker ack / retry 行為變得不明確。是否真的 ack 要看 Spring AMQP container 設定。本 flow 只能說「需要確認」，不能宣稱現況有可靠 retry。
