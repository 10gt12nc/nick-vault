# payment-order-provider-request career-interview

## Evidence 層級

- Flow：充值建單與 provider request。
- 證據層級：`專案存在 / code-backed`。
- Nick 個人貢獻：`部分 provider request 真實開發過`。
- 目前用途：Senior Backend / Platform Backend / System Owner 面試素材；可作為正式履歷的保守素材來源。
- Step 5 結論：已完成 claim gate。可升級為「參與多個 payment provider request / callback / query 對接與維護」，但不能寫主導完整金流、不能寫全部 provider owner。

## 3 分鐘講法

我會把 provider request flow 拆成三段：商戶選擇、本地建單、三方下單。

玩家先透過 `/payment/list` 和 `/payment/detail` 拿到可用支付方式與商戶，payment 會根據玩家層級、device、支付類型與 Redis 裡的 merchant config 回傳可用選項。玩家選定商戶後，會打到對應 provider controller 的 `/newPay`。

在 `/newPay` 裡，payment 先用 `merchantId` 和 `thirdPayCode` 找商戶帳號與通道配置，再做金額檢查。接著呼叫共用的 `createOrderNo` 建立 `payment_order`，狀態是 `WAIT`。本地的 `billNo` 會被放進 provider request，作為 provider merchant order id，後續 callback 和查單都靠它對回本地訂單。

provider request 會依每家規格組商戶號、金額、notify URL、支付產品碼和簽章。provider 回 accepted 時，payment 回前端 QR code 或 pay URL；但這不代表充值成功，真正上分要等 callback 進 notify MQ。provider 回失敗或 response 格式異常時，payment 會把本地訂單標 `ERROR`。

這條 flow 我會特別看幾個 owner 風險：本地訂單 insert 成功後 provider request timeout、provider accepted 但 callback 不來、金額單位轉換錯、簽章 / notify URL 設定錯、重複 `/newPay` 造成多筆 provider 訂單，以及查單 / reconciliation 是否能收斂 `WAIT` aging order。

## 可用履歷 bullet

可放正式履歷的保守版本：

- 參與 iwin 金流 provider 對接與維護，涵蓋 Pay4z、NaNapay、BFPAY 等 provider 的 request / callback / query 流程，處理商戶設定、簽章、金額單位、merchant order id 與訂單狀態邊界。
- 維護 payment provider integration 的 request / callback consistency，聚焦本地 `payment_order`、三方 provider order、玩家上分副作用、timeout / unknown 與查單補償風險。

更保守的一行版：

- 參與第三方金流 provider request / callback / query 對接與維護，熟悉簽章驗證、訂單狀態、金額單位、查單補償與 callback 冪等風險。

## 面試可說

- 「我會把 provider request 和 callback 分開看，因為 request accepted 不等於玩家已付款。」
- 「本地 `billNo` 是串 payment order、provider merchant order id、callback 和查單的關鍵 trace key。」
- 「provider request timeout 不能直接當失敗，因為 provider 可能已經建單。」
- 「金額單位和 payUnit 是高風險點，分 / 元轉錯就是錯帳。」
- 「每家 provider 自己組 request 很快，但 owner 角度要補 adapter contract、response validator 和 reconciliation。」

## 不能誇大

- 不能說 Nick 主導 provider request 架構。
- 不能說 Nick 對接全部 payment provider。
- 不能說 Nick 修過建單 id collision 或 pay failure callback。
- 不能說所有 provider 都已標準化。
- 不能說已建立完整自動 reconciliation。

## 追問準備

| 問題 | 回答方向 |
| --- | --- |
| provider request timeout 怎麼辦？ | 不能直接視為 failed；要保留 `WAIT` / unknown，使用 `billNo` 查 provider，必要時人工 reconciliation。 |
| 為什麼先建本地訂單？ | 需要本地 `billNo` 當 provider merchant order id，否則 callback / 查單對不回來。代價是 provider request fail 會留下本地待處理狀態。 |
| 怎麼避免重複下單？ | 本地 `billNo` 可作 key，但本輪未確認 DB unique 與 provider idempotency；前端重送若產新 billNo 仍可能產生多筆訂單。 |
| provider 回成功後可以上分嗎？ | 不行。`newPay` 成功只是拿到支付資訊，真正付款成功要等 callback / 查單確認。 |
| 哪些欄位最危險？ | `billNo`、amount、payUnit、notify URL、merchantId、thirdPayCode、sign key、provider order id。 |

## 面試主線拆法

### 1. 交易邊界

回答重點：

- `createOrderNo` 先 insert `payment_order WAIT`，再呼叫 provider pay URL。
- 這不是同一個 transaction，所以 insert 成功但 provider timeout 是典型 unknown。
- `newPay` 回 QR / pay URL 只代表支付單建立或等待付款，不代表 money movement 完成。

可用句：

> 我會先把 `newPay` 和 callback 拆開。`newPay` 只建立本地訂單和 provider 支付單的關聯，真正上分要等 callback / query 確認，所以 transaction boundary 不能畫錯。

### 2. Idempotency

回答重點：

- 自然 idempotency key 是 `billNo`。
- 本輪只確認 `billNo` 會放進 provider merchant order id；未確認 DB unique、provider 對同一 merchant order id 的語意、前端重送是否沿用同一筆訂單。
- callback / 查單要保護終態訂單，避免重複上分。

可用句：

> 我不會只說「有 billNo 就 idempotent」。end-to-end idempotency 要同時看本地 unique、provider merchant order id、callback no-op，以及人工修單是否會覆蓋終態。

### 3. Reconciliation

回答重點：

- provider controller 有 `/pay/getOrderStatus`，可用 `billNo` 查 provider。
- 但查單入口不等於自動 reconciliation；是否有 job 掃 `WAIT` aging order 仍待確認。
- 面試時可提出理想設計：aging monitor、query job、raw request / response log、manual repair SOP。

可用句：

> 我會把 `WAIT` aging order 當作一個 owner dashboard 指標。超過 SLA 的單要先查 provider，不應直接補上分，也不應直接改失敗。

### 4. Provider adapter trade-off

回答重點：

- 現況是每個 provider controller 自己處理 sign、amount、response parse。
- 好處是對接快；壞處是 timeout / error mapping / response validation 容易不一致。
- 改善方向不是一次重寫，而是先定 provider adapter contract 與狀態語意。

可用句：

> 如果要收斂技術債，我會先標準化 request result：accepted、rejected、unknown 三種語意，再逐步把 sign / amount / response validator 收進 adapter contract。

## STAR 版本

Situation：payment 需要支援多個三方充值 provider，每家 request 欄位、簽章、金額單位、回傳格式和查單 API 都不同。

Task：要確保玩家發起充值時，本地 `payment_order`、provider order、callback 上分三者能用同一個 key 對回來，並且能處理 timeout、失敗與重送。

Action：我會先定位玩家支付列表 / 詳情、provider `/newPay`、`createOrderNo`、provider request、callback 與查單入口，確認 `billNo` 是 correlation key，再整理 failure window：insert 後 timeout、provider accepted no callback、response abnormal、金額單位錯、重複下單、sign / notify URL 錯。

Result：這個 flow 可用來說明我怎麼分析跨系統金流 consistency：先分清 source of truth，再處理 idempotency、unknown state、query / reconciliation 和 observability。

證據層級：`專案存在 / code-backed`；Nick 本人參與已確認部分 provider path-specific evidence。

Step 5 證據更新：Pay4z / NaNapay / BFPAY / NimTestPay 相關 commits 由 `10gt12nc` authored / committed，且包含 `origin/pay4z-Nick`、`origin/NaNapay_Nick` 等分支線索。正式面試可說「我參與過 provider 對接與維護」，但仍避免主導、全權 owner 與量化改善。

## 下一步

```text
- 歷史下一步已完成：iwin_gameserver center-http-deposit-withdraw 已完成到 Step 5；目前沒有預設下一步，請以 senior-owner-playbook/01-senior-owner-flow-inventory.md 與 senior-owner-playbook/06-todo.md 為準。
```

## 履歷 claim 分層（2026-05-18 KB 對齊）

- 可放履歷：真實開發過。Nick / `10gt12nc` 在 Pay4z、NaNapay、BFPAY、GoldenPay、NimTestPay 與 `createOrderNo` 相關 commits / branches 有 path-specific evidence，可保守寫「參與第三方金流 provider request / callback / query 對接與維護」。
- 可面試講：code-backed / 分析過。可用本 flow 說明 provider request、callback、query、timeout unknown、訂單狀態與查單補償風險。
- 不可誇大：不是主導完整金流 owner。不得寫成主導 iwin payment、對接全部 provider、設計完整 provider 架構、建立完整 reconciliation 或改善成功率 X%。
