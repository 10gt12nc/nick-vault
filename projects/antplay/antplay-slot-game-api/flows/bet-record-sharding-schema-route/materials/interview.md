# Interview Notes

## Step 4 狀態

- 日期: 2026-05-21
- 狀態: Step 5 已完成
- 用途: 正式面試 case；不直接更新 `05 / 08`
- 證據層級: 真實開發過 + code-backed；可回填 project-level consolidation，不升級成完整 sharding owner claim

## Q1. 這條 flow 在解什麼問題？

它在解高流量明細表的資料治理問題。Bet record、request log、transfer wallet transaction 都是寫入量高、查詢通常按 agent + 時間範圍查的資料。如果所有資料都用單 schema / 單表無限制累積，後面會遇到大表掃描、查單慢、補償查不到資料、維護困難。

## Q2. Schema route 怎麼做？

Method 用 `@UseSchema` 標記，`SchemaRouteAspect` 會從參數或物件找 `agentId`，再透過 `SchemaContextUtils` 決定 schema group。`SchemaContextHolder` 保存 route context，datasource 借 connection 時設定 schema / catalog。

## Q3. 分表 key 是什麼？

主要是 `pt_day` 與 `agent_id`，再搭配 `time`、`id` 或 transaction reference。Bet record 查詢也會限制日期 range，避免無限制掃描。

## Q4. 為什麼這不是單純 CRUD？

因為它碰到 route correctness。CRUD 只問資料能不能寫進去；這條 flow 要問寫到哪個 schema、查哪個日期 partition、跨日怎麼算、read replica 是否會 lag、physical table 是否已建立。

## Q5. 最大風險是什麼？

第一是 AOP 沒攔到，例如 self-call bypass，導致 schema context 不生效。第二是缺 `agentId` 或 `pt_day`，導致錯 schema 或大表掃描。第三是建表流程與 runtime 寫入不同步。current code 裡 table creator service 存在，但 create-table job 的實際 agent loop 已停用，所以 production 建表來源必須補證。

## Q6. 你會怎麼改善？

- 對所有 high-traffic table repository 加 partition-key checklist。
- 對 `@UseSchema` route 加測試，特別是 missing agentId / self-call / readOnly。
- 對 create table 流程加 missing-table alert、registry vs DB metadata check。
- 對跨日查詢與 UTC day boundary 補文件與測試。
- 對 dynamic table name 做 whitelist / pattern validation。

## Q7. 履歷怎麼講才不誇大？

可以講參與 bet record / request log / transfer transaction 的分表、schema routing 與高流量表查寫風險治理。不要講主導完整 sharding platform，也不要講 production 建表自動化已完整確認。

## 30 秒版本

我有參與 AntPlay game-api 高流量明細表的 schema route 與 partition key 治理。像 bet record、request log、transfer wallet transaction 這類資料，會透過 `@UseSchema` 依 `agentId` 切 schema，SQL 也要求帶 `pt_day`、`agent_id`、`time`、`id` 來避免大表掃描。我會特別看 AOP route、跨日查詢、建表流程與 read replica lag 這幾個 failure window。

## 90 秒版本

這條 flow 我會拆成 schema route 跟 table partition 兩層。Schema route 是 `@UseSchema` + `SchemaRouteAspect`，從 method args 或 object 取 `agentId`，再決定 schema group 與 datasource。Table partition 是 bet record、transfer wallet transaction、transfer request log 這些 SQL 都帶 `pt_day`、`agent_id`、`time`、`id`，讓查寫範圍可控。

Senior 角度我會追 route correctness，不只看 SQL。因為 AOP 可能被 self-call 繞過，缺 `agentId` 可能 route 錯，UTC `pt_day` 跟本地業務日界線可能造成漏查，read-only 查詢可能遇到 replica lag。current code 也顯示 table creator service 存在，但 create-table job 的實際 agent loop 已停用，所以我不會把它講成完整自動建表平台。

## 3 分鐘版本

AntPlay slot runtime 會產生大量交易明細，例如下注紀錄、request log、transfer wallet transaction。這些資料如果只放單表，後續查單、客服查詢、補償或報表都會碰到大表掃描與資料定位問題。所以這條 flow 的重點不是某一支 API，而是高流量資料的 route 與 query boundary。

我把它拆成兩層。第一層是 schema route。`@UseSchema` 透過 AOP 找 `agentId`，再決定 schema group 與 master / read-only datasource。這裡要注意的不是 annotation 本身，而是 method 有沒有真的經過 Spring proxy，否則 schema context 不會正確建立。

第二層是 partition key。Bet record、transfer wallet transaction、transfer request log 都會帶 `pt_day`、`agent_id`，再用 `time`、`id` 或 transaction reference 收斂查詢。這讓資料查寫可以按 agent / date 定位，而不是全表掃描。

我會主動講幾個風險。`pt_day` 是 UTC day，業務查詢若用本地日界線要小心轉換；read-only route 要注意剛寫入後的 replica lag；table creator service 與 `tool_create_table` registry 能確認，但 current create-table jobs 已停用 agent loop，所以 production 建表來源要補證；repo 內也沒看到 logical `pt_*` 到 physical table 的完整 mapping，所以不能誇大成完整 sharding architecture。

## STAR

### Situation

Slot runtime 的交易明細資料量大，bet record / request log / transfer wallet transaction 都需要依 agent 與日期定位，否則容易出現大表掃描、查單慢、補償找不到資料。

### Task

需要整理出這套 code 如何做 schema route、partition key 查寫與建表治理，並把風險整理成 Senior 面試能講清楚的 case。

### Action

追 `@UseSchema`、`SchemaRouteAspect`、`SchemaAwareDataSource`、`ShardingTableName`、bet record / transfer transaction / request log SQL、table creator service、create-table jobs 與相關 commit evidence，並把 current disabled-job caveat 標清楚。

### Result

形成可面試的 high-traffic data governance case：能講 schema route、partition key、UTC day boundary、AOP self-call、read replica lag、missing table、registry drift 與 logical-to-physical mapping 待確認；履歷上只保守講參與分表 / schema route 維護，不講完整 sharding owner。

## Lead / Architect 追問

### 如果 annotation 很方便，為什麼還有風險？

因為 annotation 只是宣告，真正生效要靠 Spring AOP 攔截。若 method 是 self-call，或物件不是 Spring 管理的 bean，就可能沒有 route context。這類設計要靠 proxy discipline、測試與 review checklist。

### 為什麼不只靠 tableName 分表？

只靠 tableName 會把 routing 邏輯散在 SQL 字串或 repository 裡，容易失控。這套 code 至少把 schema route 與 table constants 集中起來；但 tableName 如果來自 lookup 或字串，仍需要 whitelist / pattern validation。

### 你怎麼判斷查詢會不會爆？

先看是否強制帶 `agent_id` 與 `pt_day` range，再看 time range 是否有限制。Bet record 管理查詢有七天範圍限制，這是好的防線；其他補償 / 查單 path 也要檢查是否可能跨太多日期。

### 如果 production table 漏建，怎麼處理？

短期要能快速補建並避免重複建表；中期要加 missing-table alert、create-table idempotency、registry vs DB metadata 校驗；長期要把建表來源明確化，不能只靠 repo 裡已停用的 job 推測。

### Step 5 結論是什麼？

Step 5 已追 commit / blame / diff。可以回填 project-level contribution consolidation，因為 `10gt12nc` 對 schema route、bet record 分表、db partition v2、table creator 都有 direct evidence。但 live DB logical-to-physical mapping 仍待確認，所以不升級成完整 sharding owner claim。

## 下一步

```text
antplay antplay-slot-game-api runtime-rtp-darkpool-player-control Step 3
```
