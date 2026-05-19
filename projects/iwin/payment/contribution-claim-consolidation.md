# iwin payment contribution claim consolidation

日期：2026-05-19
掃描等級：Level 3 取向 contribution claim 掃描
狀態：已完成
證據層級：`本人確認 + 真實開發過 + code-backed` 混合

## 結論

Nick 的 `iwin/payment` 經驗不能再只寫成「分析過」或「待確認」。

本文件是履歷 claim consolidation，不是 payment 全專案 completion。它不代表全部 provider、全部金流 flow、transfer wallet、MQ / reconciliation 或所有 production incident 都已整理完；它只代表目前已有足夠 evidence 可以保守界定 Nick 在 `payment` 的可寫履歷範圍。

本輪已把 Nick 本人確認、`10gt12nc` author / committer、Nick branch、重要 provider diff 與既有 payment Top 5 flow evidence 合併。可保守升級為：

- 可放履歷：`真實開發過`。Nick 參與 iwin payment 多個第三方金流 provider 對接、維護與金流訂單一致性修正。
- 可面試講：`code-backed / 分析過`。五條 payment flow 都可作 Senior Backend / Platform Backend 面試素材。
- 不可誇大：不是主導完整 `payment`、不是全權金流 owner、不是設計完整 wallet / reconciliation 架構。

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
- `projects/iwin/payment/flows/*/flow.md`
- `projects/iwin/payment/flows/*/career-interview.md`
- `projects/iwin/payment/flows/*/materials/evidence.md`
- `projects/iwin/payment/flows/*/materials/claim-boundary.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

## source repo 狀態

### `/Users/nick/Git/iwin/payment`

- 已執行 `git fetch --all --prune` 更新 remote refs。
- 目前分支：`k3s`
- local HEAD：`bb7794e55386d914801887cc43b53d263c74d3c3`
- `origin/k3s` HEAD：`cd03174b60fefa0c7f312912c179a3b6b9818664`
- ahead / behind：`0 / 2`
- 本機工作樹：有既有未追蹤 `payment/src/main/java/cn/com/payment/service/impl/.DS_Store`。
- 本輪只讀 source repo；未 pull、未 checkout、未 merge、未 rebase、未改公司 repo。

判斷：

- 本機工作樹落後 `origin/k3s` 2 commits，落後內容是 k3s deploy / manifest 類修正，不影響本輪對 Nick payment provider / order consistency contribution 的判斷。
- 本輪 claim 以 fetch 後的全 remote refs、全分支 log 與重要 diff 為準。

## 掃描範圍

已掃：

- `git log --all --author='10gt12nc'`
- payment runtime path-specific history：
  - `payment/src/main/java/cn/com/payment/controller/merchant`
  - `payment/src/main/java/cn/com/payment/service/impl/withdraw`
  - `payment/src/main/java/cn/com/payment/service/impl/AdminGmServiceImpl.java`
  - `payment/src/main/java/cn/com/payment/service/impl/PayTypeServiceImpl.java`
  - `payment/src/main/java/cn/com/payment/service/impl/withdraw/BaseServiceImpl.java`
  - `payment/src/main/java/cn/com/payment/utils`
  - `payment/src/test/java/cn/com/payment`
- 遠端分支：
  - `origin/pay4z-Nick`
  - `origin/NaNapay_Nick`
  - `origin/Nick-02-mdTest`
  - `origin/Nick-03`
  - `origin/Nick-work-item-201-test`
  - `origin/feature/nimtestpay-dev`
  - `origin/Kafka-Nick`
  - `origin/k3s`
  - `origin/main`

未掃 / 不宣稱：

- 未逐檔逐行掃所有 provider controller。
- 未掃完整 DB schema / migration / production unique key。
- 未掃完整 timer / reconciliation job。
- 未掃 game lobby / center wallet 去重實作。
- 未掃 ticket 系統與 production incident 系統。
- 未把 source repo 本機工作樹更新到最新 `origin/k3s`。

## 全量 contribution evidence 摘要

`git log --all --author='10gt12nc' -- payment/src/main/java/cn/com/payment` 顯示至少 67 筆 payment runtime 相關 commit 線索。以下只列能支撐履歷 / 面試邊界的重要代表，不平均列 class summary。

| 類型 | Evidence | 判斷 |
| --- | --- | --- |
| Provider 對接 | `f48ec45` / `f7a3bad`：Owenpay 代付串接與後續 log 清理 | 真實開發過，可作早期 provider withdraw integration evidence |
| Provider 對接 | `1147605`、`d6d6db6`、`30c4a31`、`1adacc5`、revert commits：HamBit provider 對接與 callback / internal sign 修正 | 真實開發過，可講 provider callback 格式與 sign 風險 |
| Provider 對接 | `6a0c6e6`：Wwwpago controller / withdraw service | 真實開發過，可作 provider integration evidence |
| Provider 對接 | `ad5eb30`、`79a5acf`、`f649f27`、`260e550`、`ce953f8`、`f8f24ee`、`b07a93e`、`82ce119`：BFPAY request / query / callback / withdraw service | 真實開發過，可講 provider request / callback / query 維護 |
| Provider 對接 | `7853917` / `702cc73`、`2e1b25e`、`4df2c22`、`1385c27`、`ec85d21`：Pay4z 對接與後續欄位調整 | 真實開發過，是最強履歷 evidence 之一 |
| Provider 對接 | `65eebbb` / `7ae7f11`、`b41806d`、`8342090`、`d4f797f`、`a5ae013`、`f251c1c`、`c92ff2b`：NaNapay 對接與後續修正 | 真實開發過，是最強履歷 evidence 之一 |
| Callback / sign bugfix | `9aa0477`、`884230a`、`3277b78`、`6c15b21`：Pay4z / NaNapay callback、sign、ack / retry 相關修正 | 真實維護過，可講 callback trust boundary，但不說設計完整 callback 架構 |
| Provider bugfix | `0a37844`、`be147f7`、`e2b54de`：SitoBank sign / transMsg 修正 | 真實維護過，可作 provider sign bugfix evidence |
| Provider bugfix | `989dd8d`、`4113eea`、`46b906c`、`75bb869`：OnePay / Pay4z response parse、DB 欄位大小修正 | 真實維護過，可講 provider response 格式變更 |
| Provider / user input | `58b963b`、`ae57125`：HKP / SitoBank CPF / name 欄位需求修正 | 真實維護過，可講 provider contract / field requirement |
| Test / sign support | `ff1ee40`：AOApay sign verification util / test | 真實測試支援，可講 sign verification，不包裝成完整 production provider owner |
| Local / SIT tooling | `7403277`、`2fc3e63`、`c3310b3`、`234d9e5`、`173074a`、`d804ee8`、`e4b1dcc`、`bb7794e`：NimTestPay local / SIT manual testing controller、mock pay-failure / withdraw flow、provider order storage、k3s profile | 真實測試支援 / SIT tooling；不可寫成正式 production provider 上線成果 |
| Order consistency | `03c28e3`：清除 `BaseServiceImpl#createOrderNo` 複製出的 id 並補 unit test | 真實修復過，可講 payment insert id collision / money order consistency |
| Withdraw consistency | `6539d7a`：清除 withdraw insert 複製出的 id | 真實修復過，可講 withdraw order insert consistency |
| Auto withdraw safety | `8a01070`、`a70a5c3`：auto withdraw switch null-safe | 真實維護過，但只可說 null-safety / defensive fix，不升級成自動出款 owner |
| MQ / observability support | `aa55d61`、`3b6c19a`、`97a0d5b`：Kafka test / consumer sample 線索；`b141529`：ProducerUtil log | 可面試輔助，不當主履歷 bullet |

## 可放履歷：真實開發過

可以保守寫：

- 參與 iwin payment 第三方金流 provider 對接與維護，包含 Owenpay、HamBit、Wwwpago、BFPAY、Pay4z、NaNapay 等 provider 的 request / callback / query / withdraw service 新增或修正。
- 維護 provider callback / sign / response parsing 相關問題，例如 Pay4z、NaNapay、SitoBank、OnePay 類 provider 的驗簽、callback ack、欄位格式、provider response 變更。
- 處理 payment order / withdraw order 建單一致性問題，例如 `createOrderNo` 與 withdraw insert 的 copied id collision 風險修正。
- 建立或維護 local / SIT payment 測試輔助，例如 NimTestPay manual testing controller、mock callback / withdraw flow、AOApay sign verification test。

履歷建議口徑：

```text
參與 iwin payment 第三方金流 provider 對接與維護，包含多個 provider 的 request / callback / query / withdraw flow，處理簽章驗證、金額單位、merchant order id、callback ack、查單與 provider response 異常；並修正 payment / withdraw order 建單 id collision 等資料一致性問題。
```

更短版本：

```text
參與第三方金流 provider 對接與維護，處理 provider request / callback / query、簽章驗證、訂單狀態與 payment order consistency bugfix。
```

## 可面試講：code-backed / 分析過

可以講，但要清楚標成「我參與過部分 provider，並深度分析過整體 flow」：

- `payment-provider-callback`：provider callback、狀態 mapping、ack、XXL-MQ retry、玩家上分 / 提現退款、副作用與終態 guard。
- `withdrawal-auto-review-refund`：玩家提款、扣分、自動審核 / 自動出款、provider 代付、失敗退款與人工 fallback。
- `payment-order-provider-request`：建單、`billNo` / merchant order id、簽章、金額單位、request timeout、provider accepted 不等於付款成功、查單 / 對帳邊界。
- `manual-order-review-repair`：人工審核、補單、狀態修復、直接修 DB 狀態的風險與 break-glass repair boundary。
- `payment-channel-config-selection`：支付方式 / 商戶 / 玩家層級 / 提現設定選擇，DB -> Redis projection、冷 cache、部分同步與 fail closed 風險。
- Provider integration owner decision：raw request / response log、callback inbox、provider adapter、timeout unknown、reconciliation dashboard、manual repair SOP。

面試講法邊界：

- 可以說「我實際參與多個 provider 對接 / 維護，也把 payment Top 5 flow 梳理成可追蹤的 production flow」。
- 不要說「我設計了整個 payment 架構」。
- 不要把分析過的 `withdrawal-auto-review-refund` / `manual-order-review-repair` / `payment-channel-config-selection` 都包成 Nick 親自開發。

## 不可誇大

不得寫：

- Nick 主導 `iwin/payment`。
- Nick 是完整金流 owner。
- Nick 對接全部 payment provider。
- Nick 設計完整 provider gateway / callback framework。
- Nick 建立完整 reconciliation / 對帳平台。
- Nick 保證所有 provider idempotent 或 wallet exactly-once。
- Nick 解決所有 payment production incident。
- Nick 改善成功率 / 降低事故率 X%，除非後續補 production metric。
- NimTestPay 是正式 production provider 上線成果。

## 各 flow claim 更新判斷

| Flow | 原本 Step 5 | consolidation 後判斷 |
| --- | --- | --- |
| `payment-provider-callback` | 單條 flow 不更新正式履歷 | 仍不單獨寫成 callback owner；但可併入「實際參與多 provider callback / sign / ack 維護」 |
| `withdrawal-auto-review-refund` | 不更新正式履歷 | 仍不寫成自動出款 owner；但 `6539d7a`、`8a01070`、`a70a5c3` 可支撐 withdraw insert consistency / null-safety 維護 |
| `payment-order-provider-request` | 可升級部分 provider request 真實開發過 | 升級為 payment project 主力履歷 evidence；仍只用「參與 / 維護」 |
| `manual-order-review-repair` | 不更新正式履歷 | 不寫人工審核 / 補單 owner；只作面試素材，另可提 order consistency bugfix 與 repair boundary 分析 |
| `payment-channel-config-selection` | 不更新正式履歷 | 不寫支付設定 owner；只作 runtime config consistency 面試素材 |

## 履歷 / 自傳更新結論

本輪應更新：

- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`
- `projects/iwin/payment/README.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`

不應新增：

- 新的流水帳 / operation log。
- 新的 flow Step。
- 公司 repo 文件。

## 下一步建議

只推薦一件事：

```text
iwin iwin_gameserver contribution claim consolidation
```

原因：

- payment project-level contribution consolidation 已先保守收斂，但不是 payment 全專案完成。
- 目前總 queue 已移到 `iwin_gameserver`，因為 gameserver 已有 code-backed money flow，但尚未做 project-level contribution claim consolidation。
- 會產出 gameserver 的「可放履歷 / 可面試講 / 不可誇大」三層判斷。
