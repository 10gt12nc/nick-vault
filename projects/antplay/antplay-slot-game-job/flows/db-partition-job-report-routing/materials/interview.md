# db-partition-job-report-routing Interview Notes

日期: 2026-05-25

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

## 下一步 Step 4 應補

- 把本檔轉成完整 30 秒 / 90 秒 / 3 分鐘說法。
- 補 Senior / Lead 追問: AOP vs explicit routing、ThreadLocal vs transaction boundary、table partition vs schema sharding。
- 補 STAR 中不可誇大邊界。
