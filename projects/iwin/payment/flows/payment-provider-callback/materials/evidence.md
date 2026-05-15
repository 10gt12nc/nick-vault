# payment-provider-callback evidence

## 掃描範圍

本 flow 使用 Level 2 掃描。Step 4 不是重開新 flow，而是在 Step 3 主報告上補 failure / consistency evidence。

2026-05-15 KB 更新後複查：

- 已重新讀 KB 與本 flow 文件。
- 已依新版模板補強 `flow.md` 的閱讀定位、初中階 code 分層、業務問題、系統位置與 code path。
- Step 3 內容方向判斷：`可沿用 / 已補格式與 evidence 邊界`。
- Step 4 內容方向判斷：`可沿用 / 已補 failure evidence`。
- 下一步是 Step 5，不新增自創任務名稱。

2026-05-15 Step 4 深掃補充：

- vault worktree：`/Users/nick/Git/nick/nick-vault-iwin-payment`
- vault branch：`codex/iwin-payment`
- vault branch freshness：已 fetch origin，並 merge `origin/main` 到本分支。
- 掃描深度判斷：Level 2。理由是本次目標是單條 flow 的 failure / consistency evidence，需要看主線、下游入口、path-specific history 與重要 diff；尚未要求「逐檔逐行 / 每個 commit diff」的 Level 3。
- 公司 repo 僅讀取，未修改。

Step 4 code repo freshness：

| repo | branch / HEAD | remote freshness | 本輪用途 |
| --- | --- | --- | --- |
| `/Users/nick/Git/iwin/payment` | `k3s` / `e8be8a1466d9cd642e5f63af86f047c6a8d054bd` | `origin/k3s` 同步，ahead / behind `0 / 0` | callback 主線、月表 routing、bugfix diff |
| `/Users/nick/Git/iwin/iwin_gameserver` | `main` / `30a9fcb95bfda33b582deeb4e149eb06bed4afe3` | `origin/main` 同步，ahead / behind `0 / 0` | game lobby / center `billNo` 傳遞 |
| `/Users/nick/Git/iwin/app_bi` | `main` / `4a206a28ab8f5be4329602cdc510ee9ea41efb25` | 本機落後 `origin/main` 4 commits | repair boundary 參考；不當最新完整結論 |

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
| `projects/iwin/payment/README.md` | 已更新 | 已指向 Step 5 |
| `projects/iwin/payment/step1-candidate-flows.md` | 可沿用 | Step 2 已補 remote freshness；本輪不重寫 |
| `projects/iwin/payment/step2-flow-comparison.md` | 可沿用 | 已有 module / service 邊界與 candidate flow 比較 |
| `flows/payment-provider-callback/flow.md` | 已更新 | 補 Step 4 failure / consistency evidence |
| `flows/payment-provider-callback/career-interview.md` | 已更新 | 補 `billNo` 傳遞可講、去重不可誇大 |
| `materials/evidence.md` | 已更新 | 補 Step 4 掃描範圍、下游 evidence、bugfix diff |
| `materials/decision-notes.md` | 已更新 | 補 Step 4 owner decision |
| `materials/claim-boundary.md` | 已更新 | 補不能宣稱下游 exactly-once |

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

- DB schema / migration / unique key。
- 所有 provider controller 的完整矩陣。
- 定時對帳 job / dead letter queue / alert 設定。
- Nick 本人 MR / ticket / commit author evidence。

Step 4 另有掃：

- `/Users/nick/Git/iwin/payment/base/module/src/main/java/cn/com/config/MybatisPlusConfig.java`
- `/Users/nick/Git/iwin/payment/base/module/src/main/java/cn/com/cache/DruidDBConfig.java`
- `/Users/nick/Git/iwin/iwin_gameserver/slots-center/src/main/java/com/slots/center/service/HttpService.java`
- `/Users/nick/Git/iwin/iwin_gameserver/slots-center/src/main/java/com/slots/sql/job/HttpNewBill.java`
- `/Users/nick/Git/iwin/iwin_gameserver/slots-center/src/main/java/com/slots/center/job/http/NewBillJob.java`
- `/Users/nick/Git/iwin/iwin_gameserver/slots-center/src/main/java/com/slots/center/data/PlayerData.java`
- `/Users/nick/Git/iwin/app_bi/app/common/Common.php`
- `/Users/nick/Git/iwin/app_bi/app/admin/controller/UserCurrency.php`
- `/Users/nick/Git/iwin/app_bi/app/validateService/SubscriberService.php`
- payment shared path diff：`ff42791`、`ef67724`、`03c28e3`

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

### Step 4：月表與 DB unique key evidence

已確認：

- `MybatisPlusConfig` 會把邏輯表 `payment_order` 改寫成 `payment_order_yyyy_MM`。
- `DruidDBConfig` 也有 `payment_order` 動態表名 handler；查詢可用 context table name，審核 / 更新可由 `billNo` 前 8 碼推月份，預設用目前月份 `payment_order_yyyy_M`。
- `03c28e3 fix: clear copied order id before payment insert` 顯示 `BeanUtil.toBean(user, OrderVO.class)` 會把 `log_user.id` 複製到 `OrderVO.id`，因此 create order insert 前必須 `orderVO.setId(null)`，避免同 uid 第二筆撞主鍵；該 commit 也補了單元測試描述這個風險。

待確認：

- 本輪在 payment repo 未找到 `payment_order` DDL / migration，所以不能確認 `bill_no` 是否有 DB unique key。
- 月表自增主鍵、`bill_no` index、跨月搬移後是否仍能保證唯一，需補 DB schema 或 DBA evidence。

### Step 4：game lobby / center `billNo` 傳遞 evidence

已確認：

- payment `upperDeposit` 會送 `cmd=DEPOSIT`，並把 `orderVO.getBillNo()` 放入 `billNos`。
- payment `gmDownScore` 會送 `cmd=WITHDRAW`，並把 `orderInsert.getBillNo()` 放入 `billNos`。
- iwin_gameserver `HttpService.deal` 會把 `billNos` 傳入 `onDeposit` / `onWithdraw`。
- `onDeposit` / `onWithdraw` 建立 `HttpNewBill`，將 `billNo` 放進 job。
- `HttpNewBill.runImpl` 建立 `NewBillJob`，繼續傳遞 `billNo`。
- `NewBillJob.modifyCoin` 依大廳 / 銀行路徑呼叫 `modifyAndGetCoinFromBill` 或 `modifyAndGetBankCoin`，並帶入 `billNo`。
- `PlayerData.modifyAndGetCoin` / `modifyAndGetBankCoin` 會把 `billNo` 帶入 `buildCurrencyLog`。

保守解讀：

- `billNo` 是 code-backed 的 cross-system correlation key，也是 idempotency key 候選。
- 但本輪掃到的下游檔案未看到「先查同一 billNo 是否已處理」或「同一 billNo unique constraint 失敗就 no-op」的明確 evidence。
- 因此只能說下游有帶 `billNo` 做 audit / trace，不可說 game lobby / center 已確認 idempotent。

### Step 4：app_bi repair boundary evidence

已確認，但只限 app_bi local HEAD：

- `Common::edit_currency_status($BillNos, $Status, $Mid, $month, $datas)` 會用 `BillNos` 推出 `payment_order_yyyy_M`，查 `bill_no` 且狀態必須為 `1`，再更新 status、mid、last_modify_time、reason / remark。
- `UserCurrency::bill_check` 會寫 `opLog`，並在拒絕 / 退回等分支呼叫 `edit_currency_status`。
- `SubscriberService::checkAuditShareAward` 會檢查 `billNos` / `status`，且拒絕時要求 reason。

保守解讀：

- app_bi 可作為人工審核 / 修復入口的 evidence，但不是 payment callback 主線。
- app_bi 本機分支落後 `origin/main` 4 commits；本輪未 pull 公司 repo，所以不能說這是最新 app_bi 行為。
- 沒有 Nick 本人 evidence 前，不把 app_bi repair 包裝成 Nick 主導的 owner 成果。

### Step 4：重要 bugfix diff evidence

- `ff42791 feat(#bugs/withdraw_notify_consumer): 提現 防止已退款又拋出异常，導致mq retry又進入退款流程`
  - 對 `WITHDRAW ERROR` 分支加 try / catch。
  - catch 後重新查訂單，設定失敗狀態與「需人工處理」語意，並回 `MqResult.SUCCESS`。
  - 註解明確指出目標是避免已退款後 exception 造成 MQ retry 再次進入退款流程。
- `ef67724 feat(#bugs/withdraw_notify_consumer): 提現異常 加上原本流程 clearBillNo`
  - 在上述 catch return 前補 `DynamicDataSourceContextHolder.clearBillNo()`。
  - evidence 顯示跨月 / 動態表 context cleanup 也是這條 flow 的 failure concern。
- `03c28e3 fix: clear copied order id before payment insert`
  - 在 create order insert 前清空 `OrderVO.id`。
  - 單元測試證明 `BeanUtil` 會把 `LogUserVO.id` 複製成 `OrderVO.id`，若不清空可能造成同 uid 後續訂單撞主鍵。

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
- `payment_order` 有動態月表 routing。
- payment 會把 `billNo` 以 `billNos` 傳給 game lobby / center。
- game lobby / center 會把 `billNo` 帶入玩家餘額異動與 currency log。
- app_bi local HEAD 有人工訂單狀態修復入口。

推測：

- 定時對帳、callback replay 或 dead letter 可能存在於其他排程 / 營運工具，但本輪未找到 code-backed evidence。

待確認：

- DB unique key 與分月表 schema。
- 下游 `DEPOSIT` / `WITHDRAW` 是否 idempotent。
- 下游是否有 `billNo` 查重或 currency log unique key。
- MQ produce failure 是否有 alert。
- callback raw event 是否被保存。
- provider 查單與定時對帳如何回寫 order。
- app_bi 最新 `origin/main` 是否與 local HEAD repair 行為一致。
- Nick 本人參與 evidence。
