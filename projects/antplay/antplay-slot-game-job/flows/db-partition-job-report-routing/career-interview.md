# db-partition-job-report-routing Career Interview

日期: 2026-05-25

## Evidence Level

真實開發過 + code-backed，但要保守分層:

- Nick / `10gt12nc` direct evidence: `b754dae feat: db_partition v2`、`6866866 fix ag_report_player`。
- Current schema route framework: code-backed / 多人後續脈絡，主要由 Eliot / Arnold 建立與調整。
- 正式履歷: 只能併入 project-level job / event processing / report partition supporting evidence，不單獨寫完整 DB sharding owner。

## 30 秒說法

我在 AntPlay job repo 看過並參與過 bet record、request log、report 這類高流量表的分區與 schema routing 維護。這類改動不只是改表名，而是要確保 `agentId` 可以穩定 route 到正確 schema，SQL 還要帶 `pt_day`、`agent_id`、currency 等必要條件，避免跨代理或跨日資料污染。我的實際 evidence 包含 `db_partition v2` 與 `ag_report_player` 修正；完整 schema route framework 是多人接續完成，所以我會保守講成參與維護與 code-backed analysis。

## 90 秒說法

這條 flow 的背景是 AntPlay 的投注紀錄、request log、代理玩家報表資料量都很大，早期 code 曾用動態表名，例如依日期和 agent 拼出不同表。後來 current code 走向固定表名加 partition key，再用 `@UseSchema` 依 agent 的 `dbGroupNum` 切 logical schema。

我會從三個風險看這件事。第一是 route key，Aspect 一定要拿到正確 `agentId`，否則可能查錯 schema。第二是 SQL 條件，固定表名後必須帶 `pt_day`、`agent_id`，report 還要帶 currency / player 條件，不然會跨 agent 污染。第三是 context restore，schema context 是 ThreadLocal，method 結束或 exception 時要 restore，否則同 thread 後續 query 會被污染。

我實際能講的 evidence 是 `db_partition v2` 和 `fix ag_report_player` 這類 path-specific commit；current `@UseSchema` 框架是多人後續完成，所以正式履歷我只會寫參與分表 / report path 維護，不會誇大成完整 sharding architecture owner。

## STAR

Situation:

AntPlay job repo 中 bet record、request log、代理玩家報表都屬於高流量資料。舊式動態表名與 per-agent report table 會讓 repository 分散處理 table routing，後續調整 schema / partition 時容易漏條件。

Task:

把查寫路徑整理成更穩定的 partition / schema route pattern，讓 repository 可以用固定 table name，並透過 `agentId` / `pt_day` / schema context 保持資料隔離。

Action:

- 在 bet record / request log path 使用 `pt_bet_record`、`pt_request_log`，用 `pt_day` 與 `agent_id` 做查詢範圍。
- 在 report path 將 `report_agent_player_{agentId}` 類型改向固定 `ag_report_player`，並補 `agent_id` 條件。
- 讀 current `@UseSchema` / `SchemaRouteAspect` / `SchemaContextHolder`，確認它依 agent `dbGroupNum` 切 schema，並在 finally restore context。
- 區分 direct commit evidence 與多人後續 framework，不把整體 schema route 誇成個人完整主導。

Result:

可以形成一個 Senior Backend 面試 case：高流量表拆分後，真正要守住的是 route key、SQL filter、metadata cache、context restore、migration / backfill 與 report summary 的 consistency。

## 常見追問

Q: 為什麼不用每個 agent 一張表？

A: 每 agent 一張表直覺，但 repository 會到處拼表名，migration / DDL / index / query path 都容易分散。固定表名加 partition key / schema route 可以集中治理，但前提是 route metadata 和 SQL filter 要嚴格。

Q: `@UseSchema` 最大風險是什麼？

A: 它把 route 變成隱性 contract。呼叫 method 時如果沒有可解析的 `agentId`，或 agent cache 的 `dbGroupNum` 錯，SQL 可能打到錯 schema。我的 owner 判斷會是找不到 `agentId` 要 fail fast，並對 schema route failure 補 metric / alert。

Q: 你能說自己主導 sharding 嗎？

A: 不能。我的 direct evidence 是 `db_partition v2` 與 `ag_report_player` 修正；current schema route framework 是多人後續完成。我可以講我參與分表 / report path 維護、理解 schema route 的風險，但不會說主導完整 sharding architecture。

Q: 這和 report projection 有什麼關係？

A: report projection 會累積代理玩家每日投注 / 輸贏資料。projection 寫入時若同一批資料包含不同 agent，就要先 group by agent，再分別進 routed internal repository；summary / backup / delete 也要依 `agentId` route 且 SQL 帶 agent filter。

## 履歷邊界

可放:

- 參與 AntPlay slot job / event processing 開發維護，處理 Kafka / Quartz job、代理玩家報表聚合、big-win notification 與 bet record / request log / report 分表維護。

不可放:

- 主導完整 DB sharding architecture。
- 主導完整 schema routing framework。
- 主導完整 migration / backfill / DDL。
- 完整 BI / report platform owner。

## 下一步

```text
antplay antplay-slot-game-job db-partition-job-report-routing Step 4
```
