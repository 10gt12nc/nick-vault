# payment-provider-callback interview

## 開場講法

這條 flow 我會拿來講金流 callback 的一致性，而不是只講串接第三方 API。provider callback 通常是非同步、可能重送、可能晚到，也可能和 payment 本地狀態不同步；所以重點是怎麼驗證 callback、怎麼把外部狀態轉成內部狀態、怎麼避免重複入帳 / 退款，以及出了問題怎麼對帳補償。

## STAR 草稿

Situation：

payment 專案需要接多家三方金流 provider，每家都有自己的 callback 欄位、簽章規則、狀態碼與 ack 字串。充值與提現 callback 都會影響玩家餘額與訂單終態。

Task：

整理 provider callback 到內部狀態更新的主線，確認 money correctness、state transition、MQ retry、idempotency 與人工補償邊界。

Action：

我會從 provider controller 入口開始看，確認白名單、簽章、provider 狀態 mapping，再追到 `asynUpdateOrderStatus`、XXL-MQ consumer 與 `updateUserInfo`。接著把充值成功、提現成功、提現失敗退款三條分支拆開，標出哪些地方是本地 DB、哪些地方是外部 game lobby / center HTTP，哪些地方靠終態 guard 或 MQ retry 收斂。

Result：

目前 code-backed 能整理出 callback 入口 guard、consumer 端 no-op、防重複退款處理，以及 callback ack / MQ enqueue / 外部錢包更新之間的 failure window。正式成果仍需補下游 wallet idempotency、DB schema、對帳 / 補單流程與 Nick 本人參與 evidence。

## 深問題庫

### 怎麼處理 provider callback sign 驗證？

不同 provider controller 會依各自規則重算 sign。以代表 provider 看，controller 會取 callback map 裡的欄位，排除 sign 後組簽章字串，搭配商戶 secret 或 private key 算 hash，再跟 provider 傳入 sign 比對。這屬於 provider adapter 的責任。

### 為什麼不在 controller 直接改訂單？

controller 只做 callback trust boundary 與狀態 normalization，真正副作用交給 MQ consumer。好處是 callback request thread 不承擔外部 game lobby HTTP、寄信、跑馬燈、跨月處理等長流程；壞處是 ack 與 durable enqueue 的邊界要處理好，否則 produce 失敗可能造成 callback 被 ack 但沒有後續處理。

### MQ retry 會不會造成重複上分？

有風險，所以 consumer 端會再查訂單狀態，只允許 `WAIT` / `PROCESSING` 處理。終態訂單直接回 SUCCESS。但這只保護 payment 本地狀態，外部 game lobby 是否用 billNo 做 idempotency 還要補證據。

### 提現失敗退款怎麼處理？

provider 回提現失敗時，consumer 會把訂單 tradeType 改成 withdraw back，呼叫 `upperDeposit` 把錢退回玩家，再把訂單更新成 ERROR 並寫入失敗原因。這段 code 有 catch exception 後回 SUCCESS 的處理，目的是避免退款成功後因後續 exception 觸發 MQ retry，造成重複退款。

### 你會怎麼改善？

第一是補 callback inbox 或 outbox，讓 callback event durable、可查、可 replay。第二是確認並強化 wallet API 的 idempotency，使用 billNo 作為外部副作用去重 key。第三是把 provider status mapping 集中化，減少每家 controller 寫法漂移。第四是補對帳 job 與人工補償 dashboard。

## 面試可用詞

- 「至少一次 delivery 下的冪等保護」
- 「訂單終態 guard」
- 「callback trust boundary」
- 「外部 provider 狀態與內部 order state normalization」
- 「提現失敗退款的 duplicate compensation 風險」
- 「ack 與 durable enqueue 的 failure window」
- 「人工對帳與補償不是例外，是金流系統必要設計」

## 面試避免詞

- 「完全一致」
- 「零風險」
- 「我主導」
- 「我設計」
- 「保證不會重複上分」
- 「所有 provider 都一樣」
