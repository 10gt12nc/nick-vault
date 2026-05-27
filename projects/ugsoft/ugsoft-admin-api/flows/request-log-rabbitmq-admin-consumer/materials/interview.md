# Interview Notes - request-log-rabbitmq-admin-consumer

日期: 2026-05-27

## Step 4 用途

這份是 Step 4 的正式面試素材。它把 Step 3 的 code-backed flow 收斂成 30 秒、90 秒、3 分鐘、STAR、追問、回答邊界與誇大陷阱。

## 面試主軸

一句話:

> RequestLog RabbitMQ 非同步化是把 request / response audit log 從主請求路徑拆出去，降低 DB logging 對主流程的影響；但拆出去後要補 idempotency、partition、retry / DLQ 與查詢一致性的 owner 判斷。

## 30 秒版

我參與過 UGSoft 後台 API 的 RequestLog RabbitMQ 非同步入庫。上游 provider / connector request flow 會把 request-response log 丟 MQ，admin-api consumer 再寫入 `pt_request_log`，供後台依 agent scope、時間與分區查詢。我會把這條 case 重點放在 duplicate check、`ptDay` 分區、consumer exception 對 retry / DLQ 的影響，以及 list / count 查詢條件一致性；因為它是 audit / observability 資料，failure policy 不能和 BetRecord / wallet 類 flow 混在一起講。

## 90 秒版

UGSoft 的 RequestLog 是 provider / connector request-response 的 audit log。如果同步寫 DB，主請求會被 log DB、分表或慢查詢拖住，所以這條 flow 改成由上游 publish MQ，admin-api 的 `RequestLogListener` 消費後寫入 `pt_request_log`。

consumer 收到 message 後會 parse 欄位，包含 request id、agent、step、target、request / response、status、elapsed time；再依 message time 算 `ptDay`，用 `pt_day + agent_id + time + id` 查重，不存在才 insert。後台 `/request_log` 查詢則會依 role / agent scope、time range 和 partition keys 查 list / count。

這條 case 我會講三個 Senior 判斷。第一，RequestLog 是 audit / observability supporting data，不是 money source of truth，所以非同步化可以降低主流程耦合，但要定義 log loss 能不能接受。第二，check-then-insert 不是 exactly-once，仍需 DB unique / upsert 類策略兜底。第三，listener catch exception 後是否會 retry，要看 RabbitMQ ack mode / retry / DLQ config，不能沒證據就說可靠重試。

## 3 分鐘版

我會把這條 case 定位成 request-response audit log 的非同步化。UGSoft provider / connector request 會產生 request log，內容包含 id、time、agentId、step、target、URI、method、headers、params、body、response、status、error message 和 elapsed time。這些資料對客服排查、provider 對接、錯誤追蹤很有價值，但它不是交易帳務來源，所以不應該讓主請求被 logging DB 寫入卡住。

流程上，上游 request 結束後會透過 RabbitTemplate publish `RequestLogMessageDto` 到 RequestLog exchange / routing key。admin-api 的 `RequestLogListener` 監聽 queue，收到 JSON 後 parse 欄位，用 message 裡的 `time` 算 `ptDay`，再組 `RequestLog` entity。寫入前會先查 `pt_request_log`，條件是 `pt_day + agent_id + time + id`，不存在才 insert。後台查詢 `/request_log` 時，controller 會依角色決定可查 agent 範圍，限制查詢時間，service 組 `ptDays x agentIds` partition keys，mapper 再查 list / count。

這裡我會主動講 owner decision。第一是 idempotency：MQ 可能 redelivery 或 producer retry，查重可以防多數重複，但 query + insert 分開不是 atomic exactly-once，嚴謹上要靠 DB unique / insert-ignore / upsert。第二是 retry / DLQ：目前 code 裡 listener catch exception 後只 log，沒有看到 rethrow；所以是否會 retry 要看 Spring AMQP ack mode 和 retry interceptor，不能直接宣稱有可靠重試。第三是查詢一致性：request log 查詢涉及 role / agent scope、partition、time range、list / count 條件，一旦條件不一致，後台頁數或篩選結果會錯。

所以我不會把這條 flow 誇大成完整 RabbitMQ 平台或 exactly-once。它的價值是我能說清楚 async audit log 的 decoupling、idempotency、partition、retry / DLQ 與 observability 邊界，也能和 BetRecord MQ 做差異化：BetRecord 更接近交易 / report source data，RequestLog 偏 audit / troubleshooting，兩者的 failure policy 不一樣。

## STAR 版

Situation: provider / connector request-response log 需要被後台查詢，但同步 DB logging 會讓主請求和 log DB 耦合。

Task: 將 RequestLog 改為 RabbitMQ 非同步入庫，同時保留後台查詢能力與基本 duplicate 防護。

Action: 參與 listener / processor / mapper 相關調整；consumer parse MQ payload，依 event time 建 `ptDay`，用 `pt_day + agent_id + time + id` 查重後寫入 `pt_request_log`；後台 query 則依 role / agent scope、time range 與 partition keys 查詢。

Result: 主請求與 log DB 寫入耦合降低，後台仍能查 request-response audit log；同時這條 flow 可延伸討論 idempotency、retry / DLQ、partition、list / count consistency 與 audit log failure policy。

## 可被追問的點

| 追問 | 回答方向 |
| --- | --- |
| 為什麼不用同步寫 DB？ | logging 是 observability / audit supporting path，同步寫會讓主請求受 log DB 影響 |
| duplicate 怎麼處理？ | 用 `pt_day + agent_id + time + id` 查重，但 check-then-insert 仍需 DB unique 兜底 |
| catch exception 後會不會 retry？ | 本輪未驗證 ack mode；若 exception 被吃掉，可能不觸發 retry / DLQ，所以不能宣稱可靠重試 |
| RequestLog 和 BetRecord MQ 差在哪？ | RequestLog 偏 audit；BetRecord 更接近交易 / report source data，failure policy 更嚴 |
| `ptDay` 為什麼重要？ | 分區查詢與 duplicate 都依賴它；用錯時間會導致查不到或重複 |
| 後台查詢有什麼風險？ | role / agent scope、7 天限制、partition keys、list / count 條件一致性 |
| 如果要把它做更穩，你會補什麼？ | 補 DB unique / upsert、consumer retry / DLQ、poison message metric、producer publish confirm、查詢條件測試與告警 |
| 這條可以放履歷嗎？ | 可保守放「參與 RequestLog RabbitMQ 非同步入庫與後台查詢支援」，不寫主導完整 MQ platform 或 exactly-once |

## Lead / Architect 追問

| 追問 | 回答方向 |
| --- | --- |
| 如果 request log 量很大，怎麼設計？ | 先分清查詢熱資料與冷資料；DB 分區 / 索引 / retention，或把長期查詢移到 log search / OLAP；避免後台直接掃大表 |
| 是否需要 outbox？ | 若 RequestLog loss 不可接受，可考慮 outbox / publish confirm；但 audit log 通常要先定義 loss tolerance，不一定和 money flow 同級 |
| poison message 怎麼處理？ | 不應只 catch-and-log；要有 retry limit、DLQ、可觀測 metric、重放工具或人工處理流程 |
| 權限怎麼避免資料外洩？ | controller / service 層要用 role / agent scope 控制可查範圍；log body 也要考慮敏感資訊遮罩與保留期限 |
| 如何驗證 list / count 一致？ | 查詢條件應共用條件 builder 或覆蓋測試；尤其 target / error / step / time range / partition keys 要一致 |

## 誇大陷阱

- 不講 exactly-once。
- 不講完整 RabbitMQ platform。
- 不講完整 DLQ / replay。
- 不講完整 money correctness。
- 不把 supervisor commits 當自己的實作。

## 與其他 case 的串接

- 對 `connect-bet-record-mq-ingestion`: BetRecord 偏交易 / report source data，RequestLog 偏 audit / observability；兩者都用 MQ，但 failure policy 不同。
- 對 `ugsoft-connector-api request-bet-record-mq-sync`: connector 偏上游 producer / job sync，admin-api RequestLog 偏下游 consumer / 後台查詢。
- 對 `antplay-slot-game-api request-log-rabbitmq-async`: 可以形成跨 domain 的 request log async story，但要各自講清楚 evidence，不混成同一套 owner claim。

## Step 4 結論

這條 flow 已具備正式面試素材，且後續 Step 5 claim gate 已完成。`ugsoft-admin-api` 本批三條代表 flows 均已 Step 5，project-level contribution claim consolidation refresh 也已完成。
