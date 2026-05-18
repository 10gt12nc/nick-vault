# interview

## Senior 追問

| 追問 | 回答 |
| --- | --- |
| 這條 flow 的 source of truth 是什麼？ | payment 內部訂單看 `payment_order`；provider 訂單看三方；玩家餘額看 game lobby / center。`newPay` 只建立前兩者的關聯，玩家上分要等 callback。 |
| 為什麼 provider request 成功不能代表充值成功？ | provider accepted 只代表支付單建立或 QR / pay URL 可用，玩家是否付款要等 callback / 查單確認。 |
| provider request timeout 怎麼處理？ | 不能直接當 failed。因為 provider 可能已建單，要用本地 `billNo` 查 provider，或進人工 reconciliation。 |
| 金額單位風險在哪？ | 本地 `payment_order.money` 是內部單位，各 provider 依 `payUnit` 轉成分或元；轉錯會造成 provider 顯示金額和本地訂單不一致。 |
| `billNo` 的角色是什麼？ | 它是本地訂單號，也是 provider merchant order id、callback order id、查單 key。 |
| 怎麼避免重複下單？ | 本輪只確認每次會生成 `billNo`，未確認 DB unique 或 provider idempotency；前端重送若產新 `billNo` 仍可能產生多筆單。 |
| provider response fail 但後來 callback success 怎麼辦？ | 這是狀態衝突，要看本地訂單是否已 `ERROR`、callback guard 是否允許處理，以及人工 SOP 是否允許覆蓋。不能腦補一定安全。 |

## Lead / Architect 追問

| 追問 | 回答 |
| --- | --- |
| 如果重構，第一步改什麼？ | 先建立 provider adapter contract，統一 request / response validation、timeout 語意、accepted / rejected / unknown 狀態。 |
| 會怎麼設計 reconciliation？ | 掃 `WAIT` aging order，用 `billNo` 查 provider；success 補 callback / 上分，failed 標失敗，unknown 保持人工待查。 |
| 會怎麼設計 idempotency？ | `payment_order.bill_no` DB unique；provider merchant order id 使用同一 `billNo`；callback / query result 落 raw event 並以 `billNo + provider event id` 去重。 |
| timeout 應該標什麼狀態？ | 理想上不要直接標 `ERROR`，而是標 request unknown / pending query，避免 provider 已建單但本地先失敗。 |
| 監控先補哪三個？ | `WAIT` aging、provider request timeout / parse fail rate、callback delay。 |

## 90 秒版本

這條 provider request flow 我會先分成本地訂單和三方訂單兩件事。玩家選定支付方式後會打 provider `/newPay`，controller 先從 Redis 取商戶設定，檢查金額，再呼叫 `createOrderNo` 建立 `payment_order`，狀態是 `WAIT`。接著用本地 `billNo` 當 provider merchant order id，組商戶號、金額、notify URL、簽章，呼叫 provider pay URL。

provider 回成功時，payment 只回前端 QR code 或 pay URL，不能立刻上分。真正充值成功要等 provider callback 或查單確認，再進 notify MQ 和 `updateUserInfo`。我會特別看幾個 failure window：本地訂單已建立但 provider request timeout、provider accepted 但 callback 不來、金額單位轉錯、簽章或 notify URL 錯、provider response fail 但其實已建單，以及重複下單是否會產生多筆 provider order。

## 可說案例

> 我會把 provider request 視為跨系統建單問題，而不是單純 HTTP call。本地 `payment_order`、provider order、玩家錢包是三個狀態來源。`newPay` 只完成本地訂單與 provider 訂單的關聯，真正 money movement 要等 callback。owner 要處理的是 timeout、unknown、重複 request、查單和人工補償邊界。

## 不要說

- 不要說已完成 Level 3 逐檔逐行。
- 不要說已確認所有 provider 一致。
- 不要說已確認 provider 端 idempotency。
- 不要說 Nick 本人對接或主導這條 flow。

## 下一步

```text
iwin payment payment-order-provider-request Step 4
```
