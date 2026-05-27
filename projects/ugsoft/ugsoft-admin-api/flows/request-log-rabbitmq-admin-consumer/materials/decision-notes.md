# Decision Notes - request-log-rabbitmq-admin-consumer

日期: 2026-05-27

## Decision 1: RequestLog 非同步化

判斷:

- RequestLog 是 audit / observability supporting data，不應讓主請求因 log DB 寫入變慢或失敗。
- 用 MQ 拆出去可降低 request latency coupling。

代價:

- producer publish 失敗、message loss、consumer insert 失敗時，log 可能缺失。
- 需要明確定義 audit log loss 是否可接受，以及是否要補 DLQ / replay / metric。

## Decision 2: Dedupe Key

目前使用:

```text
pt_day + agent_id + time + id
```

好處:

- 避免同一 request message 重送造成重複 log。
- 和分區查詢 `pt_day` 對齊。

限制:

- check-then-insert 不是 atomic exactly-once。
- 未驗證 DB unique key 前，不能保證 race 下完全不重複。

## Decision 3: Transaction Boundary

`insertRequestLog` 有 `@Transactional`，但 duplicate query 與 insert 分別由 listener 呼叫。

面試說法:

- 這是合理的基本防重，但不是完整 exactly-once。
- 真正要提高正確性，要補 DB unique、insert-ignore / upsert、或把 duplicate policy 放到 DB 層。

## Decision 4: Ack / Retry / DLQ

listener catch all exception 並 log，沒有在本輪看到 rethrow。

風險:

- 若 ack mode / container retry 依賴 exception 往外拋，catch-and-log 可能導致 message 被視為成功。
- poison message 沒有 DLQ / metric 會變成靜默缺 log。

Step 4 追問時要保守回答:

- 本輪 code-backed 能講「需要確認 / 建議補」。
- 不能說系統已完整具備 DLQ / replay。

## Decision 5: Admin Query 一致性

後台查詢用 agent scope、time range、partition keys 查 `pt_request_log`。current behavior context 顯示 step filter 在 count / list 可能需要再確認是否一致。

面試說法:

- admin query 不是單純查資料，還要保證權限範圍、分區、列表與總數條件一致。
- 若 list / count 條件不一致，使用者會看到頁數或篩選結果不準。
