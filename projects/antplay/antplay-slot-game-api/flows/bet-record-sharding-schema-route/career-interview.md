# bet-record-sharding-schema-route Career Interview

## 狀態

- 日期: 2026-05-21
- Step: 3
- 履歷狀態: 先作 code-backed 面試素材；不直接更新 `05 / 08`
- 證據層級: 真實開發過 + code-backed；Nick / `10gt12nc` 有 #167、db partition、`@UseSchema`、table creator、bet record query fix direct commits

## 30 秒講法

我有參與 / 研究過 AntPlay game-api 裡高流量交易明細表的治理。這類資料像 bet record、request log、transfer wallet transaction，不能只靠單張大表查寫，所以 code 透過 `@UseSchema` 依 `agentId` 切 schema，SQL 也要求帶 `pt_day`、`agent_id`、`time`、`id` 來收斂查詢範圍。我在看這條 flow 時，特別關注 schema route 是否會被 AOP 正確攔到、分表 key 是否完整、建表流程是否會漏，以及查詢跨日 / replica lag 這些風險。

## 90 秒講法

這條 flow 可以分兩層講。第一層是 schema route：method 用 `@UseSchema` 標記，AOP 會從參數或物件裡找 `agentId`，再決定 schema group 與 master / read-only datasource。第二層是 high-traffic table partition key：bet record、transfer wallet transaction、transfer request log 這些查寫都會帶 `pt_day`、`agent_id`、`time`、`id`，避免全表掃描。

我會把它當成 production data governance 題，而不是單純 CRUD。因為它真正的 failure window 在邊界：AOP self-call 可能繞過 route、缺 `agentId` 可能寫錯 schema、UTC `pt_day` 跟業務日界線可能造成漏查、table creator job 停用可能讓 physical table 漏建。current code 能確認建表 service 與 `tool_create_table` registry，但 logical `pt_*` table 到 physical table 的 live mapping 還需要 DB / migration 補證，所以面試時我會保守講成 code-backed 分表 / schema route 維護，不講成完整 sharding 平台 owner。

## 可放履歷的方向

Step 3 還不直接改正式履歷。等 Step 5 claim gate 後，可考慮回填到 project-level:

- 參與 AntPlay game-api 高流量交易明細表治理，包含 bet record / request log / transfer wallet transaction 的 schema route、partition key 查寫與建表風險整理。

## 不可誇大

- 不寫主導完整 sharding architecture。
- 不寫完整設計所有 DB partition 規則。
- 不寫 production automatic table creation 已確認正常。
- 不寫 ShardingSphere / middleware owner。
- 不把 table creator job 當成 current active behavior，因為 current code 已停用實際 agent loop。

## Step 4 要補

- STAR 故事線。
- Senior / Lead 追問。
- failure scenario 的口語版。
- 「真實開發過」與「code-backed 分析」的面試邊界。

```text
antplay antplay-slot-game-api bet-record-sharding-schema-route Step 4
```
