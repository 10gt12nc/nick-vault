# request-log-rabbitmq-async Interview Notes

日期: 2026-05-21

## 1. Step 5 定位

本檔是 `request-log-rabbitmq-async` 的正式面試稿。主報告在 `flow.md`；履歷邊界看 `career-interview.md` 與 `materials/claim-boundary.md`。

Step 5 已追 #774 producer / consumer 重要 diff、current code 與 blame。兩個來源 repo 本輪嘗試 fetch 仍因內網不可達失敗，因此仍標示未確認最新遠端、依本地 refs / 本地 working tree 保守分析。

## 2. 30 秒版本

我會把 request log MQ 化講成 audit side effect 解耦，而不是單純用了 RabbitMQ。Provider API 的 balance、bet、settle、rollback 是主流程；request log 是 audit / observability。改成 MQ 後，主流程不用等 request log DB，但也要接受 eventual consistency，所以要補 dedupe、DLQ / retry、queue lag 和 consumer error 的治理。

## 3. 90 秒版本

這條 flow 是 `AgentApiFacade#execute` 呼叫第三方 provider 後，不管成功或失敗，都在 `finally` 收集 request、response、error 和 elapsed time，包成 `RequestLogMessageDto` 丟到 RabbitMQ。下游 admin-api consumer 監聽 `request-log` queue，parse JSON，依 `(pt_day, agent_id, time, id)` 查重後寫入 `pt_request_log`。

設計重點是 source of truth 邊界：bet record、wallet、provider response 才是交易事實；request log 只是排查 evidence。所以 MQ 失敗不應讓 bet / settle rollback，但 audit loss 要能被看見。這就是 async observability flow 的 trade-off：降低主流程耦合，換來 message delay、duplicate、consumer failure、schema compatibility 的治理成本。

## 4. 3 分鐘版本

這條 flow 的背景是第三方 agent API 都需要 request / response log，讓後台查詢、客服排查和事故 RCA 有 evidence。但如果 request log 同步寫 DB，DB latency、分表問題或連線異常會拖慢甚至干擾主交易流程。

改成 RabbitMQ 後，game-api producer 在 `AgentApiFacade#execute` 的 `finally` 裡做 audit publish。它會組 `RequestLogMessageDto`，內容包含 id、time、agentId、type、step、target、uri、request / response / error / elapsedTime，再由 `RabbitMqService` 序列化成 JSON AMQP message，用 direct exchange 與 routing key 投到 `request-log` queue。

consumer 在 admin-api，`RequestLogListener` 收 message 後 parse JSON，組成 `RequestLog`，依 message time 算 `ptDay`，再交給 processor 查重和 insert `pt_request_log`。這裡看到的是 at-least-once 傾向的設計：producer 盡力 publish，consumer 透過查重降低重複 insert，但 Step 3 還沒確認 DB unique key、publisher confirm、ack mode、DLQ。

面試時我會強調這不是 money correctness 的主線，而是 observability pipeline。MQ publish 失敗不應 rollback provider API，因為 request log 不是交易 source of truth；但是 audit loss 不能無聲發生，至少要有 publish failure metric、consumer error、queue lag、DLQ 或重放策略。這樣才能清楚回答 owner trade-off：哪些 failure 要隔離，哪些 failure 要觀測和補償。

## 5. STAR

| 區塊 | 說法 |
| --- | --- |
| Situation | Provider API request log 對排查很重要，但同步寫 DB 可能讓 audit DB 風險進入主交易流程。 |
| Task | 將 request log 改成 RabbitMQ 非同步投遞，讓主流程與 audit 落庫解耦。 |
| Action | 追 producer、DTO、exchange / queue / routing key、admin-api consumer、查重與 insert path，整理 message lifecycle 與 failure window。 |
| Result | 可清楚說明主流程不等待 audit DB、consumer eventual insert、message duplication / delay / failure 的治理邊界；不誇大成 exactly-once 或完整 MQ 平台 owner。 |

## 6. Failure Scenarios

| Scenario | 風險 | 面試回應 |
| --- | --- | --- |
| Provider call 成功後 JVM crash | 交易已發生但 request log 沒 publish | 這是 audit loss；若要求更高可靠性，要引入 outbox 或持久化投遞。 |
| MQ publish 失敗 | 後台少一筆 request log | 不 rollback money flow，但要 alert / metric / fallback。 |
| Routing key mismatch | message 送不到 consumer | 用 config contract、啟動檢查或部署驗證防止 producer / consumer key 不一致。 |
| Consumer parse failed | message 被 retry 或進錯誤狀態 | 補 schema version、DLQ、error alert、replay tooling。 |
| 重複消費 | 重複 request log | consumer dedupe + DB unique key；只先查再 insert 不夠硬。 |
| Queue lag | 後台查不到最新 log | queue depth / lag metrics / consumer health alert。 |

## 7. Senior 追問

| 問題 | 回答 |
| --- | --- |
| Request log MQ 掛了，bet / settle 要不要 rollback？ | 不要。request log 是 observability，不是交易事實；應隔離主流程，但要告警與補償 audit loss。 |
| 你怎麼界定 source of truth？ | Bet record、wallet state、provider transaction / response 才是交易 source of truth；RabbitMQ message 和 `pt_request_log` 是排查 evidence。 |
| 這條 flow 的一致性是什麼？ | 不是強一致，而是 eventual consistency。主流程完成後，request log 稍後由 consumer 落庫。 |
| 怎麼處理 duplicate message？ | 目前看到 consumer 查 `(pt_day, agent_id, time, id)`；更完整要 DB unique key 或 idempotent upsert。 |
| 如果 consumer 壞了怎麼知道？ | 要看 queue lag、consumer error、DLQ count、last consume time；本輪未確認這些已落地，所以只能講 owner 改善方向。 |
| 為什麼不用 Kafka？ | 這條是固定 routing 的 audit log queue，Direct exchange + queue 已能支撐單一 consumer 落庫；除非要多 consumer fan-out / stream retention / replay，再評估 Kafka。 |

## 8. Lead / Architect 追問

| 問題 | 回答方向 |
| --- | --- |
| 如果 audit log 也不能丟，怎麼升級？ | 用 outbox pattern：主流程本地 transaction 寫 outbox，再由 relay publish；或至少 publisher confirm + retry storage。 |
| 同步 DB vs MQ 怎麼選？ | 如果 log 是交易條件，同步或 transaction-bound evidence 才合理；如果是 audit / RCA，用 MQ 解耦更適合，但要治理 delay / loss。 |
| 如何避免 producer / consumer schema 不相容？ | DTO version、backward-compatible fields、consumer tolerant parser、contract test。 |
| request body 很大或有敏感資料呢？ | payload 截斷、欄位遮罩、PII / secret filter，並限制 log retention。Step 3 只看到長度截斷，敏感資料遮罩未確認。 |
| 怎麼證明這個改造有價值？ | 可用主流程 latency、request log DB failure 隔離效果、queue lag、consumer error rate、audit availability 衡量；但本輪沒有 metric，不寫改善百分比。 |

## 9. 可用金句

- request log 是 RCA evidence，不是 money source of truth。
- MQ 解耦的是 audit side effect，不是替交易流程提供 exactly-once。
- 主流程不該因 audit DB 失敗 rollback，但 audit loss 也不該無聲發生。
- At-least-once 的重點不是不重送，而是 consumer 要能 idempotent。
- 從同步 DB 改 MQ，不是免費的穩定性；它把風險從 latency coupling 轉成 observability governance。

## 10. 不可踩線

- 不說完整 RabbitMQ 平台 owner。
- 不說 exactly-once 已做到。
- 不說 outbox 已落地。
- 不說完整 DLQ / retry / alert 已確認。
- 不說 request log 能保證交易一致性。
- 不說 routing key / queue key 最終格式修正是 Nick 完成；這是後續他人 context evidence。
- 不寫量化改善，除非後續補 metric / ticket / production evidence。

## 11. 下一步

```text
antplay antplay-slot-game-api runtime-rtp-darkpool-player-control Step 3
```
