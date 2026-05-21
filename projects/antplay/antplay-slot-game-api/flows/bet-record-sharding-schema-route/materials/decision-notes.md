# Decision Notes

## 1. Schema route 與 table partition 要分開講

`@UseSchema` 解決的是「去哪個 schema / datasource」，`pt_day` / `agent_id` 解決的是「查哪個日期與 agent 的資料」。兩者都重要，但不是同一層。

- Schema route 錯: 可能寫到錯 DB / 讀不到資料。
- Partition key 錯: 可能掃大表、漏查跨日資料，或找不到補償資料。

## 2. AOP route 的 owner checklist

- Annotated method 是否一定透過 Spring proxy 呼叫。
- Method args 是否明確有 `agentId`。
- `agentIds` 取第一個是否符合批次操作語意。
- `tableName` parsing 是否只接受受控格式。
- nested schema switch 是否可觀測。
- finally 是否 restore context。

## 3. `pt_day` 的 day boundary

Current code 用 UTC timestamp 轉 `LocalDate` 作 `pt_day`。這讓 partition key 穩定，但 UI / report / 客服查詢若用本地日界線，必須明確轉換，否則跨日附近容易漏查。

## 4. 建表治理

Table creator service 本身有 idempotent 思路：先看 `tool_create_table`，再 create table like base table，最後寫 registry。但 owner 要注意：

- registry 不能完全取代 DB metadata。
- create table job 必須有失敗告警。
- 預建窗口要涵蓋時區與流量高峰。
- current code 的 scheduled job agent loop 已停用，所以 production 建表來源要補證。

## 5. Read-only route

`@UseSchema(readOnly=true)` 對查詢可以減壓，但 money / compensation 查詢要注意 replica lag。若是查剛寫入的 bet record 或 transaction，owner 要決定是否強制 master。

## 6. 面試可以強調的取捨

- 用 annotation / AOP 能降低每個 repository 手動切 schema 的重複，但需要嚴格 proxy discipline。
- 用 `pt_day` / `agent_id` 顯式 key 可讓 SQL 可控，但所有新 query 都要遵守。
- Table creator 與 `tool_create_table` 可以讓分表治理可追蹤，但停用 job 或 registry drift 會變成 production risk。

## 下一步

```text
antplay antplay-slot-game-api runtime-rtp-darkpool-player-control Step 5
```
