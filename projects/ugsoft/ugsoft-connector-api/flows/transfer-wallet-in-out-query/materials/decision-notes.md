# Decision Notes - transfer-wallet-in-out-query

## 技術判斷

### Redis 3 秒 guard 不是完整 idempotency

現有 `TransferService#isTransferReferenceIdExist` 用 Redis hash 記錄 `transferReferenceId` 與 account，TTL 3 秒。這適合防止短時間重複點擊或重複提交，但不能防：

- 3 秒後重試。
- process crash。
- provider 成功但 DB 寫入失敗。
- 跨日 retry。

因此面試時要說「short-term duplicate guard」，不要說 exactly-once。

### DB success replay 是 durable idempotency 的雛形

`beforeTransaction` 查 `pt_transfer_player_wallet_transaction`，若同 reference 已成功，直接回放既有交易。這比 Redis 更接近 durable idempotency，但目前看到它查的是當日 `pt_day`，且依賴成功交易已落地。

### Provider success but local persist failed 是核心 failure window

provider call 與 `afterTransaction` 的 DB insert 不在同一個 transaction boundary。只要 provider 成功、但 insert transaction 或 lookup 失敗，商戶查單與本地追蹤都會出現缺口。

Owner decision 可以這樣說：

- 短期：確保 request / response log 足夠、提供人工補單或 repair SOP。
- 中期：建立 provider success but local persist missing 的 recovery job。
- 長期：把 reference id 狀態機持久化，並用 unique key / outbox pattern 降低不一致。

### AntPlay vs DerPlay adapter 差異

AntPlay transfer path 比較像單次 transfer API call，DerPlay 是多步驟：

1. query balance before。
2. create transfer order。
3. transfer chip。
4. query transfer order。
5. query balance after。

DerPlay 的好處是有較多 provider-side evidence；風險是任一步失敗都要判斷 provider state 是否已變更。

### Lookup table 是 query contract bridge

`ag_transfer_order_lookup` 把商戶 `transferReferenceId` 轉成 provider transaction id / transaction table id。這讓 connector 可以支援 get-single-transaction，但也代表 lookup 寫入失敗時，查單會很脆弱。

## 可改進設計

1. 對 `agent_id + reference_id` 建立 durable unique constraint 或 request state table。
2. 將 provider call 前後拆出明確狀態：PENDING、PROVIDER_SUCCESS、LOCAL_PERSISTED、LOCAL_PERSIST_FAILED。
3. `afterTransaction` 失敗時不要只靠 log，應寫入可重放事件或 repair queue。
4. 查單 fallback：lookup 缺失時，若 request log 有足夠 provider order id，可查 provider 並回補 lookup。
5. 建立 consistency dashboard：provider success / local persist mismatch、lookup missing、duplicate request、provider timeout。
