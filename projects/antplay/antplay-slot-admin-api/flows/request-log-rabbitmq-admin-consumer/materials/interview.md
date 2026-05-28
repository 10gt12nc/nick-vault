# request-log-rabbitmq-admin-consumer Interview Notes

日期: 2026-05-28

## Step 4 定位

本檔是 Step 4 正式面試素材。素材只使用 Step 3 已確認的 code / commit evidence，不新增未驗證 claim。這條 flow 的定位是 `async audit / admin consumer / troubleshooting`，不是交易正確性主線。

## 30 秒摘要

這條 flow 是 AntPlay 後台的 RequestLog RabbitMQ consumer。game-api 把 provider API request / response log 送到 RabbitMQ，admin-api 消費後解析 message，依 `pt_day`、`agent_id`、`time`、`id` 查重，再寫入 `pt_request_log`，供後台查詢。它是 audit / observability flow，不是交易 source of truth，所以面試重點是 async ingestion、idempotency、consumer failure、DLQ / retry 與 audit loss 風險。

## 90 秒版本

這個 case 的價值在於把 provider API request log 從主流程同步寫庫拆出來。producer 在 game-api，consumer 在 admin-api。admin-api 這邊用 `@RabbitListener` 監聽 `request-log` queue，收到 message 後 parse JSON，轉成 `RequestLog` entity，透過 `@UseSchema` 依 agentId route 到對應 schema，先查 `pt_request_log` 是否已有相同 `(pt_day, agent_id, time, id)`，沒有才 insert。

我會保守把它定位成 async audit flow。它降低主交易流程和 audit DB 的 coupling，但不是 exactly-once。查重和 insert 不是同一個原子 upsert，本輪也沒確認 DB unique key、DLQ、retry、ack mode，所以正式 owner 要補這些可靠性設計與監控。

## 3 分鐘版本

這個 flow 背景是 provider API request / response log 需要被保存下來，讓客服、營運或工程師後續能查某次外部 API 呼叫的 request、response、錯誤訊息和耗時。但這種 request log 不是交易 source of truth，如果同步寫 DB，可能讓 provider call 或主交易流程受到 audit DB、分表或 schema route 問題影響。

所以系統把它拆成 RabbitMQ async ingestion。game-api producer 在 provider call 結束後送 request log message，admin-api consumer 監聽 `request-log` queue。consumer 會 parse JSON，讀出 `id`、`time`、`agentId`、`step`、`target`、request / response body、status、error message、elapsed time，接著用 `time` 算 `ptDay`，組成 `RequestLog`。

落庫前有兩個重點。第一是 `@UseSchema`，依 `agentId` route 到對應 schema，這對多租戶 / 多 schema 的後台資料很重要。第二是 dedupe，SQL 會用 `(pt_day, agent_id, time, id)` 查 `pt_request_log` 是否已存在，沒有才 insert。insert method 有 `@Transactional`，所以單次 insert 是 transaction boundary。

我會主動說這不是 exactly-once。它目前比較像 application-level dedupe；如果同一 message 併發重送，query-then-insert 仍可能有 race，所以正式 production owner 應該用 DB unique key / upsert 保底，再補 DLQ、retry、queue lag、consumer error rate 和 bad message handling。這條 flow 的價值是 async audit 與排障能力，不是直接證明下注或錢包正確性。

## STAR 版本

- Situation: provider API request / response log 需要被後台查詢，但同步寫庫會增加主流程和 audit DB 的耦合。
- Task: 讓 request log 以 RabbitMQ 非同步方式落到 admin-api 的 `pt_request_log`，支援後續查詢和排障。
- Action: 參與 consumer parse、`ptDay` partition key、`@UseSchema` schema route、查重與 insert path；並能分析 producer / consumer message contract 和 deployment config 風險。
- Result: request log 從主交易流程解耦，後台可查 provider API request / response；同時保留 eventual consistency、dedupe、retry / DLQ、observability 的改善邊界。

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

### Q6: 如果 request log 消費延遲，客服說查不到，你怎麼判斷?

我會先確認這是 audit eventual consistency，不直接推論交易失敗。排查順序會看 producer 是否有送出、RabbitMQ queue lag、consumer error log / DLQ、DB insert failure、schema route 是否正確，最後再比對 provider result / bet record / wallet source。這能避免把 audit delay 誤判成 money flow 問題。

### Q7: `@UseSchema` 在這裡為什麼重要?

因為 request log 是依 agent / tenant 查詢的資料，message 裡的 `agentId` 會影響 schema route。若 agentId 缺失或錯誤，可能寫錯 schema、查不到 log 或污染資料邊界。所以這條 flow 不能只看 MQ，也要看 schema route 和 partition key。

### Q8: request / response body 可能有敏感資料，怎麼處理?

本輪沒有看到完整 masking evidence，所以不能宣稱已處理。正式 owner 會要求 producer 或 consumer 做敏感欄位遮罩、bad message sample sanitization、log retention / access-control，避免排障資料本身變成風險。

## Lead / Architect 追問

### Q1: 你會選 RabbitMQ 還是直接同步 DB?

這取決於資料定位。這裡 request log 是 audit data，不是交易 truth，所以我會偏向 MQ async，避免 audit DB 影響主流程 latency。若是 money state transition，就不能用同樣口徑，需要更強 transaction / outbox / reconciliation 設計。

### Q2: 如果要做到更可靠，會不會用 outbox?

如果 producer 端要保證「交易完成和 audit event 一定一起留下」，可以評估 outbox。但這條 flow 目前 evidence 只看到 producer finally 送 MQ，不能說已有 outbox。面試可以把 outbox 當改善方案，而不是現況 claim。

### Q3: 這條 flow 怎麼定 SLO?

我不會用交易成功率來定，因為它不是交易主線。可用 audit ingestion delay、queue lag、consumer error rate、DLQ count、DB insert failure rate、request log query availability 來定。嚴重度取決於它是否影響事故排查和客訴處理。

## 回答框架

```text
定位：這是 async audit，不是 money source-of-truth。
流程：game-api publish -> RabbitMQ -> admin-api listener -> parse -> schema route -> dedupe -> pt_request_log insert。
取捨：解耦主流程，但接受 eventual consistency。
風險：duplicate、parse fail、DB fail、schema route fail、queue lag、sensitive body。
改善：unique key / upsert、DLQ / retry、metrics / alert、message version、masking。
邊界：不能說 exactly-once / complete RabbitMQ platform / money correctness。
```

## 常見陷阱

- 把 request log 講成交易正確性的 source of truth。
- 宣稱 exactly-once，但沒有 DB unique key / outbox / idempotent consumer evidence。
- 忽略 schema route，導致 multi-tenant 寫入邊界說不清。
- 只講 RabbitMQ 名詞，不講 failure window。
- 把 Step 3 的 code-backed 分析擴張成主導完整 RabbitMQ platform。
- 沒有 evidence 卻說已經有 DLQ、retry、metrics 或 masking。

## 反問面試官

- 你們的 audit log / request log 是同步寫庫還是 async pipeline?
- 你們怎麼處理 consumer 重複消費、DLQ 和 poison message?
- 對非交易 source-of-truth 的 audit loss，你們會用什麼 SLO 或告警?
- request / response body 這類 troubleshooting data 會怎麼做 masking、retention 和權限控管?
