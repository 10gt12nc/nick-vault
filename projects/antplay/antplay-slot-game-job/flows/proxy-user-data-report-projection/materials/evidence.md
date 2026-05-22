# Evidence - proxy-user-data-report-projection

日期: 2026-05-22

## 1. Source Repo 狀態

| 項目 | 狀態 |
| --- | --- |
| source path | `/Users/nick/Git/antplay/antplay-slot-game-job` |
| local branch | `master` |
| local HEAD | `d847357` |
| local `origin/master` | `d847357` |
| ahead / behind | `0 / 0` |
| source working tree | clean |
| remote refs | `git fetch --all --prune` 失敗一次；依 KB 不反覆重試 |
| 最新性判斷 | 未確認最新遠端；本 Step 依本地 refs / 本地 working tree 保守分析 |

注意: source remote 是內網環境，vault 不記錄內網 URL / IP / sensitive remote detail。

## 2. 本輪掃描範圍

Vault:

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/antplay/antplay-slot-game-job/README.md`
- `projects/antplay/antplay-slot-game-job/step1-candidate-flows.md`
- `projects/antplay/antplay-slot-game-job/step2-flow-comparison.md`
- `projects/antplay/antplay-slot-game-job/contribution-claim-consolidation.md`

Source code:

- `ProxyUserDataConsumerService`
- `ReportAgentPlayerJob`
- `ReportAgentPlayerService`
- `ReportAgentPlayerServiceImpl`
- `ReportAgentPlayerRepositoryCustomImpl`
- `ReportAgentPlayerRepositoryInternal`
- `ReportAgentPlayer`
- `SettleBetsVo`
- `BetRecord`
- `JobOptions`
- relevant `application.yml` toggle / topic key only；未記錄環境或敏感設定

Git history:

- path-specific log for report projection code path。
- selected important commit stats / diffs for `#386`、`#590`、`#702`、`fix ag_report_player`、current report-agent-player fixes。

未掃 / 待確認:

- 上游 `settled_bets` producer 端完整 code path。
- Spring Kafka container ack / retry / DLQ 設定。
- live DB unique key / index / constraint。
- report dashboard query path。
- production runbook / on-call alert。

## 3. Code Evidence

| Area | File | Evidence |
| --- | --- | --- |
| Kafka consumer | `ProxyUserDataConsumerService` | `@KafkaListener(topics = {"settled_bets"}, groupId = "proxy_user_data")`，解析 `SettleBetsVo` 後投影 report |
| Feature toggle | `ProxyUserDataConsumerService` / `application.yml` | `spring.kafka.consumer.task.proxy-user-data` 控制 consumer 是否啟用 |
| Projection key | `ProxyUserDataConsumerService` | key 為 `agentId + playerId + dataDay + currency` |
| Aggregation state | `ProxyUserDataConsumerService` | service field `ConcurrentHashMap reportMap` 累加 betCount / betAmount / totalWin / profit |
| Payload | `SettleBetsVo` | 包 `batch_id` 與 `List<BetRecord>` |
| Bet data | `BetRecord` | 含 `agentId / playerId / account / bet / totalWin / currency` |
| Report model | `ReportAgentPlayer` | 含 player、account、agent、dataDay、betCount、betAmount、totalWin、profit、currency |
| DB write | `ReportAgentPlayerRepositoryInternal` | insert / update `ag_report_player`，update 條件含 `agent_id / player_id / data_day / currency` |
| Old row lookup | `ReportAgentPlayerRepositoryInternal` | `(agent_id, player_id, data_day, currency) IN (...)`，current code 用 800 batch padding |
| Summary job | `ReportAgentPlayerJob` | 每 enabled agent / player，summary 三天前以前資料，再 backup / delete |
| Scheduler | `JobOptions` | `reportAgentPlayerJob` 每天 02:00 執行 |
| Backup / cleanup | `ReportAgentPlayerRepositoryCustomImpl` | `ag_report_player` -> `ag_report_player_bak`，再 delete old daily rows |

## 4. Commit Evidence

| Commit | Author | Date | Evidence |
| --- | --- | --- | --- |
| `12df475` | `10gt12nc` | 2025-04-08 | `feat(#386): 代理用戶數據 kafka匯入db`，新增 report projection 主要 code path |
| `7945101` | `10gt12nc` | 2025-04-08 | `feat(#386): Squash 代理用戶數據`，代理用戶數據相關 follow-up |
| `7aa06ed` | `10gt12nc` | 2025-04-08 | `feat(#386): Squash 代理用戶數據`，代理用戶數據相關 follow-up |
| `512f1dd` | `10gt12nc` | 2025-04-11 | `feat(#386): 代理用戶數據 job匯總`，Quartz summary path 調整 |
| `4411d88` | `10gt12nc` | 2025-05-27 | `feat(#590): ReportAgentPlayer 拆 currency`，projection / query / summary 加入 currency 維度 |
| `a9bb983` | `10gt12nc` | 2025-05-28 | `feat(#590): 少欄位 currency`，currency follow-up |
| `dbb5a66` | `10gt12nc` | 2025-05-28 | `feat(#590): log`，report path logging context |
| `806dc23` | `10gt12nc` | 2025-06-02 | `fix(#590): 每天一次`，schedule / job frequency context |
| `ef4f8f5` | `10gt12nc` | 2025-08-29 | `fix(#702): key重複`，oldMap / report key 加入 agentId |
| `38b74bd` | `10gt12nc` | 2025-12-12 | `fix`，report path fix context，含 repository internal / job changes |
| `6866866` | `10gt12nc` | 2025-12-17 | `fix ag_report_player`，改為 `ag_report_player` / `ag_report_player_bak` 並帶 `agent_id` 條件 |
| `ca17054` | `arnold` | 2026-02-24 | current report-agent-player context，後續修正屬他人 context |
| `d847357` | `arnold` | 2026-02-24 | current HEAD；`ReportAgentPlayerRepositoryInternal` batch lookup padding / safety context，屬他人 context |

## 5. 已確認

- 這條 flow 是 Kafka `settled_bets` event -> report DB projection -> Quartz historical summary / backup / delete。
- `10gt12nc` 有直接 commit 在主要 flow 上，證據比一般 code-backed 分析更強。
- currency 是後續明確補上的報表維度。
- `#702` key 重複修正與 `agentId` 納入 oldMap / key 判斷直接相關。
- current code 使用 `ag_report_player` / `ag_report_player_bak`，並用 `agent_id` 條件過濾。
- report row 的 identity 至少在 code 層是 `agent_id + player_id + data_day + currency`。
- summary row 以 `dataDay = 0` 表示歷史彙總。

## 6. 合理推論

- `ag_report_player` 是 derived report table，不應視為下注結算或錢包 source of truth。
- Quartz summary / backup / delete 若沒有 job-level transaction 或 idempotent marker，存在 partial failure 重跑風險。
- `reportMap` 長生命週期可能是 memory / concurrency / restart consistency 風險。
- 若 production 要 owner-grade，需要 replay / rebuild / reconciliation runbook。

## 7. 待確認

- 上游 producer 是否保證同一 message 只含同一 agent，或 mixed agent 是常態。
- Kafka ack mode、exception handling、retry / DLQ。
- `ag_report_player` 是否有 unique key。
- backup table 是否允許 duplicate backup。
- report dashboard 是否讀 summary row `dataDay = 0` 與 daily row 的 union。
- 是否有人工補數 / rebuild tooling。
