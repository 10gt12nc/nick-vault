# request-log-rabbitmq-async Interview Notes

日期: 2026-05-21

## 1. Step 3 定位

本檔是 Step 3 後的面試素材草稿，正式面試稿等 Step 4 再收斂。

## 2. 3 分鐘草稿

這條 flow 是把 provider API request log 從同步 DB 寫入改成 RabbitMQ 非同步投遞。它的目的不是改交易狀態，而是讓 audit log 不阻塞 balance、bet、settle、rollback 這些主流程。

主流程都會走 `AgentApiFacade#execute`。不管 provider call 成功或失敗，finally 會收集 request、response、error message 和 elapsed time，包成 `RequestLogMessageDto`，透過 direct exchange 和 routing key 丟到 `request-log` queue。下游 admin-api consumer 監聽 queue，parse JSON 後轉成 `RequestLog`，先查 `pt_request_log` 是否已有同一筆，再 insert。

Senior 角度我會強調三件事。第一，request log 是 observability，不是 transaction source of truth，所以 MQ send 失敗不應該 rollback provider API 主流程。第二，改成 async 後會有 eventual consistency：後台短時間查不到 log 是可能的，要靠 queue lag、consumer error、DLQ 或告警來管理。第三，RabbitMQ 常見語意是 at-least-once，所以 consumer 需要 idempotent insert；目前看到有查重，但是否有 DB unique key還要再確認。

這個 case 可以講工程取捨：同步 DB 寫入比較直覺但會把 audit DB 風險帶進交易主線；MQ 解耦比較穩，但要接受 log delay、duplicate message、consumer failure 和 schema compatibility 的治理成本。

## 3. Senior 追問草稿

| 問題 | 回答方向 |
| --- | --- |
| MQ send 失敗要不要讓 bet / settle 失敗？ | 不建議。request log 是 audit，不是 money source of truth；但要告警與補償。 |
| 這是不是 exactly-once？ | 不是。只能保守說 producer send + consumer dedupe；完整 exactly-once / outbox 未確認。 |
| message 重複怎麼辦？ | consumer 先查 `(pt_day, agent_id, time, id)` 是否存在；更穩應加 DB unique key。 |
| consumer 掛掉怎麼辦？ | queue 會堆積；要看 ack / retry / DLQ / monitoring，本輪未確認。 |
| 為什麼不是直接同步寫 DB？ | 同步寫 DB 會讓 audit DB 延遲 / 分表問題影響 provider API 主流程。 |
| request log 可以當交易事實嗎？ | 不行。交易事實仍看 bet record、provider response、wallet / transaction。 |

## 4. Failure Scenarios 草稿

| Scenario | 風險 | 改善方向 |
| --- | --- | --- |
| provider 成功後 JVM crash | 主流程完成但 log 沒送 | outbox 或本地持久化投遞 |
| RabbitMQ publish 失敗 | audit log loss | publisher confirm、alert、fallback |
| routing key 不一致 | message 沒進 consumer queue | contract test / config check |
| consumer parse 失敗 | message 消費失敗或重送 | schema version / DLQ |
| consumer 重複消費 | 重複 request log | idempotent insert + unique key |
| queue lag | 後台查詢延遲 | lag metrics / autoscale / alert |

## 5. 不可踩線

- 不說完整 RabbitMQ 平台 owner。
- 不說 exactly-once 已做到。
- 不說有完整 DLQ / retry / alert。
- 不說 request log 能保證交易一致性。

## 6. 下一步

```text
antplay antplay-slot-game-api request-log-rabbitmq-async Step 4
```
