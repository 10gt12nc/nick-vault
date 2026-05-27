# provider-callback-bet-settle-to-mq Interview Notes

## Step 3 口說草稿

這條 flow 是 UGSoft provider connector 的 callback / MQ 非同步入庫。AntPlay 或 DerPlay 打 callback 到 connector 後，系統先做 IP 白名單、驗簽、agent / wallet type 檢查，接著透過 adapter callback 呼叫商戶側 betSettle。只有商戶側成功時，connector 才把 provider callback payload 正規化成 `ConnectBetRecordMqDto` 丟 RabbitMQ；下游 `ugsoft-admin-api` consumer consume 後做 duplicate check、寫 bet record，再觸發 quota update。

我會把它定位成 eventual consistency：provider callback、MQ、consumer 入庫都可能重送或失敗，所以重點是 duplicate key、amount / currency mapping、consumer idempotency、retry / DLQ / monitoring，而不是說它是 exactly-once。

## Senior 追問初稿

### Q：callback 重送怎麼處理？

目前 producer 可能重送 MQ，真正去重在 admin-api consumer。consumer 會根據 event time 算 `ptDay`，再用 agent / id / providerBetId / currency 類欄位查重後才 save。這是 at-least-once + idempotent consumer 思路，不是 producer 端 exactly-once。

### Q：MQ publish 失敗怎麼辦？

目前 `ConnectBetRecordMqService#send` catch exception log，不往外拋。這代表 callback response 和 MQ 入列之間可能不一致。若我要補強，會加 publish failure metric / alert、outbox 或 callback request log replay 機制。

### Q：為什麼成功後才送 MQ？

因為這條 flow 的語意是 provider callback 進來後，還要先呼叫商戶端 betSettle / money_inout。若商戶端回失敗就不應把它當成功 bet record 送下游，否則會產生商戶錢包狀態和平台 bet record 不一致。

### Q：AntPlay 和 DerPlay 差在哪？

AntPlay callback 是 JSON DTO，amount 已是 BigDecimal 欄位。DerPlay 是 form callback，message 裡有 bet / win / settle time 等欄位，amount 單位和 game code prefix 要另外 normalize。這也是 provider integration 最容易出錯的地方。

## Step 4 待補

- 30 秒 / 90 秒 / 3 分鐘正式版。
- STAR 完整版。
- Lead / Architect 追問。
- 誇大陷阱清單。
- 與 transfer wallet flow 的差異。
