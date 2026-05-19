# evidence

## 本次掃描定位

- 任務：`iwin payment manual-order-review-repair Step 3`。
- 日期：2026-05-18。
- 掃描等級：Level 2 Flow 深掃。
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
- `projects/iwin/payment/flows/payment-provider-callback/materials/evidence.md`
- `projects/iwin/payment/flows/withdrawal-auto-review-refund/materials/evidence.md`
- `projects/iwin/payment/flows/payment-order-provider-request/materials/evidence.md`
- `projects/iwin/app_bi` 既有 payment repair 相關索引與 flow 摘要
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/06-todo.md`
- `senior-owner-playbook/13-code-capability-map.md`

## source repo 狀態

### `/Users/nick/Git/iwin/payment`

- 已執行 `git fetch --all --prune` 更新 remote refs。
- 分支：`k3s`
- local HEAD：`bb7794e55386d914801887cc43b53d263c74d3c3`
- `origin/k3s` HEAD：`bb7794e55386d914801887cc43b53d263c74d3c3`
- ahead / behind：`0 / 0`
- 工作樹狀態：只有既有未追蹤 `payment/src/main/java/cn/com/payment/service/impl/.DS_Store`；本輪只讀未動。

### `/Users/nick/Git/iwin/app_bi`

- 已執行 `git fetch --all --prune` 更新 remote refs。
- 本機分支：`main`
- local HEAD：`4a206a28ab8f5be4329602cdc510ee9ea41efb25`
- `origin/main` HEAD：`fd9881fc417e01f960d758b4b91ba1a10b507855`
- ahead / behind：`0 / 4`
- 本輪未 pull、未 checkout、未改工作樹。
- app_bi repair evidence 以 `origin/main:<path>` 讀最新遠端檔案確認；本機工作樹仍落後 4 commits。

## 已掃 code path

payment：

- `payment/src/main/java/cn/com/payment/controller/PayPublicController.java`
- `payment/src/main/java/cn/com/payment/service/impl/PayTypeServiceImpl.java`
- `payment/src/main/java/cn/com/payment/service/impl/withdraw/BaseServiceImpl.java`
- `base/module/src/main/java/cn/com/enums/OrderReviewStatusEnum.java`
- `base/module/src/main/java/cn/com/enums/TradeTypeEnum.java`
- `base/module/src/main/java/cn/com/enums/CenterServerCurrencyEnum.java`
- `base/module/src/main/java/cn/com/cache/DruidDBConfig.java`
- `payment/src/main/java/cn/com/payment/vo/OrderVO.java`
- `payment/src/main/java/cn/com/payment/vo/OrderViewVO.java`
- `payment/src/main/java/cn/com/payment/vo/PlayerVO.java`

app_bi：

- `app/common/DbConfig.php`
- `app/admin/controller/UserCurrency.php`
- `app/common/Common.php`
- `app/business/DataListService.php`
- `public/admin/gameMaster/operatorWorkList.html`

## 已確認 facts

- `PayPublicController` 提供 `/payment/public/gameRecharge` 與 `/payment/public/oderView`。
- `app_bi DbConfig` 的 `Deposit` 指向 `/payment/public/gameRecharge`，`VipDeposit` 指向 `/payment/public/oderView`。
- `app_bi UserCurrency::bill_check` 只查 `payment_order_yyyy_M.status=1` 的待審單。
- `bill_check` 一般審核會呼叫 payment `/oderView`。
- `bill_check` status=5 自動出款會呼叫 `/withdraw/bank/{merchant}`、`/withdraw/zfb/{merchant}` 或 `/withdraw/pix/{merchant}`。
- `PayTypeServiceImpl#oderView` 查 `log_user`、設定 `billNo` routing、讀 `payment_order`，並 guard 訂單必須是 `WAIT`。
- `OrderReviewStatusEnum` 定義：`WAIT=1`、`REFUSE=2`、`BACK=3`、`ERROR=4`、`PAYSUCCESS=5`、`PROCESSING=6`、`PENALTIESERR=7`、`SUCCESS=0`。
- 充值審核成功會呼叫 `upperDeposit`，再 `updateOrderStatus`、`updateServiceFiled`，並非只改 status。
- 提現退回會把 reason / tradeType 改成提現退回語意，呼叫 `upperDeposit` 退款，再更新訂單與玩家統計。
- 提現通過會更新訂單、玩家提現統計，並可能發 mail / marquee。
- 拒絕 / 其他失敗類狀態會更新訂單與失敗統計、發送通知。
- `gameRecharge` 支援銀商代理充值、兌換補單、GM 人工充值、線上掉單補單、其他充值與 GM 下分。
- `DruidDBConfig` 會依 `billNo` 前 8 碼 routing 到 `payment_order_yyyy_M`。
- `BaseServiceImpl#updateOrderStatus` insert / update 失敗會 throw `UPDATE_ORDER_FAIL`。
- `BaseServiceImpl#updateServiceFiled` 會更新 `log_user` 與 `user_behaviour` 的充值 / 提現統計。
- `app_bi DataListService::repairOrderService` 可直接更新 `payment_order_yyyy_M.status`、`remark`、`last_modify_time`，允許狀態值 `0/2/3/4`。
- `repairOrderService` 會寫 `opLog`，但 method 內未看到上分、退款、provider 查單或統計更新。
- `app_bi Common::edit_currency_status` 只允許 `status=1` 的訂單被審核修改，會寫 merchant、status、mid、remark / reason。
- `operatorWorkList.html` 有明確提示：自動提現提交異常時要先用代付快查與商戶後台確認，不要盲目改手動出款。

## path history evidence

payment manual-review 相關 path：

- `03c28e3` / `10gt12nc`：修正 `BaseServiceImpl#createOrderNo` payment insert id copy 問題，與人工補單 / gameRecharge 會共用建單風險相關，但不是 `oderView` 主線。
- `6539d7a` / `10gt12nc`：修正 withdraw insert id copy 問題，與提款建單一致性相關，但不是人工審核主線。
- `5b02942` / Derek：iwin 免人工審核提款功能。
- `433a0c0` / Derek：提現自動審核條件判斷，不符條件改人工審核。
- `ff42791` / gill：提現防止已退款後 exception 導致 MQ retry 又進退款流程。
- `ef67724` / gill：提現異常補 clearBillNo。

app_bi repair 相關 path：

- `42feaee` / gill：充值、提现状态修正列表。
- `d3b33ee` / gill：oplog、訂單修復狀態搬到 service，新開 API 給提现状态修正列表。
- `36718d4` / gill：充值單編輯狀態跨月問題。
- `900c7d5` / gill：提現申請列表改為 merchant_name 搜尋。
- 目前未找到 `10gt12nc` 直接修改 app_bi repair / bill_check / repairOrderService 的 path-specific history。

## 合理推論

- `oderView` 是正常人工審核 API，會處理 money side effect。
- `gameRecharge` 是人工補單 / GM 上下分入口，適合放在同一條 recovery flow 裡理解。
- `repairOrderService` 是最後手段的狀態修復工具，應該需要外部 SOP 支撐。
- `PROCESSING` 卡住的訂單需要 provider 查單或商戶後台確認，不應直接退款或改成功。
- 人工修復與 provider callback 晚到會互相影響，必須依終態 guard 和 repair SOP 避免重複上分 / 退款。

## 待確認

- `payment_order.bill_no` 是否 unique。
- 下游 game lobby / center 是否用 `billNo` 強制去重。
- `repairOrderService` 的實際權限、審批流與操作 SOP。
- 是否有完整 before-after audit。
- 是否有 aging order dashboard / query job / reconciliation job。
- 是否有 production incident 或 ticket 證明 Nick 參與 manual repair flow。

## 未掃

- 未逐行掃完整 `app_bi` 全部 GM / payment UI。
- 未掃 DB schema / migration / production index。
- 未掃完整 provider 查單 matrix。
- 未掃 production runbook。
- 未逐 commit diff 展開所有人工審核歷史。

## Step 4 補充

- 任務：`iwin payment manual-order-review-repair Step 4`。
- 日期：2026-05-18。
- 掃描等級：Level 2 Flow 深掃延伸；以 Step 3 code evidence 為基礎，補面試 case、SOP、claim boundary。
- `/Users/nick/Git/iwin/payment` 已重新 fetch：local HEAD / `origin/k3s` 仍為 `bb7794e55386d914801887cc43b53d263c74d3c3`，ahead / behind `0 / 0`。
- `/Users/nick/Git/iwin/app_bi` 已重新 fetch：local HEAD `4a206a28ab8f5be4329602cdc510ee9ea41efb25`，`origin/main` `fd9881fc417e01f960d758b4b91ba1a10b507855`，ahead / behind `0 / 4`；本輪仍不 pull、不 checkout、不改工作樹。
- Step 4 未新增正式履歷 claim；只把 Step 3 evidence 轉成面試可口述 case。

## Step 4 結論

- 本 flow 已完成 Step 4。
- `manual-order-review-repair` 可作 Senior 面試素材，重點是人工介入 money state 的狀態機與補償邊界。
- 目前不更新正式履歷 / 自傳。
- Nick 貢獻仍標 `待確認`；只可說 Nick 已在 payment provider request 共用建單風險有 evidence，不能延伸成 Nick 主導人工審核 / 修單。
- 下一步 Step 5 只做 claim gate，預期仍是不更新正式履歷。

## Step 5 補充

- 任務：`iwin payment manual-order-review-repair Step 5`。
- 日期：2026-05-18。
- 掃描等級：Level 2 Flow 深掃延伸；以 Step 3 / Step 4 code evidence 為基礎，補最後履歷 / 自傳 claim gate。
- 已重讀 KB：`AGENTS.md`、`senior-owner-playbook/00-operating-rules.md`、`senior-owner-playbook/09-ai-prompt-manual.md`、`senior-owner-playbook/03-flow-learning-package-template.md`。
- 已重讀本 flow：`flow.md`、`career-interview.md`、`materials/claim-boundary.md`。
- 已重讀正式素材：`senior-owner-playbook/05-resume-master-zh.md`、`senior-owner-playbook/08-application-autobiography-zh.md`。

### Step 5 source repo 狀態

`/Users/nick/Git/iwin/payment`：

- 已重新執行 `git fetch --all --prune` 更新 remote refs。
- 本機分支：`k3s`
- local HEAD：`bb7794e55386d914801887cc43b53d263c74d3c3`
- `origin/k3s` HEAD：`57306977c0e2f82f39becdf9076ce3e2a73952b8`
- ahead / behind：`0 / 1`
- 工作樹狀態：既有未追蹤 `payment/src/main/java/cn/com/payment/service/impl/.DS_Store`；本輪只讀未動。
- 判斷：本機工作樹落後遠端 1 commit，本輪不 pull、不 checkout、不改公司 repo；Step 5 claim gate 以 `origin/k3s` path history 補判讀，不能宣稱本機 working tree 已是最新 code。

`/Users/nick/Git/iwin/app_bi`：

- 已重新執行 `git fetch --all --prune` 更新 remote refs。
- 本機分支：`main`
- local HEAD：`4a206a28ab8f5be4329602cdc510ee9ea41efb25`
- `origin/main` HEAD：`fd9881fc417e01f960d758b4b91ba1a10b507855`
- ahead / behind：`0 / 4`
- 本輪未 pull、未 checkout、未改工作樹。
- 判斷：app_bi 本機仍落後 4 commits，path history 以 `origin/main` 補判讀。

### Step 5 path history re-check

payment manual-review / repair 相關 path 的 `10gt12nc` history：

- `03c28e3` / `10gt12nc`：`fix: clear copied order id before payment insert`。
- `6539d7a` / `10gt12nc`：`fix: clear copied order id before withdraw insert`。
- `3908da9`、`4a0a261`：merge commits。

app_bi `bill_check` / `repairOrderService` / repair UI 相關 path 的 `10gt12nc` history：

- 未找到直接修改紀錄。

最新 remote refs 相關 path 仍顯示 app_bi repair 主要是 gill / arnold 的歷史，例如 `42feaee`、`d3b33ee`、`36718d4`、`900c7d5`、`57819d9`；payment 人工審核 / repair 相關 path 仍以 Derek / gill / arnold 等 commit 為主。

## Step 5 結論

- `manual-order-review-repair` 已完成 Step 5。
- 不更新 `senior-owner-playbook/05-resume-master-zh.md`。
- 不更新 `senior-owner-playbook/08-application-autobiography-zh.md`。
- 最終證據層級維持：`專案存在 / code-backed`、`分析素材 / learning-only`。
- Nick 個人貢獻：未確認到可放正式履歷的直接 evidence。
- `03c28e3`、`6539d7a` 只能支撐共享建單 / 提款 insert consistency 題材，不能延伸成 Nick 主導人工審核 / 補單 / 修單。

## 下一步

```text
iwin game_api partner-deposit-withdraw-bill Step 4
```
