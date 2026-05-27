# Evidence - request-bet-record-mq-sync

## 任務

- 任務：`ugsoft ugsoft-connector-api request-bet-record-mq-sync Step 3`
- 日期：2026-05-27
- 掃描等級：Level 2 Flow 深掃；未做 Level 3 逐檔逐行 / 每 commit diff 全量追查。
- 目標：建立 provider bet record job sync / Redis watermark / duplicate check / MQ 補資料的 Step 3 learning package。

## Step 4 補充掃描

- 任務：`ugsoft ugsoft-connector-api request-bet-record-mq-sync Step 4`
- 日期：2026-05-27
- 掃描等級：Level 2 Flow 深掃 / interview case；未做 Level 3 逐檔逐行。
- 已重讀 Step 3 `flow.md`、`career-interview.md`、`materials/evidence.md`、`materials/interview.md`、`materials/claim-boundary.md`、`materials/decision-notes.md`。
- 已重新執行 `/Users/nick/Git/ugsoft/ugsoft-connector-api` 的 `git fetch --all --prune`；第一次受 sandbox 權限限制失敗後，經 approval 成功更新 remote refs。
- source repo 狀態：local branch `Nick_Test`，local HEAD `c2cab730c0cd6ead6d92a038ef56f97987577059`；`origin/master` `4bd2195e1e574978f11a1d4b5e744792f16ecad0`；`origin/develop` `079aa6603b50db3c185e383295ca5966bbe272fb`；local vs `origin/master` `0 / 61`，local vs `origin/develop` `190 / 0`。
- source repo 工作樹仍有既有 `.DS_Store`、test、docs 等 local changes / untracked files；Step 4 只採 Step 3 已記錄的 remote refs / git history / code-backed evidence，不採 source repo 髒檔。
- Step 4 未新增 code path evidence；本輪目標是把 Step 3 evidence 轉成正式 90 秒 / 3 分鐘面試 case、追問題庫、回答要點與誇大邊界。

## KB / Vault 已重讀

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/ugsoft/README.md`
- `projects/ugsoft/ugsoft-connector-api/README.md`
- `projects/ugsoft/ugsoft-connector-api/step1-candidate-flows.md`
- `projects/ugsoft/ugsoft-connector-api/step2-flow-comparison.md`
- `projects/ugsoft/ugsoft-connector-api/contribution-claim-consolidation.md`
- 既有 flows：`transfer-wallet-in-out-query`、`provider-callback-bet-settle-to-mq`

既有文件判斷：

- Step 1 / Step 2 可沿用；Step 2 已把本 flow 排第三順位。
- 第一條 `transfer-wallet-in-out-query` 已完成 Step 5。
- 第二條 `provider-callback-bet-settle-to-mq` 已完成 Step 5。
- 本 flow 之前沒有 `flows/request-bet-record-mq-sync/`，本輪新增 Step 3 learning package。

## Source Repo 狀態

來源 repo：

```text
/Users/nick/Git/ugsoft/ugsoft-connector-api
```

遠端 / 本機狀態：

- 已執行 `git fetch --all --prune`；第一次受 sandbox 權限限制失敗後，經 approval 成功更新 remote refs。
- local branch：`Nick_Test`
- local HEAD：`c2cab730c0cd6ead6d92a038ef56f97987577059`
- `origin/master`：`4bd2195e1e574978f11a1d4b5e744792f16ecad0`
- `origin/develop`：`079aa6603b50db3c185e383295ca5966bbe272fb`
- local vs `origin/master`：ahead / behind = `0 / 61`
- local vs `origin/develop`：ahead / behind = `190 / 0`
- source repo 工作樹不乾淨：有 `.DS_Store`、test、docs 等既有 local changes / untracked files。本輪只採 remote refs / `origin/master` / git history，不採 source repo 髒檔作正式 evidence，也不改 source repo。

未確認：

- 未確認 production 實際部署 branch。
- 未確認 RabbitMQ publisher confirm、DLQ、consumer retry 與監控告警。
- 未確認真實 incident / ticket。

## 已掃 Code Path

- `origin/master:src/main/java/com/ps/quartz/main/job/AntplayBetRecordSyncJob.java`
- `origin/master:src/main/java/com/ps/quartz/main/job/DerplayBetRecordSyncJob.java`
- `origin/master:src/main/java/com/ps/quartz/main/JobOptions.java`
- `origin/master:src/main/java/com/ps/quartz/main/service/ConnectBetRecordSyncService.java`
- `origin/master:src/main/java/com/ps/quartz/main/repository/ConnectBetRecordSyncRepository.java`
- `origin/master:src/main/java/com/ps/domain/connector/service/ConnectAntplayAdapter.java`
- `origin/master:src/main/java/com/ps/domain/connector/service/ConnectDerPlayAdapter.java`
- `origin/master:src/main/java/com/ps/domain/connector/vo/ConnectBetRecordResponse.java`
- `origin/master:src/main/java/com/ps/domain/connector/service/ConnectBetRecordMqService.java`
- `origin/master:src/main/java/com/ps/domain/connector/vo/ConnectBetRecordMqDto.java`
- `origin/master:src/main/java/com/ps/common/constant/RedisKey.java`
- `origin/master:src/main/resources/application-common.yml` 僅確認 Quartz job store / scheduler context；未把內部連線資訊寫入 vault。

## Git Evidence

### Nick / 10gt12nc direct evidence

| Commit | Author | 意義 |
| --- | --- | --- |
| `a173cf3` | `10gt12nc` | `feat: mq`，包含 `ConnectBetRecordMqService`、AntPlay / DerPlay bet record sync job、repository、sync service 等。 |
| `3e9d0d0` | `10gt12nc` | `feat: mq 跨日 pt_day`，支撐跨日查重 / `pt_day` evidence。 |
| `df1fdcf` | `10gt12nc` | `fix: mq Derplay 單一錢包 日期`，DerPlay MQ 日期 / 單一錢包相關修正。 |
| `fb84a47` | `10gt12nc` | `fix: job mq`，job / MQ 修正 context。 |
| `cc26172` | `10gt12nc` | `feat: mq -log`，MQ log context。 |
| `b6329a8` | `10gt12nc` | `fix: job多 單 BetRecordMq 01`，callback / MQ / job 多處修正。 |
| `5747ff3` | `10gt12nc` | `fix: job MQ currency`，currency mapping / default context。 |
| `ac6d25c` | `10gt12nc` | `fix: job MQ currency`，currency mapping / default context。 |
| `edf8f26` | `10gt12nc` | `fix: job MQ currency 預設幣別為 CNY`，currency default context。 |
| `1c269ee` | `10gt12nc` | `feat: job BetRecordMq 註解`，job BetRecord MQ 維護 context。 |
| `3f23274` | `10gt12nc` | `feat: mq`，BetRecord MQ path context。 |

### Code-backed / team context

| Commit | Author | 意義 |
| --- | --- | --- |
| `4086e48` | 未升級為 Nick direct | `feat: BetRecord sync 改用 Redis watermark 縮短時間窗`，目前 watermark behavior context。 |
| `482000c` | 未升級為 Nick direct | 移除 AntPlay sync 對 merchant callback URL 的 skip 判斷。 |
| `185f4c5` | `arnold` / team context | betSettle error code 與 DerPlay per-currency sync behavior。 |
| `277807a` / `1f063a0` | team context | provider bet record sync amount normalization。 |
| `d7a1717` | team context | subAgentId 傳遞到下游 AntPlay context。 |

Nick 已確認 `arnold` 是主管帳號；`arnold` commits 不作 Nick direct evidence。

## Code Evidence 摘要

### Quartz jobs

已確認：

- `AntplayBetRecordSyncJob` 讀 `startTime / endTime`，呼叫 `syncAllAntplay`。
- `DerplayBetRecordSyncJob` 讀 `startTime / endTime`，呼叫 `syncAllDerplay`。
- `JobOptions` 註冊兩個 BetRecord sync job，排程每分鐘執行。

### ConnectBetRecordSyncService

已確認：

- 掃 enabled agents。
- 跳過單一錢包 agent。
- DerPlay 檢查 `derplayOpen` 與 currencies。
- 自動模式使用 Redis watermark；手動模式使用 job data map 指定時間。
- default time window 避開最新幾分鐘，並限制回掃範圍。
- response null / code 非 0 不推進 watermark。
- 空資料 response 視為成功，可推進 watermark。
- items 依事件日期分組後查 DB existing keys。
- 同批資料用 `providerBetId|currency` 去重。
- 未存在資料轉成 `ConnectBetRecordMqDto` 後送 MQ。
- watermark 只允許往前推進。

### ConnectBetRecordSyncRepository

已確認：

- 查 `pt_bet_record`。
- 條件包含 `pt_day`、`agent_id`、`provider`、`provider_bet_id`。
- 分批處理 provider bet ids。
- 回傳 `providerBetId|currency` key。

### Provider adapters

已確認：

- AntPlay bet record API 會組 agent / trace / time window / page / pageSize / signTime / sign。
- DerPlay bet record API 支援 currency overload。
- DerPlay response 會 normalize 欄位，包含 provider id、game、account、amount、settle time。

### MQ producer

已確認：

- `ConnectBetRecordMqService#send` 被本 flow 與 callback flow 共用。
- producer catch publish exception 並 log；exception 不往外拋。
- 這會影響 watermark 是否安全推進，是本 flow 主要 failure window。

## 已確認 / 推測 / 待確認

### 已確認

- 本 flow 存在 Quartz job-driven provider bet record sync。
- 自動模式有 Redis watermark。
- 手動模式可指定時間窗。
- 有跨日 `pt_day` 查重。
- 有 `providerBetId|currency` 去重。
- 有 MQ producer 共用 callback path 的 BetRecord MQ DTO。

### 合理推測

- 這條 flow 用於 callback path 以外的 late data / 補資料場景。
- Redis watermark 是為了縮短自動 sync 時間窗，降低 provider API 與 DB 查重負擔。
- Cross-day grouping 是為了避免時間窗跨日時查錯 `pt_day`。

### 待確認

- production 實際 scheduler branch / cron config。
- provider API 是否可能超過 pageSize，是否另有外部限制。
- MQ producer 是否有 publisher confirm 或外層 retry。
- admin-api consumer 的 retry / DLQ / idempotency 是否能完全覆蓋 publish 後 failure。
- DerPlay per-currency partial success 是否在 production 有額外保護。
- 是否有 manual catch-up runbook。

## Relationship Check

- Step 3 已同步過的 project README / step files / inventory / todo 仍可沿用。
- 本輪 Step 4 已把狀態從「下一步 Step 4」推進到「Step 4 已完成 / 下一步 Step 5」。
- 已同步 `projects/ugsoft/ugsoft-connector-api/README.md`、`step1-candidate-flows.md`、`step2-flow-comparison.md`、`contribution-claim-consolidation.md`、`projects/ugsoft/README.md`、`projects/source-repo-inventory.md`、`projects/source-repo-flow-audit.md`、`senior-owner-playbook/01-senior-owner-flow-inventory.md`、`senior-owner-playbook/06-todo.md`、`senior-owner-playbook/13-code-capability-map.md`。
- `05 / 08 / 04 / 17`：本輪不更新。Step 4 是面試 case，不是 final claim gate，也沒有新的 project-level final consolidation。
