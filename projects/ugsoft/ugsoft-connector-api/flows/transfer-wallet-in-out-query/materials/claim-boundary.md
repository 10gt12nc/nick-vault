# Claim Boundary - transfer-wallet-in-out-query

## Step 4 判定

Step 5 已完成 claim gate。此時可把本 flow 當作 `ugsoft-connector-api` provider connector / transfer wallet / transaction lookup 的 project-level 強化 evidence，但不代表整個 project final consolidation，也不直接更新正式履歷 / 自傳。

目前最安全的面試口徑：

- 「我參與過 AntPlay / DerPlay provider adapter transfer / query transaction 串接與維護。」
- 「我深掃並能說明 UGSoft connector transfer wallet flow 的 request log、duplicate guard、transaction table、order lookup 與 provider query order 邊界。」
- 「我能指出 Redis short guard、DB success replay 與 provider success but local persist failed 的風險與補強方向。」

目前不安全的口徑：

- 「我主導完整 transfer wallet 架構。」
- 「我 owner 完整 idempotency / recovery / reconciliation。」
- 「transaction facade / Redis guard / lookup replay 都是我本人寫的。」

## Step 5 判定

本 flow 已完成 Step 5 單條 flow claim gate。

### 結論

可以升級為 `ugsoft-connector-api` project-level 履歷 claim 的強化 evidence，但不單獨更新 `05 / 08`，也不單獨寫成完整 transfer wallet owner。

### 可寫入 project-level 履歷 / 自傳素材

保守寫法：

- 參與 UGSoft provider connector / gateway 開發維護，處理 AntPlay / DerPlay transfer in / out / out-all、get-single-transaction adapter、provider response normalization 與 transfer wallet transaction / lookup 相關維護。

更短寫法：

- 參與 AntPlay / DerPlay provider adapter 與 transfer wallet flow 維護，涵蓋轉入 / 轉出 / 全額轉出、查單、provider order mapping 與本地 transaction / lookup 邊界。

### 可面試講

- Nick / `10gt12nc` 直接做過 AntPlay / DerPlay transfer adapter path。
- DerPlay transfer 不是單一 API，而是 before balance、create order、transfer chip、query order、after balance 的多步 provider contract。
- 本地 transaction / lookup 與 provider order id 的對應邊界。
- Redis short guard + DB success replay 的層次，以及它們為什麼不是 exactly-once。
- provider success but local persist failed 的 failure window 與補強方向。

### 不可寫 / 不可講成

- 不寫「我主導完整 transfer wallet 架構」。
- 不寫「我建立完整 idempotency / outbox / recovery / reconciliation」。
- 不寫「beforeTransaction / afterTransaction 全部是我本人開發」。
- 不寫「我 owner 完整 UGSoft connector」。
- 不寫量化改善、事故 owner、production incident 修復。

## 可放履歷

可以保守寫：

- 參與 UGSoft provider connector / gateway 開發維護，處理 AntPlay / DerPlay transfer in / out / out-all 與 get-single-transaction adapter 串接。
- 參與 transfer wallet provider adapter、交易查詢與 provider response normalization 維護，並能說明 request log、transaction table、order lookup 與 provider query order 邊界。

## 可面試講

- 商戶 transfer API 的 controller / service / adapter 分層。
- 驗簽、agent / subAgent / wallet type、provider position。
- Redis 3 秒 duplicate guard。
- DB success replay 與 order lookup。
- AntPlay direct transfer API 與 DerPlay multi-step transfer order 差異。
- provider 成功但本地 DB persist 失敗的 failure window。
- 後續可補強的 recovery / durable idempotency 設計。

## Nick direct evidence

目前可列為 Nick / `10gt12nc` direct evidence：

- AntPlay transfer API adapter：`575996d`、`80828c1`、`04473d5` 等。
- DerPlay transfer in / out / out-all adapter：`48ee13b`、`afff4b1`、`04e2c84`。
- DerPlay get single transaction：`1a1c0b1`、`92b6a1f`。

## Code-backed / 主管或團隊 context

只可作 code-backed 分析或團隊 context，不可直接當 Nick direct evidence：

- `TransferFacade#beforeTransaction / afterTransaction`。
- Redis 3 秒 guard。
- `ag_transfer_order_lookup` insert / query。
- transfer replay 回傳 balance before / after。
- subAgentId 全鏈路傳遞與 replaceAgentId lookup 修正。
- provider position / inner transfer latest behavior。

## 不可寫

- 主導完整 UGSoft connector architecture。
- 完整 wallet / ledger / reconciliation owner。
- 建立 exactly-once / outbox / complete recovery。
- 主導全部 provider integration。
- 解決所有 transfer consistency / deadlock / compensation 問題。
- 量化改善、事故 owner、production incident 修復，除非後續補 ticket / incident / 本人確認。

## Step 5 前待補

- 若要再升級成更強 claim，仍需補 MR / ticket / 本人確認，尤其是 transaction facade / idempotency / lookup / recovery 部分。
- production branch 與 deployment version 仍未確認。
- DB unique constraint 或 repair SOP 仍未確認。
