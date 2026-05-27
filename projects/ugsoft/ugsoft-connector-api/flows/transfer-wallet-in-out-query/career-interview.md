# transfer-wallet-in-out-query Career / Interview

## Step 4 狀態

本檔是 `ugsoft-connector-api transfer-wallet-in-out-query` 的 Step 4 面試 case。Step 4 只把 Step 3 的 code-backed flow 轉成可口說、可追問、可防誇大的面試素材；不直接更新 `05-resume-master-zh.md`、`08-application-autobiography-zh.md` 或正式履歷 claim。

Step 5 前的保守結論：

- 可以面試講 provider connector / transfer wallet / adapter / idempotency boundary。
- 可以說 Nick / `10gt12nc` 有 AntPlay / DerPlay adapter transfer 與 DerPlay get-single-transaction direct commits。
- `TransferFacade#beforeTransaction / afterTransaction`、Redis 3 秒 guard、lookup replay、subAgent 修正屬於 code-backed / 主管或團隊 context，不可說成 Nick direct work。

## Step 5 claim gate

Step 5 已完成單條 flow claim gate。結論是：本 flow 可以升級為 `ugsoft-connector-api` project-level provider connector / transfer wallet 履歷素材的強化 evidence，但不單獨寫成完整 transfer wallet owner。

可放入 project-level claim 的安全範圍：

- Nick / `10gt12nc` 有 AntPlay transfer API adapter direct commits，涵蓋 transfer in / out / out-all / get-single-transaction 的 request mapping、sign、response normalization。
- Nick / `10gt12nc` 有 DerPlay transfer in / out / out-all、before / after balance、create transfer order、transfer chip、query order 與 get-single-transaction direct commits。
- 2025 transfer transaction / lookup / compensation path 有 Nick / `10gt12nc` direct evidence，可支撐「理解並參與 transfer wallet transaction / lookup / compensation 類維護」。

仍只能當 code-backed / team context 的範圍：

- `TransferFacade#beforeTransaction / afterTransaction` 的主體 current blame 是 `arnold`。
- Redis 3 秒 duplicate guard 原始 current blame 不是 Nick。
- 最新 subAgent 全鏈路修正、replaceAgentId lookup、回放 balance before / after 屬 `arnold` / 團隊 context。

正式履歷建議只寫 project-level 句子，不把本 flow 拆成一條獨立誇大 bullet。

## 定位

這條 flow 可作 UGSoft 非 iwin 廣度補強，主軸是 provider connector / transfer wallet / money-adjacent transaction correctness。

證據層級：

- `真實開發過 + code-backed`：Nick / `10gt12nc` 在 AntPlay transfer API、DerPlay transfer in / out / out-all、DerPlay get single transaction 有 direct commits。
- `code-backed / 主管或團隊 context`：`TransferFacade#beforeTransaction / afterTransaction`、Redis 3 秒 guard、lookup replay、二級代理修正多為 `arnold` commits；Nick 已確認 `arnold` 是主管，不是 Nick direct evidence。
- `分析素材 / 待確認`：production branch、incident、完整 recovery / reconciliation 流程。

## 30 秒說法

UGSoft connector 有一條商戶轉帳錢包 flow，商戶可以呼叫 transfer in、transfer out、transfer out all 或 get single transaction。系統會先驗簽、檢查 agent / subAgent / wallet type，依玩家 provider position 路由到 AntPlay 或 DerPlay adapter；轉帳前會寫 request log，並用 Redis 對 transferReferenceId 做短時間重複提交擋板，成功後再寫本地 transaction 分表與 lookup，讓後續可以用 reference id 查 provider transaction。

## 90 秒說法

這條 case 我會定位成 provider connector 的 transfer wallet correctness，而不是完整金流或 ledger。商戶呼叫 UGSoft connector 的 transfer in / out / out-all 或查單 API，connector 先做簽名、agent、subAgent、wallet type 檢查，再依玩家 provider position 決定要打 AntPlay 或 DerPlay。

轉帳前會進 `TransferFacade#beforeTransaction`，先寫 request log，然後用 Redis 對 `transferReferenceId + account` 做 3 秒 duplicate guard，再查當日 transaction。如果同 reference 已成功，就回放既有交易，不再打 provider。新交易才進 `AdapterClient`，由 AntPlay 或 DerPlay adapter 呼叫 provider。

成功後 `afterTransaction` 會寫本地 transaction table 和 order lookup，查單時再用 lookup 把 merchant reference id 對到 provider transaction id。這裡我會主動講清楚風險：Redis 3 秒 guard 不是 durable idempotency，provider 成功但 DB / lookup persist 失敗是主要 failure window，所以如果要補強，我會往長期 idempotency key、state machine、recovery job 與 provider query fallback 設計。

## 3 分鐘面試講法

我在 UGSoft connector 這邊整理過一條 transfer wallet flow。它不是單純 CRUD，而是商戶透過 connector 對第三方 provider 做 transfer in、transfer out、transfer out all，以及單筆交易查詢。

入口是在 `ConnectClientController` 的 `/connect/client/transfer/*`。Controller 只做 request validation、呼叫 service，最後記 client out log。真正 business 在 `ConnectClientService`：它會驗簽，確認 agent 是否啟用、wallet type 是否支援 transfer wallet，處理 subAgent，然後透過 `AgTransferProviderPositionService` 找玩家目前在哪個 provider。

轉帳前會進 `TransferFacade#beforeTransaction`。這裡先寫一筆 transfer request log，再用 Redis key 對 `transferReferenceId` 做 3 秒 guard，防止短時間重複提交。接著查 `pt_transfer_player_wallet_transaction`，如果同 reference 已經成功，就回放既有交易，不再打 provider；如果之前失敗，直接回失敗。

如果是新交易，service 會呼叫 `AdapterClient`，由它 dispatch 到 AntPlay 或 DerPlay adapter。AntPlay 比較像直接呼叫 transfer API；DerPlay 比較複雜，會先 query balance、create transfer order、transfer chip，再 query order，最後再查 balance。provider 成功後回到 `TransferFacade#afterTransaction`，寫 `pt_transfer_player_wallet_transaction`，再寫 `ag_transfer_order_lookup`，讓 get-single-transaction 可以從 reference id 找到 provider transaction id。

這條 flow 的風險在於 provider call 和本地 DB persistence 不是同一個原子交易。Redis 3 秒 guard 只能防短時間連點，不是完整 idempotency；provider 成功但 `afterTransaction` 寫 DB 或 lookup 失敗時，本地查單會有缺口。面試時我會把它定位成 provider gateway 的 transfer correctness，而不是完整 wallet ledger 或 reconciliation owner。

## STAR 版本

### Situation

UGSoft connector 需要對上提供商戶統一的 transfer wallet API，對下串 AntPlay / DerPlay 等 provider。這類 flow 的風險不是只有 API 能不能打通，而是同一筆 `transferReferenceId` 重送、provider 成功但本地落地失敗、以及後續查單找不到 provider transaction id。

### Task

我整理與參與的範圍是 provider adapter transfer / query transaction，以及 code-backed 分析整條 connector transfer flow：入口驗簽、provider position、request log、duplicate guard、provider dispatch、transaction / lookup 落庫與查單邊界。

### Action

我會先把 flow 分層：Controller 只處理 request validation 和 client out log；Service 處理簽名、agent / subAgent / wallet type、provider position；`TransferFacade` 處理 request log、Redis short guard、既有 transaction replay 和成功後落庫；`AdapterClient` 統一 dispatch 到 AntPlay / DerPlay adapter。再把 AntPlay direct transfer API 和 DerPlay query balance / create order / transfer chip / query order 的差異拆開。

### Result

這條 case 最終可以支撐我面試時講清楚 provider connector 的 money-adjacent correctness：哪些保護已存在、哪些只是短期擋板、failure window 在哪裡、如果要 owner 這條 flow 我會先補哪些 recovery / durable idempotency 設計。但我不會把它包裝成我主導完整 UGSoft connector 或完整 wallet ledger。

## 可用履歷素材

保守 bullet：

- 參與 UGSoft provider connector / gateway 開發維護，串接 AntPlay / DerPlay transfer in / out / out-all 與 get-single-transaction adapter，協助處理 provider contract mapping、交易查詢與轉帳錢包資料流。
- 分析並維護 connector transfer wallet flow，涵蓋簽名驗證、subAgent / wallet type 檢查、provider position、request log、transaction table、order lookup 與 provider query order 邊界。

更保守版本：

- 參與 UGSoft connector 的 AntPlay / DerPlay provider adapter 維護，範圍包含 transfer API、query transaction 與 provider response normalization。

Step 5 判斷：以上可作 `ugsoft-connector-api` project-level contribution consolidation 的 flow evidence。正式 `05 / 08` 不由單條 flow 直接改，仍以 project-level consolidation 回填。

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

### Q6：如果面試官問「你本人做了哪一段」？

保守回答：我有 Nick / `10gt12nc` direct evidence 的部分主要在 AntPlay / DerPlay provider adapter transfer、DerPlay get-single-transaction 這類 provider contract mapping 與 response normalization。整條 transfer flow 的 `beforeTransaction / afterTransaction`、Redis guard、lookup replay 與 subAgent 修正，我把它當成 code-backed 的團隊 context 來理解與面試分析，不會說成全都是我本人寫的。

### Q7：如果要你接手 owner，第一週會看什麼？

我會先確認三件事：第一，production 實際部署 branch 與 provider spec；第二，`transferReferenceId` 是否有 DB unique constraint 或 durable idempotency 紀錄；第三，provider 成功但本地 transaction / lookup 缺失時，有沒有 repair SOP、job 或人工查 provider 的流程。確認完才會改 recovery / state machine，不會只把 Redis lock 拉長。

## 正式面試邊界

可以說：

- 我參與過 provider adapter transfer / query transaction 的開發維護。
- 我能讀懂並說明 connector transfer wallet 的 request log、idempotency boundary、transaction lookup 與 provider 查單。
- 我能指出 provider 成功但本地 DB persist 失敗的風險，並提出 durable idempotency / recovery 補強方向。

不要說：

- 我主導完整 UGSoft connector architecture。
- 我 owner 完整 wallet / ledger / reconciliation。
- 我已經落地 exactly-once、outbox 或完整自動補償。
- transaction facade / Redis guard / lookup replay 全部是我個人開發。

## Step 5 收口

本 flow 已完成 Step 5。`ugsoft-connector-api` 第二順位 `provider-callback-bet-settle-to-mq` 與第三順位 `request-bet-record-mq-sync` 均已完成 Step 5，且 project-level `contribution claim consolidation refresh` 已完成。若 Nick 想重產完整履歷包，應另跑 rolling resume package，不得宣稱全 project 所有候選 flows 都已完成。
