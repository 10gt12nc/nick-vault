# evidence

## 本次掃描定位

- 任務：`iwin payment payment-order-provider-request Step 3`。
- 日期：2026-05-18。
- 掃描等級：Level 2 Flow 深掃；建立充值建單與 provider request 主學習包。
- 證據層級：`專案存在 / code-backed`；Nick 貢獻 `待確認`。

## 自動重讀

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/CONVENTIONS.md`

已重讀 vault：

- `projects/iwin/payment/README.md`
- `projects/iwin/payment/step1-candidate-flows.md`
- `projects/iwin/payment/step2-flow-comparison.md`
- `projects/iwin/payment/flows/payment-provider-callback/flow.md`
- `projects/iwin/payment/flows/withdrawal-auto-review-refund/flow.md`
- `projects/iwin/payment/flows/*/materials/*.md`

## source repo 狀態

### `/Users/nick/Git/iwin/payment`

- 已執行 `git fetch --all --prune` 更新 remote refs。
- 分支：`k3s`
- local HEAD：`bb7794e55386d914801887cc43b53d263c74d3c3`
- `origin/k3s` HEAD：`bb7794e55386d914801887cc43b53d263c74d3c3`
- ahead / behind：`0 / 0`
- 工作樹狀態：只有既有未追蹤 `payment/src/main/java/cn/com/payment/service/impl/.DS_Store`；本輪只讀未動。

## 已掃 code path

payment：

- `payment/src/main/java/cn/com/payment/controller/PayPublicController.java`
- `payment/src/main/java/cn/com/payment/service/impl/PayTypeServiceImpl.java`
- `payment/src/main/java/cn/com/payment/controller/BaseController.java`
- `payment/src/main/java/cn/com/payment/service/impl/withdraw/BaseServiceImpl.java`
- `payment/src/main/java/cn/com/payment/controller/merchant/NimTestPayController.java`
- `payment/src/main/java/cn/com/payment/controller/merchant/Pay4zController.java`
- `payment/src/main/java/cn/com/payment/controller/merchant/NewCashPayController.java`
- `payment/src/main/java/cn/com/payment/controller/merchant/EPayController.java`
- `payment/src/main/java/cn/com/payment/vo/PayVO.java`
- `payment/src/main/java/cn/com/payment/vo/OrderVO.java`

## 已確認 facts

- `/payment/list` 會依玩家層級、device、支付方式、商戶設定回傳可用支付方式。
- `/payment/detail` 會依 pay code、device、玩家層級、商戶設定回傳可用商戶 / 檔位。
- provider-specific `/newPay` 入口大量存在；本輪代表掃 `NimTestPayController`、`Pay4zController`、`NewCashPayController`。
- `PayVO` 帶 `openId`、`money`、`merchantId`、`uid`、`payCode`、`merchantName`、`thirdPayCode`。
- `BaseController#getMerchant` 先從 `MERCHANTLIST_ACCOUNT` 找 merchant account，再依 `thirdPayCode` 從 `MERCHANTLIST` 找通道設定。
- `BaseController#validMoney` 會檢查金額大於 0，並依 `decide` 檢查 min/max 或 gears。
- `BaseServiceImpl#createOrderNo` 會查 `log_user`、產生 `billNo`、建立 `payment_order`，初始狀態 `WAIT`。
- `03c28e3` 修正 `createOrderNo` 從 `log_user` 複製 id 後未清掉，造成 payment order insert 主鍵 collision 風險。
- `NimTestPayController.newPay` 會用 `mchNo`、`mchOrderId`、`payType`、`amount`、`notifyUrl`、`signType`、`version` 組 request，簽章為 SHA512。
- `Pay4zController.newPay` 會用 `merchantNo`、`merchantOrderNo`、`amount`、`code`、`currency`、`content`、`clientIp`、`callback` 組 request，簽章為 MD5。
- `NewCashPayController.pay` 會用 `merchantOrderId`、`notifyUrl`、`amount`、`merchantUserId` 組 request，並使用 Basic auth / response sign verify。
- 多個 provider 成功回 `CashPayVO` 給前端，包含 amount、orderId、qrcode / pay URL、expireTime、merchantName。
- provider request fail 或 response abnormal 時，多數 provider 會把本地訂單改 `ERROR` 並寫 `recordRemark`。
- provider callback success 不在 `newPay` 完成；會進 `asynUpdateOrderStatus` / notify MQ / `updateUserInfo`。
- `NimTestPayController`、`Pay4zController`、`NewCashPayController` 都有 `/pay/getOrderStatus` 查單入口線索。
- `a56b407` 對 payment 做過憑證外部化與 sign key log mask 類安全修正。

## 相關 commit 線索

- `03c28e3 fix: clear copied order id before payment insert`
- `173074a fix: handle nimtestpay pay failure callback`
- `d804ee8 fix: store nimtestpay withdraw provider order`
- `9aa0477 fix: pay4z errorMsg 加号被空格替换 sign 不一致`
- `a56b407 security: 憑證外部化 + 日誌 key 遮罩(payment)`
- `7853917 feat(#285): pay4z 对接`
- `7403277 feat: add NimTestPayController for local/SIT manual testing`

## 合理推論

- `billNo` 是 payment 與 provider request / callback / query order 的主要 correlation key。
- `newPay` 成功只表示 provider accepted 或提供支付資料，不代表玩家已付款。
- `WAIT` aging order 需要 provider query / callback / 人工補償收斂，但本輪未確認自動 reconciliation job。
- provider request timeout 是 unknown，不應直接當作 provider failed。
- 各 provider 的 request / response validator 分散在 controller，長期維護風險高。

## 待確認

- `payment_order.bill_no` 是否 unique。
- provider 對同一 merchant order id 重送下單是否 idempotent。
- 是否有 scheduled reconciliation job 掃 `WAIT` aging order。
- provider request / response 是否有統一 raw log / outbox / inbox。
- `PayTypeServiceImpl#createPayOrder` 是否仍有實際呼叫路徑，或主要 provider request 都走 `BaseServiceImpl#createOrderNo`。
- app_bi / admin 是否能安全查 provider order status 並修補 payment order。
- Nick 本人是否參與任一 provider request 對接、bugfix、ticket 或 production incident。

## 未掃

- 未逐 provider 全部 `/newPay` 逐行掃。
- 未逐 commit diff 展開所有 provider 對接。
- 未掃 DB schema / migration / production index。
- 未掃 timer / reconciliation job 全貌。
- 未掃完整 app_bi repair UI。

## Step 3 結論

- 本 flow 已建立 Step 3 主學習包，足以讓 Nick 先讀懂 provider request 主線。
- 核心講法：`payment_order` 先建 `WAIT`，`billNo` 帶到 provider 當 merchant order id；provider accepted 後仍要等 callback / 查單確認。
- 高風險斷點：本地 insert 成功但 provider request timeout、金額單位轉換、provider accepted no callback、response fail 但 provider 已建單、重複 `/newPay`、sign / notify URL 設定錯。
- 正式履歷不更新，因為 Nick 本人 evidence 未補。

## 下一步

```text
iwin payment payment-order-provider-request Step 4
```
