# request-log-rabbitmq-admin-consumer Evidence

日期: 2026-05-28

## Source Repo 狀態

| Repo | Branch | Local HEAD | Local remote ref | Ahead / behind | Working tree | Fetch |
| --- | --- | --- | --- | --- | --- | --- |
| `antplay-slot-admin-api` | `main` | `2e15503d88de3af133cce17fbf3ff80262678419` | `origin/main` = `2e15503d88de3af133cce17fbf3ff80262678419` | `0 / 0` | clean | 嘗試 fetch 失敗，依本地 refs |
| `antplay-slot-game-api` | `develop` | `079aa6603b50db3c185e383295ca5966bbe272fb` | `origin/develop` = `079aa6603b50db3c185e383295ca5966bbe272fb` | `0 / 0` | clean | 嘗試 fetch 失敗，依本地 refs |

注意: 不記錄內網 remote URL / IP / sensitive detail。

## Vault 掃描範圍

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/CONVENTIONS.md`
- `projects/antplay/antplay-slot-admin-api/README.md`
- `step1-candidate-flows.md`
- `step2-flow-comparison.md`
- `contribution-claim-consolidation.md`
- `projects/antplay/antplay-slot-game-api/flows/request-log-rabbitmq-async/flow.md`
- `projects/antplay/antplay-slot-game-api/flows/request-log-rabbitmq-async/materials/evidence.md`

## Source Code 掃描範圍

Admin-api consumer:

- `RequestLogListener`
- `RequestLogListenerProcessor`
- `RequestLogMapper`
- `RequestLogMapper.xml`
- `RequestLog`
- `RequestLogPK`
- `RabbitMQConfig`
- `RabbitMq`
- `RequestLogController`
- `RequestLogsService`
- `UseSchema`
- `SchemaRouteAspect`

Game-api upstream context:

- `AgentApiFacade#sendRequestLogMq`
- `RequestLogMessageDto`
- `RabbitMqService`
- `RabbitMQConfig`
- `RabbitMq`

## Code Evidence

| Code path | Evidence | 判斷 |
| --- | --- | --- |
| `RequestLogListener#receive` | `@RabbitListener(queues = RabbitMq.REQUEST_LOG_QUEUE_KEY)` | admin-api consumer entry 已確認 |
| `RequestLogListener#receive` | parse JSON fields: `id`、`time`、`agentId`、`step`、`target`、body、status、error、elapsedTime | message contract consumer side 已確認 |
| `DateTimeUtil.getLocalDate(time)` | 由 message time 算 `ptDay` | partition / query key 已確認 |
| `RequestLogListenerProcessor#queryRequestLogExists` | `@UseSchema` + mapper query | schema route + dedupe check 已確認 |
| `RequestLogListenerProcessor#insertRequestLog` | `@Transactional` + `@UseSchema` | insert transaction boundary 已確認 |
| `RequestLogMapper.xml#queryRequestLogExists` | WHERE `pt_day`, `agent_id`, `time`, `id` | application-level dedupe key 已確認 |
| `RequestLogMapper.xml#insertRequestLog` | INSERT INTO `pt_request_log` | DB insert path 已確認 |
| `RabbitMQConfig` | direct exchange + durable queue + routing key binding | MQ topology 已確認 |
| `RequestLogController` / `RequestLogsService` | 後台依 agent / date / target / error 查 `pt_request_log` | admin query context 已確認 |
| `SchemaRouteAspect` | 從 `agentId` / `getAgentId()` route schema | multi-schema boundary 已確認 |

## Commit Evidence

| Commit | Repo | Author | Evidence | Claim 判斷 |
| --- | --- | --- | --- | --- |
| `814b024` | admin-api | `10gt12nc` | #774 建立 RequestLog RabbitMQ consumer 與 mapper insert | Nick direct evidence |
| `a66007d` | admin-api | `10gt12nc` | #774 同題 consumer 初始脈絡 | Nick direct evidence |
| `f3a9d72` | admin-api | `10gt12nc` | G2 調整，拆出 processor 等修正 | Nick direct evidence |
| `5f838f8` | admin-api | `10gt12nc` | G2 修正 listener path | Nick direct evidence |
| `fa86544` | admin-api | `10gt12nc` | UAT config 修正，補 RabbitMQ config / constants | Nick direct evidence |
| `d3e0002` / `71fff7b` | game-api | `10gt12nc` | #774 producer、DTO、message service、RabbitMQ config | upstream Nick direct evidence |
| `bce581d` | admin-api | `10gt12nc` | MyBatis 導入，request log mapper supporting context | supporting evidence |

## 已確認

- admin-api consumer 會監聽 `request-log` queue。
- consumer parse JSON message，建立 `RequestLog`。
- consumer 依 `time` 算 `ptDay`。
- consumer 先查重，查無才 insert。
- insert method 有 `@Transactional`。
- `@UseSchema` 會用 `agentId` 做 schema route。
- 後台查詢會讀 `pt_request_log`。
- game-api producer path 已用既有 flow 與本地 code 補定位。

## 待確認

- Rabbit listener ack mode、retry、DLQ / requeue policy。
- `pt_request_log` DDL 與 unique key。
- queue lag / consumer error / DLQ count 是否有正式監控。
- message schema versioning。
- producer / consumer 是否在 production deploy 上有相容性檢查。

## Evidence Level

- Flow: 真實開發過 + code-backed。
- Nick direct evidence: admin-api `814b024`、`a66007d`、`f3a9d72`、`5f838f8`、`fa86544`；game-api `d3e0002`、`71fff7b` 作 upstream direct evidence。
- Context evidence: 既有 game-api `request-log-rabbitmq-async` flow。
- 不可升級: 完整 RabbitMQ platform、exactly-once、outbox、完整 DLQ / retry / alert owner。
