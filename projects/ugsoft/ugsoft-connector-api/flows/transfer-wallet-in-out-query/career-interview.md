# transfer-wallet-in-out-query Career / Interview

## 定位

這條 flow 可作 UGSoft 非 iwin 廣度補強，主軸是 provider connector / transfer wallet / money-adjacent transaction correctness。

證據層級：

- `真實開發過 + code-backed`：Nick / `10gt12nc` 在 AntPlay transfer API、DerPlay transfer in / out / out-all、DerPlay get single transaction 有 direct commits。
- `code-backed / 主管或團隊 context`：`TransferFacade#beforeTransaction / afterTransaction`、Redis 3 秒 guard、lookup replay、二級代理修正多為 `arnold` commits；Nick 已確認 `arnold` 是主管，不是 Nick direct evidence。
- `分析素材 / 待確認`：production branch、incident、完整 recovery / reconciliation 流程。

## 30 秒說法

UGSoft connector 有一條商戶轉帳錢包 flow，商戶可以呼叫 transfer in、transfer out、transfer out all 或 get single transaction。系統會先驗簽、檢查 agent / subAgent / wallet type，依玩家 provider position 路由到 AntPlay 或 DerPlay adapter；轉帳前會寫 request log，並用 Redis 對 transferReferenceId 做短時間重複提交擋板，成功後再寫本地 transaction 分表與 lookup，讓後續可以用 reference id 查 provider transaction。

## 3 分鐘面試講法

我在 UGSoft connector 這邊整理過一條 transfer wallet flow。它不是單純 CRUD，而是商戶透過 connector 對第三方 provider 做 transfer in、transfer out、transfer out all，以及單筆交易查詢。

入口是在 `ConnectClientController` 的 `/connect/client/transfer/*`。Controller 只做 request validation、呼叫 service，最後記 client out log。真正 business 在 `ConnectClientService`：它會驗簽，確認 agent 是否啟用、wallet type 是否支援 transfer wallet，處理 subAgent，然後透過 `AgTransferProviderPositionService` 找玩家目前在哪個 provider。

轉帳前會進 `TransferFacade#beforeTransaction`。這裡先寫一筆 transfer request log，再用 Redis key 對 `transferReferenceId` 做 3 秒 guard，防止短時間重複提交。接著查 `pt_transfer_player_wallet_transaction`，如果同 reference 已經成功，就回放既有交易，不再打 provider；如果之前失敗，直接回失敗。

如果是新交易，service 會呼叫 `AdapterClient`，由它 dispatch 到 AntPlay 或 DerPlay adapter。AntPlay 比較像直接呼叫 transfer API；DerPlay 比較複雜，會先 query balance、create transfer order、transfer chip，再 query order，最後再查 balance。provider 成功後回到 `TransferFacade#afterTransaction`，寫 `pt_transfer_player_wallet_transaction`，再寫 `ag_transfer_order_lookup`，讓 get-single-transaction 可以從 reference id 找到 provider transaction id。

這條 flow 的風險在於 provider call 和本地 DB persistence 不是同一個原子交易。Redis 3 秒 guard 只能防短時間連點，不是完整 idempotency；provider 成功但 `afterTransaction` 寫 DB 或 lookup 失敗時，本地查單會有缺口。面試時我會把它定位成 provider gateway 的 transfer correctness，而不是完整 wallet ledger 或 reconciliation owner。

## 可用履歷素材

保守 bullet：

- 參與 UGSoft provider connector / gateway 開發維護，串接 AntPlay / DerPlay transfer in / out / out-all 與 get-single-transaction adapter，協助處理 provider contract mapping、交易查詢與轉帳錢包資料流。
- 分析並維護 connector transfer wallet flow，涵蓋簽名驗證、subAgent / wallet type 檢查、provider position、request log、transaction table、order lookup 與 provider query order 邊界。

更保守版本：

- 參與 UGSoft connector 的 AntPlay / DerPlay provider adapter 維護，範圍包含 transfer API、query transaction 與 provider response normalization。

## 不能說

- 不說我主導完整 UGSoft connector architecture。
- 不說我 owner 完整 wallet / ledger / reconciliation。
- 不說我設計完整 exactly-once / outbox / auto recovery。
- 不說 transaction facade / Redis guard / lookup replay 全部是 Nick direct work；目前這些主要是 code-backed / 主管團隊 context。
- 不說有量化改善或正式事故修復，除非之後補 ticket / incident evidence。

## 常見追問

### Q1：怎麼防重複轉帳？

目前有兩層：第一層是 Redis 3 秒 guard，用 `transferReferenceId` 加 account 做短時間重複提交擋板；第二層是 DB transaction success replay，如果同 reference 當日已有成功交易，就回放既有 transaction，不再打 provider。這不是完整 exactly-once，跨日或 provider 成功但本地落地失敗仍要另外補 recovery。

### Q2：provider 成功但 DB 寫入失敗怎麼辦？

這是主要 failure window。現有 code 看到 provider 成功後才寫 transaction 與 lookup，沒有看到完整 outbox 包住 provider call。所以我會把它標成需要補 recovery job / manual repair / provider query fallback 的風險點，不會誇大說已完整解決。

### Q3：為什麼 get-single-transaction 要先查本地 lookup？

商戶拿的是 `transferReferenceId`，但 provider 查單可能需要 provider transaction id。成功 transfer 後系統寫 `ag_transfer_order_lookup`，把 reference id 映射到 transaction table id 與 provider transaction id，查單時先走 lookup，再查本地 transaction 取得 provider / account / currency，最後打 provider query order。

### Q4：AntPlay 和 DerPlay transfer 差在哪？

AntPlay 比較像 connector 直接組 transfer-in / transfer-out / transfer-out-all / get-single-transaction request，帶簽名後呼叫 provider API。DerPlay flow 比較長，會 query balance、create transfer order、transfer chip、query order、再 query balance，因此 failure window 和 provider order id 對應更明顯。

### Q5：這條 flow 能不能代表你做過金流？

不能說完整金流 owner。比較準確是 provider connector / transfer wallet / money-adjacent transaction flow。可以講 provider adapter、transaction record、idempotency boundary、查單與 failure window；但不能包裝成完整 wallet ledger、對帳或金流架構主導。
