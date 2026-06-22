# db-partition-job-report-routing Career Interview

日期: 2026-05-25
Step 4 補充日期: 2026-05-25
Step 5 補充日期: 2026-05-25

## Evidence Level

真實開發過 + code-backed，但要保守分層:

- Nick / `10gt12nc` direct evidence: `b754dae feat: db_partition v2`、`38b74bd fix`、`6866866 fix ag_report_player`。
- Current schema route framework: code-backed / 多人後續脈絡，主要由 Eliot / Arnold 建立與調整。
- Step 5 結論: 可作 project-level supporting evidence；不單獨新增 `05 / 08` bullet，不寫完整 DB sharding / schema routing owner。

## 30 秒說法

我在 AntPlay job repo 看過並參與過 bet record、request log、report 這類高流量表的分區與 schema routing 維護。這類改動不只是改表名，而是要確保 `agentId` 可以穩定 route 到正確 schema，SQL 還要帶 `pt_day`、`agent_id`、currency 等必要條件，避免跨代理或跨日資料污染。我的實際 evidence 包含 `db_partition v2` 與 `ag_report_player` 修正；完整 schema route framework 是多人接續完成，所以我會保守講成參與維護與 code-backed analysis。

## 90 秒人話版

這條 flow 我會用「job repo 裡的高流量報表和交易明細，怎麼從動態表名走向比較可治理的分區和 schema routing」來講。

AntPlay job repo 裡有幾類資料量很大的表，例如 bet record、request log、代理玩家報表。早期有些路徑會用日期和 agent 去拼動態表名，直覺上可以切資料，但缺點是每個 repository 都要自己拼表名，後續要改 DDL、index、migration 或查詢條件時，很容易漏掉某條 path。

後來 current code 的方向比較像固定表名加 route。像 `pt_bet_record`、`pt_request_log` 會用固定 logical table，再靠 `pt_day`、`agent_id` 這些條件收斂查詢；代理玩家報表則從 `report_agent_player_{agentId}` 這種 per-agent table，改往固定 `ag_report_player`，再補 `agent_id` 條件。schema 層則透過 `@UseSchema`，讓 Aspect 從 method 參數或物件裡找到 `agentId`，再依 agent 的 `dbGroupNum` 切到對應 schema。

我看這條 flow 時，會特別注意幾個風險。第一，Aspect 一定要拿到正確 `agentId`，不然可能打到錯 schema。第二，schema route 不能取代 SQL 條件，固定表名後還是要帶 `pt_day`、`agent_id`、currency、player 這些條件，不然會跨代理或跨日污染。第三，schema context 是 ThreadLocal，method 結束或 exception 時要 restore，否則同一條 thread 後續 query 可能被污染。第四，report summary / backup / delete 是 derived data，要考慮重跑和 partial failure。

我的 direct evidence 是 `db_partition v2`、`fix`、`fix ag_report_player` 這類 path-specific commit；current `@UseSchema` framework 是多人後續完成，所以我會保守講成參與分表 / report path 維護和 code-backed analysis，不會講成我主導完整 sharding architecture。

## 3 分鐘說法

我會把這條 flow 講成「高流量資料表分區與 schema routing 的一致性治理」，不是單純說我改了幾個 table name。

背景是 AntPlay job repo 裡有幾類資料量大的表：投注紀錄、request / response log、代理玩家報表。早期寫法偏向動態表名，例如依日期和 agent 拼出表；這樣短期很直覺，但長期會讓 repository 到處拼 SQL，DDL、index、migration、查詢條件都分散，任何一個 path 漏掉 agent 或日期條件，都可能查錯資料。

current code 的方向是固定表名加 route：`pt_bet_record`、`pt_request_log` 用 `pt_day` 和 `agent_id` 限制資料；`ag_report_player` 用 `agent_id`、player、day、currency 做 report key；然後透過 `@UseSchema` 的 Aspect 從 method 參數或物件裡抓 `agentId`，再用 agent 的 `dbGroupNum` 切到對應 schema。report projection 還會先 group by agent，再逐組呼叫 internal repository，避免一個 batch 裡不同 agent 混在同一次 routed SQL。

這裡 Senior 追問的重點不是「有沒有切 schema」，而是五個風險。第一，`agentId` 找不到要 fail fast，不能默默打 default schema。第二，固定表名後 SQL 還是要帶 `agent_id` / `pt_day`，schema route 不能取代資料條件。第三，`Agent.dbGroupNum` 是 route metadata，cache stale 或 migration 時要有 refresh / freeze / rollback。第四，schema context 是 ThreadLocal，exception、nested route、async thread 都要注意 restore。第五，report summary / backup / delete 是 derived data，要有重跑、checksum 或人工 repair 邊界。

我的 evidence 邊界也會講清楚：我有 `db_partition v2` 和 `fix ag_report_player` direct commits，可以說參與分表與 report path 維護；但完整 `@UseSchema` framework 是多人後續完成，所以不能說我主導整套 sharding architecture。這樣講比較扛得住追問，也比較符合實際貢獻。

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

Q: 為什麼 schema route 了還要在 SQL 帶 `agent_id`？

A: 因為 route 是 physical / logical schema 的隔離，`agent_id` 是資料層條件與防呆。兩者目的不同。少了 `agent_id`，即使在同 schema 內也可能跨 agent；少了 route，可能打錯庫。高流量多租戶資料通常兩層都要守。

Q: AOP route 和顯式 route 你怎麼取捨？

A: AOP 的好處是呼叫方簡潔，缺點是 hidden contract 很強，review 時不一定看得出有切 schema。若用 AOP，我會要求 fail fast、route log / metric、annotation 使用規範，以及 integration test；如果是極 critical path，顯式 route 或 route wrapper 會比較容易被 code review 看見。

Q: 你會怎麼驗證 migration 沒壞？

A: 我會分成 DDL / data / application 三層。DDL 看 index 和 unique key 是否支撐新查詢；data 看 row count、checksum、抽樣對帳、舊表新表雙讀比對；application 看錯 schema / missing `agentId` / slow query / report summary error 的 metric 和 alert。這些本 Step 沒有 production runbook evidence，所以面試時只能講 owner 建議，不說已全部落地。

Q: 如果 `Agent.dbGroupNum` 改錯怎麼辦？

A: 這是 route metadata 問題，比一般 config 更危險。要有變更凍結窗口、cache invalidation、灰度驗證、rollback plan，最好還有 route decision log，能查某次 query 是因哪個 agent metadata 打到哪個 schema。

## Senior / Lead 追問提綱

| 追問方向 | 保守回答 |
| --- | --- |
| table partition vs sharding | 本 flow 是 high-traffic table partition + schema route 維護，不是完整 sharding platform |
| ThreadLocal 安全嗎 | 同步 method 內可用 try / finally 控制；async / nested route / thread pool 要額外防 context leak |
| G3 是否完整 | current code 有 G3 enum 與 aspect selection，但 `DataSourceType` / `DataSourceConfig` / holder 明確 data source branch 只看到 DEFAULT/G1/G2；不宣稱完整 G3 |
| report summary 是否 idempotent | current Step 確認 summary / backup / delete path；backup duplicate / partial failure 仍需 owner runbook / idempotent backup key |
| Nick 貢獻程度 | direct evidence 是 `db_partition v2` / `fix ag_report_player`；schema framework 多人後續，不講完整 owner |

## 履歷邊界

可放:

- 參與 AntPlay slot job / event processing 開發維護，處理 Kafka / Quartz job、代理玩家報表聚合、big-win notification 與 bet record / request log / report 分表維護。
- 若面試追問，可展開 `db_partition v2`、`fix ag_report_player` 與 current `@UseSchema` code-backed analysis；履歷文字仍放在 project-level。

不可放:

- 不得寫成主導完整 DB sharding architecture。
- 不得寫成主導完整 schema routing framework。
- 不得寫成主導完整 migration / backfill / DDL。
- 完整 BI / report platform owner。

## 下一步

後續 rolling resume package 已完成；目前沒有預設下一步。
