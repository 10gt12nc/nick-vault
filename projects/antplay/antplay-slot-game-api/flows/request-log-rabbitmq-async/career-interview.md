# request-log-rabbitmq-async Career / Interview

日期: 2026-05-21

## 0. 定位

- Flow: `request-log-rabbitmq-async`
- 狀態: Step 5 已完成
- 證據層級: 真實開發過 + code-backed；Nick / `10gt12nc` 有 game-api producer 與 admin-api consumer 相關 direct commits
- 履歷狀態: 可作 `antplay-slot-game-api` project-level async audit / observability evidence；本輪已回填 project-level consolidation，但不直接單獨寫進 05 / 08

## 1. 30 秒說法

這條 case 是把 provider API 的 request / response log 從主交易流程同步寫 DB，改成 RabbitMQ 非同步投遞。重點不是「用了 MQ」，而是把 audit side effect 和 balance、bet、settle、rollback 這些主流程解耦：MQ 或 request log DB 出問題時，不應該反向拖垮交易；但 audit log 也不能無聲消失，所以要補 queue lag、consumer error、DLQ / retry 和 dedupe 的 owner 邊界。

## 2. 90 秒說法

AntPlay slot game API 會透過 `AgentApiFacade#execute` 呼叫第三方 provider，例如 balance、bet、settle、rollback。這些呼叫都需要留下 request / response / error / elapsed time，讓後台查詢和事故 RCA 有 evidence。

同步寫 DB 的問題是 audit DB、分表或連線慢會拖住主交易流程。#774 之後，game-api 在 `finally` 裡把 log 包成 `RequestLogMessageDto`，透過 `RabbitTemplate#convertAndSend` 發到 `request-log.direct` exchange 和 `request-log` routing key；admin-api 的 `RequestLogListener` 消費 queue，parse JSON 後查重，再 insert `pt_request_log`。

Senior 角度我會把它定義成 observability flow，不是 money source of truth。MQ publish 失敗不應 rollback bet / settle，但要有告警和補寫策略。改成 async 後，後台短時間查不到 log、message 重複、consumer parse / insert 失敗都是預期要治理的 failure window。

## 3. 3 分鐘說法

這條 flow 的背景是 provider API request log 既重要，又不應該站在主交易流程的 critical path 上。對 slot runtime 來說，balance、bet、settle、rollback 的交易結果要看 provider response、bet record、wallet state；request log 只是 audit / observability，用來做後台查詢、客服排查和事故 RCA。

實作上，game-api 的 `AgentApiFacade#execute` 是第三方 agent API 的共用呼叫入口。provider call 成功或失敗後，`finally` 會收集 request、response、error message 和 elapsed time，呼叫 `sendRequestLogMq`。這個方法會建立 `RequestLogMessageDto`，經由 `RabbitMqService` 序列化成 JSON AMQP message，再用 direct exchange 和 routing key 投遞到 `request-log` queue。

下游是 admin-api 的 `RequestLogListener`。consumer 收到 message 後 parse 成 request log model，依 message time 算 `ptDay`，再用 `(pt_day, agent_id, time, id)` 查重，沒有重複才 insert `pt_request_log`。這表示目前看到的語意比較接近 at-least-once + consumer dedupe，而不是 exactly-once。

這個設計的取捨是：同步寫 DB 比較直覺，寫完立刻可查，但會把 audit DB 的延遲、分表錯誤或連線問題帶進交易主線；MQ 非同步可以隔離主流程和 audit DB，但要承擔 eventual consistency。比如 provider call 已成功但 JVM 在 publish 前掛掉，或 MQ publish 失敗，就會有 audit loss；consumer 掛掉時 queue 會堆積；message schema 改了，consumer 可能 parse 失敗；重送時還要靠 idempotent insert 或 DB unique key 防重。

如果我是 owner，我會把責任切清楚：request log 失敗不 rollback money flow，但要有 publisher confirm / mandatory return、DLQ / retry、consumer error metrics、queue lag alert，以及 DB unique key。這樣面試時可以講清楚「哪些 failure 不應影響主交易，哪些 failure 必須被觀測與修復」。

## 4. STAR

| 區塊 | 內容 |
| --- | --- |
| Situation | Provider API request log 原本容易和主流程耦合；audit DB 或 request log 寫入問題可能拖慢 balance / bet / settle / rollback。 |
| Task | 將 request / response / error log 從同步落庫改為 RabbitMQ 非同步投遞，同時保留後台查詢與事故排查需要的 audit trail。 |
| Action | 在 game-api producer 端整理 `RequestLogMessageDto`、exchange / routing key / queue config 與 publish path；在 admin-api consumer 端接 message、parse、查重並寫入 `pt_request_log`；分析 MQ failure、duplicate、delay 與 source-of-truth 邊界。 |
| Result | 主交易流程和 audit DB 風險解耦；request log 轉為 eventual consistency 的 observability pipeline。可面試講 reliability trade-off，但不宣稱完整 exactly-once / outbox / DLQ 已落地。 |

## 5. Failure Scenarios

| Scenario | 面試回答重點 |
| --- | --- |
| Provider 成功後，MQ publish 前 JVM crash | 主流程可能已完成但 log 沒送；若 audit 完整性很重要，可考慮 outbox 或本地持久化投遞。 |
| `convertAndSend` 失敗 | 不應 rollback bet / settle，因為 request log 不是交易事實；但要有 publisher confirm、error metric、alert 或 fallback。 |
| Routing key / queue key 不一致 | Producer 送出但 consumer 收不到；要用 config contract test、啟動檢查或部署前驗證。 |
| Consumer parse 失敗 | message schema 相容性問題；應補 schema version、DLQ、錯誤告警與重放策略。 |
| Consumer 重複消費 | RabbitMQ 常見是 at-least-once；要用 idempotent insert 和 DB unique key，而不只靠先查再 insert。 |
| Queue lag | 後台短時間查不到 log；要用 queue depth / lag metrics 判斷是正常延遲還是 consumer 故障。 |

## 6. Senior / Lead 追問

| 追問 | 回答方向 |
| --- | --- |
| MQ send 失敗要不要讓下注失敗？ | 不要。request log 是 audit，不是 money source of truth；失敗要告警與補寫，不應反向 rollback 主流程。 |
| 這是不是 exactly-once？ | 不是。只能保守說 producer publish + consumer dedupe；完整 exactly-once / outbox 未確認，也不應用 RabbitMQ 自動保證。 |
| 查重 SQL 夠不夠？ | 先查再 insert 能降低重複，但併發或重送下仍建議 DB unique key 才是硬保護。 |
| 為什麼不用同步 DB？ | 同步 DB 讓 audit DB 風險進入交易 critical path；MQ 解耦後犧牲即時一致性，換取主流程穩定性。 |
| Request log 能不能當交易事實？ | 不能。交易事實仍看 bet record、wallet / provider transaction、provider response；request log 是 RCA evidence。 |
| 如果只能補一件事，先補什麼？ | 先補可觀測性：publish failure、consumer error、queue lag、DLQ；因為這條 flow 的最大風險是 audit loss 無聲發生。 |

## 7. 履歷保守 bullet 候選

可回填 project-level consolidation:

- 參與 AntPlay slot game API request log RabbitMQ 非同步化，將 provider API audit log 從主交易流程同步寫庫改為 MQ 投遞與下游 consumer 落庫，降低主流程與 audit DB 的耦合，並整理 message duplication、consumer delay 與 observability loss 邊界。

不建議單獨寫成完整主成果；可併入「game runtime / betting settlement / transfer wallet / async log」大 bullet。

## 8. 不可誇大

- 不寫主導完整 RabbitMQ architecture。
- 不寫 exactly-once / outbox 已完成。
- 不寫完整 event-driven platform owner。
- 不寫 request log 能保證交易一致性；它只是 audit / observability。
- 不寫有完整 DLQ / retry / alert；Step 5 未補到已落地 evidence。
- 不把 2026-01-21 routing key / queue key 最終格式修正寫成 Nick 完成，該修正屬他人 context evidence。

## 9. 下一步

Step 5 已完成，這條 flow 可回填 project-level async audit / observability claim。下一步回同 project Step 2 ranking，做 Rank 4 `bet-record-sharding-schema-route Step 3`。

```text
antplay antplay-slot-game-api bet-record-sharding-schema-route Step 3
```
