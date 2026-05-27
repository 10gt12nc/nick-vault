# Interview - transfer-wallet-in-out-query

## Step 4 結論

本 flow 已完成 Step 4，轉成正式面試 case。這份是 flow-level 面試材料，不是正式履歷 master。正式履歷 / 自傳是否更新，等 Step 5 claim gate 或 project-level contribution consolidation 判斷。

可主打的面試定位：

- UGSoft provider connector / transfer wallet / transaction lookup。
- Provider adapter contract mapping：AntPlay direct transfer API vs DerPlay multi-step transfer order。
- Idempotency boundary：Redis 3 秒 guard + DB success replay，不等於 exactly-once。
- Failure window：provider success but local persist / lookup failed。

保守證據：

- Nick / `10gt12nc` direct evidence：AntPlay / DerPlay adapter transfer 與 DerPlay get-single-transaction。
- Code-backed / team context：`TransferFacade`、Redis guard、lookup replay、subAgent 全鏈路修正。

## 30 秒摘要

這條是 UGSoft connector 的 transfer wallet flow。商戶呼叫 transfer in / out / out-all 或查單，connector 會驗簽、檢查 agent / subAgent / wallet type，依 provider position 路由到 AntPlay 或 DerPlay adapter。轉帳前有 request log、Redis 3 秒 duplicate guard 與既有 transaction replay；provider 成功後寫 transaction 分表與 order lookup，支援後續單筆交易查詢。

## 90 秒版本

這條 case 我會講成 provider connector 的 transfer wallet correctness。商戶透過 UGSoft connector 呼叫 transfer in / out / out-all 或 get-single-transaction，系統先驗簽、確認 agent / subAgent / wallet type，再依玩家 provider position 決定要打 AntPlay 或 DerPlay adapter。

新轉帳會先進 `TransferFacade#beforeTransaction`：寫 request log、用 Redis 對 `transferReferenceId + account` 做 3 秒 duplicate guard，並查當日 transaction。如果已成功就 replay，不再呼叫 provider；新交易才由 `AdapterClient` dispatch 到 provider adapter。AntPlay 比較像直接 transfer API，DerPlay 則是 query balance、create order、transfer chip、query order，再查 balance。

成功後 `afterTransaction` 會寫 transaction table 和 `ag_transfer_order_lookup`，查單時才能由 merchant reference id 找到 provider transaction id。這條 flow 的重點不是說它有完整 exactly-once，而是看得出保護層次與缺口：Redis 只防短時間重送，provider 成功但本地 DB / lookup 寫失敗仍需要 recovery job、manual repair 或 provider query fallback。

## 3 分鐘版本

我會把這條 flow 分成三層講：入口層、provider adapter 層、本地一致性層。

入口層在 `ConnectClientController` 的 `/transfer/transfer-in`、`transfer-out`、`transfer-out-all`、`get-single-transaction`。Controller 做 request validation，呼叫 `ConnectClientService`，最後記 client out log。

Service 層會先驗簽。transfer in 的簽名內容包含 agentId、traceId、account、currency、amount、transferReferenceId、signTime；transfer out 類似；查單則用 agentId、traceId、transferReferenceId、signTime。接著檢查 agent、wallet type、subAgent，並用 provider position 決定玩家目前在 AntPlay 還是 DerPlay。

一致性層在 `TransferFacade`。`beforeTransaction` 先寫 request log，再用 Redis 對 `transferReferenceId` 做 3 秒 guard，擋短時間重複提交。之後查同日 transaction，如果已經成功就回放，不再呼叫 provider；如果失敗就直接回失敗。新交易才會打 provider adapter。

Provider adapter 層由 `AdapterClient` 統一 dispatch。AntPlay 是直接組 transfer API request、簽名、送出；DerPlay 比較多步，會 query balance、create transfer order、transfer chip、query order，再 query balance。成功 response 回來後，`afterTransaction` 寫本地 transaction table 與 lookup table，讓查單可以從 merchant reference id 找到 provider transaction id。

這條 flow 最重要的風險是 provider call 和本地 DB 落地不是原子交易。Redis guard 也只有 3 秒，所以不能講 exactly-once。比較成熟的說法是：現有系統有基本 request log、短期 duplicate guard、success replay、order lookup；若要再強化，需要 recovery job、長期 idempotency key、provider success but local persist failed 的補償流程。

## 5 分鐘深講順序

1. 先定位：這是 provider gateway / transfer wallet，不是完整 ledger。
2. 講入口：`/connect/client/transfer/*`，包含 transfer in / out / out-all / get-single-transaction。
3. 講 validation：sign、agent、subAgent、wallet type、provider position。
4. 講 beforeTransaction：request log、Redis 3 秒 guard、既有 transaction replay。
5. 講 provider adapter：AntPlay direct API；DerPlay multi-step order。
6. 講 afterTransaction：transaction table、order lookup、request log update。
7. 講查單：reference id -> lookup -> local transaction -> provider query order。
8. 講 failure window：provider success but local persist failed、跨日 retry、lookup missing。
9. 講補強：durable idempotency、state machine、recovery job、observability。
10. 收 claim boundary：我有 adapter direct evidence；transaction facade 屬 code-backed team context。

## Senior 能力點

- 能分清 provider gateway transaction record 與完整 wallet ledger。
- 能指出 Redis short TTL guard 與 DB success replay 是不同層級的 idempotency。
- 能說明 provider success / local persist failure 的 failure window。
- 能把 AntPlay direct transfer API 與 DerPlay multi-step transfer order flow 比較清楚。
- 能保守處理 Nick direct evidence 與主管 / 團隊 context。

## 白板圖講法

```text
Merchant
  -> ConnectClientController
  -> ConnectClientService
     -> validate sign / agent / subAgent / wallet type
     -> provider position
     -> TransferFacade.beforeTransaction
        -> request log
        -> Redis 3s guard
        -> existing transaction replay
     -> AdapterClient
        -> AntPlay adapter or DerPlay adapter
     -> TransferFacade.afterTransaction
        -> transaction table
        -> order lookup
  -> get-single-transaction
     -> lookup -> local transaction -> provider query order
```

這張圖要刻意講出三個 boundary：

- Redis 是 short guard，不是 durable idempotency。
- 本地 transaction / lookup 是 connector record，不是 provider wallet ledger。
- provider call 與 DB persist 不是原子交易，所以 recovery 是設計重點。

## Lead / Architect 追問

### 1. 如果你要重構，第一個會補什麼？

先補 idempotency source of truth。現在 Redis 3 秒 guard 只是 frequency guard，我會讓 `transferReferenceId` 在長期 lookup 或 transaction table 有唯一約束，並把 request state 拆成 PENDING、PROVIDER_SUCCESS、LOCAL_PERSISTED、LOCAL_PERSIST_FAILED。

### 2. provider 成功但本地 DB 寫失敗怎麼補？

要先確保 provider response / request 有足夠 audit log。短期可以做人工 repair 或 admin repair；長期可以做 recovery job，掃 request log 中 provider success 但沒有 lookup / transaction 的紀錄，再查 provider query order 回補。

### 3. 為什麼不直接把 Redis lock 拉長？

拉長只能降低重複提交機率，不能解決 process crash、DB 寫失敗、跨日重試或 provider 已成功但本地沒落地。真正的 idempotency 需要 durable storage 與唯一 key。

### 4. 查單為什麼不能直接用 transferReferenceId 打 provider？

要看 provider contract。這份 code 中 DerPlay query order 用 provider order id，所以本地 lookup 把 merchant reference id 映射到 provider transaction id；如果 lookup 缺失，就算 provider 有資料，本地也可能不知道該用哪個 provider order id 查。

### 5. 這條能不能包裝成 money correctness？

可以講 money-adjacent correctness 或 transfer wallet correctness boundary，但不能講完整 money correctness owner。它處理 provider transfer request、transaction record、lookup 與 failure window；完整 ledger、對帳、清分、財務 reconciliation 不在本 flow evidence 裡。

### 6. 如果問「你做過什麼，不只是讀 code 嗎？」

回答要保守但不要把自己抹掉：

我有直接 evidence 的部分是 AntPlay / DerPlay provider adapter transfer 與 DerPlay get-single-transaction，這些涉及 provider request mapping、sign / amount / response normalization 與查單 contract。整條 transfer flow 的 transaction facade / Redis guard / lookup replay 則是我依 code 深掃後整理出的團隊 context，我可以講設計與風險，但不會說全是我本人寫的。

### 7. 如果問「你會怎麼驗證這條 flow 沒有重複扣款或漏單？」

我會分三層看：

- Request 層：同 `transferReferenceId + account` 是否被短時間重送、是否有 request log。
- Local persistence 層：transaction table 與 lookup 是否成對存在，status / provider transaction id 是否完整。
- Provider 層：provider query order 是否能查到同一筆 provider order，balance before / after 是否合理。

如果要 production 化，我會補 reconcile / repair 查詢，把 provider success but local missing 的資料找出來。

### 8. 如果問「為什麼不用 MQ / outbox？」

這條目前沒有看到完整 outbox。provider call 是外部副作用，不適合假裝和 DB insert 能在同一個 local transaction 內完成。若要補 outbox，我會把 request state durable 化，至少把 provider request / response 與 local persist state 拆清楚，再由 recovery job 對 `PROVIDER_SUCCESS / LOCAL_PERSIST_FAILED` 做回補；而不是把 provider call 本身塞進 DB transaction。

### 9. 如果問「這跟 iwin / antplay transfer wallet 有什麼不同？」

UGSoft 這條比較像 connector gateway：它對上統一商戶 API，對下轉接 AntPlay / DerPlay provider；本地主要保存 transaction record / lookup / request log。AntPlay game-api 的 transfer wallet 更靠近遊戲 API runtime 自己的 wallet flow；iwin 的 payment / gameserver 則各自有不同 money / provider 邊界。面試時我會把 UGSoft 定位成非 iwin 廣度：provider connector、adapter contract 與 transaction lookup。

## Case-specific 基本功檢查

- BigDecimal / cents：amount、balance before / after 要避免浮點誤差，落庫前要確認單位轉換。
- Idempotency：Redis short lock 不能取代 DB unique key / lookup durable record。
- Transaction boundary：provider call 和 DB insert 不能假裝原子；要設計 failure recovery。
- SQL / index：lookup 應能用 reference id、agent / subAgent、pt_day 找到 transaction。
- Redis：TTL 設計只適合防連點，不適合跨日重試。
- Provider timeout：timeout 不能直接等同 fail，要有 query order / unknown state。
- Observability：request log、provider response、transaction id、elapsed time、error message 要能串起來。

## 誇大陷阱

- 把 provider connector 說成完整金流。
- 把 transaction record 說成 ledger。
- 把 3 秒 Redis guard 說成 exactly-once。
- 把 `arnold` 的 before / after transaction commits 說成 Nick direct work。
- 忽略 production branch 未確認。

## Step 5 待補

- Step 5 已完成：adapter direct evidence 足以支撐 project-level provider connector / transfer wallet 履歷素材。
- transaction facade / Redis guard / final lookup replay 仍主要是 team context，不能說成 Nick direct work。
- `05 / 08 / 04 / 17` 本輪不直接更新；正式履歷仍以 project-level contribution consolidation 回填。
