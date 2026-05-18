# decision-notes

## Step 4 判斷

`payment-order-provider-request` 已完成 Step 5 claim gate。這條 flow 補上 provider callback 之前的前半段：本地訂單如何建立、如何組 provider request、如何處理 accepted / rejected / unknown，以及如何用 `billNo` 接 callback / 查單。

Step 5 新結論：Nick 不應維持 `待確認`。path-specific git history 已確認 `10gt12nc` 在 Pay4z、NaNapay、BFPAY、NimTestPay 與 `createOrderNo` 相關 code 有實際新增 / 修正。但 owner decision 仍只能說「參與 provider 對接與維護」，不可說完整金流架構 owner。

## Owner Decision

| Decision | 目前 code 形狀 | 風險 | Step 4 要補 |
| --- | --- | --- | --- |
| 先建本地訂單再打 provider | `createOrderNo` insert `WAIT` 後呼叫 provider pay URL | provider request fail / timeout 會留下本地待處理訂單 | Step 4 面試可談 unknown state / query job / outbox |
| 每個 provider controller 自行組 request | `NimTestPay`、`Pay4z`、`NewCashPay` 各自簽章與解析 response | 重複邏輯多，錯誤處理不一致 | Step 4 面試可談 provider adapter contract |
| `billNo` 作 provider merchant order id | request、callback、query 都靠它對應 | DB unique / provider idempotency 待確認 | Step 4 面試可談 idempotency 分層 |
| provider fail 直接標 `ERROR` | response fail / abnormal 時更新本地訂單 | timeout / partial success 可能被誤判 | Step 4 面試可談 rejected vs unknown |
| 查單入口存在 | 多個 provider 有 `/pay/getOrderStatus` | 入口不等於自動 reconciliation | Step 4 面試可談 aging monitor / scheduled job |

## 技術硬底子

### Accepted 不等於 Success

Provider request 成功通常只代表「provider 建立支付單」或「取得支付 URL / QR code」。玩家尚未付款，payment 不能上分；真正 money movement 必須等 callback 或查單確認。

### Timeout 是 Unknown

HTTP timeout 不能直接等同 provider fail，因為 request 可能送到 provider，provider 也可能已建立訂單。Owner 應把 timeout 視為 unknown，再靠 query order / reconciliation 收斂。

### Idempotency Key

本 flow 的自然 idempotency key 是 `billNo`。但只把 `billNo` 傳出去不等於 end-to-end idempotent；還要看 DB unique、provider merchant order id 查重、callback no-op 與人工修復 SOP。

### Rejected / Unknown / Accepted

面試時要避免把所有失敗都叫 failed：

- rejected：provider 明確回拒絕，通常可標失敗。
- unknown：timeout、parse fail、network exception，provider 可能已收到 request。
- accepted：provider 已建立支付單，但玩家未付款或 callback 未到。

這三種狀態的補償策略不同。unknown 要查單，accepted 要等 callback / 查單，rejected 才能較安全地結束。

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
iwin payment payment-channel-config-selection Step 3
```
