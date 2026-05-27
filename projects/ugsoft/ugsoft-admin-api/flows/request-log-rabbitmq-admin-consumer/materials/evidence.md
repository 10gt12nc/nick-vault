# Evidence - request-log-rabbitmq-admin-consumer Step 3

日期: 2026-05-27

## 掃描等級

- 本次深度: Level 2 單條 flow 深掃。
- 理由: Nick 指定單條 flow Step 3，需要建立 learning package，不是只排 candidate，也不是逐 commit 極限審計。
- 已做: 重讀 KB、Step 1、Step 2、project README、contribution consolidation、admin-api latest remote code snapshot、path-specific commit log、重要 diff、上游 connector producer context。
- 未做: 未驗證 RabbitMQ broker runtime、listener container ack mode、DLQ / retry interceptor、DB DDL / unique key、production data、完整 `ugsoft-connector-api` flow Step。

## KB / Vault 已重讀

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/ugsoft/ugsoft-admin-api/README.md`
- `projects/ugsoft/ugsoft-admin-api/step1-candidate-flows.md`
- `projects/ugsoft/ugsoft-admin-api/step2-flow-comparison.md`
- `projects/ugsoft/ugsoft-admin-api/contribution-claim-consolidation.md`

## Source Repo 狀態

### ugsoft-admin-api

來源 repo:

```text
/Users/nick/Git/ugsoft/ugsoft-admin-api
```

遠端狀態:

- `git fetch --all --prune` 第一次因 sandbox `.git/FETCH_HEAD` 權限失敗，approval 後成功。
- local branch: `main`
- local HEAD: `0cc62e0e1a040e69b1650079d9ecfe92dd64380d`
- `origin/main`: `b1b83f64ffc971cc838ef935867a5a2234e3d201`
- ahead / behind: `0 / 42`
- source working tree: 乾淨
- 判斷方式: 本機工作樹落後 `origin/main` 42 commits，因此本輪以 fetched `origin/main` object / log 作 latest code-backed 判斷；未 pull、未 checkout、未改 source repo。

### ugsoft-connector-api

來源 repo:

```text
/Users/nick/Git/ugsoft/ugsoft-connector-api
```

遠端狀態:

- `git fetch --all --prune` 第一次因 sandbox `.git/FETCH_HEAD` 權限失敗，approval 後成功。
- local branch: `Nick_Test`
- local HEAD: `c2cab730c0cd6ead6d92a038ef56f97987577059`
- `origin/main`: 不存在；遠端主要 refs 包含 `origin/master`、`origin/develop`、`origin/release/dev` 等。
- source working tree: 有既有未提交 / untracked 檔案，包含 `.DS_Store`、test / docs 類變更；本輪只讀，不改。
- 判斷方式: 只用 local `HEAD` 檢視 RequestLog producer context；不宣稱 connector-api latest production 行為。

## Code Paths - ugsoft-admin-api

| Path | 本輪用途 |
| --- | --- |
| `src/main/java/com/ps/domain/admin/rabbitMq/RequestLogListener.java` | consumer entry；parse JSON、建 `RequestLog`、查重、insert |
| `src/main/java/com/ps/domain/admin/rabbitMq/RequestLogListenerProcessor.java` | `@UseSchema` 查重與 `@Transactional` insert |
| `src/main/java/com/ps/domain/admin/rabbitMq/requestLog/RequestLogMqPayload.java` | RequestLog payload DTO context |
| `src/main/java/com/ps/domain/admin/config/RabbitMQConfig.java` | direct exchange / queue / binding |
| `src/main/java/com/ps/domain/common/constant/RabbitMq.java` | RequestLog exchange / routing key / queue constants |
| `src/main/java/com/ps/domain/manage/data/entity/RequestLog.java` | RequestLog entity fields |
| `src/main/java/com/ps/domain/admin/mapper/RequestLogMapper.java` | mapper interface |
| `src/main/resources/mapper/RequestLogMapper.xml` | list / count / duplicate / insert SQL |
| `src/main/java/com/ps/domain/admin/controller/RequestLogController.java` | admin query entry |
| `src/main/java/com/ps/domain/manage/service/RequestLogsService.java` | partition keys and query service |

## Upstream Context - ugsoft-connector-api

| Path | 本輪用途 |
| --- | --- |
| `src/main/java/com/ps/domain/api/agent/facade/AgentApiFacade.java` | publish provider request log message |
| `src/main/java/com/ps/domain/connector/util/ConnectorUtil.java` | publish connector client / callback request log message |
| `src/main/java/com/ps/common/constant/RabbitMq.java` | exchange / routing key / queue constants align with admin-api |
| `src/main/java/com/ps/common/rabbitMq/dto/RequestLogMessageDto.java` | MQ payload fields |
| `src/main/java/com/ps/common/rabbitMq/service/RabbitMqService.java` | object mapper -> JSON message |

## Commit Evidence

### Nick / `10gt12nc` direct evidence

| Commit | Date | Evidence |
| --- | --- | --- |
| `814b024` | 2026-01-16 | `feat(#774):RequestLog改丢rabbitmq非同步执行`；新增 / 調整 RequestLog listener、mapper、entity、XML |
| `f3a9d72` | 2026-01-19 | `feat(#774):RequestLog改丢rabbitmq非同步执行 G2`；加入 `RequestLogListenerProcessor`，把 query / insert 拆到 processor |
| `5f838f8` | 2026-01-19 | `fix(#774):RequestLog改丢rabbitmq非同步执行 G2`；調整 listener 呼叫 processor |
| `fa86544` | 2026-01-19 | `fix(#774):RequestLog改丢rabbitmq非同步执行 UAT GG`；RequestLog RabbitMQ 後續 UAT 修正，未逐行展開 |
| `821bc2e` | 2026-03-27 | `feat: BetRecord、requestLog Mq`；RequestLog MQ payload / mapper 相關後續調整，與 BetRecord MQ 同批 |

### Supervisor / team context, not Nick direct evidence

| Commit | Author | Evidence |
| --- | --- | --- |
| `0f72b92` | arnold | request-log key 格式修正；只作 current behavior context |
| `5410e0a` | arnold | request log 查詢修正 / step filter context；只作 current behavior context |
| `2149e41`、`8debd0b` | arnold | RequestLogController role / scope context；不當 Nick direct evidence |

## 已確認

- `RequestLogListener` 監聽 `RabbitMq.REQUEST_LOG_QUEUE_KEY`。
- listener 手動 parse JSON，讀取 `id`、`time`、`agentId`、`type`、`step`、`target`、URI、method、request / response 欄位、status、error message、elapsed time。
- `ptDay` 由 message `time` 計算，不是 consumer now。
- duplicate check 條件是 `pt_day + agent_id + time + id`。
- `insertRequestLog` 有 `@Transactional`。
- listener catch all exception 並 log；本輪沒有看到 rethrow。
- admin query 有時間區間限制與 agent scope，透過 `ptDays x agentIds` 建 partition keys。
- upstream connector context 會 publish RequestLog MQ message 到 `ugSoftRequestLog.direct` / `ugSoftRequestLog`。

## 待確認 / 不宣稱

- 未確認 RabbitMQ ack mode、retry interceptor、DLQ、dead-letter exchange。
- 未確認 DB DDL 是否有 `pt_day + agent_id + time + id` unique / PK。
- 未確認 production broker durable / confirm / return callback 策略。
- 未完整驗證 `ugsoft-connector-api` latest remote branch；producer context 只作本輪上游理解。
- 未把 `arnold` 後續 request-log 修正當 Nick direct evidence。

## Relationship Check

本輪事實變更:

- `request-log-rabbitmq-admin-consumer` Step 3 完成。
- `ugsoft-admin-api` Flow Track 目前狀態變成: `connect-bet-record-mq-ingestion` Step 5 完成，`request-log-rabbitmq-admin-consumer` Step 3 完成，下一步是同 flow Step 4。
- 發現並修正 Step 2 中「下一步是 request-log 但理由仍寫 BetRecord」的舊內容。

同步 / 不同步判斷:

- 已同步 project README、Step 1、Step 2、project consolidation、domain README、source inventory、flow audit、todo、capability map。
- 不更新 `05 / 08 / 04 / 17`: 本輪是單條 flow Step 3，不是 project contribution refresh / rolling resume package / salary strategy。
