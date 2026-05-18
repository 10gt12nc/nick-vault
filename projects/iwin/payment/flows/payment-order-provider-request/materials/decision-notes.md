# decision-notes

## Step 3 判斷

`payment-order-provider-request` 值得作為 payment 第三條 flow，原因是它補上 provider callback 之前的前半段：本地訂單如何建立、如何組 provider request、如何處理 accepted / failed / unknown，以及如何用 `billNo` 接 callback / 查單。

## Owner Decision

| Decision | 目前 code 形狀 | 風險 | Step 4 要補 |
| --- | --- | --- | --- |
| 先建本地訂單再打 provider | `createOrderNo` insert `WAIT` 後呼叫 provider pay URL | provider request fail / timeout 會留下本地待處理訂單 | unknown state / query job / outbox |
| 每個 provider controller 自行組 request | `NimTestPay`、`Pay4z`、`NewCashPay` 各自簽章與解析 response | 重複邏輯多，錯誤處理不一致 | provider adapter contract |
| `billNo` 作 provider merchant order id | request、callback、query 都靠它對應 | DB unique / provider idempotency 待確認 | idempotency 分層 |
| provider fail 直接標 `ERROR` | response fail / abnormal 時更新本地訂單 | timeout / partial success 可能被誤判 | rejected vs unknown |
| 查單入口存在 | 多個 provider 有 `/pay/getOrderStatus` | 入口不等於自動 reconciliation | aging monitor / scheduled job |

## 技術硬底子

### Accepted 不等於 Success

Provider request 成功通常只代表「provider 建立支付單」或「取得支付 URL / QR code」。玩家尚未付款，payment 不能上分；真正 money movement 必須等 callback 或查單確認。

### Timeout 是 Unknown

HTTP timeout 不能直接等同 provider fail，因為 request 可能送到 provider，provider 也可能已建立訂單。Owner 應把 timeout 視為 unknown，再靠 query order / reconciliation 收斂。

### Idempotency Key

本 flow 的自然 idempotency key 是 `billNo`。但只把 `billNo` 傳出去不等於 end-to-end idempotent；還要看 DB unique、provider merchant order id 查重、callback no-op 與人工修復 SOP。

## Owner 改善方向

| 改善方向 | 為什麼 | 證據狀態 |
| --- | --- | --- |
| provider adapter contract | 統一 request / sign / response parse / error mapping | 目前 controller 分散 |
| response validator | 避免 accepted / missing field / bad format 被誤判 | 已看到 provider 各自驗欄位 |
| request outbox / raw log | request timeout / parse fail 後要可追 | 未確認 |
| `WAIT` aging reconciliation | provider accepted no callback 需要收斂 | 查單入口存在，自動 job 未確認 |
| secret log mask | provider sign key 不應進 log | `a56b407` 有安全修正線索 |

## 下一步

```text
iwin payment payment-order-provider-request Step 4
```
