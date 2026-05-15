# payment-provider-callback decision-notes

## 1. Callback Ack 時機

目前 code 形狀是 provider controller 驗證 callback 後呼叫 `asynUpdateOrderStatus`，然後回 provider ack。

風險點：

- `ProducerUtil.addProduce` catch produce exception 後只 log。
- 若 produce 失敗但 controller 繼續回 ack，provider 可能停止重送，但 payment 沒有進 consumer 更新。

Owner 問題：

- ack 是否應該等 durable enqueue 成功？
- produce 失敗是否要回 provider fail，讓 provider retry？
- 若不改 ack 行為，至少要有 callback log / inbox / alert / replay。

## 2. At-least-once 與 Idempotency

這條 flow 比較像 at-least-once delivery：

- provider 可能重送 callback。
- MQ consumer 可能 retry。
- 外部 game lobby / center HTTP 可能 timeout but actually succeeded。

目前防線：

- controller 入口終態 guard。
- consumer 端二次終態 guard。
- 提現失敗退款分支的 retry 防護。
- payment 會把 `billNo` 當 `billNos` 傳到 game lobby / center，下游會帶入玩家餘額異動與 currency log。

缺口：

- 下游 wallet API 是否真的以 `billNo` 查重或 unique key 去重未確認。
- callback event 是否有 inbox 去重未確認。
- 本地 DB update 與外部 HTTP 不在同一個 transaction。

## 3. 提現失敗退款

這是最高風險分支。

原因：

- 提現建單或出款前通常已扣玩家錢。
- provider 回失敗時 payment 需要退款。
- 若退款已成功但 MQ retry 再進來，可能造成重複退款。

現有 code-backed 設計：

- 退款流程 exception catch 後會更新訂單成失敗 / 需人工處理，並回 `MqResult.SUCCESS`。
- 註解明確說明目的：防止已退款後 exception 導致 MQ retry 再退款。

Owner 延伸：

- 要能 audit「退款是否真的成功」。
- 要有人工處理清單，而不是只靠 log。
- 要確認下游 `billNos` 是否可查詢 / 去重；目前只確認有帶入 currency log，不能當成已去重。

## 4. Provider Adapter 與共用狀態機

代表 provider controller 有高度相似結構：

- `pay/notify`
- `withdraw/notify`
- IP 白名單
- sign verification
- provider status mapping
- ack string
- query endpoint

風險：

- 每家 provider 自行 mapping，容易漏掉某個 status。
- ack 字串不同，provider controller 若 exception handling 不一致，會造成 provider 重送行為不同。
- sign 規則分散，容易有某 provider 特例造成維護成本。

Owner 方向：

- provider adapter 只負責 parse / verify / normalize。
- 共用 callback core 負責 order guard、status transition、MQ enqueue、event audit。
- 狀態 mapping 建表或集中 enum，避免散落在 controller。

## 5. Reconciliation

callback flow 不應只靠 callback 成功。

需要補證據的能力：

- provider 查單 endpoint 的結果是否會回寫本地。
- 是否有定時對帳 job。
- 是否有手動補單 / repair workflow；app_bi local HEAD 有 `bill_no` + 月表的狀態修復入口，但本機落後遠端 4 commits，需再確認最新行為。
- callback accepted but MQ failed 的案件如何被發現。
- DB / log / provider statement 如何對帳。

## 6. Step 4 Owner 補充

Step 4 補到三個新的 owner 判斷：

- `billNo` 是 cross-system correlation key，但不是自動等於 idempotency。只有當下游查重、唯一鍵或可重放 reconciliation 能證明時，才可說 wallet API idempotent。
- `payment_order` 是月表動態 routing；沒有 DB schema evidence 前，`bill_no` unique key 與跨月唯一性都要保留待確認。
- repair workflow 可以存在於 app_bi，但後台修狀態本身不是 reconciliation。owner 還要問：修狀態前是否能證明 provider / wallet / payment 三邊哪一邊是 source of truth。

## 7. 面試取捨說法

保守說法：

> 這類金流 callback 我不會追求單一大 transaction，而是拆成可驗證 callback、durable event、idempotent consumer、下游 wallet idempotency、reconciliation 與人工補償。現有 code 有終態 guard、MQ retry、`billNo` cross-system trace 與退款 retry 防護，但 outbox / inbox、下游強制去重、DB unique key 與對帳 evidence 還需要補齊。
