# db-partition-job-report-routing

日期: 2026-05-25

## 0. 閱讀定位

- Flow 中文名稱: DB partition / report schema routing。
- Flow slug: `db-partition-job-report-routing`。
- 完成狀態: Step 3 深掃初版完成。
- 證據層級: 真實開發過 + code-backed，但要分層。Nick / `10gt12nc` 有 `b754dae feat: db_partition v2` 與 `6866866 fix ag_report_player` direct commits；current `@UseSchema` / schema route 基礎主要由 Eliot / Arnold 在 2025-12 接續建立與調整。
- 本 flow 類型: high-traffic table partition + agent schema routing + report repository repair。
- 是否只確認到入口: 否，已確認 `@UseSchema` aspect、schema context、agent db group lookup、bet record / request log partition query、`ag_report_player` repository routing 與 path-specific history。

Source repo 遠端狀態: 先前同 repo `git fetch --all --prune` 已因內網不可達失敗；依 KB 不反覆重試。local `master` / local `origin/master` 都在 `d847357`，ahead / behind `0 / 0`，但未確認最新遠端，本文件依本地 refs / 本地 working tree 保守分析。

## 1. 白話導讀

這條 flow 處理的是「資料量大的表不能只靠原本一張表或原本一堆動態表名硬撐」的問題。

早期 code 曾用像 `bet_record_日期_agent_代理`、`request_log_日期_agent_代理`、`report_agent_player_代理` 這類動態表名。這種作法直覺，但會讓每個 repository 都要自己拼表名、自己處理日期與代理商，後續要改 schema 或分組時很容易漏。

current code 走成另一種方向:

- `pt_bet_record` / `pt_request_log` 用固定表名，加上 `pt_day` 與 `agent_id` 條件。
- `ag_report_player` / `ag_report_player_bak` 用固定表名，加上 `agent_id` 條件。
- 方法上標 `@UseSchema`，讓 Aspect 依 `agentId` 找代理商的 `dbGroupNum`，再把 DB connection 切到對應 logical schema。
- report projection 先把資料依 agent 分組，再逐組呼叫 internal repository，避免同一個 SQL batch 混到不同 schema。

所以這不是完整 database sharding platform。比較精準的說法是：它是 AntPlay job repo 裡的「高流量 table partition / schema route 維護」，讓 bet record、request log、report projection 可以在多 schema / 分區表設計下繼續查寫正確資料。

壞掉時最直覺的風險:

- `agentId` 沒被 Aspect 找到，SQL fallback 到 default schema，查錯庫。
- agent 的 `dbGroupNum` cache stale 或 null，導致 route 錯或拋錯。
- repository 忘記加 `agent_id` / `pt_day`，固定表名後可能查到跨代理或跨日資料。
- report summary / backup / delete 的 `ag_report_player` 若忘記 agent 條件，會污染其他 agent。
- schema context 沒 restore，後續同 thread query 可能串到錯 schema。
- G3 已定義，但 `SchemaContextHolder#set` current code 只明確處理 DEFAULT / G1 / G2，G3 data source routing 需要 Step 4 / Step 5 再追設定與 current behavior。

## 2. 初中階 Code 分層對照

| 分層 | Code / 資料 | 作用 | 本輪判斷 |
| --- | --- | --- | --- |
| Annotation | `UseSchema` | 標示此 method / class 需要 schema route；預設自動偵測 `agentId` | 已確認 |
| Aspect | `SchemaRouteAspect#switchSchema` | 攔截 `@UseSchema`，找 `agentId`，決定 `SchemaGroup`，push context | 已確認 |
| Agent -> DB group | `SchemaContextUtils#getDatabaseGroup` | 依 agent cache / DB fallback 找 `dbGroupNum` | 已確認 |
| Thread context | `SchemaContextHolder` | ThreadLocal 保存 schema，並切 master / slave data source | 已確認；G3 routing 待補 |
| Logical schema | `SchemaGroup` | DEFAULT / G1 / G2 / G3 對應 database name | 已確認 |
| Bet record partition | `BetRecordRepositoryImpl` | `pt_bet_record` + `pt_day` + `agent_id` 查詢 | 已確認 |
| Request log partition | `RequestLogRepositoryImpl` | `pt_request_log` + `pt_day` + `agent_id` 查寫 | 已確認 |
| Report grouping | `ReportAgentPlayerRepositoryCustomImpl` | 依 `agentId` group，再呼叫 internal repository | 已確認 |
| Report routed SQL | `ReportAgentPlayerRepositoryInternal` | `@UseSchema` 下對 `ag_report_player` 做 insert / update / find old | 已確認 |
| Report summary | `ReportAgentPlayerJob` + custom repository | 每 agent / player / currency summary、backup、delete | 已確認 |
| Current history | path-specific git log | `db_partition v2` + `fix ag_report_player` + 多人後續 schema route | 已確認 |

## 3. 最小架構圖

```text
ReportAgentPlayerJob / Kafka projection / notify job
  -> repository method with agentId
  -> @UseSchema
  -> SchemaRouteAspect
  -> SchemaContextUtils reads Agent.dbGroupNum
  -> SchemaContextHolder sets logical schema / data source
  -> fixed table SQL:
       pt_bet_record
       pt_request_log
       ag_report_player
       ag_report_player_bak
```

## 4. 正常流程圖

```text
caller passes agentId
  -> @UseSchema aspect intercepts method
  -> find agentId from parameter name / object field / tableName string
  -> get agent dbGroupNum
  -> map to SchemaGroup
  -> push SchemaContextHolder
  -> execute repository SQL on fixed table name
  -> finally close scope and restore previous context
```

Report projection path:

```text
new ReportAgentPlayer list
  -> group by agentId
  -> for each agent group
  -> internal.batchInsert / batchUpdate / findOldPlayers(agentId, list)
  -> @UseSchema routes to agent schema
  -> SQL uses ag_report_player + agent_id condition
```

## 5. 正常流程逐步說明

1. 需要切 schema 的 repository / service method 標 `@UseSchema`。
2. `SchemaRouteAspect` 在 method 執行前攔截。
3. Aspect 先從參數名找 `agentId` / `agentIds`，再從物件 getter / field 找 `agentId`；若參數名是 `tableName`，會從字串中的 `_agent_` 片段嘗試解析。
4. 找到 `agentId` 後，`SchemaContextUtils#getDatabaseGroup` 先看本地 cache。
5. cache miss 時，若目前 context 不是 default，會先暫切 default 去查 agent，避免在錯 schema 查 agent metadata。
6. 若 agent cache 找不到或 `dbGroupNum` 缺失，會 fallback 到 `AgentRepository#findById` 重讀 DB。
7. 取得 `dbGroupNum` 後，對應到 `SchemaGroup.DEFAULT / G1 / G2 / G3`。
8. `SchemaContextHolder#push` 保存 previous context / data source，設定新的 schema 與 master / slave routing。
9. Repository SQL 執行時，`SchemaAwareDataSource` 會對 borrowed connection apply current schema。
10. method 結束或丟 exception 時，Aspect 在 finally close scope，restore previous context，避免 thread 污染。

Bet record / request log path:

1. `b754dae feat: db_partition v2` 把 `bet_record` / `request_log` 類舊動態表名改成 `pt_bet_record` / `pt_request_log`。
2. 查寫條件改成明確帶 `pt_day`、`agent_id`、`time`、`id`。
3. `RequestLogService` 也改成產生 request log id，再寫入 `pt_request_log`。

Report path:

1. `15803ad` 先把 `ReportAgentPlayerRepositoryInternal` 拆出來，讓 per-agent group 可以各自走 `@UseSchema`。
2. `6866866 fix ag_report_player` 把 `report_agent_player_{agentId}` 改成固定 `ag_report_player`，並補上 `agent_id` 條件。
3. current code 中 summary / find old summary / insert / update / backup / delete 都用 `ag_report_player` 或 `ag_report_player_bak`，再靠 `agentId` route + SQL 條件限制資料。

## 6. DB / Redis / MQ / 外部 API

| 類型 | 已確認 | 說明 |
| --- | --- | --- |
| DB schema | `antplay_slot_game` / `antplay_slot_game_g2` / `antplay_slot_game_g3` | logical schema names；current holder 對 G3 data source routing 待補 |
| DB metadata | `Agent.dbGroupNum` | route 依據 |
| DB table | `pt_bet_record` | bet record 固定表 + `pt_day` / `agent_id` |
| DB table | `pt_request_log` | request / response log 固定表 + `pt_day` / `agent_id` |
| DB table | `ag_report_player` | proxy report projection / summary |
| DB table | `ag_report_player_bak` | report backup |
| MQ | Kafka `settled_bets` | report projection / notify / settle pool 上游共用背景，不是本 flow 唯一入口 |
| Redis | 無直接核心 | schema route 本身不靠 Redis |
| External API | 無直接核心 | request log 是外部 callback 記錄，但 routing flow 本身不是 HTTP adapter |

## 7. 資料狀態與 State Transition

```text
logical agent
  -> Agent.dbGroupNum
  -> SchemaGroup
  -> current ThreadLocal schema
  -> repository SQL
  -> fixed partition table + pt_day / agent_id
```

Report state:

```text
daily report rows by agent/player/day/currency
  -> summary older than three days
  -> upsert data_day = 0 summary row
  -> backup detail rows
  -> delete old detail rows
```

重要 source of truth 邊界:

- `Agent.dbGroupNum` 是 route metadata；如果它錯，schema route 會跟著錯。
- `pt_day` 是日分區 / 查詢剪裁條件；不是唯一 route 依據。
- `agent_id` 同時是 schema route key 與 SQL filter；兩者都要正確。
- report summary row `data_day = 0` 是 derived aggregate，不是原始投注 source of truth。

## 8. Failure Window / Consistency

| 風險 | 現況 | Senior / Owner 解讀 |
| --- | --- | --- |
| 找不到 `agentId` | `findAgentId` 回 null 時 current code 會走 `agentId < 0` 判斷，理論上有 NPE 風險；需 Step 4 / Step 5 再確認 current call sites 是否都帶 agentId | `@UseSchema` 的 contract 必須嚴格，不能靠猜 |
| agent cache stale | `SchemaContextUtils` 有 fallback DB reload | 有防呆，但 cache invalidation / dbGroup change rollout 仍需 runbook |
| G3 route | `SchemaGroup` 有 G3；`SchemaContextHolder#set` current code 明確處理 DEFAULT/G1/G2，未見 G3 data source branch | 若 production 有 G3，需要補查 data source config / bug context |
| nested schema switch | Aspect 用 `inUse` 記錄並 warn nested switch | 能觀察 nested routing，但不是防止所有 context leak 的完整方案 |
| context restore | finally close `schemaScope`，exception 時 clear | 基本防 ThreadLocal 污染；仍要確認 async / new thread 不共享 context |
| fixed table + missing condition | `pt_bet_record` / `pt_request_log` 必須帶 `pt_day` / `agent_id` | 少條件會造成跨日 / 跨 agent 查詢錯誤 |
| report old table migration | `report_agent_player_{agentId}` 改 `ag_report_player` | migration / backfill / DDL 未掃，不可宣稱完整遷移 owner |
| batch report grouping | custom repository 先 group by agent | 正確方向；若 list 中 agentId null 或混 group，需防資料污染 |
| summary backup delete | backup 後 delete 不是整體 durable state machine | 中途失敗可能重跑重複 backup 或 summary 增量重算，需 Step 4 轉成面試題 |

## 9. Retry / Compensation / Reconciliation

已確認:

- `@UseSchema` 用 try / finally restore context。
- `ReportAgentPlayerRepositoryCustomImpl` 先 group by agent，再呼叫 internal routed methods。
- `ReportAgentPlayerJob` per player catch exception，單一玩家失敗不停止整個 job。
- `b754dae` 把 bet record / request log 的舊動態表名改成固定 partition table + route/filter 方向。
- `6866866` 修正 `ag_report_player` query，使固定表名仍帶 agent filter。

待確認:

- `pt_bet_record` / `pt_request_log` / `ag_report_player` DDL、index、unique key。
- G3 data source routing 是否完整。
- `Agent.dbGroupNum` 改動時的 cache invalidation / migration runbook。
- schema route failure 時是否有 metric / alert。
- 歷史資料從舊 `*_agent_*` table 遷到新 `pt_*` / `ag_*` table 的 backfill 方式。

Owner 建議:

- 對 `@UseSchema` 加強 contract 檢查：找不到 `agentId` 時 fail fast 並明確 log method signature。
- 將 G1 / G2 / G3 data source mapping 做成完整 enum-driven，而不是只靠 if branch。
- 對 `Agent.dbGroupNum` 加版本 / refresh / cache TTL，避免長期 stale。
- 對 migration 補 row count / checksum / rollback plan。
- 對 report summary / backup / delete 補 job state 或 idempotent backup key。
- 對所有 routed SQL 建立檢查清單：固定表名時是否同時包含 `agent_id` / `pt_day` / currency 等必要條件。

## 10. Senior / Owner 設計取捨

| 決策點 | 現況 | 好處 | 風險 |
| --- | --- | --- | --- |
| 動態表名改固定表 + route | `pt_bet_record` / `pt_request_log` / `ag_report_player` | repository 不再大量拼表名，易於統一 route | 需要 route metadata / SQL filter 絕對正確 |
| Annotation + Aspect | `@UseSchema` | 呼叫方不用手動切 schema | 隱性 contract 強，找不到 agentId 會出錯 |
| Agent cache + fallback | `SchemaContextUtils` | 降低 metadata 查詢成本 | dbGroupNum 變更與 cache stale 要治理 |
| Per-agent grouping | report repository group by agent | 避免同 batch 混 schema | 增加 grouping / multiple DB round-trip 成本 |
| Fixed report table | `ag_report_player` + `agent_id` | 較容易做統一 SQL / backup | 需要 DDL / index 支撐，不可漏 agent filter |

## 11. 面試 / 履歷邊界摘要

可面試講:

- 「我參與過高流量 bet record / request log / report table 的分區與 schema route 維護，核心風險不是只有換表名，而是 route key、SQL filter、cache metadata、ThreadLocal restore 與 migration/backfill。」
- 「這類改動要把 repository、job、request log、report summary 一起看，否則容易出現查錯 schema、跨 agent 污染、或 summary backup/delete 中途失敗。」
- 「我會保守區分：我有 direct commit 的 `db_partition v2` / `fix ag_report_player` 是真實開發 evidence；current schema route framework 則是多人後續完成，我可以講 code-backed analysis，不誇大成完整 sharding owner。」

可放履歷方向:

- 只能作 project-level supporting evidence，併入 `antplay-slot-game-job` 的「bet record / request log / report 分表與 job 維護」。
- 不建議單獨寫「主導 DB sharding / partition architecture」。

不可誇大:

- 不寫完整 database sharding owner。
- 不寫完整 migration / backfill / DDL owner，因本 Step 未掃 DDL / production runbook。
- 不寫完整 `@UseSchema` framework owner；current framework 主要是 Eliot / Arnold 接續建立與調整。
- 不寫完整 BI / report platform owner。

下一步:

```text
antplay antplay-slot-game-job db-partition-job-report-routing Step 4
```
