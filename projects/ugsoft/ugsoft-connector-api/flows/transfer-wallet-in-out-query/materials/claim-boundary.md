# Claim Boundary - transfer-wallet-in-out-query

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

- 若要把本 flow 升級成更強履歷 claim，需補 MR / ticket / 本人確認，尤其是 transaction facade / idempotency / lookup / recovery 部分。
- 需確認 production branch 與 deployment version。
- 需確認是否有 DB unique constraint 或 repair SOP。
