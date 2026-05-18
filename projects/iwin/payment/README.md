# iwin payment

本資料夾整理 `/Users/nick/Git/iwin/payment` 的專案知識。

`payment` 是 iwin 金流 / 充值 / 提現相關的 Java Spring Boot 多 module 專案，主要價值在 third-party provider request / callback、訂單狀態轉移、玩家上下分、MQ 非同步副作用、自動出款與人工審核。這是目前 iwin domain 裡最接近 Senior Backend / Platform Backend / System Owner 面試價值的後端專案之一。

目前多數內容仍標為 `專案存在 / code-backed`；但 `payment-order-provider-request` 已完成 Step 5 claim gate，Pay4z / NaNapay / BFPAY / NimTestPay 等 provider request / query / callback 相關 code 有 Nick path-specific commit evidence，可用「參與」口徑寫入履歷素材。仍不得寫成 Nick 主導完整金流或全部 provider owner。

## 讀檔順序

1. [step1-candidate-flows.md](step1-candidate-flows.md)：Step 1 候選 flow 盤點。
2. [step2-flow-comparison.md](step2-flow-comparison.md)：Step 2 候選 flow 風險與價值比較。
3. [flows/payment-provider-callback/flow.md](flows/payment-provider-callback/flow.md)：第一條 flow 主研究報告，已完成 Step 5 claim gate。
4. [flows/payment-provider-callback/career-interview.md](flows/payment-provider-callback/career-interview.md)：該 flow 的保守面試 / 履歷素材。
5. [flows/payment-provider-callback/materials/evidence.md](flows/payment-provider-callback/materials/evidence.md)：證據、技術決策、詳細面試稿與 claim 邊界附錄入口。
6. [flows/withdrawal-auto-review-refund/flow.md](flows/withdrawal-auto-review-refund/flow.md)：第二條 flow 主研究報告，已完成 Step 5 claim gate。
7. [flows/withdrawal-auto-review-refund/career-interview.md](flows/withdrawal-auto-review-refund/career-interview.md)：該 flow 的保守面試素材。
8. [flows/payment-order-provider-request/flow.md](flows/payment-order-provider-request/flow.md)：第三條 flow 主研究報告，整理充值建單、provider request、簽章、金額單位、provider order id 與查單邊界，已完成 Step 5 claim gate。
9. [flows/payment-order-provider-request/career-interview.md](flows/payment-order-provider-request/career-interview.md)：該 flow 的保守面試 / 履歷素材。
10. [flows/manual-order-review-repair/flow.md](flows/manual-order-review-repair/flow.md)：第四條 flow 主研究報告，整理人工審核、補單、訂單狀態修復、app_bi 後台入口與 payment side effect 邊界，已完成 Step 5 claim gate。
11. [flows/manual-order-review-repair/career-interview.md](flows/manual-order-review-repair/career-interview.md)：該 flow 的保守面試素材。
12. [flows/payment-channel-config-selection/flow.md](flows/payment-channel-config-selection/flow.md)：第五條 flow 主研究報告，整理支付列表 / 商戶 / 玩家層級 / 提現設定選擇與 Redis projection consistency，已完成 Step 5 claim gate。
13. [flows/payment-channel-config-selection/career-interview.md](flows/payment-channel-config-selection/career-interview.md)：該 flow 的保守面試素材。

## 目前狀態

| 文件 / flow | 狀態 | 說明 |
| --- | --- | --- |
| `step1-candidate-flows.md` | 已建立 | Level 1 掃描，找出 Top 5 production flow 候選 |
| `step2-flow-comparison.md` | 已建立 | 已比較 callback、provider request、自動出款、玩家提款建單、人工審核 / 補單，建議第一條深挖 `payment-provider-callback` |
| `flows/payment-provider-callback/` | Step 5 已完成 | Level 2 深掃 provider callback；已補 failure / consistency evidence、下游 `billNo` 傳遞、app_bi repair boundary、bugfix diff 與履歷 / 自傳 claim gate |
| `flows/withdrawal-auto-review-refund/` | Step 5 已完成 | Level 2 深掃玩家提款、自動審核 / 自動出款、provider 代付失敗與退款主線；已補 failure / consistency / idempotency / retry / reconciliation 面試 case，並完成不更新正式履歷 / 自傳的 claim gate |
| `flows/payment-order-provider-request/` | Step 5 已完成 | Level 2+ claim gate；已確認 Nick 在 Pay4z、NaNapay、BFPAY、NimTestPay / `createOrderNo` 相關 provider request / query / callback code 有 path-specific commits，可保守寫入履歷素材 |
| `flows/manual-order-review-repair/` | Step 5 已完成 | Level 2 深掃人工審核 / 補單 / 修復；已確認 `/oderView`、`/gameRecharge`、app_bi `bill_check` / `repairOrderService` 與 direct status repair 的風險邊界；Step 5 判定不更新正式履歷 / 自傳，只作面試素材 |
| `flows/payment-channel-config-selection/` | Step 5 已完成 | Level 2+ claim gate；已確認 payment runtime 消費 app_bi Redis projection，並已轉成 config consistency / partial sync / cold-cache fallback / fail closed 面試 case；Step 5 判定不更新正式履歷 / 自傳 |

## 專案定位

已確認：

- Java / Spring Boot 多 module 專案，主要 module 包含 `payment`、`admin`、`mq`、`timer`、`xxl-mq-admin`、`base`。
- 核心 runtime code 集中在 `payment/src/main/java/cn/com/payment`。
- 入口包含玩家端 API、withdraw router、各 provider controller callback、XXL-MQ consumer、後台 / admin 與 health controller。
- 主要資料狀態集中在 `payment_order`、`log_user`、`user_behaviour`、`merchant_account`、`subscriber`、`paytype`、`convenientpay`、`vippay`、`widemarquesetting` 與 Mongo blacklist collection。
- 高價值風險集中在 money correctness、訂單狀態轉移、callback idempotency、MQ retry、玩家上下分副作用、人工修復與跨月分表。

推測：

- `payment` 是金流 orchestration 與狀態更新核心之一，但玩家錢包最終上下分會透過 game lobby / center HTTP 或 GM command；`withdrawal-auto-review-refund` Step 5 已確認 `billNo` 會傳入下游餘額異動與 currency log，但下游強制去重仍待確認。
- provider controller 大量重複相同形狀：`newPay`、`pay/notify`、`withdraw/notify`、查單，適合抽出共通 callback / provider request flow 深挖，而不是平均整理每個商戶 class。
- `app_bi` 的 `payment-order-status-repair` 應回到本專案一起看，避免只把後台人工入口誤當完整金流 flow。

待確認：

- Nick 實際參與過哪些 provider、callback、auto withdraw 或 bugfix。
- provider callback 是否都有簽章驗證、終態保護、重送 no-op 與 callback log。
- `payment_order` 分月 / 跨月搬移邏輯與 DB schema / unique key。
- game lobby / center 的上下分 API 是否具備 `billNo` 查重或 DB unique idempotency。
- `k3s` branch 與 `main` / provider feature branch 的差異是否影響要深挖的 flow。

## 履歷邊界

目前可以說：

- 可用來理解 / 分析 iwin 金流、充值、提現、provider callback、MQ retry 與人工審核修復。
- `payment-order-provider-request` 可保守寫成 Nick 參與第三方金流 provider request / callback / query 對接與維護。
- 其他 payment flow 若未補 Nick path-specific evidence，仍只作 Senior Backend 面試素材 evidence base。

目前不能說：

- Nick 主導 `payment`。
- Nick 負責完整金流或錢包 owner。
- Nick 設計了 provider callback / auto withdraw / MQ 架構。
- 單靠 Step 1 寫入正式履歷 / 自傳。

## 下一步建議

只推薦一件事：

```text
iwin payment contribution claim consolidation
```

原因：

- payment Top 5 flow 已完成到 Step 5，但單條 flow 的 claim gate 仍太分散。
- Nick 已明確補充 `payment` 實際開發很多；不能只用單條 flow 缺直接 evidence 低估整個 payment 經驗。
- 下一步應把全部 Nick / `10gt12nc` commits、branches、重要 diff、本人確認與既有 flow evidence 合併，輸出「可放履歷 / 可面試講 / 不可誇大」三層。

## 履歷 claim 分層（2026-05-18 KB 對齊）

- 可放履歷：真實開發過。已確認 Nick / `10gt12nc` 在多個 provider request / callback / query 與 payment order consistency 相關 commits / branches 有 evidence，可保守寫「參與第三方金流 provider 對接與維護」。
- 可面試講：code-backed / 分析過。五條 payment flow 都可作 Senior Backend / Platform Backend 面試素材，涵蓋 callback、提款退款、provider request、人工修復與支付設定一致性。
- 不可誇大：不是主導完整金流 owner。不得寫成負責整個 `payment`、全部 provider、完整 wallet / reconciliation 架構或量化改善。
- 待完成：正式改寫履歷 / 自傳前，先做 project-level contribution consolidation，整合全部 Nick / `10gt12nc` commits、branches、重要 diff 與本人確認。
