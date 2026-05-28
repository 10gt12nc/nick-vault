# request-log-rabbitmq-admin-consumer Career Interview

日期: 2026-05-28

## 定位

這條 flow 可作 AntPlay 後台 API / async audit / RabbitMQ consumer 的面試素材。證據層級是 `真實開發過 + code-backed`，但只限 request log consumer / audit log 入庫，不擴張成完整 RabbitMQ platform owner。

## 30 秒說法

我參與過 AntPlay 後台 RequestLog RabbitMQ consumer 相關流程。game-api 會把 provider API 的 request / response / error / elapsed time 包成 message 丟到 RabbitMQ，admin-api 端 consumer 解析 message，依 `pt_day`、`agent_id`、`time`、`id` 查重後寫入 `pt_request_log`，支援後台查詢與事故排查。這不是交易 source of truth，所以重點是 async audit、重複消費、consumer failure 與 observability 邊界。

## 3 分鐘說法

這條 flow 的背景是 provider API request log 不應該卡在主交易流程裡。game-api 呼叫第三方 provider 後，會在 finally 裡組一包 request log message，送到 RabbitMQ 的 `request-log` queue。admin-api 端用 `RequestLogListener` 消費 message，parse JSON 後組成 `RequestLog` entity，依時間算出 `ptDay`，再交給 processor。

processor 這層有兩個重點。第一是 `@UseSchema`，會依 agentId 路由到對應 schema。第二是先查 `pt_request_log` 是否已存在相同 `(pt_day, agent_id, time, id)`，不存在才 insert。insert method 有 `@Transactional`，所以單次 insert 是 transaction boundary。

這個設計的取捨是把 request log 從主流程同步寫庫改成 async audit。主交易成功不應該因為 audit DB 慢或 consumer 失敗而 rollback；但代價是後台查詢會有 eventual consistency，且 MQ send、consumer parse、DB insert 都可能失敗。所以我面試會主動講清楚：目前看到的是 application-level dedupe，不會誇大成 exactly-once。production owner 應該補 DLQ、retry、queue lag、consumer error rate、DB unique key 或 upsert 來讓 audit pipeline 更可靠。

## STAR

- Situation: provider API request / response log 需要被後台查詢，但同步寫 DB 會增加主流程 coupling。
- Task: 把 request log 落庫移到 RabbitMQ consumer，讓 admin-api 負責 audit log ingestion。
- Action: 建立 / 維護 consumer parse、`ptDay` 計算、schema route、查重與 `pt_request_log` insert path，並補 G2 / UAT config 修正。
- Result: request log 變成 async audit flow，主流程與 audit DB 解耦；後續可用後台查詢追 provider request / response。

## 可安全使用的履歷 bullet

- 參與 AntPlay 後台 RequestLog RabbitMQ consumer 與 audit log 入庫流程，處理 message parse、schema route、查重與 `pt_request_log` 寫入，支援 provider API request / response 後台追查。

## 可面試講的能力點

- RabbitMQ direct exchange / durable queue / routing key 的基本責任。
- consumer parse -> entity -> mapper -> DB insert 的分層。
- audit data 與 transaction source of truth 的邊界。
- application-level dedupe 與 DB unique key 的差異。
- `@UseSchema` schema route 對 multi-tenant / multi-schema 寫入的重要性。
- async audit 的 failure window: publish fail、parse fail、insert fail、retry / DLQ / lag。

## 不可誇大

- 不說主導完整 AntPlay slot platform。
- 不說完整 RabbitMQ / event-driven architecture owner。
- 不說 exactly-once / outbox / DLQ 已落地。
- 不說 request log 能代表交易正確性。
- 不說改善多少 latency 或故障率，除非之後補 metric / ticket。

## 下一步

```text
antplay antplay-slot-admin-api request-log-rabbitmq-admin-consumer Step 4
```
