# request-log-rabbitmq-admin-consumer Career Interview

日期: 2026-05-27

## 狀態

目前完成 Step 3。這份是履歷 / 面試素材底稿，不是最終 Step 4 / Step 5 話術。

Evidence 層級: `真實開發過 + code-backed`。Nick / `10gt12nc` 有 RequestLog RabbitMQ 非同步化 direct commits；上游 producer 與後台查詢為 code-backed context；`arnold` 後續修正只作主管 / team context。

## 30 秒草稿

我在 UGSoft 後台 API 參與過 RequestLog 從同步寫入改成 RabbitMQ 非同步入庫的 flow。這條 flow 會把 provider / connector request-response log 先丟 MQ，再由 admin-api consumer 寫入 `pt_request_log`，後台用 agent scope、時間區間與分區查詢。這裡我會特別注意 duplicate check、`ptDay` 分區、consumer exception 是否會影響 retry / DLQ，以及 list / count 查詢條件一致性；因為 request log 是 audit / observability 資料，failure policy 和 wallet / bet settle 這類 money flow 不一樣。

## 可放履歷的保守素材

- 參與 UGSoft RequestLog RabbitMQ 非同步入庫與後台查詢支援，處理 request / response audit log 的 consumer、分區、重複檢查與 DB insert。
- 維護後台非同步資料處理 flow，關注 MQ consumer 的 idempotency、retry / failure window、partition query 與 observability 邊界。

## 可面試講

- 為什麼 audit log 適合非同步化，不應阻塞主請求。
- `pt_day + agent_id + time + id` duplicate check 能防哪些重送，不能保證哪些 race。
- listener catch exception 後，如果沒有 rethrow，對 RabbitMQ ack / retry / DLQ 可能代表什麼。
- 後台查詢 request log 時，為什麼要同時看 role / agent scope、time range、partition key、list / count 條件。
- RequestLog 和 BetRecord MQ 的差異: 前者偏 audit / observability，後者更貼近交易資料正確性。

## 不可誇大

- 不說主導完整 RabbitMQ architecture。
- 不說已建立完整 exactly-once、outbox、DLQ 或 replay 機制。
- 不說這條 flow 是 wallet / money source of truth。
- 不把 `arnold` 的 request-log key / query 修正當 Nick direct evidence。

## Step 4 要打磨

Step 4 應把這條 flow 收斂成:

- 90 秒版。
- 3 分鐘版。
- 追問題庫。
- owner decision / failure window 回答。
- 與 `connect-bet-record-mq-ingestion`、`ugsoft-connector-api request-bet-record-mq-sync` 的差異化講法。
