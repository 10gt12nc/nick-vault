# Claim Boundary - slot-bet-settle-rollback

日期: 2026-05-21

## 1. 目前可說

- 已完成 `slot-bet-settle-rollback` Step 3 Level 2 code-backed 深掃、Step 4 面試 case 與 Step 5 單條 flow claim gate。
- 可說「已深挖 AntPlay game API `/game/bet` 的下注 / 開獎 / settle / rollback 主線」。
- 可說「能解釋 single wallet 與 transfer wallet 在 bet flow 裡的責任差異」。
- 可說「能指出 bet record RESULT 後 settle 失敗、transfer wallet 扣款後 deadlock、request log MQ failure 等 failure windows」。
- 可說「能把補通知 job、request log MQ、deadlock compensation 風險拆成 source of truth / audit / repair / reconciliation 四層」。
- 可說「Nick / `10gt12nc` 有本 flow path-specific direct commits，包含 deadlock / transfer wallet compensation attempt、WalletFlag、request log MQ async 等」。

## 2. 可放履歷

可併入 `antplay-slot-game-api` project-level 履歷口徑:

> 參與 AntPlay slot game API / runtime 開發維護，處理下注、bet record、single / transfer wallet、provider settle / rollback、request log MQ 與補通知相關一致性議題。

證據層級:

- 真實開發過 + code-backed。
- `10gt12nc` 有 `GameFacade` / `GameFlowFacade` / `AgentApiFacade` / `CompensationService` 相關 direct commits。
- 更適合支撐 project-level bullet，不建議拆成 standalone「完整下注結算 owner」。

本輪仍不直接更新:

- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

原因: Flow Step 5 是單條 flow claim gate；05 / 08 仍應由 project-level consolidation / rolling resume package 統一吸收。

## 3. 不可誇大

- 不說主導完整 slot betting / settlement engine。
- 不說主導完整 wallet / ledger / reconciliation。
- 不說完整 provider callback owner。
- 不說完整 RabbitMQ / Kafka architecture owner。
- 不說 exactly-once / outbox。
- 不說 deadlock compensation 已完整落地，因為 refund / fail 標記目前看到被註解。

## 4. Step 5 判定

- `a2b2af5` / `54078fe` / `31d7a46` 足以支撐 Nick / `10gt12nc` 參與本 flow 的 deadlock / transfer wallet failure-window 維護。
- `d3e0002` / `71fff7b` 足以支撐 Nick / `10gt12nc` 參與 request log MQ async audit 維護。
- `257e136` / `7fa9b7a` / `09047fd` 只作 context evidence，因為 author 不是 Nick / `10gt12nc`。
- 目前本地 `develop` 中實際 refund / fail 標記呼叫被註解，因此補償只能講成「參與設計 / 維護 / 風險辨識」，不能講成完整 production compensation。

## 5. 下一步邊界

本 flow 已完成 Step 5。後續同 project 第二順位 `transfer-wallet-money-in-out` 也已完成 Step 5，第三順位 `request-log-rabbitmq-async` 已完成 Step 3；目前下一步是 `request-log-rabbitmq-async Step 4`，不能把本 flow 當成整個 project 已完成。
