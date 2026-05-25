# db-partition-job-report-routing Evidence

日期: 2026-05-25

## 掃描等級

Level 2 single-flow scan。

理由: Nick 指定單條 flow Step 3，目標是建立可讀學習包與面試素材初版；本輪已讀入口、主要 module、資料流、path-specific history 與重要 diff。尚未做 Level 3 全 commit / DDL / migration runbook 深追。

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

## 待確認

- DDL / index / unique key: `pt_bet_record`、`pt_request_log`、`ag_report_player`、`ag_report_player_bak`。
- G3 data source routing 是否完整；current `SchemaContextHolder#set` 明確處理 DEFAULT / G1 / G2，未見 G3 branch。
- `@UseSchema` 找不到 `agentId` 的實際 production behavior；current code 需補 null safety 判斷。
- agent `dbGroupNum` 變更時的 cache invalidation / migration procedure。
- 歷史 table migration / backfill / checksum / rollback runbook。
- Kafka listener / report job 是否有 schema route failure metric / alert。

## Claim Boundary

真實開發過:

- `b754dae feat: db_partition v2`
- `6866866 fix ag_report_player`

Code-backed / 分析過:

- current `@UseSchema` / `SchemaRouteAspect` / `SchemaContextHolder` / `SchemaContextUtils` framework。
- current report grouping / internal repository routed SQL。

不可誇大:

- 不說 Nick 主導完整 schema route framework。
- 不說 Nick 主導完整 DB sharding / partition architecture。
- 不說已完成 migration / backfill / DDL owner。
