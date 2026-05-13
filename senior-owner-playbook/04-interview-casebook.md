# Senior / Lead / Architect 面試案例

這份不是背稿，而是用來把 flow 轉成面試可講的 case。

## 案例 1：金流 callback 一致性

對應 flow：

- `payment-provider-callback`
- `payment-order-provider-request`
- `withdrawal-auto-review-refund`

面試主軸：

第三方金流 callback 不是單純接 API。真正困難在於 provider 可能重送、timeout、欄位不一致、簽章失敗、訂單已終態、MQ 副作用失敗、人工補償介入。Senior 要能定義訂單 lifecycle、冪等 key、callback audit、對帳查詢與補償邊界。

可講重點：

- callback 必須先驗簽與正規化欄位。
- 訂單狀態轉移要單向、可審計。
- 重複 callback 應該 no-op 或回傳一致結果。
- 入分 / 扣分副作用不能和 callback ack 混在一起亂做。
- 若 MQ 或下游失敗，要有可查詢的 pending / failed 狀態。

Lead / Architect 追問：

- 如果 provider 說成功，但你 DB transaction rollback 怎麼辦？
- 如果 DB 成功，但 MQ 發送失敗怎麼辦？
- callback log 是 source of truth 嗎？
- 怎麼設計 reconciliation job？
- 什麼時候需要人工補償？

## 案例 2：Transfer wallet 轉入轉出

對應 flow：

- `transfer-wallet-transfer-in-out`
- `transfer-wallet-ledger`
- `provider-transfer-in-out`
- `transfer-compensation-retry`

面試主軸：

Transfer wallet 的核心是半完成狀態。轉出成功、轉入失敗、provider timeout、reference id 重複、retry 重入，都可能造成金額錯誤。Senior 要能把 transfer reference、ledger、狀態機、補償 retry 與人工查詢設計清楚。

可講重點：

- reference id 必須可追蹤且可冪等。
- ledger 要能反映每一步狀態，不只看 API response。
- timeout 不能直接當失敗。
- compensation 要有 retry count、next retry time、exhausted 狀態。
- 後台要能查到目前卡在哪一段。

## 案例 3：下注 / 派彩 / rollback

對應 flow：

- `bet-settlement`
- `game-round-settlement`
- `antplay-bet-settle-rollback`

面試主軸：

遊戲結算的風險是玩家餘額、round log、provider transaction、BI retention 不一致。Senior 要能找出真正的 commit boundary，避免 reporting job 或 request log 被誤當成結算真相。

可講重點：

- round id / transaction id 是核心冪等點。
- wallet mutation 與 round log 應有一致的 commit 狀態。
- rollback 不是反向再打一筆就好，需要狀態與副作用邊界。
- BI / report 是 projection，不應是 source of truth。

## 案例 4：Kafka / MQ 可靠性

對應 flow：

- `settled-bets-kafka`
- `bet-record-request-log-mq`
- `request-log-monitor-alert-mq`

面試主軸：

非同步不是把資料丟出去就好。要看資料進 MQ 前後的 durable state、consumer retry、DLT、重複消費、刪除時機與 audit。

可講重點：

- Redis delete-before-ack 是典型風險。
- 重要事件應有 outbox 或 durable pending state。
- consumer 要能 idempotent。
- DLT 不是垃圾桶，要有重放與告警流程。
- 監控要能從 request id / trace id 找回整條 flow。

## 案例 5：K3s / rollout / observability

對應 flow：

- `k3s-migration-track`
- `iwin-service-rollout`
- `gameserver-phased-rollout`
- `observability-pipeline`

面試主軸：

平台部署不是 YAML 管理，而是 release risk management。Senior / Lead 要能看 deployment strategy、probe、resource、config、secret、log pipeline、rollback 與 service topology。

可講重點：

- rollout 要分批、可回滾、可觀測。
- readiness / liveness 不能亂設。
- config / secret 要和 image 分離。
- log pipeline 要能支援 incident RCA。
- 服務上線前要知道依賴與 failure blast radius。

## 面試回答公式

```text
我先說這條 flow 的業務風險。
接著說 code 入口與資料怎麼走。
然後說我看到的 failure window。
再說我會優先處理哪個 owner decision。
最後說我不會誇大的邊界，以及下一步要補的 evidence。
```

