# request-log-rabbitmq-admin-consumer Decision Notes

日期: 2026-05-28

## 為什麼 async

request log 是 audit data，不是交易 source of truth。把它從主交易流程同步 DB write 改成 RabbitMQ async，有幾個好處:

- 降低 provider API latency 被 DB write 影響。
- 降低 `pt_request_log` 分表 / schema 問題對主交易的耦合。
- 讓 admin-api 負責後台 audit ingestion，service boundary 更清楚。

代價:

- 後台查詢變成 eventual consistency。
- audit loss 需要靠 retry / DLQ / alert 管。
- producer / consumer message contract 需要相容。

## Dedupe 設計

目前看到:

```text
SELECT id FROM pt_request_log
WHERE pt_day = ?
  AND agent_id = ?
  AND time = ?
  AND id = ?
LIMIT 1
```

不存在才 insert。

判斷:

- 這是 application-level dedupe。
- 能處理一般 message redelivery。
- 但 query-then-insert 不是 atomic。
- production-grade 做法需要 DB unique key / insert ignore / upsert 作最後防線。

## Transaction Boundary

`insertRequestLog` 有 `@Transactional`，但 `queryRequestLogExists` 和 `insertRequestLog` 是兩個 method。這代表:

- 單次 insert 有 transaction。
- 查重 + insert 不是單一 atomic operation。
- 若同一 message 被兩個 consumer instance 同時處理，仍可能 race。

## Schema Route

`@UseSchema` 依 `agentId` route schema。這是這條 flow 的重要 owner 點:

- message 必須帶正確 `agentId`。
- schema route 失敗會影響寫入位置。
- 後台查詢也依 agentIds / ptDays 查 `pt_request_log`。

## Owner Checklist

若要把這條 flow 提升到更穩的 production owner 水準，應補:

- DB unique key / upsert。
- DLQ / retry / requeue policy。
- queue lag / consumer error rate / insert failure metrics。
- poison message sample，但要避免敏感 request / response 外洩。
- message schema version。
- producer / consumer deployment compatibility check。
