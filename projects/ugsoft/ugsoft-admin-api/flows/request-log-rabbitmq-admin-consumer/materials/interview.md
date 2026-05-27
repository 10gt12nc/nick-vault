# Interview Notes - request-log-rabbitmq-admin-consumer

日期: 2026-05-27

## Step 3 用途

這份是 Step 3 的面試素材暫存。正式 90 秒 / 3 分鐘回答、追問題庫與回答邊界要在 Step 4 完成。

## 面試主軸

一句話:

> RequestLog RabbitMQ 非同步化是把 request / response audit log 從主請求路徑拆出去，降低 DB logging 對主流程的影響；但拆出去後要補 idempotency、partition、retry / DLQ 與查詢一致性的 owner 判斷。

## 可被追問的點

| 追問 | 回答方向 |
| --- | --- |
| 為什麼不用同步寫 DB？ | logging 是 observability / audit supporting path，同步寫會讓主請求受 log DB 影響 |
| duplicate 怎麼處理？ | 用 `pt_day + agent_id + time + id` 查重，但 check-then-insert 仍需 DB unique 兜底 |
| catch exception 後會不會 retry？ | 本輪未驗證 ack mode；若 exception 被吃掉，可能不觸發 retry / DLQ，所以不能宣稱可靠重試 |
| RequestLog 和 BetRecord MQ 差在哪？ | RequestLog 偏 audit；BetRecord 更接近交易 / report source data，failure policy 更嚴 |
| `ptDay` 為什麼重要？ | 分區查詢與 duplicate 都依賴它；用錯時間會導致查不到或重複 |
| 後台查詢有什麼風險？ | role / agent scope、7 天限制、partition keys、list / count 條件一致性 |

## 誇大陷阱

- 不講 exactly-once。
- 不講完整 RabbitMQ platform。
- 不講完整 DLQ / replay。
- 不講完整 money correctness。
- 不把 supervisor commits 當自己的實作。

## Step 4 要補

- 90 秒版。
- 3 分鐘版。
- 5-8 題追問與回答。
- 和 BetRecord MQ / connector producer case 的串接講法。
