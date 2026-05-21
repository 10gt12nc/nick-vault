# Claim Boundary

## Step 5 結論

本 flow 已完成 Step 5。結論是：可回填 `antplay-slot-game-api` project-level contribution consolidation，作「高流量交易明細表分表 / schema routing / partition key 查寫治理」的強化 evidence；但不單獨寫成完整 sharding architecture owner，也不直接更新 `05 / 08`。

## 可講

- 我參與 / 研究過 AntPlay game-api 的 bet record、request log、transfer wallet transaction 分表與 schema routing。
- Code 透過 `@UseSchema` / AOP 依 `agentId` 切 schema group，並用 `pt_day`、`agent_id`、`time`、`id` 限制高流量表查寫範圍。
- Nick / `10gt12nc` 在 #167 分表、db partition v2、`@UseSchema`、table creator、bet record 查詢修正有 direct commits。
- 我知道這類設計的 failure window：AOP self-call、缺 `agentId`、UTC day boundary、查詢缺 partition key、建表 job disabled、logical-to-physical mapping 未確認。

## 可放 project-level consolidation 的方向

可以回填:

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

## Step 5 可用履歷口徑

可併入 `antplay-slot-game-api` project-level bullet，建議不要單獨拉成一條:

- 參與 AntPlay game-api 高流量交易明細表治理，包含 bet record / request log / transfer wallet transaction 的 schema route、`pt_day` / `agent_id` partition key 查寫、table creator 與建表風險整理。

更短版:

- 參與 AntPlay game-api bet record / request log / transfer transaction 分表與 schema routing 維護，整理高流量表查寫邊界與建表風險。

不可寫:

- 主導 AntPlay 完整 sharding platform。
- 設計完整分庫分表架構。
- 負責 production 自動建表平台。
- 已確認所有 logical table 到 physical table routing 規則。

## 下一步

```text
antplay antplay-slot-game-api runtime-rtp-darkpool-player-control Step 4
```
