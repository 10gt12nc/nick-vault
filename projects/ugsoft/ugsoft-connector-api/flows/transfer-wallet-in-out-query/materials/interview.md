# Interview - transfer-wallet-in-out-query

## 30 秒摘要

這條是 UGSoft connector 的 transfer wallet flow。商戶呼叫 transfer in / out / out-all 或查單，connector 會驗簽、檢查 agent / subAgent / wallet type，依 provider position 路由到 AntPlay 或 DerPlay adapter。轉帳前有 request log、Redis 3 秒 duplicate guard 與既有 transaction replay；provider 成功後寫 transaction 分表與 order lookup，支援後續單筆交易查詢。

## 3 分鐘版本

我會把這條 flow 分成三層講：入口層、provider adapter 層、本地一致性層。

入口層在 `ConnectClientController` 的 `/transfer/transfer-in`、`transfer-out`、`transfer-out-all`、`get-single-transaction`。Controller 做 request validation，呼叫 `ConnectClientService`，最後記 client out log。

Service 層會先驗簽。transfer in 的簽名內容包含 agentId、traceId、account、currency、amount、transferReferenceId、signTime；transfer out 類似；查單則用 agentId、traceId、transferReferenceId、signTime。接著檢查 agent、wallet type、subAgent，並用 provider position 決定玩家目前在 AntPlay 還是 DerPlay。

一致性層在 `TransferFacade`。`beforeTransaction` 先寫 request log，再用 Redis 對 `transferReferenceId` 做 3 秒 guard，擋短時間重複提交。之後查同日 transaction，如果已經成功就回放，不再呼叫 provider；如果失敗就直接回失敗。新交易才會打 provider adapter。

Provider adapter 層由 `AdapterClient` 統一 dispatch。AntPlay 是直接組 transfer API request、簽名、送出；DerPlay 比較多步，會 query balance、create transfer order、transfer chip、query order，再 query balance。成功 response 回來後，`afterTransaction` 寫本地 transaction table 與 lookup table，讓查單可以從 merchant reference id 找到 provider transaction id。

這條 flow 最重要的風險是 provider call 和本地 DB 落地不是原子交易。Redis guard 也只有 3 秒，所以不能講 exactly-once。比較成熟的說法是：現有系統有基本 request log、短期 duplicate guard、success replay、order lookup；若要再強化，需要 recovery job、長期 idempotency key、provider success but local persist failed 的補償流程。

## Senior 能力點

- 能分清 provider gateway transaction record 與完整 wallet ledger。
- 能指出 Redis short TTL guard 與 DB success replay 是不同層級的 idempotency。
- 能說明 provider success / local persist failure 的 failure window。
- 能把 AntPlay direct transfer API 與 DerPlay multi-step transfer order flow 比較清楚。
- 能保守處理 Nick direct evidence 與主管 / 團隊 context。

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

## 誇大陷阱

- 把 provider connector 說成完整金流。
- 把 transaction record 說成 ledger。
- 把 3 秒 Redis guard 說成 exactly-once。
- 把 `arnold` 的 before / after transaction commits 說成 Nick direct work。
- 忽略 production branch 未確認。
