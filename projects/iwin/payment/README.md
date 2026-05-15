# iwin payment

本資料夾整理 `/Users/nick/Git/iwin/payment` 的專案知識。

`payment` 是 iwin 金流 / 充值 / 提現相關的 Java Spring Boot 多 module 專案，主要價值在 third-party provider request / callback、訂單狀態轉移、玩家上下分、MQ 非同步副作用、自動出款與人工審核。這是目前 iwin domain 裡最接近 Senior Backend / Platform Backend / System Owner 面試價值的後端專案之一。

目前所有內容只標為 `專案存在 / code-backed`；沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認前，不寫成 Nick 真實開發成果。

## 讀檔順序

1. [step1-candidate-flows.md](step1-candidate-flows.md)：Step 1 候選 flow 盤點。
2. [step2-flow-comparison.md](step2-flow-comparison.md)：Step 2 候選 flow 風險與價值比較。
3. 待建立：`flows/{flow-name}/flow.md`：單條 flow 的主研究報告。
4. 待建立：`flows/{flow-name}/career-interview.md`：該 flow 的保守面試 / 履歷素材。
5. 待建立：`flows/{flow-name}/materials/`：證據、技術決策、詳細面試稿與 claim 邊界附錄。

## 目前狀態

| 文件 / flow | 狀態 | 說明 |
| --- | --- | --- |
| `step1-candidate-flows.md` | 已建立 | Level 1 掃描，找出 Top 5 production flow 候選 |
| `step2-flow-comparison.md` | 已建立 | 已比較 callback、provider request、自動出款、玩家提款建單、人工審核 / 補單，建議第一條深挖 `payment-provider-callback` |
| `flows/` | 尚未建立 | Step 1 不建立 flow folder，等 Nick 選定單條 flow 後再建 |

## 專案定位

已確認：

- Java / Spring Boot 多 module 專案，主要 module 包含 `payment`、`admin`、`mq`、`timer`、`xxl-mq-admin`、`base`。
- 核心 runtime code 集中在 `payment/src/main/java/cn/com/payment`。
- 入口包含玩家端 API、withdraw router、各 provider controller callback、XXL-MQ consumer、後台 / admin 與 health controller。
- 主要資料狀態集中在 `payment_order`、`log_user`、`user_behaviour`、`merchant_account`、`subscriber`、`paytype`、`convenientpay`、`vippay`、`widemarquesetting` 與 Mongo blacklist collection。
- 高價值風險集中在 money correctness、訂單狀態轉移、callback idempotency、MQ retry、玩家上下分副作用、人工修復與跨月分表。

推測：

- `payment` 是金流 orchestration 與狀態更新核心之一，但玩家錢包最終上下分會透過 game lobby / center HTTP 或 GM command，下游 source of truth 仍需 Step 2 / Step 3 補確認。
- provider controller 大量重複相同形狀：`newPay`、`pay/notify`、`withdraw/notify`、查單，適合抽出共通 callback / provider request flow 深挖，而不是平均整理每個商戶 class。
- `app_bi` 的 `payment-order-status-repair` 應回到本專案一起看，避免只把後台人工入口誤當完整金流 flow。

待確認：

- Nick 實際參與過哪些 provider、callback、auto withdraw 或 bugfix。
- provider callback 是否都有簽章驗證、終態保護、重送 no-op 與 callback log。
- `payment_order` 分月 / 跨月搬移邏輯與 DB schema / unique key。
- game lobby / center 的上下分 API 是否具備 idempotency。
- `k3s` branch 與 `main` / provider feature branch 的差異是否影響要深挖的 flow。

## 履歷邊界

目前可以說：

- 可用來理解 / 分析 iwin 金流、充值、提現、provider callback、MQ retry 與人工審核修復。
- 可作為 Senior Backend 面試素材 evidence base，但需先完成單條 flow Level 2 深掃。

目前不能說：

- Nick 主導 `payment`。
- Nick 負責完整金流或錢包 owner。
- Nick 設計了 provider callback / auto withdraw / MQ 架構。
- 單靠 Step 1 寫入正式履歷 / 自傳。

## 下一步建議

只推薦一件事：

```text
iwin payment payment-provider-callback Step 3
```

原因：

- Step 2 已完成候選比較。
- `payment-provider-callback` 最能代表金流 callback、訂單狀態、MQ retry、玩家上下分 / 退款與人工補償邊界。
- Step 3 會建立單條 flow 學習包；不更新正式履歷，除非之後補到 Nick 本人 evidence。
