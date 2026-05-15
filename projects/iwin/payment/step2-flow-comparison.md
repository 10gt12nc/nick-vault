# iwin payment Step 2：候選 Flow 比較

更新時間：2026-05-15
掃描等級：Level 1.5 Flow 比較
狀態：已建立
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 本次結論

建議第一條進 Step 3 / Level 2 深掃：

```text
payment-provider-callback
```

原因：

- 它是 `payment` 專案最有 Senior / Owner 價值的 production flow，直接碰 provider callback、訂單終態、MQ retry、玩家上分 / 退款、人工補償與對帳查詢。
- code evidence 分布集中：`controller/merchant/*Controller`、`BaseServiceImpl#asynUpdateOrderStatus`、`NotifyComsumer#doConsume`、`BaseServiceImpl#updateUserInfo`。
- bug history 線索強：全分支 log 有 provider callback 格式變更、重複回調、withdraw notify consumer retry / refund 相關修正。
- 它可以自然帶出 `withdrawal-auto-review-refund` 和 `manual-order-review-repair` 的邊界，但不會一開始就陷入單一 provider request 細節。

不更新履歷。沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認前，本文件只作 `專案存在 / code-backed` 與 `分析素材 / learning-only`。

## 自動重讀紀錄

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`

已重讀 vault：

- `projects/iwin/payment/README.md`
- `projects/iwin/payment/step1-candidate-flows.md`
- `projects/iwin/app_bi/README.md`
- `projects/iwin/app_bi/step1-candidate-flows.md`
- `projects/iwin/app_bi/step2-flow-comparison.md`
- `projects/iwin/game_api/README.md`
- `projects/iwin/game_api/step1-candidate-flows.md`

已重讀 code repo：

- `/Users/nick/Git/iwin/payment`
- 已執行 `git fetch --all --prune` 更新 remote refs。
- 目前分支：`k3s`
- local HEAD：`e8be8a1466d9cd642e5f63af86f047c6a8d054bd`
- `origin/k3s` HEAD：`e8be8a1466d9cd642e5f63af86f047c6a8d054bd`
- ahead / behind：`0 / 0`
- repo 狀態：仍有未追蹤 `payment/src/main/java/cn/com/payment/service/impl/.DS_Store`，本輪只讀不碰。
- fetch 後看到新 tag `2.8` 指向目前 `k3s` HEAD。

已看主要 code / history：

- `payment/src/main/java/cn/com/payment/controller/PayPublicController.java`
- `payment/src/main/java/cn/com/payment/controller/WithdrawController.java`
- `payment/src/main/java/cn/com/payment/controller/merchant/*Controller.java`
- `payment/src/main/java/cn/com/payment/service/impl/PayTypeServiceImpl.java`
- `payment/src/main/java/cn/com/payment/service/impl/HandlerRouterServiceImpl.java`
- `payment/src/main/java/cn/com/payment/service/impl/AdminGmServiceImpl.java`
- `payment/src/main/java/cn/com/payment/service/impl/withdraw/BaseServiceImpl.java`
- `payment/src/main/java/cn/com/payment/service/impl/withdraw/*ServiceImpl.java`
- `payment/src/main/java/cn/com/payment/comsumer/NotifyComsumer.java`
- `payment/src/main/java/cn/com/payment/comsumer/WithdrawComsumer.java`
- `payment/src/main/java/cn/com/payment/utils/ProducerUtil.java`
- `payment/src/main/java/cn/com/payment/vo/OrderVO.java`
- `base/module/src/main/java/cn/com/enums/OrderReviewStatusEnum.java`
- `base/module/src/main/java/cn/com/enums/PayTypeOrderEnum.java`
- `base/module/src/main/java/cn/com/enums/WithdrawTypeEnum.java`
- 全分支 log grep：`notify`、`callback`、`withdraw`、`NaNapay`、`Pay4z`、`OnePay`、`Kafka`、`Nick` 等線索。

未重讀：

- 未 checkout 每個遠端分支。
- 未逐 commit diff。
- 未讀完整 DB schema / migration / unique key。
- 未掃 `iwin_gameserver` / game lobby / center HTTP handler。
- 未深讀 `admin`、`timer`、`mq`、`xxl-mq-admin` 全部 module。
- 未確認 Nick 本人 MR / ticket / production issue。

## 既有文件狀態判斷

| 文件 | 狀態 | 判斷 |
| --- | --- | --- |
| `projects/iwin/payment/README.md` | 可沿用 / 本輪同步 | 專案定位清楚，本輪更新 Step 2 狀態與下一步 |
| `projects/iwin/payment/step1-candidate-flows.md` | 可沿用 / 需補 remote freshness | Step 1 結構乾淨；本輪 Step 2 補上 fetch 後最新性 |
| `projects/iwin/app_bi/step2-flow-comparison.md` | 可沿用 | 已正確指出 payment repair 應回到 `payment` source of truth |
| `senior-owner-playbook/04-interview-casebook.md` | 可參考 | 已有 callback 一致性通用框架，但不能取代本 repo evidence |

## 掃描等級判斷

本次使用 Level 1.5。

原因：

- Step 2 目標是比較候選 flow，不是建立單條 flow package。
- 需要比 Step 1 多看 module 邊界、主要 code path、branch / remote 最新性與 history 線索。
- 尚未選定 provider / callback 樣本，不值得直接 Level 3。

本次不建議 Level 3：

- 還沒有確認 Nick 本人 evidence。
- provider 數量很多，逐檔逐行會太早陷入商戶細節。
- 更高價值的做法是先選定 `payment-provider-callback`，Step 3 再用 Level 2 追完整資料流與代表 provider。

## Module / Service 邊界

| module / 目錄 | 類型 | Step 2 判斷 |
| --- | --- | --- |
| `payment/` | 核心 runtime service | 主要金流 API、provider callback、MQ consumer、訂單狀態與玩家統計更新；Step 3 主掃 |
| `base/module` | common model / enum / component | 定義 `OrderReviewStatusEnum`、`PayTypeOrderEnum`、`WithdrawTypeEnum`、通用 config / validation；Step 3 必掃 |
| `base/utils` | common utility | 加密、HTTP、Redis、工具類；只掃與簽章 / HTTP / Redis 直接相關部分 |
| `admin/` | 管理後台 service | 本輪只定位，未深讀；人工操作 / 權限可能補 `manual-order-review-repair` 時再看 |
| `timer/` | 排程 service | 本輪只定位；若 Step 3 發現對帳 / retry job，再補掃 |
| `mq/` | 另一組 MQ sample / consumer app | 目前看起來多為已註解 RocketMQ sample 或 health；不作第一條主線 |
| `xxl-mq-admin` | XXL-MQ admin / broker 管理 | 支援 MQ 平台，不直接當 payment business flow |
| `sql-script/` | SQL script | 目前只看到 `xxl-mq.sql` 線索；DB schema / unique key 待補 |
| `payment-thirdparty-simulator` | 另 repo simulator | Step 3 若要驗 callback payload / provider contract，可作輔助，不當 source of truth |
| `app_bi` | 後台 / BI 入口 | 人工查單 / 修單入口；不能單獨代表 payment flow |
| `game lobby / center` | 下游待確認 | `upperDeposit` / `gmDownScore` 的實際錢包 side effect 可能在這裡；Step 3 要標待確認或補掃 |

## 候選 Flow 比較

### 1. `payment-provider-callback`

中文名稱：三方金流 provider callback
建議：第一條進 Step 3
證據層級：專案存在 / code-backed；Nick 貢獻待確認

已確認：

- 多數 provider controller 都有 `pay/notify` 與 `withdraw/notify`。
- callback 通常會 normalize provider status 後呼叫 `asynUpdateOrderStatus`。
- `BaseServiceImpl#asynUpdateOrderStatus` 會把狀態變更包成 `MessageJson`，預設丟 `xxlmq_topic_notify`。
- `NotifyComsumer#doConsume` 解析 MQ message 並呼叫 `updateUserInfo`。
- `BaseServiceImpl#updateUserInfo` 是真正處理訂單狀態、玩家上分 / 提現退款、統計更新、mail / marquee 的核心。
- `OrderReviewStatusEnum` 有 `WAIT`、`PROCESSING`、`SUCCESS`、`ERROR`、`BACK`、`REFUSE`、`PENALTIESERR` 等狀態。

Senior / Owner 價值：

- 最高。callback 是金流系統最常見的 production failure source。
- 可以討論 duplicate callback、終態保護、MQ retry、ack timing、provider unknown status、manual reconciliation。
- 能自然延伸到 order lifecycle、idempotency key、audit log、outbox / inbox、dead letter / 人工補償。

風險與待確認：

- 各 provider 的 callback status mapping 不一致。
- `WAIT` / `PROCESSING` 以外狀態會 early return，但 Step 3 要確認是否所有 provider 都走同一條保護。
- `ERROR` / `BACK` / `REFUSE` 對提現退款語意不同，容易造成錯誤退款或重複退款。
- MQ 生產失敗目前看起來只 log error，是否有 fallback / retry 待確認。
- 下游上分 / 退款 HTTP 是否 idempotent 待確認。

適合 Step 3 的原因：

- evidence 最集中。
- 面試可轉換性最高。
- 可以選 1-2 個代表 provider 深挖，不需要平均掃全部 provider。

### 2. `withdrawal-auto-review-refund`

中文名稱：玩家提款、自動審核 / 自動出款與失敗退款
建議：第二優先；可在 callback Step 3 裡作關聯邊界
證據層級：專案存在 / code-backed；Nick 貢獻待確認

已確認：

- `PayPublicController#/payment/public/withdraw` 建立玩家提现單。
- `PayTypeServiceImpl#withdraw` 做玩家、綁定資料、黑名單、提现設定、待審單、打碼目標、玩家狀態、手續費與 `gmDownScore`。
- `HandlerRouterServiceImpl` 會更新訂單為 `PROCESSING` 並丟 auto withdraw MQ。
- `WithdrawComsumer` 依 `withdrawType` 反射呼叫 provider service 的 `unified*WithdrawOrder`。
- `AdminGmServiceImpl` 有定時查 auto withdraw order、`getAutoMerchant`、`checkAutoWithdraw` 的線索。

Senior / Owner 價值：

- 很高。比 callback 更貼近「玩家按提现」的完整業務流程。
- 能討論扣分、建單、出款、callback 成功 / 失敗退款、免審規則與商戶路由。

風險與待確認：

- 玩家扣分成功但建單失敗或 MQ 失敗的補償待確認。
- `checkAutoWithdraw` 與 `isAutoReview` 的關係需要追定時流程。
- provider request timeout / unknown status 是否停 `PROCESSING` 待確認。
- 會需要補掃下游 game lobby / center，Step 3 範圍較大。

為什麼不是第一條：

- 範圍比 callback 更大，會同時吃掉玩家建單、扣分、MQ、provider request、callback refund。
- 適合在 callback 主線完成後，回來補成第二條完整 flow。

### 3. `payment-order-provider-request`

中文名稱：充值建單與 provider request
建議：第三優先；Step 3 若 Nick 指定某 provider 才適合先做
證據層級：專案存在 / code-backed；Nick 貢獻待確認

已確認：

- `PayPublicController#/payment/gamePay/fastPayment` 與 provider controller `newPay` 形成充值建單 / provider request 入口。
- `PayTypeServiceImpl#createPayOrder` 會建立支付訂單。
- provider service / controller 內有 provider-specific 組參、簽章、HTTP call、查單 endpoint。
- log history 有 NaNapay、Pay4z、OnePay、NimTestPay 等 provider 對接 / bugfix 線索。

Senior / Owner 價值：

- 高。適合講 provider integration、簽章、金額單位、request timeout、provider order id、查單 / 對帳。

風險與待確認：

- 需要先選代表 provider，否則會變成 provider class summary。
- provider request 成功但本地未保存 provider order id 的 failure window 要逐 provider 確認。
- 對帳 / query order 是人工查詢還是 job 待確認。

為什麼不是第一條：

- provider request 的面試價值略低於 callback recovery。
- 沒有 provider 選定前，範圍容易發散。

### 4. `manual-order-review-repair`

中文名稱：人工審核、補單與訂單修復
建議：第四優先；和 `app_bi` repair 合併看
證據層級：專案存在 / code-backed；Nick 貢獻待確認

已確認：

- `PayPublicController#/payment/public/oderView` 進入 `PayTypeServiceImpl#oderView`。
- 充值成功會上分、更新訂單與玩家統計，並非只改 status。
- 提現退回會將 reason / tradeType 改成退款語意並 `upperDeposit`。
- 提現通過會更新訂單 / 統計，並可能發 mail / marquee。
- `gameRecharge` 支援 GM人工充值、線上掉單補單、GM下分等場景。
- `app_bi` 已有 payment order search / repair 入口線索。

Senior / Owner 價值：

- 中高。這是 recovery / operation boundary，很適合問「人工修復能改什麼，不能改什麼」。

風險與待確認：

- `app_bi` 是直接改 DB 還是調 API 待確認。
- 人工修復 audit log 待確認。
- 手動補單與 provider callback 重送如何防衝突待確認。

為什麼不是第一條：

- 它是 failure recovery 的下游，先看 callback 主流程更容易建立狀態機。

### 5. `payment-channel-config-selection`

中文名稱：支付方式 / 商戶 / 提现設定選擇
建議：第五優先；可作 config consistency 補充
證據層級：專案存在 / code-backed；Nick 貢獻待確認

已確認：

- `PayPublicController#/payment/list` 與 `/payment/detail` 取得玩家可用支付方式與商戶。
- `PayTypeServiceImpl#fetchPayTypeList` 會讀玩家層級、支付類型、商戶、快捷充值、VIP 充值。
- `PayTypeServiceImpl#fetchPayTypeDetail` 會依 pay code、device、玩家層級、商戶檔位輸出支付詳情。
- `withdrawConfig` 回傳可用提现設定與帳號遮罩。
- 黑名單使用 Mongo collection：`blackBankList`、`blackZfbList`、`blackPixList`。

Senior / Owner 價值：

- 中。它偏 runtime config / eligibility，不是最核心 money side effect。
- 適合銜接 `app_bi/admin-config-redis-sync`，講 DB -> Redis projection -> runtime consumer。

風險與待確認：

- Redis key 來源與刷新機制待確認。
- 設定更新 propagation delay 待確認。
- 它本身不夠作為第一條高價值金流 case。

## 排序矩陣

| 排名 | Flow | Money correctness | Failure window | Evidence 強度 | Step 3 可控性 | 面試價值 | 下游依賴成本 | 結論 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | `payment-provider-callback` | 高 | 高 | 高 | 中高 | 高 | 中 | 第一條深挖 |
| 2 | `withdrawal-auto-review-refund` | 高 | 高 | 高 | 中 | 高 | 高 | 第二條候選 |
| 3 | `payment-order-provider-request` | 高 | 中高 | 中高 | 中 | 中高 | 中 | 需先選 provider |
| 4 | `manual-order-review-repair` | 高 | 中高 | 中高 | 中 | 中高 | 中 | 適合 callback 後補 |
| 5 | `payment-channel-config-selection` | 中 | 中 | 中高 | 高 | 中 | 中 | 補充型 flow |

## 第一條 Flow 選擇

選 `payment-provider-callback`。

Step 3 建議掃描策略：

- 先建立共通 callback 主線：provider callback -> status normalize -> `asynUpdateOrderStatus` -> notify MQ -> `updateUserInfo` -> order / user / behaviour / mail / marquee / cross-month update。
- 再挑 1-2 個 provider 作代表，不平均掃所有 provider。
- Provider 代表選擇順序建議：
  - 優先：有 Nick branch / bugfix 線索的 provider，例如 NaNapay、Pay4z、NimTestPay、OnePay。
  - 次選：callback 分支完整、同時有 pay / withdraw notify 與 query endpoint 的 provider。
- 必須補看：
  - `BaseServiceImpl#upperDeposit`
  - `BaseServiceImpl#gmDownScore`
  - `BaseServiceImpl#updateOrderStatus`
  - `BaseServiceImpl#updateServiceFiled`
  - `ProducerUtil#addProduce`
  - `NotifyComsumer#doConsume`
  - provider controller 的 callback ack 字串與 status mapping
  - path-specific history / important diff

Step 3 暫不做：

- 不平均整理全部 provider controller。
- 不更新正式履歷。
- 不宣稱 Nick 主導金流 callback。
- 不把下游 game lobby / center 腦補成已確認；沒掃到就標待確認。

## 本次不更新的內容

- 不建立 `flows/payment-provider-callback/`：Step 2 只做比較，Step 3 才建立單條 flow folder。
- 不更新 `senior-owner-playbook/05-resume-master-zh.md` 或 `08-application-autobiography-zh.md`：Nick 本人 evidence 不足。
- 不更新共用 KB：本輪沒有新增通用規則，只套用既有 Step 2 / remote freshness / multi-module 規則。
- 不建立 architecture-map：本輪 module 邊界已放在 Step 2，足夠支撐第一條 flow 選擇；未來如果 payment flow 變多，再考慮補 project-level map。

## 下一步建議

只推薦一件事：做 `payment-provider-callback` Step 3。

為什麼現在做它：

- Step 2 已完成候選比較，callback 是最值得先做的金流主線。
- Step 3 會開始建立單條 flow 學習包，把 callback 的正常流程、狀態轉移、MQ retry、failure window、idempotency 與面試邊界寫清楚。

會產出什麼：

- `projects/iwin/payment/flows/payment-provider-callback/flow.md`
- `projects/iwin/payment/flows/payment-provider-callback/career-interview.md`
- `projects/iwin/payment/flows/payment-provider-callback/materials/evidence.md`
- `projects/iwin/payment/flows/payment-provider-callback/materials/decision-notes.md`
- `projects/iwin/payment/flows/payment-provider-callback/materials/interview.md`
- `projects/iwin/payment/flows/payment-provider-callback/materials/claim-boundary.md`

是否更新履歷：

- 不更新正式履歷。
- 只會產出 flow-level 保守面試 / 履歷候選素材，並標 `專案存在 / code-backed`、`分析素材 / learning-only` 或 `待確認`。

是否需要 commit / push：

- 本輪 Step 2 完成後自動 commit。
- 不需要 push，除非 Nick 明確要求。

```text
iwin payment payment-provider-callback Step 3
```
