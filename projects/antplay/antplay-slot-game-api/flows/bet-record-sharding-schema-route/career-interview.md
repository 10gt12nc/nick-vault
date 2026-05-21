# bet-record-sharding-schema-route Career Interview

## 狀態

- 日期: 2026-05-21
- Step: 5
- 履歷狀態: 單條 flow claim gate 已完成；可回填 project-level consolidation，不直接更新 `05 / 08`
- 證據層級: 真實開發過 + code-backed；Nick / `10gt12nc` 有 #167、db partition、`@UseSchema`、table creator、bet record query fix direct commits

## 30 秒講法

我有參與 AntPlay game-api 裡高流量交易明細表的分流與查寫治理，像 bet record、request log、transfer wallet transaction 這些資料不能只靠單張大表撐。這套 code 透過 `@UseSchema` 依 `agentId` 切 schema，SQL 也要求帶 `pt_day`、`agent_id`、`time`、`id` 收斂查詢範圍。我會特別看 AOP route 有沒有真的生效、跨日時區會不會漏查、建表流程會不會漏，以及 logical table 到實體分表的映射是否有足夠證據。

## 90 秒講法

這條 flow 可以分兩層講。第一層是 schema route：method 用 `@UseSchema` 標記，AOP 會從參數或物件裡找 `agentId`，再決定 schema group 與 master / read-only datasource。第二層是 high-traffic table partition key：bet record、transfer wallet transaction、transfer request log 這些查寫都會帶 `pt_day`、`agent_id`、`time`、`id`，避免全表掃描。

我會把它當成 production data governance 題，而不是單純 CRUD。因為真正的 failure window 在邊界：AOP self-call 可能繞過 route、缺 `agentId` 可能寫錯 schema、UTC `pt_day` 跟業務日界線可能造成漏查、table creator job 停用可能讓 physical table 漏建。current code 能確認建表 service 與 `tool_create_table` registry，但 logical `pt_*` table 到 physical table 的 live mapping 還需要 DB / migration 補證，所以面試時我會保守講成參與分表 / schema route 維護，不講成完整 sharding 平台 owner。

## 3 分鐘講法

AntPlay slot runtime 會產生大量交易明細資料，例如下注紀錄、request log、transfer wallet transaction。這些資料有共同特性：寫入量高、查詢多半按 agent 與時間範圍查，而且如果 partition key 沒帶好，就很容易變成跨大表或跨 schema 的查詢問題。

我整理這條 flow 時，把它拆成兩個責任。第一個責任是 schema route。`@UseSchema` 透過 AOP 從 method args、domain object 或 tableName 取出 `agentId`，再決定 schema group 與 datasource。這代表 route correctness 不只在 SQL，而是在 service / repository 是否真的走到 Spring proxy。像 repository 內使用 `proxy()` 呼叫，就是為了避免 self-call 繞過 AOP。

第二個責任是 partition query boundary。Bet record、transfer wallet transaction、transfer request log 這類 path 都會帶 `pt_day`、`agent_id`，再搭配 `time`、`id` 或 transaction reference。這樣查寫不需要靠全表掃描，也能支援後續查單、補償與客服查詢。

Senior 角度我會追幾個風險：第一，`agentId` 找不到時，route 可能錯或直接發生例外；第二，`pt_day` 是 UTC date，業務查詢若用本地日界線，跨日附近可能漏資料；第三，read-only route 可能遇到 replica lag；第四，建表服務雖然存在，但 current create-table job 的實際 agent loop 已停用，所以不能假設 production 仍由這個 job 自動建表；第五，repo 內沒看到 logical `pt_*` table 到 physical date / agent table 的完整 mapping，因此這一段要標成待確認。

所以我面試時會把成果講成「參與高流量交易明細表的 schema route、partition key 查寫與分表風險治理」，而不是誇大成「主導完整 sharding platform」。如果要追問 owner decision，我會說這類系統要有 partition-key checklist、route 測試、建表監控、missing-table alert、registry vs DB metadata check，並對需要強一致的查單避免盲目走 read replica。

## STAR 故事線

| 段落 | 內容 |
| --- | --- |
| Situation | Slot runtime 的 bet record、request log、transfer wallet transaction 是高流量明細資料，查寫若沒有 schema / partition boundary，容易變成大表掃描或查單錯位。 |
| Task | 需要讓資料依 agent / date 收斂，並讓面試時能說清楚 schema route、partition key、建表治理與不可誇大的邊界。 |
| Action | 追 `@UseSchema`、`SchemaRouteAspect`、datasource switch、`pt_day` / `agent_id` SQL、table creator service、create-table jobs、path-specific commits 與 current disabled-job caveat。 |
| Result | 形成一條可面試的 high-traffic data governance case：能講 route correctness、query boundary、UTC day、read replica lag、missing table、logical-to-physical mapping 待確認；不把它包裝成完整 sharding owner。 |

## Step 5 Claim 結論

可回填到 `antplay-slot-game-api` project-level consolidation:

- 參與 AntPlay game-api 高流量交易明細表治理，包含 bet record / request log / transfer wallet transaction 的 schema route、partition key 查寫與建表風險整理。

可講原因:

- `10gt12nc` 對 `@UseSchema` / `SchemaRouteAspect` 有 direct commits。
- `10gt12nc` 對 #167 bet record 分表、bet 寫日表、查詢七天限制與 `pt_bet_record` 修正有 direct commits。
- `10gt12nc` 對 db_partition v2、transfer wallet transaction / transfer request log `pt_day` / `agent_id` path 有 direct commits。
- `10gt12nc` 對 table creator service 有 direct commits。

仍要保守:

- current create-table jobs 已被他人 commit 停用，不能講成目前自動建表 active。
- repo 內未找到 logical `pt_*` 到 physical table 的完整 live routing config，不能講完整 sharding owner。

## Senior 追問速答

### 為什麼不是單純分表？

因為它同時有 schema route 與 table partition 兩層。`@UseSchema` 決定 DB/schema，`pt_day` / `agent_id` 決定查寫範圍。兩層任一層錯，結果都可能是查不到資料、寫錯 schema 或大表掃描。

### AOP route 最大風險？

Method 看起來有 annotation，但呼叫沒有經過 Spring proxy，例如 self-call。這會讓 schema context 沒設好。Bet record repo 用 `proxy()` 呼叫部分 method，就是在處理這類風險。

### 為什麼要強調 UTC `pt_day`？

因為 partition key 是 timestamp 轉 UTC day。它讓資料切分穩定，但客服、報表或後台如果用本地日界線查，跨日附近要做一致轉換，否則會漏查或重複查。

### 建表 job disabled 要怎麼講？

保守講：current repo 能確認 table creator service 與 registry，但 scheduled job 的實際 agent loop 已停用，所以 production 建表來源要另外補證。這是我會主動列出的風險，不會講成已確認自動建表。

### 如果你是 owner，會加什麼保護？

我會加 partition-key checklist、route unit / integration test、missing-agentId 防呆、missing-table alert、registry vs DB metadata check，並針對剛寫入後的查單評估是否強制 master，避免 read replica lag。

## 不可誇大邊界

- 不寫主導完整 sharding architecture。
- 不寫完整設計所有 DB partition 規則。
- 不寫 production automatic table creation 已確認正常。
- 不寫 ShardingSphere / middleware owner。
- 不把 table creator job 當成 current active behavior，因為 current code 已停用實際 agent loop。

## 後續回填

本 flow 已回填 project-level consolidation。後續 Rank 5 `runtime-rtp-darkpool-player-control Step 5` 與 project-level contribution claim consolidation refresh 已完成；下一步做 rolling resume package。

```text
rolling resume package
```
