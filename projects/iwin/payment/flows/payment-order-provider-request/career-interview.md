# payment-order-provider-request career-interview

## Evidence 層級

- Flow：充值建單與 provider request。
- 證據層級：`專案存在 / code-backed`。
- Nick 個人貢獻：`待確認`。
- 目前用途：Senior Backend / Platform Backend / System Owner 面試素材，不進正式履歷 master。
- Step 3 結論：已建立 flow learning package；正式履歷 / 自傳不更新。

## 3 分鐘講法

我會把 provider request flow 拆成三段：商戶選擇、本地建單、三方下單。

玩家先透過 `/payment/list` 和 `/payment/detail` 拿到可用支付方式與商戶，payment 會根據玩家層級、device、支付類型與 Redis 裡的 merchant config 回傳可用選項。玩家選定商戶後，會打到對應 provider controller 的 `/newPay`。

在 `/newPay` 裡，payment 先用 `merchantId` 和 `thirdPayCode` 找商戶帳號與通道配置，再做金額檢查。接著呼叫共用的 `createOrderNo` 建立 `payment_order`，狀態是 `WAIT`。本地的 `billNo` 會被放進 provider request，作為 provider merchant order id，後續 callback 和查單都靠它對回本地訂單。

provider request 會依每家規格組商戶號、金額、notify URL、支付產品碼和簽章。provider 回 accepted 時，payment 回前端 QR code 或 pay URL；但這不代表充值成功，真正上分要等 callback 進 notify MQ。provider 回失敗或 response 格式異常時，payment 會把本地訂單標 `ERROR`。

這條 flow 我會特別看幾個 owner 風險：本地訂單 insert 成功後 provider request timeout、provider accepted 但 callback 不來、金額單位轉換錯、簽章 / notify URL 設定錯、重複 `/newPay` 造成多筆 provider 訂單，以及查單 / reconciliation 是否能收斂 `WAIT` aging order。

## 可用履歷 bullet

目前不建議放正式履歷。若 Nick 後續確認參與 evidence，可保守改寫成：

- 參與 / 分析 iwin 金流 provider request 鏈路，梳理充值建單、商戶設定、簽章、provider order id、callback 與查單補償邊界。
- 梳理 payment provider integration 的 request / callback consistency 風險，聚焦本地 `payment_order`、三方 provider order 與玩家上分副作用。

## 面試可說

- 「我會把 provider request 和 callback 分開看，因為 request accepted 不等於玩家已付款。」
- 「本地 `billNo` 是串 payment order、provider merchant order id、callback 和查單的關鍵 trace key。」
- 「provider request timeout 不能直接當失敗，因為 provider 可能已經建單。」
- 「金額單位和 payUnit 是高風險點，分 / 元轉錯就是錯帳。」
- 「每家 provider 自己組 request 很快，但 owner 角度要補 adapter contract、response validator 和 reconciliation。」

## 不能誇大

- 不能說 Nick 主導 provider request 架構。
- 不能說 Nick 對接 Pay4z / NimTestPay / CashPay。
- 不能說 Nick 修過建單 id collision 或 pay failure callback。
- 不能說所有 provider 都已標準化。
- 不能說正式履歷可直接放。

## 追問準備

| 問題 | 回答方向 |
| --- | --- |
| provider request timeout 怎麼辦？ | 不能直接視為 failed；要保留 `WAIT` / unknown，使用 `billNo` 查 provider，必要時人工 reconciliation。 |
| 為什麼先建本地訂單？ | 需要本地 `billNo` 當 provider merchant order id，否則 callback / 查單對不回來。代價是 provider request fail 會留下本地待處理狀態。 |
| 怎麼避免重複下單？ | 本地 `billNo` 可作 key，但本輪未確認 DB unique 與 provider idempotency；前端重送若產新 billNo 仍可能產生多筆訂單。 |
| provider 回成功後可以上分嗎？ | 不行。`newPay` 成功只是拿到支付資訊，真正付款成功要等 callback / 查單確認。 |
| 哪些欄位最危險？ | `billNo`、amount、payUnit、notify URL、merchantId、thirdPayCode、sign key、provider order id。 |

## 下一步

```text
iwin payment payment-order-provider-request Step 4
```
