# evidence

## 本次掃描定位

- 任務：`iwin payment withdrawal-auto-review-refund Step 5`。
- 日期：2026-05-18。
- 掃描等級：Level 2 Flow 深掃；Step 5 複查 Step 4 evidence，完成履歷 / 自傳 claim gate。
- 證據層級：`專案存在 / code-backed`；Nick 貢獻 `待確認`。

## 自動重讀

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`

已重讀 vault：

- `projects/iwin/payment/README.md`
- `projects/iwin/payment/step1-candidate-flows.md`
- `projects/iwin/payment/step2-flow-comparison.md`
- `projects/iwin/payment/flows/payment-provider-callback/flow.md`
- `projects/iwin/payment/flows/withdrawal-auto-review-refund/flow.md`
- `projects/iwin/payment/flows/withdrawal-auto-review-refund/career-interview.md`
- `projects/iwin/payment/flows/withdrawal-auto-review-refund/materials/*.md`
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

### `/Users/nick/Git/iwin/iwin_gameserver`

- 已執行 `git fetch --all --prune` 更新 remote refs。
- 本機分支：`main`
- local HEAD：`30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- `origin/k3s` HEAD：`2baab333e31a525aafc74fd9bc4e5d39e0b36cf4`
- ahead / behind：`0 / 33`
- 本輪未 pull、未 checkout、未改工作樹。
- 下游 evidence 只用 `git show origin/k3s:<path>` 讀遠端版本關鍵檔案；因此只確認 `origin/k3s` 的 code path，不宣稱本機 gameserver 已更新。

## 已掃 code path

payment：

- `payment/src/main/java/cn/com/payment/controller/PayPublicController.java`
- `payment/src/main/java/cn/com/payment/service/impl/PayTypeServiceImpl.java`
- `payment/src/main/java/cn/com/payment/controller/WithdrawController.java`
- `payment/src/main/java/cn/com/payment/service/impl/HandlerRouterServiceImpl.java`
- `payment/src/main/java/cn/com/payment/comsumer/WithdrawComsumer.java`
- `payment/src/main/java/cn/com/payment/service/impl/AdminGmServiceImpl.java`
- `payment/src/main/java/cn/com/payment/service/impl/withdraw/BaseServiceImpl.java`
- `payment/src/main/java/cn/com/payment/service/impl/withdraw/NimTestPayServiceImpl.java`
- `payment/src/main/java/cn/com/payment/service/impl/withdraw/Pay4zServiceImpl.java`
- `payment/src/main/java/cn/com/payment/controller/merchant/NimTestPayController.java`
- `payment/src/main/java/cn/com/payment/controller/merchant/Pay4zController.java`
- `payment/src/main/java/cn/com/payment/comsumer/NotifyComsumer.java`
- `payment/src/main/java/cn/com/payment/utils/ProducerUtil.java`
- `payment/src/main/java/cn/com/payment/vo/OrderVO.java`
- `payment/src/main/java/cn/com/payment/vo/QueryWithdrawVO.java`
- `base/module/src/main/java/cn/com/enums/OrderReviewStatusEnum.java`
- `base/module/src/main/java/cn/com/enums/PayTypeOrderEnum.java`
- `base/module/src/main/java/cn/com/enums/TradeTypeEnum.java`
- `base/module/src/main/java/cn/com/enums/WithdrawTypeEnum.java`

gameserver `origin/k3s`：

- `slots-center/src/main/java/com/slots/center/service/HttpService.java`
- `slots-center/src/main/java/com/slots/center/job/http/NewBillJob.java`
- `slots-center/src/main/java/com/slots/center/data/PlayerData.java`

## 已確認 facts

- `/payment/public/withdraw` 入口在 `PayPublicController.withdraw`。
- `PayTypeServiceImpl.withdraw` 會驗玩家、支付綁定、黑名單、提款配置、待審單、打碼目標、玩家狀態與手續費。
- 建單前會呼叫 game lobby / center：
  - `QUERY_BET_TARGET`
  - `PLAYERINFO`
  - `WITHDRAW`
- `gmDownScore` 會送 `cmd=WITHDRAW`、`billNos=orderNo`、`value=order.money`。
- `gmDownScore` 成功後才 `orderInsert.insert()`。
- `getAutoMerchant` 依自動審核商戶金額區間與時間區間判斷是否符合。
- 若符合自動審核商戶，建單時會設 `is_auto_review=1`。
- `AdminGmServiceImpl.autoWithdraw` 每分鐘跑，讀 job switch，不為 `1` 就不執行。
- `fetchAutoWithdrawLog` 掃最近 3 天 `billType=WITHDRAW`、`is_auto_review=1`、`status=WAIT` 訂單。
- `checkAutoWithdraw` 檢查玩家 `is_autowithdraw`、玩家層級、`is_open_autowithdraw`、金額上下限、今日充值、今日打碼比例。
- 自動出款 job 會把訂單改 `PROCESSING`，寫 merchant name / id，反射呼叫 provider service。
- `WithdrawController` + `HandlerRouterServiceImpl` 是另一條人工 / router 指定商戶出款路徑，會先改 `PROCESSING` 再丟 auto withdraw MQ。
- `WithdrawComsumer` 消費 auto withdraw MQ 後依 `withdrawType` 反射呼叫 provider service。
- 代表 provider service 若下單失敗或 response parse fail，會呼叫 `asynUpdateOrderStatus(ERROR, WITHDRAW)`。
- Pay4z / NimTestPay controller 均有 `/withdraw/getOrderStatus` 代付查單入口線索。
- `NotifyComsumer` 消費 notify topic 後呼叫 `updateUserInfo`；處理 exception 時回 `MqResult.FAIL`。
- `ProducerUtil.addProduce` catch exception 只 log，沒有往外拋。
- `updateUserInfo` 提現失敗分支會把 `tradeType` 改 `WITHDRAWBACK`，呼叫 `upperDeposit` 退款，再更新訂單 `ERROR` 與失敗原因。
- `updateUserInfo` 在處理提現前會 guard 訂單狀態必須是 `WAIT` 或 `PROCESSING`。
- `ff42791` / `ef67724` 相關 diff 顯示曾補過「防止已退款後 MQ retry 再次進退款流程」與 clear billNo。
- gameserver `origin/k3s` 的 `HttpService` 會 dispatch `DEPOSIT` / `WITHDRAW` / `PLAYERINFO` / `QUERY_BET_TARGET`。
- gameserver `origin/k3s` 的 `NewBillJob` 會用 `billNo` 呼叫 `modifyAndGetCoinFromBill` / `modifyAndGetBankCoin`，並寫入 currency log。

## 相關 commit 線索

payment log grep 找到：

- `5b02942 feat(#優化): iwin免人工審核提款功能`
- `433a0c0 feat(#優化): 提現自動審核條件判斷, 不符條件改人工審核`
- `6b410c9 feat(#自動提現): 修改autowithdraw_remark備註說明`
- `8a01070 fix: read auto withdraw switch null-safely`
- `a70a5c3 fix: guard auto withdraw switch null`
- `6539d7a fix: clear copied order id before withdraw insert`
- `ff42791 feat(#bugs/withdraw_notify_consumer): 提現 防止已退款又拋出异常，導致mq retry又進入退款流程`
- `ef67724 feat(#bugs/withdraw_notify_consumer): 提現異常 加上原本流程 clearBillNo`
- `d804ee8 fix: store nimtestpay withdraw provider order`

## 合理推論

- `PROCESSING` 是 provider request 已送出或正在出款的中間態。
- 自動出款失敗但還沒有 provider 終態時，多數 branch 會轉人工審核，而不是直接退款。
- provider 明確失敗或 callback 明確失敗時，會走退款。
- `billNo` 是跨 payment 與 game lobby / center 的主要 idempotency / trace key，但本輪只確認傳遞與 log，未確認強制去重。
- 多 provider 查單入口可以作為 reconciliation 輔助，但目前只確認入口存在，未確認定時自動對帳 job。
- `ProducerUtil` 的錯誤處理使 MQ produce fail 可能不會中止上游流程，因此 Step 4 把它列為 owner 風險。

## 待確認

- `payment_order.bill_no` 是否 unique。
- 扣分成功但 `payment_order.insert()` 失敗時，有沒有補償 / repair job。
- `ProducerUtil.addProduce` fail 只 log 後，是否有外部 MQ producer alarm。
- provider accepted 後長時間無 callback，是否有 query order / reconciliation。
- gameserver / DB 是否用 `billNo` 阻止重複上下分 / 退款。
- app_bi 人工補單 / 改狀態與這條 flow 的 repair 邊界。
- Nick 本人是否參與任一 commit、MR、ticket 或 production incident。

## 未掃

- 未逐 provider 全部 service 逐行掃。
- 未逐 commit diff 展開所有 provider 變更。
- 未掃 DB schema / migration / production index。
- 未掃完整 app_bi repair UI。
- 未掃 timer / reconciliation job 全貌。

## Step 4 面試 case 結論

- 本 flow 已可作為保守面試 case，主軸是跨系統 money correctness。
- 核心講法：`payment_order`、game lobby / center 餘額、provider payout status 是三個 source of truth。
- 高風險斷點：扣分成功但建單失敗、MQ produce fail only log、provider accepted no callback、重複 callback / MQ retry、下游 `billNo` 去重待確認。
- 仍不可升級履歷 claim，因為 Nick 本人 evidence 未補。

## Step 5 claim gate 結論

- 不更新正式履歷 / 自傳。
- 不更新 `senior-owner-playbook/05-resume-master-zh.md`。
- 不更新 `senior-owner-playbook/08-application-autobiography-zh.md`。
- 原因：目前 evidence 只到 `專案存在 / code-backed` 與 `分析素材 / learning-only`，缺 Nick 本人 MR / ticket / commit author / production issue / 本人確認。
- 可保留為 Senior 面試素材：提款 money correctness、扣分後建單 failure window、自動審核條件、provider accepted 不等於成功、失敗退款防重複、`billNo` trace 與 reconciliation 邊界。
- 不可升級成：Nick 主導自動出款、設計提款退款架構、修復重複退款、確認 wallet exactly-once、完整 payment owner。

## 下一步

payment project 已完成 Top 5 flow 與 contribution consolidation；下一步回到 `game_api contribution claim consolidation`。
