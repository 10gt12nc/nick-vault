# transfer-wallet-money-in-out Claim Boundary

日期: 2026-05-21

## 1. Step 5 Claim 狀態

本 flow 目前已完成 Step 5。

結論:

- 可回填 `antplay-slot-game-api` project-level contribution consolidation，作 transfer wallet / DB + Redis consistency / 分表查單 / wallet update failure-window 的強化 evidence。
- 不能單獨升級成「Nick 主導完整 transfer wallet」claim，因為 transfer wallet API 初版主要 commit author 是 Derek。
- Nick / `10gt12nc` direct evidence 集中在後續分表 / schema route / transaction / lookup table path / `updatePlayerWallet` rows check 與 deadlock 補償關聯改造。

## 2. 可以說

- 我理解並整理過 AntPlay `antplay-slot-game-api` transfer wallet 的轉入 / 轉出 / 全轉出流程。
- 這條 flow 涉及 signature、`transferReferenceId` 防重、transaction record、order lookup、DB wallet、Redis balance 與 request log。
- Nick / `10gt12nc` 有 transfer wallet 相關後續維護 evidence，例如 `updatePlayerWallet` rows check / boolean return、分表 / schema route 相關 commits。
- 可保守說「參與 transfer wallet 相關維護與分析，處理 transaction / lookup / DB + Redis balance / 分表路由的一致性邊界」。

## 3. 可面試講

- transfer wallet 的 idempotency / consistency 設計風險。
- Redis short lock 和 transaction table 的責任差異。
- DB + Redis dual-write failure window。
- transfer-out 併發與 negative balance 防護。
- lookup table 如何支撐單筆交易查詢。

## 4. 可放履歷

只建議併入 project-level bullet:

> 參與 AntPlay slot game API / runtime 開發維護，處理下注結算、transfer wallet、transaction / order lookup、DB / Redis balance、分表與 request log 相關一致性議題。

不建議單獨寫:

> 主導 transfer wallet 系統。

## 5. 不能說

- 不能說 Nick 主導 transfer wallet 初版。
- 不能說 Nick 完整設計 wallet / ledger / reconciliation。
- 不能說已做完整 exactly-once。
- 不能說所有 failure compensation / repair 已完整落地。
- 不能把本 flow 單獨寫成完整 `05 / 08` 主成果；若要更新 05 / 08，必須透過 project-level contribution consolidation 或 rolling resume package。

## 6. Step 5 Evidence

已追:

- `54078fe`: `updatePlayerWallet` 改為 boolean，加入 DB update rows check，rows != 1 時回 false，不做 Redis increment。
- `718a207`: transfer transaction / request log / order lookup / wallet table 轉向分表 / schema route / BufferId path，影響 transfer wallet runtime final behavior。
- `99c63ff`、`aaddfdc`、`3531f42`、`eb2573a`: transfer wallet、order lookup、transaction、request log table path / table creator 類 direct evidence。
- `41cd5ae`: transfer-out negative balance 補強，author 不是 Nick，只作 context。
- current `develop` blame 仍顯示 `updatePlayerWallet` rows check / boolean return 由 `10gt12nc` 貢獻；API controller 初版與多數 orchestration 仍主要是 Derek / 後續他人。

## 7. 下一步

```text
rolling resume package
```
