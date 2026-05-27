# request-log-rabbitmq-admin-consumer Career Interview

日期: 2026-05-27

## 狀態

目前完成 Step 4。這份是可練習的面試稿與保守履歷素材，但還不是 Step 5 claim gate；正式是否回填 `05 / 08` 要等 Step 5 或 project-level consolidation refresh 判斷。

Evidence 層級: `真實開發過 + code-backed`。Nick / `10gt12nc` 有 RequestLog RabbitMQ 非同步化 direct commits；上游 producer 與後台查詢為 code-backed context；`arnold` 後續修正只作主管 / team context。

## 30 秒版

我在 UGSoft 後台 API 參與過 RequestLog 從同步寫入改成 RabbitMQ 非同步入庫的 flow。這條 flow 會把 provider / connector request-response log 先丟 MQ，再由 admin-api consumer 寫入 `pt_request_log`，後台用 agent scope、時間區間與分區查詢。這裡我會特別注意 duplicate check、`ptDay` 分區、consumer exception 是否會影響 retry / DLQ，以及 list / count 查詢條件一致性；因為 request log 是 audit / observability 資料，failure policy 和 wallet / bet settle 這類 money flow 不一樣。

## 90 秒版

我在 UGSoft 後台 API 參與過 RequestLog RabbitMQ 非同步入庫。這條 flow 的背景是，provider 或 connector 的 request / response log 如果同步寫 DB，主請求會被 log DB、分表或慢查詢拖住；所以設計上會由上游把 request id、agent、step、target、request / response body、status、elapsedTime 等 payload 丟到 RabbitMQ，再由 admin-api 的 `RequestLogListener` 消費，依 event time 算 `ptDay`，用 `pt_day + agent_id + time + id` 做查重，最後寫入 `pt_request_log`，供後台 `/request_log` 依 agent scope、時間區間、target、error / step 條件查詢。

我看這條 flow 時不會只把它當 logging。它的重點是非同步化後要補 owner 判斷：第一，RequestLog 是 audit / observability supporting data，不是 money source of truth，所以 failure policy 可以和 BetRecord 或 wallet 不同；第二，check-then-insert 只能做基本 idempotency，若要更穩，要靠 DB unique key、insert-ignore / upsert 或 DLQ / replay 補強；第三，listener 目前 catch exception 後只 log，是否會 retry 要看 RabbitMQ ack mode / container 設定，不能直接宣稱已有可靠重試。這些邊界講清楚，才是這條 flow 的 Senior 價值。

## 3 分鐘版

這條 case 我會用「audit log 非同步化」來講。UGSoft 有 provider / connector 的 request-response log，內容包含 request id、agentId、step、target、URI、method、header、body、response、status、error message 和 elapsed time。這些資料對排查問題、客服查詢、provider 對接很重要，但它不是交易 source of truth，所以不適合讓主請求卡在同步 DB 寫入上。

完整流程是：上游 request flow 結束後，透過 RabbitTemplate 把 `RequestLogMessageDto` 發到 `ugSoftRequestLog.direct`；admin-api 的 `RequestLogListener` 監聽 queue，收到 JSON message 後 parse 欄位，依 message 裡的 `time` 算 `ptDay`，組成 `RequestLog` entity。接著它會呼叫 processor 查 `pt_request_log` 是否已經存在，用的是 `pt_day + agent_id + time + id`，不存在才呼叫 insert。後台查詢 `/request_log` 時，controller 會先依角色算 agent scope，也限制時間範圍，再由 service 建出 `ptDays x agentIds` 的 partition keys 交給 mapper 查 list / count。

這條 flow 的技術重點有三個。第一是 idempotency：MQ consumer 可能遇到 redelivery 或 producer retry，所以要查重；但目前是 query 跟 insert 分開呼叫，不是 atomic exactly-once。如果要更嚴謹，我會期待 DB 層有 unique / PK 兜底，或改成 insert-ignore / upsert 類策略。第二是 failure policy：listener catch exception 後只 log，這代表能不能 retry 取決於 RabbitMQ listener 的 ack mode 和 retry / DLQ 設定；如果 exception 被吃掉，可能不會觸發 redelivery，這對 audit log 來說要明確定義能不能接受 loss。第三是查詢一致性：後台不是單純 select，還有 role / agent scope、時間區間、partition key、list / count 條件一致性，這些如果不一致，客服看到的頁數或篩選結果會不準。

這個 case 我不會誇大成完整 RabbitMQ platform 或 exactly-once。我會把它定位成：我參與過 request log 非同步入庫與後台查詢支援，能說清楚 async audit log 的 idempotency、partition、retry / DLQ、observability 和查詢一致性邊界。它也能和 BetRecord MQ case 對照：BetRecord 更接近交易 / report source data，RequestLog 則偏 audit / observability，所以兩者 failure policy 不應該講成一樣。

## STAR 版

Situation: UGSoft provider / connector request-response log 若同步寫 DB，會把主請求和 logging DB 耦合，且後台仍需要依 agent、時間與分區查詢 request log。

Task: 把 RequestLog 改成 RabbitMQ 非同步入庫，同時保留後台查詢能力，並處理 duplicate、partition、consumer failure 與不可誇大的可靠性邊界。

Action: 參與 RequestLog RabbitMQ listener / processor / mapper 調整；consumer 解析 MQ message，依 event time 算 `ptDay`，用 `pt_day + agent_id + time + id` 查重後寫入 `pt_request_log`；後台查詢則維持 role / agent scope、時間窗口與 partition keys。

Result: 主請求與 request log 入庫耦合降低，後台仍可查 audit log；面試時可延伸到 idempotency、check-then-insert race、RabbitMQ ack / retry / DLQ、list / count consistency 與 audit log failure policy。

## 可放履歷的保守素材

- 參與 UGSoft RequestLog RabbitMQ 非同步入庫與後台查詢支援，處理 request / response audit log 的 consumer、分區、重複檢查與 DB insert。
- 維護後台非同步資料處理 flow，關注 MQ consumer 的 idempotency、retry / failure window、partition query 與 observability 邊界。

## 可面試講

- 為什麼 audit log 適合非同步化，不應阻塞主請求。
- `pt_day + agent_id + time + id` duplicate check 能防哪些重送，不能保證哪些 race。
- listener catch exception 後，如果沒有 rethrow，對 RabbitMQ ack / retry / DLQ 可能代表什麼。
- 後台查詢 request log 時，為什麼要同時看 role / agent scope、time range、partition key、list / count 條件。
- RequestLog 和 BetRecord MQ 的差異: 前者偏 audit / observability，後者更貼近交易資料正確性。

## Senior 追問回答

| 追問 | 回答 |
| --- | --- |
| 為什麼不用同步寫 DB？ | RequestLog 是 audit / observability supporting data，同步寫會讓主請求被 log DB、分表或慢查詢拖住；非同步化可以降低耦合。 |
| 這樣會不會掉 log？ | 可能，所以要定義 failure policy。若 producer publish 失敗、consumer parse 失敗、insert 失敗，audit log 可能缺失；要看業務是否接受，不能把它講成 money flow 等級。 |
| duplicate 怎麼做？ | 目前用 `pt_day + agent_id + time + id` 查重，能防多數重送；但 query + insert 分開不是 atomic，嚴謹上要靠 DB unique / insert-ignore / upsert 兜底。 |
| catch exception 後會 retry 嗎？ | 本輪 code 看到 listener catch all exception 並 log，沒有看到 rethrow；是否 retry 要看 listener container ack mode / retry / DLQ 設定，所以不能宣稱可靠重試。 |
| `ptDay` 為什麼用 message time？ | RequestLog 要依事件發生時間落到對應分區；如果用 consumer now，延遲消費或補送資料會落錯分區，後台也可能查不到。 |
| 和 BetRecord MQ 差異？ | BetRecord 更接近交易 / report source data，failure policy 要更嚴；RequestLog 偏 audit / troubleshooting，可以接受的補償策略不同，但仍要有 observability。 |
| 後台查詢有什麼坑？ | role / agent scope、time range、partition key、list / count 條件要一致；否則後台頁數、篩選或客服排查結果會不準。 |

## 反問面試官

- 你們的 request / audit log 類資料，是同步寫 DB、MQ 非同步、還是走集中式 log pipeline？
- 對 audit log loss 的容忍度怎麼定義？有沒有 DLQ / replay / alert？
- MQ consumer 的 idempotency 通常靠 application check、DB unique，還是 outbox / inbox pattern？
- 後台查詢大量 request log 時，分區、索引與資料保留策略怎麼設計？

## 不可誇大

- 不說主導完整 RabbitMQ architecture。
- 不說已建立完整 exactly-once、outbox、DLQ 或 replay 機制。
- 不說這條 flow 是 wallet / money source of truth。
- 不把 `arnold` 的 request-log key / query 修正當 Nick direct evidence。

## Step 4 結論

這條 flow 已可作非 iwin 的 RabbitMQ / async audit / admin query 面試 case。下一步若繼續同 flow，應做 Step 5 claim gate，確認單條 flow 能否回填 project-level claim，以及哪些說法仍只能保留面試素材。
