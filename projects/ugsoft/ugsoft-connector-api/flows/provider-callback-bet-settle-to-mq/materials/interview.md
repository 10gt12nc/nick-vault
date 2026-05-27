# provider-callback-bet-settle-to-mq Interview Notes

## Step 4 正式面試稿

Step 4 已完成。正式口說主稿放在 `career-interview.md`；本檔保留追問題庫、回答要點、陷阱與基本功檢查。

## 追問題庫

### Q1：callback 重送怎麼處理？

回答要點：

- 不假設 producer exactly-once。
- Provider callback 重送時，connector 可能再次送 MQ。
- 下游 admin-api consumer 用 providerBetId / id / ptDay / agentId / currency 相關欄位查重後才 save。
- 這是 at-least-once + idempotent consumer，不是 producer 端 exactly-once。

### Q2：MQ publish 失敗怎麼辦？

回答要點：

- `ConnectBetRecordMqService#send` 目前 catch exception log，不往外拋。
- 這代表 callback response 和 MQ 入列可能不一致。
- 補強方向：publish failure metric / alert、outbox、request log replay。
- 不說現有系統已完整解決。

### Q3：為什麼成功後才送 MQ？

回答要點：

- Provider callback 進來後，還要先呼叫商戶端 betSettle / money_inout。
- 商戶端失敗時不應直接入 bet record，否則平台資料和商戶錢包狀態不一致。
- 因此 MQ publish 條件綁在 adapter callback success。

### Q4：AntPlay 和 DerPlay 差在哪？

回答要點：

- AntPlay callback 是 JSON DTO，amount 已偏 BigDecimal 模型。
- DerPlay 是 form callback，message 裡有 betAmount / winAmount / settleTime / betID。
- DerPlay amount 單位、game prefix、currency from token 都要 normalize。
- History 顯示 amount scaling / currency 多次修正，這是 provider integration 高風險點。

### Q5：consumer 怎麼防重？

回答要點：

- consumer parse payload 後檢查 agentId / provider / providerBetId / currency。
- 用 event time 算 ptDay。
- 查既有 bet record，未存在才寫入。
- producer 的 id / providerBetId / currency 口徑必須和 consumer 一致。

### Q6：如果 callback 和 job sync 都補到同一筆，怎麼避免重複？

回答要點：

- 需要共同 duplicate key，至少要對齊 providerBetId、agentId、currency、ptDay。
- callback-driven 和 job-driven 都可能送 MQ，所以 consumer idempotency 是最後防線。
- 若 key 口徑不同，就可能重複或漏判。

### Q7：這和 transfer wallet flow 差在哪？

回答要點：

- transfer wallet 是 merchant 主動 transfer in / out / 查單。
- callback / MQ 是 provider 主動 callback bet-settle。
- transfer flow 重點是 transferReferenceId、provider transaction、request log、lookup。
- callback / MQ 重點是 payload normalization、MQ delivery、consumer idempotency、bet record persistence。

### Q8：這和 request-bet-record-mq-sync 差在哪？

回答要點：

- callback / MQ 是即時 provider callback pipeline。
- request-bet-record-mq-sync 是 job-driven pull provider bet record，用時間窗補 late / missing data。
- 兩者都可能寫同一類 bet record，因此 duplicate boundary 要一致。

## Lead / Architect 追問

### Q：如果你 owner 這條 flow，第一個補強是什麼？

回答要點：

- 先補觀測，不是直接重寫。
- callback success / failure、adapter response code、MQ publish failure。
- queue depth / consumer lag。
- consumer save success / duplicate / invalid payload。
- quota update failure。
- 有了可觀測性，再決定是否需要 outbox / replay。

### Q：為什麼不直接同步入庫？

回答要點：

- 同步入庫會把 provider callback、merchant callback、BetRecord DB、quota update 都綁在同一 request。
- 任一段慢或失敗都會影響 provider callback response。
- MQ 解耦入口和資料落地，也能削峰。
- 代價是 eventual consistency、duplicate、missing message 和監控成本。

### Q：如何設計 replay？

回答要點：

- 先確保 callback request log 或 outbox 保存足夠重建 MQ payload 的資料。
- Replay 要以 providerBetId / agentId / currency / ptDay 為 idempotency key。
- Replay 不能無腦重送，要先查 downstream 是否已有資料。
- 人工 replay 要有 audit trail。

### Q：這條 flow 的 SLI / SLO 會怎麼訂？

回答要點：

- callback success rate。
- adapter callback success rate。
- MQ publish success rate。
- queue lag / consumer processing latency。
- consumer invalid / duplicate / save failure count。
- bet record delay from provider settle time to DB saved time。

## Case-specific 基本功

- RabbitMQ direct exchange / routing key / durable queue。
- At-least-once delivery。
- Idempotent consumer。
- BigDecimal / amount unit normalization。
- Provider callback signature。
- Consumer duplicate key。
- Outbox pattern。
- DLQ / retry / alert。
- Eventual consistency。

## 誇大陷阱

- 不說 exactly-once。
- 不說完整 outbox / DLQ 已存在。
- 不說完整 owner callback / MQ / admin consumer / quota 全鏈路。
- 不說所有 amount scaling / subAgent / whitelist 都是 Nick direct work。
- 不說已完整解決 reconciliation。

## Step 5 結論

- Step 5 已完成。
- 本 flow 可作 `ugsoft-connector-api` project-level provider connector / callback / MQ claim 強化 evidence。
- 面試可講 provider callback / merchant callback / MQ / downstream consumer eventual consistency。
- 正式履歷不直接由本單條 flow 更新，仍吃 project-level consolidation。
- 誇大邊界維持：不說完整 exactly-once / outbox / DLQ / reconciliation owner，也不把 `arnold` / 團隊 context 當成 Nick direct evidence。
