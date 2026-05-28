# request-log-rabbitmq-admin-consumer Career Interview

日期: 2026-05-28

## 定位

這條 flow 可作 AntPlay 後台 API / async audit / RabbitMQ consumer 的正式面試素材。完成狀態是 Step 5；證據層級是 `真實開發過 + code-backed`，但只限 request log consumer / audit log 入庫，不擴張成完整 RabbitMQ platform owner。

## 30 秒說法

我參與過 AntPlay 後台 RequestLog RabbitMQ consumer 相關流程。game-api 會把 provider API 的 request / response / error / elapsed time 包成 message 丟到 RabbitMQ，admin-api 端 consumer 解析 message，依 `pt_day`、`agent_id`、`time`、`id` 查重後寫入 `pt_request_log`，支援後台查詢與事故排查。這不是交易 source of truth，所以重點是 async audit、重複消費、consumer failure 與 observability 邊界。

## 90 秒說法

這個 case 我會定位成 async audit pipeline。背景是第三方 provider API 的 request / response log 需要留存給客服、營運和工程師追問題，但這種 audit log 不應該卡住下注或 provider call 主流程。

實作上，game-api 在 provider call 結束後送 request log message 到 RabbitMQ；admin-api 端宣告 direct exchange、durable queue 和 routing key，`RequestLogListener` 消費後 parse JSON，算出 `ptDay`，再透過 processor 依 `agentId` 做 `@UseSchema` schema route。落庫前會用 `(pt_day, agent_id, time, id)` 查 `pt_request_log` 是否已存在，不存在才 insert。

我會主動講清楚取捨：這能解耦主流程和 audit DB，但它是 eventual consistency。現有 evidence 只確認 application-level dedupe 和 transactional insert，沒有確認 DB unique key、DLQ、ack mode 或 queue lag monitoring，所以不能誇大成 exactly-once。若我是 owner，會補 unique key / upsert、retry / DLQ、consumer error rate、queue lag 和 bad message handling。

## 3 分鐘說法

這條 flow 的背景是 provider API request log 不應該卡在主交易流程裡。game-api 呼叫第三方 provider 後，會在 finally 裡組一包 request log message，送到 RabbitMQ 的 `request-log` queue。admin-api 端用 `RequestLogListener` 消費 message，parse JSON 後組成 `RequestLog` entity，依時間算出 `ptDay`，再交給 processor。

processor 這層有兩個重點。第一是 `@UseSchema`，會依 agentId 路由到對應 schema。第二是先查 `pt_request_log` 是否已存在相同 `(pt_day, agent_id, time, id)`，不存在才 insert。insert method 有 `@Transactional`，所以單次 insert 是 transaction boundary。

這個設計的取捨是把 request log 從主流程同步寫庫改成 async audit。主交易成功不應該因為 audit DB 慢或 consumer 失敗而 rollback；但代價是後台查詢會有 eventual consistency，且 MQ send、consumer parse、DB insert 都可能失敗。所以我面試會主動講清楚：目前看到的是 application-level dedupe，不會誇大成 exactly-once。production owner 應該補 DLQ、retry、queue lag、consumer error rate、DB unique key 或 upsert 來讓 audit pipeline 更可靠。

這個 case 我會特別強調邊界：`pt_request_log` 可以幫忙追 provider request / response、error message、elapsed time，但不能拿來當下注或錢包正確性的唯一依據。真正的交易 truth 還是 bet record、wallet state 或 provider result。這樣講比較像 Senior backend，而不是只背 RabbitMQ 名詞。

## STAR

- Situation: provider API request / response log 需要被後台查詢，但同步寫 DB 會增加主流程 coupling。
- Task: 把 request log 落庫移到 RabbitMQ consumer，讓 admin-api 負責 audit log ingestion。
- Action: 建立 / 維護 consumer parse、`ptDay` 計算、schema route、查重與 `pt_request_log` insert path，並補 G2 / UAT config 修正。
- Result: request log 變成 async audit flow，主流程與 audit DB 解耦；後續可用後台查詢追 provider request / response。

## 面試官追問回答骨架

| 追問 | 回答方向 |
| --- | --- |
| 這是不是 exactly-once? | 不是，只能說 application-level dedupe；要靠 DB unique key / upsert + retry / DLQ 才更完整。 |
| request log 掛掉會不會影響交易? | 不應該影響交易，因為它是 audit data；但會影響 troubleshooting，所以要監控 audit loss。 |
| 為什麼要用 `ptDay`? | 它是 request log partition / query key，可縮小查詢範圍；但 time / timezone 錯會造成查詢錯位。 |
| schema route 風險是什麼? | message 的 `agentId` 決定 schema，錯了可能寫錯位置或查不到，所以 route error 需要 alert。 |
| 你會怎麼改善? | unique key / upsert、DLQ / retry、queue lag、consumer error metric、bad message handling、message schema version。 |

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

## Step 5 結論

這條 flow 已可作「後台 async audit / RabbitMQ consumer / troubleshooting」正式面試 case，也可以回填 `antplay-slot-admin-api` project-level consolidation 的 supporting evidence。

不直接更新 `05 / 08`。原因是單條 flow Step 5 不代表整個 project final consolidation；`05 / 08` 仍應吃 project-level contribution consolidation 或 rolling resume package。若之後 refresh project-level claim，可把本 flow 併入「AntPlay 後台 API / control plane / 非同步資料處理」段落。

## 下一步

```text
antplay antplay-slot-admin-api game-api-whitelist-sync Step 4
```
