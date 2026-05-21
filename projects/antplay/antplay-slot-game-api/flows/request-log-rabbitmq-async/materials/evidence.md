# request-log-rabbitmq-async Evidence

日期: 2026-05-21

## 1. Source Repo 狀態

| Repo | Branch | Local HEAD | Local remote ref | Ahead / behind | Working tree | Fetch |
| --- | --- | --- | --- | --- | --- | --- |
| `antplay-slot-game-api` | `develop` | `079aa66` | `origin/develop` = `079aa66` | `0 / 0` | clean | 本輪嘗試 fetch 失敗，依本地 refs |
| `antplay-slot-admin-api` | `main` | `2e15503` | `origin/main` = `2e15503` | `0 / 0` | clean | 本輪嘗試 fetch 失敗，依本地 refs |

注意: 不記錄內網 remote URL / IP / sensitive detail。

## 2. 掃描範圍

Vault:

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `projects/antplay/antplay-slot-game-api/README.md`
- `step1-candidate-flows.md`
- `step2-flow-comparison.md`
- `contribution-claim-consolidation.md`

Source:

- game-api `AgentApiFacade#execute`
- game-api `AgentApiFacade#sendRequestLogMq`
- game-api `RabbitMQConfig`
- game-api `RabbitMq`
- game-api `RabbitMqService`
- game-api `RequestLogMessageDto`
- admin-api `RequestLogListener`
- admin-api `RequestLogListenerProcessor`
- admin-api `RequestLogMapper`
- admin-api `RequestLogMapper.xml`
- admin-api `RabbitMq`
- path-specific git log / selected commit diff

## 3. Code Evidence

| Code path | Evidence | 判斷 |
| --- | --- | --- |
| `AgentApiFacade#execute` | provider API call 後在 `finally` 呼叫 `sendRequestLogMq` | 成功 / 失敗都盡量送 audit log |
| `AgentApiFacade#sendRequestLogMq` | 組 `RequestLogMessageDto`，`convertAndSend` 到 exchange / routing key | producer 主路徑已確認 |
| `RabbitMqService#convertToQueueMessage` | DTO -> JSON AMQP message，content type `application/json` | message serialization 已確認 |
| `RabbitMQConfig` | `DirectExchange` + durable `Queue` + binding | exchange / queue / routing key 已確認 |
| `RequestLogMessageDto` | id / time / agent / type / step / target / uri / body / response / status / error / elapsedTime | message contract 已確認 |
| admin-api `RequestLogListener#receive` | `@RabbitListener(queues = RabbitMq.REQUEST_LOG_QUEUE_KEY)`，parse JSON，組 `RequestLog` | consumer 已確認 |
| admin-api `RequestLogListenerProcessor` | `queryRequestLogExists` 後 `insertRequestLog` | consumer dedupe / insert path 已確認 |
| admin-api `RequestLogMapper.xml` | `(pt_day, agent_id, time, id)` 查重，insert `pt_request_log` | 落庫 SQL 已確認 |

## 4. Commit / History Evidence

| Commit | Repo | Author | Evidence | Claim 判斷 |
| --- | --- | --- | --- | --- |
| `d3e0002` | game-api | `10gt12nc` | #774，RequestLog 改丟 RabbitMQ 非同步執行；新增 producer、DTO、service、config，移除同步 log service path | Nick direct evidence |
| `71fff7b` | game-api | `10gt12nc` | #774 同題 commit，內容與 `d3e0002` 同脈絡 | Nick direct evidence |
| `7c9d0f6` | game-api | Arnold | 調整 request-log exchange / routing / queue key 命名 | context evidence |
| `a66007d` / `814b024` | admin-api | `10gt12nc` | #774 request log RabbitMQ consumer 初始脈絡 | Nick direct evidence |
| `f3a9d72` / `5f838f8` / `fa86544` | admin-api | `10gt12nc` | #774 G2 / UAT 修正脈絡 | Nick direct evidence |
| `0f72b92` | admin-api | Arnold | rabbitmq request-log key 格式修正 | context evidence |

## 5. 已確認

- `AgentApiFacade#execute` 是 provider API 共用呼叫入口，包含 balance、bet、settle、rollback 類 single-wallet callback。
- request log MQ 發送在 `finally` 中執行。
- MQ send / serialization failure 被 catch，只 log error，不阻斷主流程。
- message payload 有 request / response / error / elapsedTime。
- admin-api consumer 會將 message 寫入 `pt_request_log`。
- consumer 有查重邏輯，但本輪未確認 DB unique constraint。

## 6. 待確認

- RabbitMQ publisher confirm / mandatory / return callback 是否配置。
- Consumer ack mode、retry、DLQ、requeue policy。
- Queue lag / failure alert / monitoring。
- `pt_request_log` 是否有 matching unique key。
- message schema 變更時是否有 versioning。
- admin-api consumer deployment 是否和 game-api producer 同步上線。

## 7. Evidence Level

- Flow: 真實開發過 + code-backed。
- Nick direct evidence: game-api `d3e0002` / `71fff7b`，admin-api `a66007d` / `814b024` / `f3a9d72` / `5f838f8` / `fa86544`。
- Context evidence: game-api `7c9d0f6`，admin-api `0f72b92`。
- 履歷結論: 可回填 project-level async log / audit / observability claim；不單獨寫完整 MQ architecture owner。
