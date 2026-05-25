# db-partition-job-report-routing Claim Boundary

日期: 2026-05-25
Step 4 補充日期: 2026-05-25
Step 5 補充日期: 2026-05-25

## 結論

本 flow Step 5 claim gate 已完成。它可作 `antplay-slot-game-job` project-level supporting evidence，但不直接更新 `05 / 08`，也不單獨拉成正式 DB sharding bullet。

可保守定位:

- 真實開發過: Nick / `10gt12nc` 有 `db_partition v2`、`fix`、`fix ag_report_player` direct commits，支撐 bet record / request log / report 分表與 report path repair。
- Code-backed: current `@UseSchema` / schema routing / report repository 分組查寫已讀懂。
- 不可誇大: current schema route framework 是多人後續建立與修正，不是 Nick 完整主導。

## 可放履歷

只建議併入既有 project-level bullet:

> 參與 AntPlay slot job / event processing 開發維護，處理 Kafka / Quartz job、代理玩家報表聚合、big-win notification 與 bet record / request log / report 分表維護。

## 可面試講

- 高流量表從動態表名改固定表 + partition key / schema route 的風險。
- `@UseSchema` / AOP routing 如何從 `agentId` 找 schema。
- `agentId`、`pt_day`、currency、playerId 等 query condition 對資料隔離的重要性。
- schema context restore / ThreadLocal leak 風險。
- report projection 多 agent batch 要先 group by agent，再分別 routed query。

## 不可誇大

- 不說主導完整 DB sharding architecture。
- 不說主導完整 `@UseSchema` framework。
- 不說完整 migration / backfill / DDL / index owner。
- 不說完整 report / BI platform owner。
- 不說 G3 routing 已完整確認；current code 有 G3 enum / aspect branch，但未看到 G3 data source mapping。
- 不把 `@UseSchema` framework 說成 Nick 完整主導；current route framework 主要是 Eliot / Arnold 後續脈絡。

## 後續回填條件

若後續要升級成更強履歷 claim，需補以下 evidence:

- DDL / index / unique key。
- Nick direct commits 是否包含更多 schema route / migration / report repair。
- production issue / ticket / MR 說明 Nick 實際處理過 route bug 或 partition migration。
- G3 data source routing current behavior與 production runbook。

## Project-level 回填

已回填 `antplay-slot-game-job contribution claim consolidation refresh`:

- `db_partition v2` / `fix ag_report_player` 作「分表 / report path 維護」直接 evidence。
- `@UseSchema` / G2 routing / ThreadLocal restore 作 code-backed 面試素材。
- G3 / DDL / migration / backfill 作不可誇大與待補邊界。
