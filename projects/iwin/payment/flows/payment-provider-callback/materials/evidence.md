# payment-provider-callback evidence

## 掃描範圍

本輪 Step 3 使用 Level 2 掃描。

2026-05-15 KB 更新後複查：

- 已重新讀 KB 與本 flow 文件。
- 已依新版模板補強 `flow.md` 的閱讀定位、初中階 code 分層、業務問題、系統位置與 code path。
- Step 3 內容方向判斷：`可沿用 / 已補格式與 evidence 邊界`。
- 下一步仍是 Step 4，不新增自創任務名稱。

Vault：

- 已重讀 `AGENTS.md`。
- 已重讀 `senior-owner-playbook/00-operating-rules.md`。
- 已重讀 `senior-owner-playbook/09-ai-prompt-manual.md`。
- 已重讀 `senior-owner-playbook/03-flow-learning-package-template.md`。
- 已重讀 `projects/iwin/payment/README.md`。
- 已重讀 `projects/iwin/payment/step2-flow-comparison.md`。
- 已重讀 `projects/iwin/payment/flows/payment-provider-callback/flow.md`。
- 已重讀 `projects/iwin/payment/flows/payment-provider-callback/career-interview.md`。
- 已重讀 `projects/iwin/payment/flows/payment-provider-callback/materials/evidence.md`。

Code repo：

- repo：`/Users/nick/Git/iwin/payment`
- branch：`k3s`
- fetch：Step 3 建檔時已執行 `git fetch --all --prune`；2026-05-15 KB 更新後複查也已重新執行。
- HEAD：`e8be8a1466d9cd642e5f63af86f047c6a8d054bd`
- `origin/k3s`：`e8be8a1466d9cd642e5f63af86f047c6a8d054bd`
- ahead / behind：`0 / 0`
- 公司 repo 既有 untracked：`payment/src/main/java/cn/com/payment/service/impl/.DS_Store`，未修改。

既有文件狀態判斷：

| 文件 | 狀態 | 調整 |
| --- | --- | --- |
| `projects/iwin/payment/README.md` | 可沿用 | 已指向 Step 4；本輪不改 Step 主線 |
| `projects/iwin/payment/step1-candidate-flows.md` | 可沿用 | Step 2 已補 remote freshness；本輪不重寫 |
| `projects/iwin/payment/step2-flow-comparison.md` | 可沿用 | 已有 module / service 邊界與 candidate flow 比較 |
| `flows/payment-provider-callback/flow.md` | 可沿用 / 已補強 | 補新版 KB 要求的 code 分層、系統位置、code path 與業務問題 |
| `flows/payment-provider-callback/career-interview.md` | 可沿用 | 保守 claim 清楚，不更新正式履歷 |
| `materials/evidence.md` | 可沿用 / 已補強 | 補 KB 更新後複查與最新 fetch 記錄 |
| `materials/decision-notes.md` | 可沿用 | 已有 ack timing、idempotency、退款、reconciliation owner decision |
| `materials/claim-boundary.md` | 可沿用 | 已明確標示沒有 `真實開發過` evidence |

本輪有掃：

- `payment/src/main/java/cn/com/payment/controller/BaseController.java`
- `payment/src/main/java/cn/com/payment/service/impl/withdraw/BaseServiceImpl.java`
- `payment/src/main/java/cn/com/payment/comsumer/NotifyComsumer.java`
- `payment/src/main/java/cn/com/payment/utils/ProducerUtil.java`
- `payment/src/main/java/cn/com/payment/vo/OrderVO.java`
- `base/module/src/main/java/cn/com/enums/OrderReviewStatusEnum.java`
- `payment/src/main/java/cn/com/payment/controller/merchant/NanaPayController.java`
- `payment/src/main/java/cn/com/payment/service/impl/withdraw/NanaPayServiceImpl.java`
- `payment/src/main/java/cn/com/payment/controller/merchant/Pay4zController.java`
- `payment/src/main/java/cn/com/payment/service/impl/withdraw/Pay4zServiceImpl.java`
- 上述 path 的 `git log --all --oneline --max-count=80`

本輪未掃 / 待確認：

- game lobby / center 下游實作。
- DB schema / migration / unique key。
- 所有 provider controller 的完整矩陣。
- app_bi / admin 補單與人工修復 flow。
- 定時對帳 job / dead letter queue / alert 設定。
- Nick 本人 MR / ticket / commit author evidence。

## 代表 code evidence

### Provider callback 入口

NanaPay：

- `NanaPayController.payNotify`：解析 `mchOrderId`、白名單、簽章，`payStatus=1` 時送 `PAY + SUCCESS` 到 `asynUpdateOrderStatus`，回 `ok`。
- `NanaPayController.withdrawNotify`：解析 `mchOrderId`、`payStatus`、`failReason`，成功送 `WITHDRAW + SUCCESS`，失敗送 `WITHDRAW + ERROR`，特定狀態送 `BACK`。
- `NanaPayServiceImpl.unifiedPixWithdrawOrder`：代付下單成功更新 order merchant / outBillNo；下單失敗呼叫 `asynUpdateOrderStatus(..., WITHDRAW + ERROR, ...)`。

Pay4z：

- `Pay4zController.payNotify`：`PAID` 送 `PAY + SUCCESS`；`PAY_FAILED` / `REFUND` 走 `autoViewOrder(..., ERROR, status)`。
- `Pay4zController.withdrawNotify`：`PAID` 送 `WITHDRAW + SUCCESS`；`PAY_FAILED` / `REFUND` 送 `WITHDRAW + ERROR`。
- `Pay4zServiceImpl.unifiedPixWithdrawOrder`：代付下單成功更新 order merchant / outBillNo；下單失敗呼叫 `asynUpdateOrderStatus(..., WITHDRAW + ERROR, ...)`。

### 共用 guard

`BaseController.getOrderVO(response, orderNo, returnMsg)`：

- 查不到訂單會先寫回 provider `returnMsg`，再丟 exception。
- `status` 已是 `0 / 2 / 3 / 4` 時，會寫回 provider `returnMsg`，再丟 `ORDER_ALREADY_DONE`。
- 非 `WAIT` / `PROCESSING` 時也會寫回 provider `returnMsg`，再丟 `ORDER_IS_ALREADY_AUDIT`。

這是 provider callback 重送時最早的防線。

### MQ producer / consumer

`BaseServiceImpl.asynUpdateOrderStatus`：

- 組 `MessageJson`，包含 `orderNo`、`msg`、`billType`、`status`、`outBillNo`。
- 預設非同步，呼叫 `ProducerUtil.addProduce(topicNotify, groupNotify, ..., 50)`。
- `isAsyn=false` 時可直接呼叫 `updateUserInfo`。

`ProducerUtil.addProduce`：

- 建立 `XxlMqMessage`，設定 topic、group、data、effectTime、retryCount。
- `XxlMqProducer.produce(message)` exception 被 catch 並 log，沒有往上拋。

`NotifyComsumer.doConsume`：

- 反序列化 `MessageJson`。
- 呼叫 `updateUserInfo`。
- exception 時回 `MqResult.FAIL`。

### 狀態與副作用核心

`BaseServiceImpl.updateUserInfo`：

- PAY：
  - 只允許 `WAIT` / `PROCESSING`。
  - 呼叫 `upperDeposit(order, logUser, true, true)`。
  - 更新 order status / outBillNo。
  - 更新 user service field、player layer、email、kyOrder。
- WITHDRAW SUCCESS：
  - 只允許 `WAIT` / `PROCESSING`。
  - 更新 order status / outBillNo。
  - 更新 user service field。
  - 可能寄信 / 跑馬燈。
- WITHDRAW ERROR：
  - 設定 `TradeTypeEnum.WITHDRAWBACK`。
  - 呼叫 `upperDeposit(order, logUser, false, true)` 退款。
  - 更新 order status / outBillNo / recordRemark。
  - 更新 user service field、刪 Redis billNo、寄信。
  - catch exception 時更新訂單成失敗並回 `MqResult.SUCCESS`，註解指出是為了避免已退款後 MQ retry 又進入退款流程。

### 訂單狀態

`OrderReviewStatusEnum`：

- `WAIT=1`
- `REFUSE=2`
- `BACK=3`
- `ERROR=4`
- `PAYSUCCESS=5`
- `PROCESSING=6`
- `PENALTIESERR=7`
- `SUCCESS=0`

`OrderVO`：

- table：`payment_order`
- callback 相關欄位：`billNo`、`billType`、`status`、`merchantId`、`merchantName`、`tradeType`、`recordRemark`、`outBillNo`、`money`、`uid`、`openId`。

## Path history evidence

NanaPay path history：

- `a56b407 security: 憑證外部化 + 日誌 key 遮罩(payment)`
- `7d4de37 feat(phase5b): SB 2.7→3.2 + Java 11→21 + Jakarta EE 9 + 大批依賴升級`
- `884230a fix:pay4z errorMsg不加簽; nanapay回ok不再回調`
- `3277b78 fix(#1842): NaNapay提现回调sing少failReason`
- `65eebbb feat(#1842):NaNapay商户对接`
- `7ae7f11 feat(#1842):NaNapay商户对接`

Pay4z path history：

- `7d4de37 feat(phase5b): SB 2.7→3.2 + Java 11→21 + Jakarta EE 9 + 大批依賴升級`
- `9aa0477 fix: pay4z errorMsg 加号被空格替换 sign 不一致`
- `884230a fix:pay4z errorMsg不加簽; nanapay回ok不再回調`
- `46b906c fix: pay4z onepay DB欄位大小`
- `4113eea fix: pay4z onepay 无法解析响应`
- `75bb869 fix: pay4z onepay 无法解析响应`
- `ec85d21 feat(#285): pay4z PixAccount`
- `1385c27 feat(#285): pay4z PixAccount`
- `4df2c22 feat(#285): pay4z 对接 註解`
- `2e1b25e feat(#285): pay4z 对接 註解`
- `7853917 feat(#285): pay4z 对接`
- `702cc73 feat(#285): pay4z 对接`

Shared callback / MQ path history：

- `3908da9 merge: feature/nimtestpay-dev into k3s`
- `03c28e3 fix: clear copied order id before payment insert`
- `7d4de37 feat(phase5b): SB 2.7→3.2 + Java 11→21 + Jakarta EE 9 + 大批依賴升級`
- `ef67724 feat(#bugs/withdraw_notify_consumer): 提現異常 加上原本流程 clearBillNo`
- `ff42791 feat(#bugs/withdraw_notify_consumer): 提現 防止已退款又拋出异常，導致mq retry又進入退款流程`
- `60c2951 feat(#notify_comsumer_error): msg大於500 就擷取前500字`
- `c4d29d3 feat(#優化): mybatis切換不同DB連線`
- `5b02942 feat(#優化): iwin免人工審核提款功能`
- `6b410c9 feat(#自動提現): 修改autowithdraw_remark備註說明`
- `433a0c0 feat(#優化): 提現自動審核條件判斷, 不符條件改人工審核`
- `b141529 feat(新增): ProducerUtil 加 log`

解讀：

- history 支持「這條 flow 曾經涉及 provider 對接、callback ack、sign、DB 欄位、訊息長度、退款 retry」等問題。
- history 不足以證明 Nick 個人做過，除非補 commit author / MR / ticket / 本人確認。

## 已確認 / 推測 / 待確認

已確認：

- provider callback 入口存在 pay / withdraw notify。
- callback 會做白名單與簽章。
- callback 會 mapping provider status 到內部 `OrderReviewStatusEnum`。
- 主線透過 XXL-MQ 非同步進 `updateUserInfo`。
- consumer 端有訂單狀態 guard。
- 提現失敗退款有避免 MQ retry 重複退款的例外處理。

推測：

- app_bi / admin 應提供人工修復或補單入口，但本輪未納入 code-backed 主線。
- game lobby / center 可能用 `billNos` 做冪等鍵，但本輪未掃下游，不能當已確認。

待確認：

- DB unique key 與分月表 schema。
- 下游 `DEPOSIT` / `WITHDRAW` 是否 idempotent。
- MQ produce failure 是否有 alert。
- callback raw event 是否被保存。
- provider 查單與定時對帳如何回寫 order。
- Nick 本人參與 evidence。
