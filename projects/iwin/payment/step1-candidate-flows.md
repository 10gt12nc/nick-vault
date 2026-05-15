# iwin payment Step 1：候選 Flow 盤點

更新時間：2026-05-15
掃描等級：Level 1 Flow 掃描
狀態：已建立
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 本次結論

`payment` 是 iwin 金流 / 充值 / 提現核心後端，比 `app_bi` 更值得深挖。`app_bi` 只能看到人工查單 / 修單入口；真正的 provider request、callback、MQ 消費、玩家上下分與訂單狀態轉移在本 repo。

目前最值得延伸的候選 flow：

1. `payment-provider-callback`：三方充值 / 提現 callback，面試價值最高，直接涉及重複 callback、終態保護、MQ retry、玩家上下分與退款。
2. `withdrawal-auto-review-refund`：玩家提款建單後的自動出款 / 失敗退款，money correctness、狀態轉移與 failure window 最密集。
3. `payment-order-provider-request`：玩家充值建單與 provider request，涉及商戶路由、簽章、外部 API timeout、訂單與 provider order id。
4. `manual-order-review-repair`：人工審核 / 補單 / 退回，銜接 `app_bi` 的 payment repair 線索，適合做 owner decision 與補償邊界。
5. `payment-channel-config-selection`：支付列表 / 支付詳情 / 提現設定選擇，涉及 Redis projection、玩家分層、商戶配置與 runtime 設定一致性。

不更新履歷。沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認前，所有候選 flow 都只當 `專案存在 / code-backed` 或 `分析素材 / learning-only`。

## 自動重讀紀錄

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/CONVENTIONS.md`
- `projects/source-repo-inventory.md`

已重讀 vault：

- `projects/README.md`
- `projects/iwin/app_bi/README.md`
- `projects/iwin/app_bi/step1-candidate-flows.md`
- `projects/iwin/app_bi/step2-flow-comparison.md`
- `projects/iwin/game_api/README.md`
- `projects/iwin/game_api/step1-candidate-flows.md`
- 確認 `projects/iwin/payment/` 原本不存在，這輪新建 README 與 Step 1。

已做重複 flow 檢查：

- `projects/**/flows/`：尚無 `payment` flow folder。
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`：已有 `payment-provider-callback`、`payment-order-provider-request`、`withdrawal-auto-review-refund` 候選索引。
- `senior-owner-playbook/04-interview-casebook.md`：已有金流 callback 一致性的通用面試框架。
- `projects/iwin/app_bi/step2-flow-comparison.md`：已指出 `payment-order-status-repair` 不適合只在 `app_bi` 深挖，需回到 `payment` repo。

已重讀 code repo：

- `/Users/nick/Git/iwin/payment`
- 目前分支：`k3s`
- repo 狀態：`payment/src/main/java/cn/com/payment/service/impl/.DS_Store` 未追蹤；本輪只讀不碰。
- 遠端分支清單：已看到 `origin/k3s`、`origin/main`、`origin/feature/nimtestpay-dev`、`origin/NaNapay_Nick`、`origin/pay4z-Nick`、`origin/Kafka-Nick`、`origin/bugs/notify_comsumer_error` 等線索。
- 近期 log：已看到 k3s / Spring Boot 3 / Java 21 升級、NimTestPay、auto withdraw null guard、order id copy 清理、安全憑證外部化與 log key mask 等近期線索。
- 全分支 grep log：已看到 NaNapay、Pay4z、OnePay、cashpay、withdraw notify consumer、auto withdraw、provider callback bugfix 等候選歷史。

已看主要 code path：

- `payment/src/main/java/cn/com/payment/controller/PayPublicController.java`
- `payment/src/main/java/cn/com/payment/controller/WithdrawController.java`
- `payment/src/main/java/cn/com/payment/controller/merchant/*Controller.java`
- `payment/src/main/java/cn/com/payment/service/impl/PayTypeServiceImpl.java`
- `payment/src/main/java/cn/com/payment/service/impl/HandlerRouterServiceImpl.java`
- `payment/src/main/java/cn/com/payment/service/impl/withdraw/BaseServiceImpl.java`
- `payment/src/main/java/cn/com/payment/service/impl/withdraw/*ServiceImpl.java`
- `payment/src/main/java/cn/com/payment/comsumer/NotifyComsumer.java`
- `payment/src/main/java/cn/com/payment/comsumer/WithdrawComsumer.java`
- `payment/src/main/java/cn/com/payment/utils/ProducerUtil.java`
- `payment/src/main/java/cn/com/payment/vo/OrderVO.java`
- `base/module/src/main/java/cn/com/enums/*`
- `payment/src/main/java/cn/com/payment/mongo/entity/*`

未重讀：

- 未 checkout 每個遠端分支。
- 未逐 commit diff。
- 未深讀 `admin`、`timer`、`mq`、`xxl-mq-admin` 全部 module。
- 未掃 `iwin_gameserver` / game lobby / center HTTP handler。
- 未讀 DB migration / schema / unique key。
- 未確認 Nick 個人 MR / ticket / production issue。

## 舊文件狀態判斷

| 文件 | 狀態 | 判斷 |
| --- | --- | --- |
| `projects/iwin/payment/README.md` | 本輪新建 | 專案入口，保守標註履歷邊界 |
| `projects/iwin/payment/step1-candidate-flows.md` | 本輪新建 | Level 1 candidate flow 盤點 |
| `projects/iwin/app_bi/step2-flow-comparison.md` | 可沿用 / 需接 payment | 已正確標出 payment repair 不能只在 `app_bi` 深挖 |
| workspace 舊 payment 文件 | 可參考 / 不搬運 | 有舊 KB 與專案文件，但可能含環境資訊與敏感配置，不能直接複製進 vault |

## 掃描等級判斷

本次使用 Level 1。

原因：

- Nick 只指定 `iwin payment`，目前 project 尚未在 vault 建立。
- Step 1 目標是找 Top 3-5 candidate flows，不是深挖單一 flow。
- `payment` provider controller / service 很多，直接 Level 2 容易被單一商戶綁住，反而看不到共通 flow。
- Level 3 目前不值得，因為尚未選定 flow，也未確認 Nick 本人 evidence。

本次做：

- 建立 `payment` 專案入口。
- 找出 Top 5 production flow 候選。
- 標清楚已確認、推測、待確認。
- 標清楚不可更新正式履歷。

本次不做：

- 不建立單條 flow folder。
- 不逐檔逐行掃完整 repo。
- 不逐 commit diff。
- 不更新履歷 / 自傳。
- 不把 `payment` 包裝成 Nick 主開發成果。

## 本次掃描範圍

已看 repo：

- `/Users/nick/Git/iwin/payment`
- `/Users/nick/Git/iwin/iwin-workspace` 的 payment 路徑索引與文件位置
- `/Users/nick/Git/nick/nick-vault`

source repo 狀態：

- `/Users/nick/Git/iwin/payment`
- 目前分支：`k3s`
- 未追蹤檔：`.DS_Store`，本輪未修改。

已看分支：

- 本地：`k3s`
- 遠端清單包含：`origin/k3s`、`origin/main`、`origin/feature/nimtestpay-dev`、`origin/NaNapay_Nick`、`origin/pay4z-Nick`、`origin/Kafka-Nick`、`origin/bugs/notify_comsumer_error`、`origin/beta`。

已看 git log：

- 近期主線 log，包含 k3s migration、NimTestPay、auto withdraw guard、憑證外部化與 log mask。
- 全分支 grep log，包含 NaNapay、Pay4z、OnePay、cashpay、withdraw notify consumer、auto withdraw、provider callback bugfix。

已看 code path：

- player API：`PayPublicController`
- withdraw router：`WithdrawController`、`HandlerRouterServiceImpl`
- provider callback / provider request：`controller/merchant/*Controller`、`service/impl/withdraw/*ServiceImpl`
- async side effect：`NotifyComsumer`、`WithdrawComsumer`、`ProducerUtil`
- shared state update：`BaseServiceImpl`
- order model / enums：`OrderVO`、`OrderReviewStatusEnum`、`PayTypeOrderEnum`、`TradeTypeEnum`、`PaymentTypeEnum`、`WithdrawTypeEnum`

## 專案定位

已確認：

- `payment` 是 Java / Spring Boot 金流專案。
- 玩家端可查支付列表 / 詳情、查充值與提现紀錄、綁定支付資料、快捷充值、提交提现、代理充值、遊戲上下分與人工審核。
- Provider controller 大量提供 `newPay`、`pay/notify`、`withdraw/notify`、`pay/getOrderStatus`、`withdraw/getOrderStatus` 類入口。
- 非同步 side effect 使用 XXL-MQ：`ProducerUtil` 發送 message，`NotifyComsumer` 處理充值 / 提現狀態更新，`WithdrawComsumer` 處理自動出款 provider request。
- `payment_order` 是本 repo 內的核心訂單狀態表，`log_user` / `user_behaviour` 承接玩家累計充值 / 提現統計。

推測：

- 真正玩家錢包餘額 source of truth 可能在 game lobby / center，不完全在 `payment`。
- Provider callback flow 的核心價值不是單一 controller，而是 callback normalize -> status update -> MQ -> `updateUserInfo` -> 玩家上下分 / refund / mail / marquee 的跨資源一致性。
- 自動出款 flow 與人工審核 flow 會共享 `payment_order` 狀態，因此 Step 2 需要比較哪一條最值得先深挖。

待確認：

- DB schema / unique key / index 是否能支撐 idempotency。
- Callback raw log、provider request log 是否完整。
- MQ retry 失敗後如何補償或人工排查。
- Downstream game lobby / center API 的扣分 / 加分是否具備冪等。
- Nick 實際參與與可用履歷 evidence。

## Top Candidate Flows

### 1. `payment-provider-callback`

中文名稱：三方金流 provider callback
證據層級：專案存在 / code-backed；Nick 貢獻待確認
建議：第一優先候選，Step 2 後若 evidence 強，建議第一條 Level 2 深挖

為什麼重要：

- 直接碰充值成功、提現成功 / 失敗、退款、玩家上下分與訂單終態。
- Senior 面試最容易追問：重複 callback、timeout、簽章、終態保護、MQ retry、ack 時機、人工補償。
- repo history 有 callback bugfix 線索，例如 provider 回調內容變更、短時間重複回調造成重複退款、withdraw notify consumer 失敗處理。

production 風險：

- provider 重送 callback，若沒有 idempotency / 終態保護，可能重複上分或重複退款。
- callback 更新訂單成功但 MQ 消費失敗，會造成訂單與玩家餘額 / 統計不一致。
- MQ retry 進入退款流程時，如果 exception handling 不乾淨，可能造成「已退款又 retry」。
- callback 欄位、簽章、錯誤碼格式變更，可能導致誤判成功 / 失敗。

已確認 evidence：

- `controller/merchant/*Controller` 大量存在 `pay/notify`、`withdraw/notify`。
- `BaseServiceImpl#asynUpdateOrderStatus` 會把 orderNo、billType、status、outBillNo 包成 message 丟到 notify topic。
- `NotifyComsumer#doConsume` 解析 message 後呼叫 `updateUserInfo`。
- `BaseServiceImpl#updateUserInfo` 會依充值 / 提現分支更新訂單、玩家統計、上分 / 退款、mail、marquee，且會檢查訂單狀態是否仍為 `WAIT` / `PROCESSING`。
- git log 有 `withdraw_notify_consumer`、`pay4z`、`onepay`、`NaNapay` callback 相關 bugfix。

推測：

- 各 provider controller 的簽章與欄位正規化邏輯差異很大，Step 3 應選 1-2 個高 evidence provider 作代表，不平均整理全部 provider。
- Callback raw request log / provider request log 可能不完整，需要確認。

待確認：

- 哪些 provider 是 Nick 實際參與。
- DB unique / update condition 是否能完整防 duplicate side effect。
- `BaseServiceImpl#updateUserInfo` 的終態判斷是否覆蓋所有 status。
- provider callback ack 字串與 retry 觸發條件。
- 下游上分 / 退款 API 是否 idempotent。

履歷邊界：

- 潛力最高，但目前不可寫進正式履歷。
- 可先當 `專案存在 / code-backed` 的金流 callback 分析素材。

### 2. `withdrawal-auto-review-refund`

中文名稱：玩家提款建單、自動出款與失敗退款
證據層級：專案存在 / code-backed；Nick 貢獻待確認
建議：高價值候選，和 callback 並列比較

為什麼重要：

- 從玩家提交提现到自動出款、provider request、callback 成功 / 失敗退款，完整碰到 money correctness。
- 涉及玩家綁定資料、黑名單、提现配置、打碼目標、玩家是否已有待審單、扣分、訂單建單、auto merchant selection、MQ 出款。
- 有自動免審 / 自動出款規則，Owner decision 題很多。

production 風險：

- 建立提现單前後若扣分 / 建單 / MQ 任一段失敗，會有玩家餘額與訂單狀態不一致風險。
- 自動出款 MQ 消費失敗或 provider timeout，訂單可能停在 `PROCESSING`。
- 出款失敗退款若 retry，不可重複退回玩家餘額。
- 自動商戶選擇若讀 Redis / 設定不一致，可能導致錯商戶或錯渠道。

已確認 evidence：

- `PayPublicController#/payment/public/withdraw` 呼叫 `PayTypeServiceImpl#withdraw`。
- `PayTypeServiceImpl#withdraw` 檢查玩家、綁定資料、黑名單、提现設定、待審單、打碼目標、玩家狀態、手續費、建單與 `gmDownScore`。
- `BaseServiceImpl#getAutoMerchant` 依自動商戶金額 / 時段設定篩選。
- `WithdrawController#/withdraw/{bank|zfb|pix}/{merchantName}` 走 `HandlerRouterServiceImpl`。
- `HandlerRouterServiceImpl` 更新訂單為 `PROCESSING`，再用 `ProducerUtil` 丟 auto withdraw topic。
- `WithdrawComsumer` 依 withdrawType 反射呼叫對應 provider service 的 unified withdraw method。

推測：

- 玩家扣分 / 加分最終落點在 game lobby / center，需補掃下游。
- 自動出款規則可能與 `app_bi` 營運設定 / Redis sync 相關。

待確認：

- `gmDownScore` failure / timeout 的語意。
- auto withdraw topic 的 retry / dead letter / 人工補償流程。
- `payment_order` 在扣分成功但 insert 失敗時是否有保護。
- `checkAutoWithdraw` 是否實際被呼叫，以及自動免審和自動出款的關係。

履歷邊界：

- 很適合做 Senior / Owner 面試 case，但目前不可寫成 Nick 主導自動出款。

### 3. `payment-order-provider-request`

中文名稱：充值建單與 provider request
證據層級：專案存在 / code-backed；Nick 貢獻待確認
建議：高價值候選，但需 Step 2 選代表 provider

為什麼重要：

- 充值建單後會對外部 provider 發起 request，回來再等 callback 完成上分。
- 涉及商戶選擇、金額單位、簽章、provider order id、timeout 與查單。
- repo history 有多個 provider 對接與修正，例如 NaNapay、Pay4z、OnePay、NimTestPay。

production 風險：

- provider request 成功但本地訂單 / provider order id 未保存，後續 callback 或查單對不上。
- request timeout 時 provider 可能已建立訂單，本地若直接失敗可能造成 orphan provider order。
- 金額單位元 / 分 / 乘倍率轉換不一致，會造成錯帳。
- 商戶配置、簽章 key、notifyUrl 來自設定，若 runtime 設定不一致，錯誤會直接影響支付。

已確認 evidence：

- `PayPublicController#/payment/gamePay/fastPayment` 會建立快捷充值訂單。
- `PayTypeServiceImpl#createPayOrder` 會依 merchant / pay code 建立支付訂單。
- `controller/merchant/*Controller#newPay` 形狀大量存在。
- `service/impl/withdraw/*ServiceImpl` 內有 provider-specific request 組參、簽章與 HTTP call。
- `OrderVO` 包含 `billNo`、`outBillNo`、`merchantId`、`merchantName`、`money`、`status`、`billType`、`tradeType`。

推測：

- 充值 request 可能由不同 provider controller 直接處理，而非完全共用 service。
- provider query order endpoint 可作 reconciliation 輔助，但需確認是否有定期 job 或只提供手動查詢。

待確認：

- 選哪個 provider 當代表最有 Nick evidence。
- request log / callback log 是否可串 trace id。
- timeout / provider unknown status 是否有對帳 job。

履歷邊界：

- 適合作為 provider integration 面試素材，但目前不可寫「主導三方金流串接」。

### 4. `manual-order-review-repair`

中文名稱：人工審核、補單與訂單修復
證據層級：專案存在 / code-backed；Nick 貢獻待確認
建議：可作第二階段候選，銜接 `app_bi` repair 線索

為什麼重要：

- Production 金流不可能只靠自動流程，人工審核 / 補單 / 退回是失敗恢復能力的一部分。
- 和 `app_bi` 的 `payment-order-status-repair` 呼應，能把後台入口和後端狀態機接起來。
- 面試可講 owner boundary：哪些狀態能人工改、哪些需要副作用、哪些只能標異常待查。

production 風險：

- 人工把訂單改成功但沒做上分 / 統計更新，造成狀態與玩家餘額不一致。
- 人工退回提现但重複退款，造成玩家多拿錢。
- 補單如果不追原 provider callback / request log，可能修錯單。

已確認 evidence：

- `PayPublicController#/payment/public/oderView` 呼叫 `PayTypeServiceImpl#oderView`。
- `oderView` 依充值 / 提現與審核狀態分支處理成功、退回、拒絕與通知。
- `gameRecharge` 支援銀商代理充值、GM人工充值、線上掉單補單、GM下分等場景。
- `BaseServiceImpl#kyOrder` 會處理跨月訂單修改。
- `app_bi` Step 1 / Step 2 已看到 payment order search / repair 與跨月查單線索。

推測：

- `app_bi` 可能是人工操作入口，`payment` 是 side effect / state transition 核心；需 Step 2 串接確認。

待確認：

- `app_bi` 呼叫 `payment` API 還是直接改 DB。
- 人工修復是否有 audit log。
- 人工補單與 provider callback 重送如何避免衝突。

履歷邊界：

- 可當 owner decision / recovery case，但目前不可寫成 Nick 負責金流補償平台。

### 5. `payment-channel-config-selection`

中文名稱：支付方式 / 商戶 / 提現設定選擇
證據層級：專案存在 / code-backed；Nick 貢獻待確認
建議：中高價值候選，適合和 `app_bi` Redis sync 合併理解

為什麼重要：

- 玩家看到哪些支付方式、商戶、檔位、提現方式，來自 Redis / DB 設定與玩家分層。
- 設定錯誤會直接造成玩家不能充值 / 提现或選到錯商戶。
- 和 `app_bi/admin-config-redis-sync` 有自然上下游關係。

production 風險：

- Redis projection 與 DB 不一致，runtime 看到舊商戶 / 舊提款設定。
- 玩家層級、渠道、device、VIP / merchant 配置交叉條件複雜，容易出現邊界錯誤。
- 黑名單 / 綁定資料 / 提现配置若不同步，可能造成風控失效。

已確認 evidence：

- `PayPublicController#/payment/list` 與 `/payment/detail`。
- `PayTypeServiceImpl#fetchPayTypeList` 讀玩家層級、支付類型、商戶、快捷充值、VIP 充值。
- `PayTypeServiceImpl#fetchPayTypeDetail` 依 pay code、device、玩家層級、商戶檔位產出支付詳情。
- `PayTypeServiceImpl#withdrawConfig` 回傳玩家可用提现設定與綁定遮罩。
- Mongo blacklist collection 包含 `blackBankList`、`blackZfbList`、`blackPixList`。

推測：

- 此 flow 本身 side effect 較少，但對 payment runtime correctness 很重要。
- 更適合作為 `app_bi` Redis sync 的 runtime consumer 補充，不一定是第一條 flow。

待確認：

- Redis key 來源與刷新機制。
- payment runtime 是否有 local cache。
- 設定變更後是否有 reload / propagation delay。

履歷邊界：

- 可作為設定一致性與 runtime projection 的分析素材，不宜單獨寫成主要履歷 bullet。

## 初步排序

| 排名 | Flow | Senior / Owner 價值 | Evidence 強度 | Nick evidence 可能性 | 建議 |
| --- | --- | --- | --- | --- | --- |
| 1 | `payment-provider-callback` | 高 | 高 | 待確認 | Step 2 優先比較，可能第一條深挖 |
| 2 | `withdrawal-auto-review-refund` | 高 | 高 | 待確認 | 和 callback 並列候選 |
| 3 | `payment-order-provider-request` | 高 | 中高 | 中高待確認 | 需選代表 provider |
| 4 | `manual-order-review-repair` | 中高 | 中高 | 待確認 | 銜接 `app_bi` repair |
| 5 | `payment-channel-config-selection` | 中 | 中高 | 待確認 | 可補 runtime config consumer |

## 不建議先做的方向

- 不要平均整理每個 provider controller；provider 太多，應選共通 callback / request flow，再挑有 evidence 的 provider 當代表。
- 不要先做 class summary；`payment` 的價值在狀態轉移和失敗恢復，不在 class 清單。
- 不要先更新履歷；Step 1 只有 code-backed project evidence，沒有 Nick 本人 evidence。
- 不要只接 `app_bi` repair；人工入口需要回到 payment order state machine 和副作用邊界。

## 下一步建議

只推薦一件事：做 `iwin payment Step 2`。

為什麼現在做它：

- `payment-provider-callback` 與 `withdrawal-auto-review-refund` 都很強，Step 2 需要比較哪條先做，才不會 Level 2 深掃時選錯 provider 或太快陷入單一商戶細節。

會產出什麼：

- `projects/iwin/payment/step2-flow-comparison.md`
- 明確選定第一條要進 Step 3 / Level 2 的 flow。
- 補清楚是否需要同步讀 `app_bi`、`game_api` 或 game lobby / center 下游。

是否更新履歷：

- 不更新。至少等單條 flow Step 4 / Step 5，且有 Nick 本人 evidence 或本人確認後才考慮。

是否需要 commit / push：

- Step 2 完成後依規則自動 commit。
- 不需要 push，除非 Nick 明確要求。

```text
iwin payment Step 2
```
