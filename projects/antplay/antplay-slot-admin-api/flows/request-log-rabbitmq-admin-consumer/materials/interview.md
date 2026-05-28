# request-log-rabbitmq-admin-consumer Interview Notes

日期: 2026-05-28

## 30 秒摘要

這條 flow 是 AntPlay 後台的 RequestLog RabbitMQ consumer。game-api 把 provider API request / response log 送到 RabbitMQ，admin-api 消費後解析 message，依 `pt_day`、`agent_id`、`time`、`id` 查重，再寫入 `pt_request_log`，供後台查詢。它是 audit / observability flow，不是交易 source of truth，所以面試重點是 async ingestion、idempotency、consumer failure、DLQ / retry 與 audit loss 風險。

## 90 秒版本

這個 case 的價值在於把 provider API request log 從主流程同步寫庫拆出來。producer 在 game-api，consumer 在 admin-api。admin-api 這邊用 `@RabbitListener` 監聽 `request-log` queue，收到 message 後 parse JSON，轉成 `RequestLog` entity，透過 `@UseSchema` 依 agentId route 到對應 schema，先查 `pt_request_log` 是否已有相同 `(pt_day, agent_id, time, id)`，沒有才 insert。

我會保守把它定位成 async audit flow。它降低主交易流程和 audit DB 的 coupling，但不是 exactly-once。查重和 insert 不是同一個原子 upsert，本輪也沒確認 DB unique key、DLQ、retry、ack mode，所以正式 owner 要補這些可靠性設計與監控。

## Senior 追問

### Q1: 這是不是 exactly-once?

不是。現在看到的是 application-level dedupe：先查 `(pt_day, agent_id, time, id)` 是否存在，不存在才 insert。這能降低一般重送造成的重複，但 query-then-insert 中間仍可能有 race。若要更接近 production-grade idempotency，要確認 DB unique key，或改用 insert ignore / upsert，再配合 MQ retry / DLQ。

### Q2: MQ 失敗會不會影響下注或結算?

不應該影響。request log 是 audit data，不是 money correctness source。主交易成功但 audit log 缺失是 observability 問題，不應 rollback provider API result。但 audit loss 會影響追查，所以需要 alert、DLQ、queue lag monitoring。

### Q3: consumer parse 失敗怎麼辦?

本輪看到 listener catch exception 並 log error，但沒有確認 ack mode / retry / DLQ。如果是 auto ack 或 exception 被吞掉，可能造成 message loss。正式設計應該區分 poison message、暫時性 DB failure、schema mismatch，並進 DLQ 或 retry。

### Q4: 為什麼要用 `ptDay`?

`ptDay` 來自 message time，用來查 `pt_request_log` partition / logical table。後台查詢也是依 agentIds + ptDays 組 partition key。這能縮小查詢範圍，但時間轉換錯、timezone 錯或 producer time 錯，都會導致寫到或查到錯誤日期範圍。

### Q5: 你會怎麼改善?

我會先補 DB unique key 或 upsert，讓 dedupe 由 DB 保底；再補 DLQ / retry policy、queue lag、consumer error rate、DB insert failure metric。message contract 加版本，parse fail 記 sanitized sample，避免敏感 request body 外洩。

## 常見陷阱

- 把 request log 講成交易正確性的 source of truth。
- 宣稱 exactly-once，但沒有 DB unique key / outbox / idempotent consumer evidence。
- 忽略 schema route，導致 multi-tenant 寫入邊界說不清。
- 只講 RabbitMQ 名詞，不講 failure window。

## 反問面試官

- 你們的 audit log / request log 是同步寫庫還是 async pipeline?
- 你們怎麼處理 consumer 重複消費、DLQ 和 poison message?
- 對非交易 source-of-truth 的 audit loss，你們會用什麼 SLO 或告警?
