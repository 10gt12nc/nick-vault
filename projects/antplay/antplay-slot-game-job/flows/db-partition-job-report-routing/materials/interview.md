# db-partition-job-report-routing Interview Notes

日期: 2026-05-25
Step 4 補充日期: 2026-05-25
Step 5 補充日期: 2026-05-25

## 核心面試題

這條 flow 適合回答:

- 高流量 table partition / schema routing 怎麼設計？
- 從動態表名改固定表 + schema route，有哪些風險？
- `@UseSchema` / AOP routing 的 hidden contract 是什麼？
- report projection 遇到多 agent / 多 schema 時怎麼避免資料污染？
- schema context 用 ThreadLocal 時怎麼避免 leak？

## 3 分鐘主線

1. 背景: bet record、request log、report 都是高流量資料，早期動態表名容易讓 repository 邏輯分散。
2. 改法: current code 用固定 table name + `pt_day` / `agent_id`，再用 `@UseSchema` 依 agent `dbGroupNum` 切 schema。
3. 風險: route key 要準、SQL filter 要完整、cache metadata 要可靠、ThreadLocal 要 restore、migration 要可回滾。
4. Evidence: Nick / `10gt12nc` 有 `db_partition v2` 與 `fix ag_report_player` direct commits；schema route framework 是多人後續完成。
5. Owner 觀點: 我不會只看能不能查到資料，還會補 fail-fast、metric、DDL / index、backfill checksum、report job idempotency。

## 30 秒版本

這條我會講成高流量表分區與 schema route 的維護經驗。AntPlay job repo 裡 bet record、request log、report projection 都需要依日期與 agent 隔離資料；current code 走固定表名加 `pt_day` / `agent_id`，再用 `@UseSchema` 依 agent `dbGroupNum` 切 schema。我的 direct evidence 是 `db_partition v2` 和 `fix ag_report_player`，可以講參與分表 / report path 維護；但 schema route framework 是多人後續完成，所以不誇大成完整 sharding owner。

## 90 秒版本

這條 flow 的重點不是單純換表名，而是多租戶高流量資料的 route correctness。早期 code 曾依日期和 agent 拼動態表名，後來 current code 改成 `pt_bet_record`、`pt_request_log`、`ag_report_player` 這類固定表名，再用 `pt_day`、`agent_id`、currency 等條件限制資料。

schema 的部分透過 `@UseSchema` Aspect 處理。Aspect 會從 method 參數或物件裡找 `agentId`，再用 agent 的 `dbGroupNum` 決定 schema，最後用 ThreadLocal context 讓 connection apply 到對應 DB。report projection 還會先 group by agent，再分別呼叫 internal repository，避免一批資料混不同 schema。

我會把風險講清楚：`agentId` 找不到要 fail fast、SQL 條件不能漏、agent route metadata 要避免 stale、ThreadLocal 要 restore、report summary / backup / delete 要考慮 partial failure。我的 direct evidence 是 `db_partition v2` 和 `fix ag_report_player`，所以我會說參與分表與 report path 維護，不說主導完整 sharding framework。

## 3 分鐘版本

我會把它拆成背景、設計、風險、我的邊界四段講。

背景是 AntPlay job repo 有幾類資料量大的表：投注紀錄、request / response log、代理玩家報表。這些資料都跟 agent、日期、玩家、幣別有關，如果用舊式動態表名，每個 repository 都要自己拼 table name，長期會讓 DDL、index、migration、查詢條件變得很分散。

設計上 current code 走固定表名加 schema route。`pt_bet_record` 和 `pt_request_log` 透過 `pt_day` / `agent_id` 限制範圍，`ag_report_player` 用 `agent_id + player + day + currency` 作 report key。schema route 則用 `@UseSchema` 的 AOP，從參數或物件抓 `agentId`，查 agent 的 `dbGroupNum`，再用 `SchemaContextHolder` 設 ThreadLocal context，讓 connection 切到對應 schema。

真正難的是一致性邊界。第一，route key 要準，找不到 `agentId` 不能默默 fallback。第二，固定表名後 SQL 一定要保留 `agent_id` / `pt_day`，schema route 不能取代資料條件。第三，`dbGroupNum` 是 metadata，cache stale 或 migration 時要有 refresh 與 rollback。第四，ThreadLocal route 要保證 finally restore，nested call 和 async thread 要特別小心。第五，report summary / backup / delete 是 derived data，partial failure 需要 checksum、job state 或 repair runbook。

我的貢獻邊界是：`db_partition v2` 和 `fix ag_report_player` 有 direct commit evidence，可以保守說參與分表與 report path 維護；但 current `@UseSchema` framework 是多人接續完成，所以我不會說主導完整 DB sharding architecture。

## Failure Scenarios

| Scenario | 會發生什麼 | 回答重點 |
| --- | --- | --- |
| `agentId` 缺失 | Aspect 無法正確 route，可能 NPE 或 fallback 錯 schema | route contract 要 fail fast |
| `dbGroupNum` stale | 查到舊 schema | cache TTL / refresh / migration freeze |
| SQL 少 `agent_id` | 固定表名後跨 agent 污染 | routed schema 不等於可省 SQL filter |
| SQL 少 `pt_day` | 查詢掃太大或跨日錯誤 | partition key 要進 query condition |
| ThreadLocal 未 restore | 後續同 thread 查錯 schema | try/finally + scope restore |
| G3 未完整 route | G3 agent 打到錯 data source 或 default | enum-driven mapping + config test |
| report backup/delete 中斷 | summary / backup / delete 不一致 | job state / checksum / retry / manual runbook |

## STAR

Situation:

AntPlay slot job repo 裡的 bet record、request log、代理玩家報表都屬於高流量資料。舊式動態表名容易讓 repository 分散處理日期 / agent table route，後續要做 schema route 或 report repair 時會有漏條件風險。

Task:

在不誇大成完整 sharding 平台的前提下，梳理分表 / schema route 的資料流、風險與可面試說法，讓它能支撐 Senior Backend 的 high-traffic data governance 題目。

Action:

- 讀 `@UseSchema`、`SchemaRouteAspect`、`SchemaContextHolder`、`SchemaContextUtils`。
- 對照 `pt_bet_record` / `pt_request_log` 的 `pt_day + agent_id` query pattern。
- 對照 `ag_report_player` 的 per-agent grouping、internal repository route 與 `agent_id / player / day / currency` 條件。
- 追 `b754dae db_partition v2` 與 `6866866 fix ag_report_player` direct commits。
- 把 current framework 多人後續脈絡與 Nick direct evidence 分開。

Result:

形成可講 3 分鐘的面試 case：重點是 route key、SQL filter、metadata cache、ThreadLocal restore、migration / report summary failure window，以及「參與維護」與「完整 owner」的 claim 邊界。

## Senior / Lead 追問

Q: 你怎麼避免 `@UseSchema` 找不到 agentId？

A: 我會把這當成 annotation contract。方法簽名必須明確有 `agentId`，或物件要有 `getAgentId()`；找不到時不要 fallback default，要 fail fast 並 log method signature。Step 5 已確認本 flow 主要 report call sites 都有傳 `agentId`，但 aspect 層仍缺 null safety，這點不能粉飾。

Q: ThreadLocal schema context 有什麼坑？

A: 同步 call path 要靠 try / finally restore；nested route 要能觀測；async thread 或 thread pool 不能假設會自動帶 context。若 route logic 進到 Kafka async、executor、transaction callback，就要明確傳遞或重新決定 route。

Q: 你怎麼看 AOP routing？

A: AOP 讓呼叫方乾淨，但 hidden contract 強，code review 容易漏。我的取捨是：一般 repository path 可以用 AOP，但要有 fail-fast、metric、test；極 critical migration / repair job 則可以用 explicit route wrapper，讓 route 在 call site 被看見。

Q: 分表後最容易出錯的是什麼？

A: 不是表名，而是 query condition 和 migration。固定表名後少 `agent_id` 或 `pt_day` 會造成跨 agent / 跨日；migration 若沒有 row count、checksum、雙讀比對，可能資料看似能查但其實漏或重。

Q: report summary 為什麼是風險？

A: 它是 derived aggregate，不是 source of truth。summary、backup、delete 若中途失敗，可能重跑重複加總、重複 backup 或漏刪。面試時我會建議 job state、idempotent backup key、checksum 與 repair runbook，但不說 current code 已完整具備。

Q: 這能放履歷嗎？

A: 能作 project-level supporting evidence，併入「參與 AntPlay slot job / event processing、report projection、分表維護」。不單獨寫主導 DB sharding，因為 direct evidence 是分表與 report path repair，完整 schema framework 是多人後續完成。

Q: G3 route 完整嗎？

A: 不能這樣說。current code 有 `SchemaGroup.G3` 和 aspect 選路，但 `DataSourceType`、`DataSourceConfig`、`SchemaContextHolder#set` 只看到 DEFAULT/G1/G2 對應的 master/slave 與 G2 data source。面試只能說我有發現這個落差，會要求 config test / route test / runbook 補齊，不能說 G3 已完整落地。

## 面試陷阱

- 不要把 partition table 和 sharding platform 混成同一件事。
- 不要說 `@UseSchema` 自動解決所有資料隔離；SQL 條件仍要嚴格。
- 不要說 ThreadLocal 一定安全；async / nested route / exception 都要看。
- 不要把多人後續 schema route framework 說成 Nick 一個人主導。
- 不要在沒看 DDL 前承諾 unique key / index 已完整。

## 反問面試官

- 你們的多租戶 / 多 schema 是靠 application routing、DB proxy、還是 physical sharding？
- agent / tenant 的 route metadata 變更時，有沒有 freeze window、cache invalidation 和 rollback？
- migration / backfill 有沒有 row count、checksum、dual-write 或 shadow read？
- schema route failure 有沒有 metric / alert？還是只靠 exception log？

## 常見陷阱

- 把 `@UseSchema` 講成萬能隔離，卻忘記 SQL 還要 `agent_id` / `pt_day`。
- 把 direct commit 的分表維護誇成完整 schema framework owner。
- 沒看 DDL 就承諾 index / unique key / migration 都完整。
- 忘記 schema context 是 ThreadLocal，對 async / nested route 沒有答案。
- 把 report summary row 當 source of truth，而不是 derived data。

## 反問面試官

- 你們 tenant / agent route metadata 是誰維護？改錯時怎麼 rollback？
- schema route failure 有 metric 嗎？能查到某次 request 打到哪個 schema 嗎？
- migration 時有沒有 shadow read、checksum、row count、雙寫或回滾表？
- report / BI derived table 有沒有 idempotent rebuild 或 repair runbook？

## Step 5 Claim Gate

- 已補 current call sites: 主要 report job / repository call sites 都有 `agentId`，但 aspect 本身仍有找不到 agentId 的 NPE / fail-fast 缺口。
- 已補 G3: logical enum / aspect branch 有 G3，data source mapping 未確認，不宣稱完整。
- 已補 path-specific blame / important commit stat: Nick direct evidence 集中在 `db_partition v2`、`fix`、`fix ag_report_player`；current schema route framework 主要是 Eliot / Arnold。
- Claim gate 結論: 作 project-level supporting evidence，不單獨更新 `05 / 08`，下一步應 refresh `antplay-slot-game-job` contribution claim consolidation。
