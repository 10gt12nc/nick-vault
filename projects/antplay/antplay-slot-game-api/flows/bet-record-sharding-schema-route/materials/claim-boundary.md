# Claim Boundary

## Step 3 結論

本 flow 已完成 Step 3。可作 `antplay-slot-game-api` 高流量資料治理的 code-backed 面試素材，但還不是正式履歷 claim gate。Step 5 前不要直接把這條 flow 單獨寫進 `05 / 08`。

## 可講

- 我參與 / 研究過 AntPlay game-api 的 bet record、request log、transfer wallet transaction 分表與 schema routing。
- Code 透過 `@UseSchema` / AOP 依 `agentId` 切 schema group，並用 `pt_day`、`agent_id`、`time`、`id` 限制高流量表查寫範圍。
- Nick / `10gt12nc` 在 #167 分表、db partition v2、`@UseSchema`、table creator、bet record 查詢修正有 direct commits。
- 我知道這類設計的 failure window：AOP self-call、缺 `agentId`、UTC day boundary、查詢缺 partition key、建表 job disabled、logical-to-physical mapping 未確認。

## 可放 project-level consolidation 的方向

等 Step 5 後可以回填:

- bet record / request log / transfer transaction 分表與 schema route。
- high-traffic table query boundary、table creator governance、schema route failure-window。
- 只作 `antplay-slot-game-api` project-level 素材，不單獨拆成完整 sharding owner。

## 不可講

- 不講主導完整 AntPlay sharding architecture。
- 不講完整設計 / 維運全部 DB partition rule。
- 不講 production automatic table creation 已確認正常。
- 不講 ShardingSphere / middleware owner。
- 不講完整 wallet / ledger / reconciliation owner。
- 不講完整 RabbitMQ owner；request log async 已由另一條 flow 收斂。

## 證據分層

| Claim | 等級 | 說法 |
| --- | --- | --- |
| `@UseSchema` schema route | 真實開發過 + code-backed | Nick / `10gt12nc` 有 direct commits，可講參與維護 / 修正 |
| bet record `pt_day` / `agent_id` 分表查寫 | 真實開發過 + code-backed | #167 / bet 寫日表 / query fix commits 支撐 |
| transfer wallet transaction partition | 真實開發過 + code-backed | db partition v2 與 transfer transaction path 支撐 |
| table creator service | 真實開發過 + code-backed | table creator commits 支撐，但 current job disabled 要講清楚 |
| logical-to-physical live route | 待確認 | repo 內未找到完整 config，不能作已確認 claim |
| full sharding owner | 不可誇大 | 沒有足夠 evidence |

## 下一步

```text
antplay antplay-slot-game-api bet-record-sharding-schema-route Step 4
```
