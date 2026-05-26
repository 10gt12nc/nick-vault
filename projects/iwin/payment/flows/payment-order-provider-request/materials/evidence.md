# evidence

## 本次掃描定位

- 任務：`iwin payment payment-order-provider-request Step 5`。
- 日期：2026-05-18。
- 掃描等級：Level 2+ claim gate；在 Step 4 code path 基礎上追 Nick branch、author、path-specific history 與重要 diff。
- 證據層級：`真實開發過` / `專案存在 / code-backed` 混合；Nick 貢獻從 `待確認` 修正為「部分 provider request 真實開發過」。

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
- `projects/iwin/payment/flows/payment-order-provider-request/flow.md`
- `projects/iwin/payment/flows/payment-order-provider-request/career-interview.md`
- `projects/iwin/payment/flows/payment-order-provider-request/materials/interview.md`
- `projects/iwin/payment/flows/payment-order-provider-request/materials/claim-boundary.md`
- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

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
- Step 4 重看代表 controller 後，確認 `Pay4zController` 查單會回 `SUCCESS` / `FAIL` / `UNKOWN` 語意，但這仍是查單入口，不等於已確認自動 reconciliation。
- Step 4 重看 `NewCashPayController` 後，確認 response abnormal / parse fail 會標 `ERROR`，但部份查單 response abnormal 的更新語句有註解線索，不能推論所有 unknown 都會進終態。

## Step 5 Nick 個人貢獻 evidence

### source repo / branch evidence

已掃遠端分支：

- `origin/pay4z-Nick`
- `origin/NaNapay_Nick`
- `origin/Nick-02-mdTest`
- `origin/Nick-03`
- `origin/Nick-work-item-201-test`
- `origin/feature/nimtestpay-dev`
- `origin/nick`
- `origin/Kafka-Nick`

已確認與本 flow 直接相關：

| 分支 / commit | Author / Committer | 日期 | 檔案 | 判斷 |
| --- | --- | --- | --- | --- |
| `origin/pay4z-Nick` / `702cc73` | `10gt12nc` | 2025-01-14 | `Pay4zController.java`、`Pay4zServiceImpl.java` | 新增 Pay4z provider request / callback / query / withdraw service |
| `7853917` | `10gt12nc` | 2025-01-14 | `Pay4zController.java`、`Pay4zServiceImpl.java` | 同 Pay4z 對接 commit，進入主線 history |
| `9aa0477` | `10gt12nc` | 2025-12-01 | `Pay4zController.java`、`Utils.java` | 修正 Pay4z callback / sign 解析：`errorMsg` 加號被空格替換造成 sign mismatch |
| `origin/NaNapay_Nick` / `7ae7f11` | `10gt12nc` | 2025-11-13 | `NanaPayController.java`、`NanaPayServiceImpl.java` | 新增 NaNapay provider request / callback / query / withdraw service |
| `260e550` | `10gt12nc` | 2024-12-26 | `BFPayController.java`、`NewCashPayController.java` | BFPAY order / callback 修正與 NewCashPay 查單更新 |
| `7403277` | `10gt12nc` | 2026-04-30 | `NimTestPayController.java`、`application.yml` | 新增 NimTestPay local / SIT manual testing controller；Co-Authored-By Claude，不能包裝成 production provider owner |
| `03c28e3` | `10gt12nc` | 2026-05-14 | `BaseServiceImpl#createOrderNo`、unit test | 修正 `log_user.id` 被 BeanUtil 複製到 `payment_order.id`，避免同 uid 第二筆 order 撞主鍵 |

### 重要 diff 摘要

- Pay4z 新增 `Pay4zController`：`/pay4z/newPay` 會 `getMerchant`、`validMoney`、`createOrderNo`，以 `merchantOrderNo = order.billNo` 組 provider request，處理 amount 分 / 元、callback URL、sign、provider response、`CashPayVO` 與 failure -> `ERROR`。
- Pay4z callback / query：同 controller 包含 `/pay/notify`、`/pay/getOrderStatus`，處理 sign、white list、`PAID` / `PAY_FAILED` / `REFUND` / unknown。
- Pay4z sign bugfix：`9aa0477` 改用不過濾 `+` 的 request parser，避免 provider callback errorMsg 中 `+` 被 decode 成空白後驗簽失敗。
- NaNapay 新增 `NanaPayController`：與本 flow 相同形狀，包含 `createOrderNo`、`mchOrderId = billNo`、SHA512 sign、amount unit、provider request、callback 與 query。
- BFPAY / NewCashPay：`260e550` 改查單 URL、sign key、query form request，並避免部分 NewCashPay 查單 response abnormal 時直接改 order `ERROR`。
- `03c28e3`：`createOrderNo` 清掉 copied id 並新增 unit test，屬於 provider request 共用建單風險修復。

### Step 5 claim 判斷

可升級為 `真實開發過`：

- Nick 參與 iwin payment 多個 provider request / callback / query 對接與維護。
- Pay4z 是最強履歷 evidence，可明確說「參與 Pay4z provider 對接」，但仍不寫主導完整金流。
- NaNapay / BFPAY 可作輔助 provider integration evidence。
- `createOrderNo` id collision fix 可說「修復 payment order 建單時 BeanUtil 複製 id 的資料一致性問題」，但不要擴大成完整建單架構 owner。

仍只能 code-backed / 待確認：

- 完整 payment 金流 owner。
- 全部 provider 一致標準化。
- DB unique key 與 production schema。
- 自動 reconciliation job。
- provider timeout 對帳 SOP。
- 所有 provider 的 end-to-end idempotency。

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
- `10gt12nc` 與 Nick 本人身份已可依本輪語境與 branch naming 作履歷素材使用；若要對外附證，仍建議 Nick 保存 GitHub / GitLab 帳號對應截圖或 HR 可接受的佐證。

## 未掃

- 未逐 provider 全部 `/newPay` 逐行掃。
- 已展開 Pay4z、NaNapay、BFPAY / NewCashPay、NimTestPay 與 `createOrderNo` 代表 diff；未逐 commit diff 展開所有 provider 對接。
- 未掃 DB schema / migration / production index。
- 未掃 timer / reconciliation job 全貌。
- 未掃完整 app_bi repair UI。

## Step 5 結論

- 本 flow 已完成 Step 5 claim gate。
- Nick 個人貢獻不應再維持「待確認」：至少 Pay4z / NaNapay / BFPAY / GoldenPay / NimTestPay 相關 provider request / query / callback code 有 `10gt12nc` path-specific commits 與 Nick branch / direct commit evidence。
- 正式履歷可保守更新為「參與第三方金流 provider request / callback / query 對接與維護」，不寫主導、不寫全權 owner、不寫量化改善。
- 核心講法仍是：`payment_order` 先建 `WAIT`，`billNo` 帶到 provider 當 merchant order id；provider accepted 後仍要等 callback / 查單確認，`newPay` success 不等於上分。

## 下一步

```text
- 全域下一步狀態：目前沒有預設 project flow 下一步；請以 senior-owner-playbook/01-senior-owner-flow-inventory.md 與 senior-owner-playbook/06-todo.md 為準。
```
