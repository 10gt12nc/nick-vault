# Evidence - connect-bet-record-mq-ingestion

## 任務

- 任務：`ugsoft ugsoft-admin-api connect-bet-record-mq-ingestion Step 3`
- 日期：2026-05-27
- 掃描等級：Level 2 Flow 深掃；未做 Level 3 逐檔逐行 / 每個 commit diff 全量追查。
- 目標：建立 `ugsoft-admin-api` BetRecord MQ consumer /入庫 / quota update supporting flow 的 Step 3 learning package。

## Step 4 補充掃描

- 任務：`ugsoft ugsoft-admin-api connect-bet-record-mq-ingestion Step 4`
- 日期：2026-05-27
- 掃描等級：Level 2 Flow 深掃 / interview case；未做 Level 3 逐檔逐行。
- 已重讀 Step 3 `flow.md`、`career-interview.md`、`materials/evidence.md`、`materials/interview.md`、`materials/claim-boundary.md`、`materials/decision-notes.md`。
- 已重新執行 `/Users/nick/Git/ugsoft/ugsoft-admin-api` 的 `git fetch --all --prune`；第一次受 sandbox 權限限制失敗後，經 approval 成功更新 remote refs。
- source repo 狀態：local branch `main`，local HEAD `0cc62e0e1a040e69b1650079d9ecfe92dd64380d`；`origin/main` `b1b83f64ffc971cc838ef935867a5a2234e3d201`；local vs `origin/main` `0 / 42`；source working tree 乾淨。
- Step 4 未新增 code path evidence；本輪目標是把 Step 3 evidence 轉成正式 30 秒 / 90 秒 / 3 分鐘面試 case、STAR、追問、回答要點與誇大邊界。

## Step 5 補充掃描

- 任務：`ugsoft ugsoft-admin-api connect-bet-record-mq-ingestion Step 5`
- 日期：2026-05-27
- 掃描等級：Level 2 Flow 深掃 / claim gate；未做 Level 3 逐檔逐行。
- 已重讀 Step 4 `flow.md`、`career-interview.md`、`materials/evidence.md`、`materials/interview.md`、`materials/claim-boundary.md`、`materials/decision-notes.md`，並重讀 project README、Step 1、Step 2、project contribution consolidation。
- 已重新執行 `/Users/nick/Git/ugsoft/ugsoft-admin-api` 的 `git fetch --all --prune`；第一次受 sandbox 權限限制失敗後，經 approval 成功更新 remote refs。
- source repo 狀態：local branch `main`，local HEAD `0cc62e0e1a040e69b1650079d9ecfe92dd64380d`；`origin/main` `b1b83f64ffc971cc838ef935867a5a2234e3d201`；local vs `origin/main` `0 / 42`；source working tree 乾淨。
- 已補看 path-specific history 與重要 diff：`98ad763`、`f641b04`、`821bc2e`、`c99a325` by `10gt12nc`；並對照 latest `origin/main` `ConnectBetRecordConsumerService`、`BetRecordMapper.xml`、`QuotaUpdateConsumer`、`BetRecord` entity。
- Step 5 結論：Nick direct evidence 足以支撐 BetRecord MQ 初版、consumer 調整、mapper 查重與 currency default；latest quota monitoring、id 生成、amount normalization 屬 `arnold` / team current behavior，只能作 supporting context。

## KB / Vault 已重讀

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/CONVENTIONS.md`
- `projects/ugsoft/README.md`
- `projects/ugsoft/ugsoft-admin-api/README.md`
- `projects/ugsoft/ugsoft-admin-api/step1-candidate-flows.md`
- `projects/ugsoft/ugsoft-admin-api/step2-flow-comparison.md`
- `projects/ugsoft/ugsoft-admin-api/contribution-claim-consolidation.md`
- `projects/ugsoft/ugsoft-connector-api/flows/request-bet-record-mq-sync/flow.md`
- `projects/ugsoft/ugsoft-connector-api/flows/request-bet-record-mq-sync/materials/evidence.md`

既有文件判斷：

- `ugsoft-admin-api` Step 1 / Step 2 可沿用；Step 2 已明確選本 flow 作第一條代表 flow。
- 本 flow 之前沒有 `flows/connect-bet-record-mq-ingestion/`；本輪新增 Step 3 learning package。
- Project-level contribution consolidation 是 Career Track rolling 版，不代表 Flow Track 完成。

## Source Repo 狀態

來源 repo：

```text
/Users/nick/Git/ugsoft/ugsoft-admin-api
```

遠端 / 本機狀態：

- 已執行 `git fetch --all --prune`；第一次受 sandbox 權限限制失敗後，經 approval 成功更新 remote refs。
- local branch：`main`
- local HEAD：`0cc62e0e1a040e69b1650079d9ecfe92dd64380d`
- `origin/main`：`b1b83f64ffc971cc838ef935867a5a2234e3d201`
- local vs `origin/main`：ahead / behind = `0 / 42`
- source repo 工作樹乾淨。
- 本輪採 `origin/main` snapshot 讀最新 code；因本機工作樹落後，涉及最新 current behavior 時標為 `origin/main` code-backed / 主管或團隊 context。

未確認：

- 未確認 production 實際部署 branch。
- 未確認 RabbitMQ ack mode、listener retry、publisher confirm、DLQ 實際 runtime 設定。
- 未確認 DB migration / table unique index 實際 production schema。
- 未確認真實 incident / ticket。

## 已掃 Code Path

- `origin/main:src/main/java/com/ps/config/RabbitMQConfig.java`
- `origin/main:src/main/java/com/ps/domain/admin/constant/RabbitMq.java`
- `origin/main:src/main/java/com/ps/domain/admin/rabbitMq/betRecord/ConnectBetRecordListener.java`
- `origin/main:src/main/java/com/ps/domain/admin/rabbitMq/betRecord/ConnectBetRecordConsumerService.java`
- `origin/main:src/main/java/com/ps/domain/admin/rabbitMq/betRecord/BetRecordMqPayload.java`
- `origin/main:src/main/java/com/ps/domain/admin/rabbitMq/betRecord/BetRecordMqMessage.java`
- `origin/main:src/main/java/com/ps/domain/manage/data/mapper/BetRecordMapper.java`
- `origin/main:src/main/resources/mapper/BetRecordMapper.xml`
- `origin/main:src/main/java/com/ps/domain/game/slot/data/entity/BetRecord.java`
- `origin/main:src/main/java/com/ps/domain/game/slot/data/repository/BetRecordRepositoryNew.java`
- `origin/main:src/main/java/com/ps/domain/game/slot/constant/BetRecordStep.java`
- `origin/main:src/main/java/com/ps/domain/admin/rabbitMq/quota/QuotaUpdatePayload.java`
- `origin/main:src/main/java/com/ps/domain/admin/rabbitMq/quota/QuotaUpdateConsumer.java`
- `projects/ugsoft/ugsoft-connector-api/flows/request-bet-record-mq-sync/*` 作上游 context。

## Git Evidence

### Nick / 10gt12nc direct evidence

| Commit | Author | 意義 |
| --- | --- | --- |
| `98ad763` | `10gt12nc` | `feat: BetRecord MQ 入庫 01`，新增 RabbitMQ config、BetRecord MQ payload / listener / consumer service 與 duplicate support 初版。 |
| `f641b04` | `10gt12nc` | `fix: job多 單 BetRecordMq 01`，調整 BetRecord MQ consumer，移除原 duplicate support class，收斂查重 /入庫路徑。 |
| `821bc2e` | `10gt12nc` | `feat: BetRecord、requestLog Mq`，調整 RabbitMQ constants、consumer service，新增 requestLog MQ payload，並補 `BetRecordMapper.xml` query。 |
| `c99a325` | `10gt12nc` | `fix: job MQ currency 預設幣別為 CNY`，支撐 currency default evidence。 |

### Code-backed / supervisor or team context

| Commit | Author | 意義 |
| --- | --- | --- |
| `f29f8e4` | `arnold` | `feat: 商户额度监控系统`，新增 quota update queue / payload / consumer / Redis quota idempotency，並讓 BetRecord consumer save 後 publish quota update。主管 context，不當 Nick direct evidence。 |
| `6ab331c` | `arnold` | `fix: pt_bet_record.id 改用 bufferId 生成，重複檢查改查 provider_bet_id`，latest duplicate / id behavior context。 |
| `b4fe846` | `arnold` | `fix: normalize money_inout amounts by provider`，latest amount normalization context。 |

Nick 已確認 `arnold` 是主管帳號；`arnold` commits 不作 Nick direct evidence。

## Code Evidence 摘要

### MQ config

已確認：

- `RabbitMQConfig#connectBetRecordExchange` 建 durable direct exchange。
- `connectBetRecordQueue` 建 durable queue。
- binding 使用 `RabbitMq.BET_RECORD_ROUTING_KEY`。
- `RabbitMq` constants 定義 BetRecord exchange / routing key / queue。

### Listener

已確認：

- `ConnectBetRecordListener#receive` 監聽 `connectBetRecordQueue`。
- 收到 message 後呼叫 `connectBetRecordConsumerService.consume(message)`。
- catch exception 後 log error，不重拋。

待確認：

- Spring AMQP ack mode / retry interceptor / error handler 是否另有設定。

### Consumer service

已確認：

- JSON parse 到 `BetRecordMqPayload`。
- payload null skip。
- `providerBetId` fallback `id`。
- `currency` fallback `CNY`。
- `eventTime` fallback current time。
- validate `agentId / provider / providerBetId / currency`。
- 用 `DateTimeUtil.getLocalDate(eventTime)` 算 `ptDay`。
- 查 `BetRecordMapper#queryBetRecordExists(ptDay, agentId, providerBetId)`。
- 不存在才建立 `BetRecord`。
- save 成功後 publish `QuotaUpdatePayload`。
- quota publish exception 只 log warn，不回滾 bet record。

### BetRecord / duplicate boundary

已確認：

- mapper query 條件是 `pt_day + agent_id + provider_bet_id`。
- `BetRecord` entity unique constraint 顯示 boundary 為 `pt_day, agent_id, provider, provider_bet_id, currency`。
- `BetRecordStep.CREATE = 1`。
- `notifyCount = 0`、`notify = false`。

待確認：

- production DB unique index 是否完全等於 entity annotation。
- duplicate query 未含 provider / currency 是設計決策或暫時折衷。

### Quota update context

已確認：

- `QuotaUpdatePayload` 包含 `agentId / currency / betRecordId / bet / totalWin / step / providerBetId`。
- `QuotaUpdateConsumer` 有 Redis `markProcessedIfAbsent` 做 betRecordId idempotency。
- quota consumer 對 `CREATE` connector record 與 `RESULT` internal result 做處理。
- quota queue 有 DLQ config。

邊界：

- quota update 是 latest behavior / 主管 context，不當 Nick direct evidence。
- 本 flow Step 3 只把 quota 當 downstream supporting path，不宣稱完整 quota owner。

## 已確認 / 推測 / 待確認

### 已確認

- `ugsoft-admin-api` 有 BetRecord MQ consumer。
- Consumer 會 parse payload、validate、算 `ptDay`、查重、寫 `pt_bet_record`。
- Nick / `10gt12nc` 有 BetRecord MQ 初版與 currency default direct evidence。
- 最新 code 在 save 後 fire-and-forget publish quota update。

### 合理推測

- 這條 flow 是 connector provider callback / job sync 的下游入庫端。
- `ptDay` 用 event time 是為了處理 late data / 跨日查重。
- quota update 和 bet record 入庫解耦，是為了避免 quota 系統拖慢或阻塞主資料落庫。

### 待確認

- RabbitMQ listener exception 被 catch 後是否仍會 ack。
- MQ publish 是否有 publisher confirm。
- quota update publish failure 是否有補償 / repair job。
- production table unique index 是否與 entity annotation 一致。
- 是否有 admin tool 能重送 quota update 或重建 quota view。

## Relationship Check

本輪事實變更：

- `ugsoft-admin-api` 第一條代表 flow `connect-bet-record-mq-ingestion` Step 5 已完成。
- 本 flow 可作 project-level RabbitMQ / bet record async processing 的強 supporting evidence，但不代表整個 `ugsoft-admin-api` Flow Track 完整。
- Career Track `contribution-claim-consolidation.md` 需回填本 flow Step 5 狀態，但不需改成 final；本輪沒有做 project-level contribution refresh。

需要同步：

- `projects/ugsoft/ugsoft-admin-api/README.md`
- `projects/ugsoft/README.md`
- `projects/source-repo-inventory.md`
- `projects/source-repo-flow-audit.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/06-todo.md`
- `senior-owner-playbook/13-code-capability-map.md`

不更新：

- `05-resume-master-zh.md` / `08-application-autobiography-zh.md`：本輪只是單條 flow Step 5，不是 project contribution refresh 或 rolling resume package。
- `04-interview-casebook.md`：本輪只完成單條 flow claim gate，尚未做全域 04 對齊檢查，不更新。
- `17-salary-negotiation.md`：沒有薪資策略變更。
