# Interview Notes

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

## 下一步

```text
antplay antplay-slot-game-api bet-record-sharding-schema-route Step 4
```
