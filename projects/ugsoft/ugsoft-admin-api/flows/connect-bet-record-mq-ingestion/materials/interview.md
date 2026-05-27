# Interview - connect-bet-record-mq-ingestion

## 狀態

本檔已完成 Step 4。用途是把 Step 3 的 code evidence 轉成正式面試 case；不是履歷 final claim，也不直接更新 `05 / 08`。

## 30 秒版本

這條 flow 是 UGSoft 的 provider bet record MQ consumer。上游 connector 把 provider callback 或 job sync 的注單資料送進 RabbitMQ，admin-api consumer 收到後解析 payload，依 event time 算 `ptDay`，查 `pt_bet_record` 是否已有同一 provider bet id，沒有才寫入正式 bet record。寫入後會 fire-and-forget 發 quota update，所以 bet record 是主事實，quota 是衍生 view。面試可以延伸 duplicate boundary、跨日 late data、MQ retry / DLQ、quota publish failure 與 outbox 補強。

## 90 秒版本

這條 flow 是 UGSoft provider bet record 的入庫端。上游 connector 可能來自 provider callback，也可能來自定期 job sync，最後都把標準化後的注單 payload 丟到 RabbitMQ。admin-api 的 listener 收到 message 後，service 會 parse payload、檢查必要欄位，然後用 payload event time 算 `ptDay`。

入庫前會查 `pt_bet_record` 是否已有同一筆 provider bet record。最新 code 是用 `ptDay + agentId + providerBetId` 查，entity unique boundary 又包含 provider / currency，所以我會把它講成「有 idempotency 防線，但 key 邊界要和上下游對齊」，不會直接說完全 exactly-once。

不存在時才建立 `BetRecord`，設成 `CREATE` step 並補 currency default。save 成功後會 fire-and-forget 發 quota update MQ。這裡的 trade-off 是 bet record 入庫不被 quota 系統阻塞，但 quota publish 失敗時會造成衍生 view 落後，需要 outbox、DLQ replay、重送 job或用 bet record rebuild quota。

## 3 分鐘版本

這條 flow 我會先從系統位置講起。`ugsoft-connector-api` 是 producer side，它把 provider callback 或 job sync 拿到的 bet record 標準化後，送到 BetRecord MQ。`ugsoft-admin-api` 是 consumer side，負責把這些資料落進正式的 `pt_bet_record`，再觸發後續 quota update。

第一步是 `ConnectBetRecordListener` 從 RabbitMQ 收 message。listener 本身很薄，收到後交給 `ConnectBetRecordConsumerService`。service 會 parse JSON payload，payload null 或必要欄位不合法就 skip。必要欄位包含 agentId、provider、providerBetId、currency。正常情況下會用 message 裡的 event time 算 `ptDay`，這點對 late data 很重要，因為 provider 注單可能晚到或跨日，用 consume time 會查錯分區或查重範圍。

接著進 duplicate check。最新 code 用 `ptDay + agentId + providerBetId` 查 `pt_bet_record` 是否存在；entity unique constraint 顯示還有 provider / currency。這裡我會保守說：目前有應用層查重和 DB unique boundary，但兩邊 key 不完全一樣，所以 owner 要確認上下游 producer、consumer 與 DB schema 對 provider / currency 的語意是否一致。

如果不存在，service 會建立 `BetRecord`：設定 provider、providerBetId、game、account、bet / win amount、currency、`BetRecordStep.CREATE`，以及 notify flags，然後 save。Nick direct evidence 主要能支撐 BetRecord MQ 初版、consumer 調整、mapper query 與 currency default；後續 id 生成、amount normalization、quota monitoring 是主管 / team current behavior，我可以說我理解這些現況，但不會說是我主導。

最後是 quota update。bet record save 成功後會 publish `QuotaUpdatePayload`。這不是同一個 transaction 的強一致設計，而是 eventual consistency：bet record 是 source of truth，quota remaining 是衍生 view。優點是主資料入庫不被 quota 系統拖慢；缺點是 quota publish 失敗時，bet record 已入庫但 quota view 沒更新。面試時我會主動提出補強方向：publisher confirm、transactional outbox、DLQ replay、重送 job、或依 `pt_bet_record` rebuild quota。

## STAR 版本

Situation：UGSoft 有 provider callback 和 job sync 兩種來源會產生 provider bet record，如果每條路徑各自寫入下游，容易造成重複入庫、跨日查重錯誤、currency default 不一致，以及 quota update 邊界混亂。

Task：把 BetRecord MQ consumer 這條入庫端整理清楚，能說明它如何從 MQ message 轉成正式 `pt_bet_record`，以及失敗時哪些狀態會不一致。

Action：我會從 listener、consumer service、mapper、entity、quota update payload 一路追。重點放在 parse / validate、event time 算 `ptDay`、duplicate check、`BetRecordStep.CREATE`、currency default、save 後 fire-and-forget quota update。

Result：這條 flow 可以清楚講出 provider data ingestion 的核心風險：at-least-once MQ 下的 idempotency、late data 的分區查重、DB unique boundary、quota eventual consistency，以及不應誇大成 exactly-once / full outbox owner。

## Senior 追問與回答要點

### Q1：如果 RabbitMQ message 重送，會不會重複入庫？

回答：目前主要靠 duplicate check 和 DB unique boundary。consumer 會用 `ptDay + agentId + providerBetId` 查是否存在，不存在才 save；entity unique constraint 還包含 provider / currency。我要保守說這是 idempotency 防線，但不是完整 exactly-once，因為查後寫仍可能有 race，且 query key 和 unique key 不完全一致。

### Q2：為什麼 `ptDay` 要用 event time，不用 consume time？

回答：provider bet record 可能晚到或跨日。若用 consume time，凌晨補昨天資料時可能查今天 partition，造成 duplicate check miss 或資料落錯日期。event time 才能對齊 provider 實際注單日期。

### Q3：bet record save 成功，但 quota update publish 失敗怎麼辦？

回答：這是這條 flow 最重要的 failure window。現況是 quota publish failure 只 log，不回滾 bet record。我的理解是 bet record 是主事實，quota 是衍生 view；這樣可以避免 quota 系統阻塞入庫，但需要補償，例如 outbox、publisher confirm、DLQ replay、重送 job、或用 `pt_bet_record` rebuild quota。

### Q4：這是不是 exactly-once？

回答：不是。比較準確是 at-least-once delivery 加上 consumer idempotency 防線。沒有看到完整 outbox、publisher confirm、全鏈路 replay dashboard，所以不能說 exactly-once。

### Q5：listener catch exception 後不重拋會怎樣？

回答：要看 Spring AMQP container 的 ack mode / error handler，但這是一個需要確認的點。如果 exception 被 catch 後 broker 視為成功，message 可能不會 redeliver。比較穩的做法是讓不可恢復錯誤進 DLQ，可恢復錯誤重試，並把 invalid payload 做 metric / alert。

### Q6：duplicate query 沒有 provider / currency，entity unique 有，這是問題嗎？

回答：不能直接判定一定是 bug，但這是 owner 要確認的 key semantic。若 providerBetId 在同 agent / ptDay 下全域唯一，query 可以成立；如果不同 provider 或 currency 可能共用 bet id，就有誤判風險。面試時我會說要和 producer contract、DB unique index、provider 規格對齊。

## Lead / Architect 追問

### Q：你會怎麼把這條 flow 做成更可靠的設計？

回答方向：

1. 先定義 source of truth：`pt_bet_record` 是主事實，quota 是衍生 view。
2. BetRecord 入庫使用 DB unique key，duplicate exception 視為 idempotent success。
3. save 成功後不要直接 fire-and-forget publish quota，而是寫 outbox。
4. outbox relay 負責 publish，支援 retry、DLQ、metric。
5. quota consumer 用 deterministic idempotency key，避免重複扣。
6. 提供 rebuild / reconciliation job，能從 `pt_bet_record` 重建 quota view。

### Q：如果 production 已經有資料不一致，你怎麼修？

回答方向：

1. 先凍結或降速相關 producer / consumer，避免擴大。
2. 以 `pt_bet_record` 作主事實，找出 quota view 和 bet record 的差異。
3. 查 MQ / app log，定位 publish failure、consumer failure、duplicate key 或 invalid payload。
4. 先產出差異報表，讓營運 / 主管確認修復範圍。
5. 用可重跑、可審計的 repair job 修 quota view，不手改散落資料。
6. 補 monitoring：publish failure、DLQ depth、duplicate count、quota rebuild diff。

## 常見陷阱

- 不要說「RabbitMQ 保證不重複」。
- 不要說「有查重就一定不會重複」。
- 不要把 quota update 說成和 bet record 同 transaction。
- 不要把 `arnold` 的 current behavior 說成 Nick direct evidence。
- 不要說完整 wallet / ledger / quota owner。

## 反問面試官

- 你們 provider callback / async consumer 的 idempotency key 通常怎麼定？
- 你們對衍生 view，例如 quota / report / balance cache，是偏 outbox、rebuild，還是人工 repair？
- 你們 MQ poison message 和 DLQ replay 有沒有標準 runbook？
- 對高交易資料，你們比較在意低延遲入庫，還是下游 view 同步一致？
