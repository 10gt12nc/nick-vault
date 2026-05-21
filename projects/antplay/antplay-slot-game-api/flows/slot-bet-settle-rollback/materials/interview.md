# Interview - slot-bet-settle-rollback

日期: 2026-05-21

## 1. Senior 追問

### Q1: 這條 flow 的 source of truth 是什麼？

保守回答:

`pt_bet_record` 是一局 bet 的狀態主紀錄，記錄 step、detail、win、notify 狀態。wallet balance 則依 wallet type 不同：single wallet 的錢主要在 provider，transfer wallet 的錢在本地 DB / Redis wallet。不能把 request log MQ 當 source of truth，它只是 audit / observability。

### Q2: transfer wallet 和 single wallet 最大差異？

保守回答:

single wallet 主要靠 provider API 做 balance / settle / rollback；transfer wallet 則在 Game API 本地維護 wallet。transfer wallet 下注前先扣 bet amount，開獎後再加 total win，所以本地 DB / Redis 和 bet record 的一致性風險更高。

### Q3: RESULT 寫入後 settle 失敗怎麼辦？

保守回答:

程式會在 settle 時 call `AgentApiFacade#betSettle`，成功後更新 notify success。若失敗，`notify = 0` 且 `step = RESULT` 的 record 可由 Quartz notify job 補 call settle。不過這是補通知 / retry，不等於完整 ledger reconciliation。

### Q4: deadlock 時你看到什麼風險？

保守回答:

`GameFacade#bet` catch deadlock 時會判斷 transfer wallet 是否已加 total win，並計算應 refund 的 amount。但我看到實際 `refundTMOnDeadlock` 和 `setStatusFail` 呼叫目前被註解，所以不能說補償完整。這是一個 owner 應優先補強的 failure window。

### Q5: request log MQ 失敗會不會影響交易？

保守回答:

目前 `sendRequestLogMq` 內部 catch exception 並 log，主流程不會因 request log MQ failure 被 rollback。這合理，因為 request log 是 audit，不是交易 source of truth；但代價是 audit 可能遺失或延遲。

## 2. Lead / Architect 追問

### Q1: 如果你是 owner，你會怎麼補強？

- 定義 bet record 與 wallet mutation 的 transaction boundary。
- 對 provider settle / rollback 引入 idempotency key 與明確 retry policy。
- 對 transfer wallet 扣款後的 deadlock 補償落實為可觀測、可重試的補償流程。
- 把 request log MQ 與交易狀態切清楚：audit failure 不影響交易，但要有告警與補寫策略。
- 補 reconciliation：比對 bet record RESULT / notify、provider settle response、wallet transaction。

### Q2: 為什麼不直接說 exactly-once？

因為本次 evidence 沒看到 outbox、dedupe table、consumer idempotency 或完整 ledger reconciliation。比較保守的說法是「用 bet id / notify job / provider callback 做 retry 與補通知」，不能包裝成 exactly-once。

### Q3: 這條 flow 的最小監控是什麼？

- `pt_bet_record step=RESULT AND notify=0` 積壓量。
- `notify_count` 超過限制的 records。
- provider settle / rollback error rate。
- transfer wallet negative balance log。
- request log MQ send failure。
- deadlock / lock exception count。

## 3. 面試時避免踩雷

不要說:

- 「我設計完整 wallet ledger」。
- 「我們做到 exactly-once」。
- 「deadlock 已完整補償」。
- 「RabbitMQ 保證交易一致性」。

可以說:

- 「我會先把 bet record、wallet、provider callback 三個狀態拆開看。」
- 「我看到補通知 job 能補 settle / rollback，但它不是完整對帳。」
- 「這條 flow 最值得補強的是 transfer wallet 扣款後的 failure window。」
