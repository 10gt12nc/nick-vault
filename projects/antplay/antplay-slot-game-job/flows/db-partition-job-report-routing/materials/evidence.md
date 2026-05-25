# db-partition-job-report-routing Evidence

日期: 2026-05-25
Step 4 補充日期: 2026-05-25
Step 5 補充日期: 2026-05-25

## 掃描等級

Level 2 single-flow scan。Step 5 追加 claim gate scan。

理由: Nick 指定同一條 flow Step 5，目標是把 Step 3 / Step 4 的 code-backed flow 轉成 claim gate；本輪沿用 Level 2，追加 current routing config、call site、path-specific blame、important commit stat / diff 線索。尚未做 Level 3 全 commit / DDL / migration runbook 深追。

## Vault / KB 重讀範圍

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `projects/antplay/antplay-slot-game-job/README.md`
- `projects/antplay/antplay-slot-game-job/step2-flow-comparison.md`
- `projects/antplay/antplay-slot-game-job/contribution-claim-consolidation.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/06-todo.md`
- `projects/antplay/antplay-slot-game-job/flows/db-partition-job-report-routing/flow.md`
- `projects/antplay/antplay-slot-game-job/flows/db-partition-job-report-routing/career-interview.md`
- `projects/antplay/antplay-slot-game-job/flows/db-partition-job-report-routing/materials/interview.md`
- `projects/antplay/antplay-slot-game-job/flows/db-partition-job-report-routing/materials/claim-boundary.md`
- `projects/antplay/antplay-slot-game-job/flows/db-partition-job-report-routing/materials/evidence.md`

## Source Repo 狀態

| 項目 | 狀態 |
| --- | --- |
| source path | `/Users/nick/Git/antplay/antplay-slot-game-job` |
| local branch | `master` |
| local HEAD | `d847357` |
| local `origin/master` | `d847357` |
| ahead / behind | `0 / 0` |
| source working tree | clean |
| remote refs | 先前 `git fetch --all --prune` 失敗一次；依 KB 不反覆重試 |
| 最新性判斷 | 未確認最新遠端；本 Step 依本地 refs / 本地 working tree 保守分析 |

注意: source remote 是內網環境，vault 不記錄內網 URL / IP / sensitive remote detail。

## Code Path

Schema routing:

- `src/main/java/com/ps/datasource/UseSchema.java`
- `src/main/java/com/ps/datasource/SchemaRouteAspect.java`
- `src/main/java/com/ps/datasource/SchemaContextUtils.java`
- `src/main/java/com/ps/datasource/SchemaContextHolder.java`
- `src/main/java/com/ps/datasource/SchemaGroup.java`
- `src/main/java/com/ps/datasource/SchemaAwareDataSource.java`
- `src/main/java/com/ps/datasource/DataSourceType.java`
- `src/main/java/com/ps/datasource/DataSourceConfig.java`
- `src/main/java/com/ps/datasource/DataSourceContextHolder.java`

Bet record / request log partition:

- `src/main/java/com/ps/domain/game/slot/data/repository/BetRecordRepositoryNew.java`
- `src/main/java/com/ps/domain/game/slot/data/repository/impl/BetRecordRepositoryImpl.java`
- `src/main/java/com/ps/domain/manage/data/repository/RequestLogRepositoryNew.java`
- `src/main/java/com/ps/domain/manage/data/repository/impl/RequestLogRepositoryImpl.java`
- `src/main/java/com/ps/domain/manage/service/RequestLogService.java`

Report routing:

- `src/main/java/com/ps/quartz/job/impl/ReportAgentPlayerJob.java`
- `src/main/java/com/ps/domain/kafka/gameData/dao/impl/ReportAgentPlayerRepositoryCustomImpl.java`
- `src/main/java/com/ps/domain/kafka/gameData/dao/impl/internal/ReportAgentPlayerRepositoryInternal.java`
- `src/main/java/com/ps/domain/kafka/gameData/entity/ReportAgentPlayer.java`

Related historical / removed context:

- `src/main/java/com/ps/quartz/job/impl/CreateBetRecordDailyTable.java` exists as commented historical table creation code.

## Important History

| Commit | Author | Date | Evidence 判斷 |
| --- | --- | --- | --- |
| `15803ad` | eliot | 2025-12-04 | `wip: apply @UseSchema`; 拆出 `ReportAgentPlayerRepositoryInternal`，report path 開始走 `@UseSchema` |
| `07267ed` | arnold | 2025-12-04 | `feat: 切partition`; 對 report / jackpot / request log 等補 route context |
| `c57deb2` | arnold | 2025-12-09 | `feat: add agentCache & UseSchema as latest`; current schema route / cache 基礎 |
| `a7071ec` | arnold | 2025-12-10 | `feat: interface add UseSchema`; interface 加 route annotation |
| `b754dae` | 10gt12nc | 2025-12-12 | `feat: db_partition v2`; direct evidence，bet record / request log / Kafka consumer / request log service 分表改動 |
| `38b74bd` | 10gt12nc | 2025-12-12 | `fix`; report path related context |
| `6866866` | 10gt12nc | 2025-12-17 | `fix ag_report_player`; direct evidence，report table 由 per-agent name 改固定 `ag_report_player` 並補 `agent_id` |
| `ca17054` | arnold | 2025-12-18 | `fix: 修正report agent player`; 後續修正 context |
| `d847357` | arnold | 2026-02-24 | `fix:report_agent_player`; current local HEAD context |

## 已確認

- `@UseSchema` 預設 `detectAgentId=true`，可自動從 method args / object getter / field 找 `agentId`。
- `SchemaRouteAspect` 在 finally close schema scope，避免 ThreadLocal context 長期污染。
- `SchemaContextUtils` 以 `Agent.dbGroupNum` 決定 `SchemaGroup`，cache miss 時可 fallback DB reload。
- `BetRecordRepositoryImpl` current code 用 `pt_bet_record`，查詢帶 `pt_day`、`agent_id`、`id`、`time` / `step`。
- `RequestLogRepositoryImpl` current code 用 `pt_request_log`，查寫帶 `pt_day`、`agent_id`、`id`、`time`。
- `ReportAgentPlayerRepositoryCustomImpl` 先 group by `agentId`，再呼叫 internal routed methods。
- `ReportAgentPlayerRepositoryInternal` 用 `@UseSchema` 查寫 `ag_report_player`，並帶 `agent_id` / `player_id` / `data_day` / `currency`。
- `6866866` 將 `report_agent_player_{agentId}` / `report_agent_player_backup_{agentId}` 改成 `ag_report_player` / `ag_report_player_bak`，補 `agent_id` 條件。
- `DataSourceConfig` current code 只看到 `master`、`master-g2`、`slave`、`slave-g2` 四組 data source bean 與 route map。
- `DataSourceType` current code 只有 `MASTER`、`MASTER_G2`、`SLAVE`、`SLAVE_G2`。
- `SchemaContextHolder#set` current code 明確處理 DEFAULT / G1 / G2，沒有 G3 data source branch。
- `ReportAgentPlayerJob` 的 summary / old summary / insert / update / backup / delete call sites 都明確傳 `agentId`。
- `ReportAgentPlayerRepositoryCustomImpl` 的 batch insert / update / find old players 先依 `ReportAgentPlayer#getAgentId` group，再把 group key 傳給 internal methods。
- `ReportAgentPlayerRepositoryInternal#findOldPlayers` current code 有 `agentId == null` guard，但 `SchemaRouteAspect` 在呼叫 method 前就會先做 `agentId < 0`，aspect 層仍有 null route key risk。

## Step 4 補充確認

- Step 3 文件符合目前 KB，可沿用；沒有發現需要先重整 Step 3 才能做 Step 4 的問題。
- `SchemaRouteAspect#switchSchema` 會在 finally close scope，Step 4 面試稿可以保守講 ThreadLocal restore，但不能說 async / nested route 全面安全。
- `SchemaContextHolder#set` current code 明確處理 DEFAULT / G1 / G2；G3 仍列待確認。
- `ReportAgentPlayerRepositoryInternal#findOldPlayers` current code對 `agentId == null` 有 guard；但 Aspect 自身對 `findAgentId` 回 null 的路徑仍需 Step 5 追 call sites / null safety。
- path-specific log 仍顯示 current schema framework 主要是 Eliot / Arnold 後續脈絡，Nick direct evidence 集中在 `db_partition v2` 與 `fix ag_report_player`。

## Step 5 補充確認

- `b754dae feat: db_partition v2` stat 顯示觸及 `BetRecordRepositoryImpl`、`RequestLogRepositoryImpl`、`RequestLogService`、Kafka consumer 與 jackpot balance delegate 等，屬 Nick / `10gt12nc` direct evidence，但不是整套 schema routing framework。
- `38b74bd fix` stat 顯示觸及 `ReportAgentPlayerRepositoryCustomImpl`、`ReportAgentPlayerRepositoryInternal`、`ReportAgentPlayerJob`，可作 report path repair 的直接 supporting evidence。
- `6866866 fix ag_report_player` stat 顯示只改 `ReportAgentPlayerRepositoryCustomImpl`，是 fixed report table + `agent_id` 條件的直接 evidence。
- `d847357 fix:report_agent_player` 是 current local HEAD 的後續 Arnold 修正，代表 report path 後續仍有人接手調整；不可把 current final behavior 全部歸給 Nick。
- `c57deb2 feat: add agentCache & UseSchema as latest` 是 current schema route / cache 基礎，author 為 Arnold；可作 code-backed analysis，不作 Nick 真實開發 claim。
- `git blame` 顯示 `SchemaRouteAspect` 主 route logic 多來自 Eliot / Arnold，`SchemaContextHolder#set` G2 data source branch 來自 Arnold。
- `git blame` 顯示 `ReportAgentPlayerRepositoryCustomImpl` 早期 report aggregation / group by agent 有 Nick / `10gt12nc` direct lines，internal routed repository 主要由 Eliot 建立並有 Nick / Arnold 後續修正。

## 待確認

- DDL / index / unique key: `pt_bet_record`、`pt_request_log`、`ag_report_player`、`ag_report_player_bak`。
- G3 data source routing 是否完整；current `SchemaGroup` 有 G3，但 `DataSourceType` / `DataSourceConfig` / `SchemaContextHolder#set` 未見 G3 data source mapping。
- `@UseSchema` 找不到 `agentId` 的實際 production behavior；current code 需補 aspect 層 null safety 判斷。
- agent `dbGroupNum` 變更時的 cache invalidation / migration procedure。
- 歷史 table migration / backfill / checksum / rollback runbook。
- Kafka listener / report job 是否有 schema route failure metric / alert。

## Claim Boundary

真實開發過:

- `b754dae feat: db_partition v2`
- `38b74bd fix`
- `6866866 fix ag_report_player`

Code-backed / 分析過:

- current `@UseSchema` / `SchemaRouteAspect` / `SchemaContextHolder` / `SchemaContextUtils` framework。
- current report grouping / internal repository routed SQL。

不可誇大:

- 不說 Nick 主導完整 schema route framework。
- 不說 Nick 主導完整 DB sharding / partition architecture。
- 不說 G3 data source routing 已完整落地。
- 不說已完成 migration / backfill / DDL owner。
