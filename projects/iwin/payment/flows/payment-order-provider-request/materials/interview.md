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
| 查單入口存在是否代表有 reconciliation？ | 不代表。`/pay/getOrderStatus` 只是可查 provider 狀態；是否有 scheduled job、aging monitor、人工修單 SOP 與 raw event log 要另外確認。 |
| 為什麼 `ERROR` 不是永遠終態？ | 如果 `ERROR` 是 provider 明確 rejected，通常可視為失敗；但如果是 timeout / parse fail 後標成 `ERROR`，provider 端可能已建單，後續 callback 回來就會產生衝突。 |
| 要怎麼判斷 provider accepted no callback？ | 看本地 `WAIT` aging order、provider query result、callback log / notify MQ 是否缺事件；不能只看前端是否拿到 QR / URL。 |

## Lead / Architect 追問

| 追問 | 回答 |
| --- | --- |
| 如果重構，第一步改什麼？ | 先建立 provider adapter contract，統一 request / response validation、timeout 語意、accepted / rejected / unknown 狀態。 |
| 會怎麼設計 reconciliation？ | 掃 `WAIT` aging order，用 `billNo` 查 provider；success 補 callback / 上分，failed 標失敗，unknown 保持人工待查。 |
| 會怎麼設計 idempotency？ | `payment_order.bill_no` DB unique；provider merchant order id 使用同一 `billNo`；callback / query result 落 raw event 並以 `billNo + provider event id` 去重。 |
| timeout 應該標什麼狀態？ | 理想上不要直接標 `ERROR`，而是標 request unknown / pending query，避免 provider 已建單但本地先失敗。 |
| 監控先補哪三個？ | `WAIT` aging、provider request timeout / parse fail rate、callback delay。 |
| 會怎麼降低每家 provider controller 分散風險？ | 先不急著抽大框架；先定 provider adapter contract：request builder、signer、amount converter、response validator、query parser、error mapping。 |
| 如果 PM 問為什麼玩家付款了但沒上分，怎麼查？ | 先用 `billNo` 查 `payment_order` 狀態，再查 provider order status，再看 callback / MQ / `updateUserInfo` 是否成功；不要直接從前端付款截圖判定。 |

## 面試情境題

### 情境 1：provider request timeout

回答：

1. 先確認本地 `payment_order` 是否已建立，狀態是否仍是 `WAIT` 或被 catch 成 `ERROR`。
2. 用 `billNo` 查 provider；如果 provider 找得到 paid / pending，就不能直接失敗。
3. 如果 provider 找不到，才依 provider contract 判斷是否可重新下單或標失敗。
4. 補監控：request timeout rate、unknown order count、`WAIT` aging。

### 情境 2：provider 回成功，但 callback 一直沒來

回答：

1. `newPay` success 只代表拿到 pay URL / QR，不代表付款成功。
2. 若玩家聲稱已付款，先用 `billNo` 查 provider 狀態。
3. provider paid 時，要確認 callback log / MQ / `updateUserInfo` 哪段斷掉。
4. 人工補償要保護 idempotency，避免 callback 晚到後重複上分。

### 情境 3：重複點擊充值按鈕

回答：

1. 若每次都產新 `billNo`，就可能產生多筆本地與 provider 訂單。
2. 真正 idempotent 需要前端 request id 或後端 pending order reuse 策略。
3. 即使 provider 對同一 merchant order id 去重，產新 `billNo` 也繞過該保護。

### 情境 4：金額單位錯

回答：

1. 先比對前端輸入、`payment_order.money`、provider request amount、provider response amount。
2. 檢查 merchant `payUnit` 分 / 元轉換與 provider 文件約定。
3. 建議補 provider contract test 或 adapter-level amount converter。

## 可問面試官的反問

- 你們 provider request timeout 通常是標 failed、unknown，還是 pending query？
- 查單是人工工具、排程 job，還是 callback fallback？
- 同一個 merchant order id 重送時，provider 端保證 idempotent 嗎？
- 人工補單和 callback 晚到的衝突怎麼處理？
- provider sign key / request raw log 會怎麼遮罩與留存？

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
iwin payment payment-order-provider-request Step 5
```
