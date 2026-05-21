# Evidence

## Source 狀態

| repo | branch | local HEAD | remote ref | ahead / behind | working tree | 遠端狀態 |
| --- | --- | --- | --- | --- | --- | --- |
| `/Users/nick/Git/antplay/antplay-slot-game-api` | `develop` | `079aa66` | `origin/develop` = `079aa66` | `0 / 0` | clean | fetch 失敗一次，依本地 refs / 本地工作樹保守分析 |

## 本輪重讀

- `/Users/nick/Git/nick/nick-vault/AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `projects/antplay/antplay-slot-game-api/README.md`
- `projects/antplay/antplay-slot-game-api/step1-candidate-flows.md`
- `projects/antplay/antplay-slot-game-api/step2-flow-comparison.md`
- `projects/antplay/antplay-slot-game-api/contribution-claim-consolidation.md`
- `projects/antplay/antplay-slot-game-api/flows/bet-record-sharding-schema-route/flow.md`
- `projects/antplay/antplay-slot-game-api/flows/bet-record-sharding-schema-route/career-interview.md`
- `projects/antplay/antplay-slot-game-api/flows/bet-record-sharding-schema-route/materials/interview.md`
- `projects/antplay/antplay-slot-game-api/flows/bet-record-sharding-schema-route/materials/claim-boundary.md`
- `projects/antplay/antplay-slot-game-api/flows/bet-record-sharding-schema-route/materials/decision-notes.md`

## Step 4 補充

- 本輪重新嘗試更新 source remote refs，因內網不可達而 fetch 失敗；未反覆重試，仍依本地 refs / 本地工作樹保守分析。
- local HEAD 與 `origin/develop` 仍同為 `079aa66`，ahead / behind = `0 / 0`。
- 本輪未新增 code scan path；Step 4 以 Step 3 evidence 為基礎，補正式面試講法、STAR、Senior / Lead 追問與 claim boundary。
- 既有 Step 3 文件狀態: 可沿用。原因是已包含白話導讀、分層對照、架構圖、正常流程、failure window、owner decision、掃描範圍與不可誇大邊界。

## Step 5 補充

- 本輪再次嘗試更新 source remote refs；因內網不可達而 fetch 失敗一次，未反覆重試，仍依本地 refs / 本地工作樹保守分析。
- local HEAD 與 `origin/develop` 仍同為 `079aa66`，ahead / behind = `0 / 0`，source working tree clean。
- 本輪補追 path-specific `git log`、`git show --stat`、`git blame` 與 current code grep，範圍包含 schema route、bet record partition、transfer wallet transaction partition、transfer request log partition、table creator、create-table jobs 與 logical-to-physical mapping 線索。
- 既有 Step 3 / Step 4 文件狀態: 可沿用；Step 5 只補 claim gate 與 evidence 分層。

## Current code evidence

| 類型 | 檔案 / 元件 | 證據 |
| --- | --- | --- |
| Schema annotation | `UseSchema` | annotation 預設 `detectAgentId=true`，可設定 schema group 與 read-only |
| Schema route AOP | `SchemaRouteAspect` | `@Around` 攔截 class / method annotation，從 args / object / tableName 找 `agentId`，push / restore schema context |
| Datasource switch | `SchemaContextHolder`、`SchemaAwareDataSource`、`DataSourceConfig` | 依 schema group 與 read-only 切 datasource，connection 借出時設定 schema / catalog |
| Schema groups | `SchemaGroup` | `DEFAULT / G1 / G2 / G3` 對應不同 schema name |
| Table constants | `ShardingTableName` | 集中 `pt_bet_record`、`pt_request_log`、`pt_transfer_request_log`、`pt_transfer_player_wallet_transaction` |
| Bet record write / state | `BetRecordManageService` | update / state transition SQL 帶 `pt_day`、`agent_id`、`time`、`id` |
| Bet record repository | `BetRecordRepositoryImpl` | insert / update / notify / find path 帶 partition key；多處使用 `proxy()` 確保 AOP 生效 |
| Bet record query | `BetRecordService`、`BetRecordProcess` | 查詢限制 `pt_day` range、`agent_id`、`time BETWEEN`；管理查詢限制七天 |
| UTC day key | `DateTimeUtil#getLocalDate(long)` | timestamp 轉 UTC `LocalDate` 作 `pt_day` |
| Transfer wallet transaction | `TransferBalanceService` | 防重與 insert path 使用 `pt_transfer_player_wallet_transaction` + `pt_day` / `agent_id` |
| Transfer order lookup | `TransferBalanceService#insertOrderLookup`、`getTransactionData` | 保存 data day / table / transaction id，用於後續查單 |
| Transfer request log | `TransferRequestLogService` | 以 `pt_day`、`agent_id`、`trace_id` 查寫 `pt_transfer_request_log` |
| Table creator | `BetRecordTableCreator`、`RequestLogTableCreator`、`TransferPlayerWalletTxnTableCreator`、`TransferRequestLogTableCreator` | 依 date + agent 建實體表，並寫 `tool_create_table` |
| Scheduled jobs | `CreateBetRecordNineDayTable` 等 | job entry 存在，但 current code 的 agent loop 已註解停用 |
| Adjacent table replacement | `AgentTables` AOP | repo 有 dynamic table replacement 機制，但本 flow 的 bet record / request log / transfer transaction path 未見直接使用 |

## History evidence

| commit | author | 類型 | 觀察 |
| --- | --- | --- | --- |
| `6f6caa7` | `10gt12nc` | #167 分表 | 大量修改 bet record / jobs / tests，屬 direct evidence |
| `057f144` | `10gt12nc` | bet 寫日表 | bet record 日表寫入 direct evidence |
| `87accd9` | `10gt12nc` | 七天內查詢 | bet record 查詢範圍限制 direct evidence |
| `311ece0` | `10gt12nc` | request_log 拆表 | request log 分表 direct evidence |
| `718a207` | `10gt12nc` | db_partition v2 | 觸及 `UseSchema`、transfer wallet transaction、request log / bet record partition path |
| `5f7edc9` / `69ea70f` / `9d14f91` 等 | `10gt12nc` | `@UseSchema` / schema route | schema route core direct evidence |
| `84fe26f` | `10gt12nc` | job fix | table creator / job 線索 |
| `d90b02e` | arnold | 停用 job | current code caveat：create table jobs 的實際 agent loop 被停用 |
| `83af871` | arnold | table name centralization | `ShardingTableName` 集中化屬 context evidence |
| `77d6de7` / `d835011` | `10gt12nc` | `pt_bet_record` fix | bet record query quoting / table fix direct evidence |

## Step 5 path-specific evidence

| Evidence | 結論 |
| --- | --- |
| `5f7edc9 feat: 換 db 抄 admin` | `10gt12nc` 新增 `UseSchema`、`SchemaRouteAspect`、`SchemaAwareDataSource`、`SchemaContextHolder`、`SchemaContextUtils`、`SchemaGroup` 等核心 schema route 檔案；可作 schema route direct evidence。 |
| `69ea70f feat: 換 db` | `10gt12nc` 後續調整 datasource / route 並觸及 `BetRecordRepositoryImpl`、`TransferBalanceService`、create-table jobs 等 path；可作跨 high-traffic table route evidence。 |
| `9d14f91 @UseSchema` | `10gt12nc` 將 `@UseSchema` 補到 bet record、transfer、request log、table creator 等多個 path；可作 AOP route rollout evidence。 |
| `6f6caa7 feat(#167):拆表` | `10gt12nc` 對 `BetRecordRepositoryImpl`、`BetRecordManageService` 大量改動，建立 #167 分表主線。 |
| `057f144 feat(#167): bet 寫日表` | `10gt12nc` 強化 bet record 寫日表 / 日表查寫 path。 |
| `87accd9 feat(#167): 查詢 七天內` | `10gt12nc` 對 bet record 查詢加七天內限制，是 query boundary direct evidence。 |
| `311ece0 fix(#167): request_log 拆表` | `10gt12nc` 對 request log 分表 path 有 direct evidence；本 flow 只作 high-traffic table governance 補充，不取代 request-log MQ flow。 |
| `718a207 db_partition v2` | `10gt12nc` 同時觸及 `BetRecordRepositoryImpl`、`TransferBalanceService`、`TransferRequestLogService`、`TransferBalanceFacade`，補 `pt_day` / `agent_id` partition key 與 transfer request log path。 |
| `84fe26f fix: job` | `10gt12nc` 對四個 table creator service 做 idempotency / already-exists / registry handling；可作 table creator direct evidence。 |
| `d90b02e feat: 停用job` | 他人 commit；current create-table jobs 的 agent loop 已被註解停用，是不可誇大的 caveat。 |
| `83af871 fix: 將分表集中化` | 他人 commit；集中 `ShardingTableName`，屬 current behavior / context evidence，不算 Nick direct contribution。 |

## Step 5 blame evidence

| Path | Blame 結論 |
| --- | --- |
| `UseSchema.java` | annotation core 由 `10gt12nc` 建立；`readOnly` 欄位由他人後續補。 |
| `SchemaRouteAspect.java` | AOP route 主體、agentId extraction、`@Around`、schema push / restore 多數由 `10gt12nc` 建立；nested switch / readOnly 整合部分由他人補。 |
| `BetRecordRepositoryImpl.java` | `pt_day` / `agent_id` query key、insert / update state path、#167 分表與 db partition v2 有大量 `10gt12nc` blame；後續 proxy split、notify cross-day、欄位擴充與 table name centralization 多為他人。 |
| `TransferBalanceService.java` | 原 transfer wallet service 初版多為 Derek；`pt_day`、`agent_id`、transaction table partition、部分 lookup 參數與 deadlock 補償相關行為有 `10gt12nc` blame。 |
| `TransferRequestLogService.java` | 原 service 初版多為 Derek；`@UseSchema`、`pt_day` / `agent_id` insert / update / query key 有 `10gt12nc` blame，table name centralization / self proxy 部分有他人補。 |
| `BetRecordTableCreator.java` | table creator 主體由 `10gt12nc` 建立，`@UseSchema` 與 idempotent already-exists handling 也有 `10gt12nc` evidence。 |
| create-table jobs | current job code 顯示 agent loop 被註解停用；因此不能宣稱目前自動建表仍由這些 jobs active 執行。 |

## Step 5 code search evidence

- `rg` 未在 `src/main/resources` / `src/main/java` 找到 ShardingSphere / actual-data-nodes 類 config。
- Repo 內能確認 `tool_create_table` registry 與 table creator service，但不能確認 logical `pt_*` table 到 physical date / agent table 的 live DB routing。
- `AgentTables` / table replacement AOP 是 adjacent mechanism；本 flow 的 bet record / request log / transfer transaction path 未見直接使用它作主要 route。

## 已確認

- `@UseSchema` + AOP schema route 存在，且 current code 會從 `agentId` 推 schema group。
- Bet record / transfer wallet transaction / transfer request log path 使用 `pt_day` 與 `agent_id` 作查寫 key。
- Bet record query path 有七天範圍限制。
- Table creator services 存在，也有 `tool_create_table` registry。
- Current create-table jobs 的實際 agent loop 已停用，不能說 current automatic table creation active。
- Nick / `10gt12nc` 對 schema route、#167 bet record 分表、db partition v2、table creator service 有 direct evidence。
- 本 flow 可回填 `antplay-slot-game-api` project-level consolidation，作 high-traffic data governance / partition key / schema route 強化素材。

## 待確認

- live DB 如何把 logical `pt_*` table 對應到 physical date / agent table。
- table creator 停用後，production 是否改由 migration、DBA job、其他 repo 或手動流程補表。
- `tool_create_table` 與 live DB metadata 是否一致。
- request log 一般表在 game-api / admin-api 之間的最終 table routing。

## 本輪未做

- 未改 source repo。
- 未更新 `05 / 08`。
- 未確認 live DB logical-to-physical routing。
