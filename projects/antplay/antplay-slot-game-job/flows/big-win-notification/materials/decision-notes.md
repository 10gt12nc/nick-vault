# Decision Notes - big-win-notification

日期: 2026-05-25

## 1. Derived Notification vs Source of Truth

`big-win-notification` 是 derived notification，不是交易 source of truth。

差異:

| 類型 | Source of truth flow | Derived notification flow |
| --- | --- | --- |
| 目的 | 決定錢、下注、結算狀態 | 告知玩家 / 前端某事件發生 |
| 錯誤影響 | 交易錯、錢錯、狀態錯 | 漏通知、重複通知、顯示錯 |
| 一致性要求 | 高，需 transaction / idempotency / reconciliation | 視業務可接受 best-effort，但要可觀測 |
| 補償 | 退款、補單、對帳 | 補推、取消顯示、告警 |

面試時要說清楚: 這條不是下注結算正確性的核心，而是 event-driven notification 的可靠性與隱私問題。

## 2. Idempotency Key

current code 每次通知生成新的 UUID `_id`。這對 trace 單筆 message 有幫助，但不是 deterministic idempotency key。

若要避免重送重複通知，較好的 key:

```text
betRecord.id + notificationType
```

或:

```text
agentId + playerId + game + betRecord.id + bigWinThreshold
```

owner decision:

- 如果重複通知可接受，只需 metric / log。
- 如果重複通知不可接受，需下游去重或 producer-side outbox。

## 3. Privacy Boundary

`maskPlayerName` 是好方向，但 payload 同時包含 `fullPlayerName`。這可能是給下游內部使用，也可能是不必要擴散。

建議拆成:

- public payload: masked player name only。
- private audit payload: full player name with stricter access。

如果同一 topic 會被前端或多個 service 消費，應優先使用最小資料原則。

## 4. Translation Fallback

current code 找不到 `TransGames` name 時直接 skip。

可選策略:

| 策略 | 好處 | 風險 |
| --- | --- | --- |
| skip | 不會顯示錯誤名稱 | 漏通知，營運看不到 |
| game code fallback | 不漏通知 | UI 可能不好看 |
| default language fallback | 玩家較容易懂 | 翻譯可能不精準 |
| alert + skip / fallback | 可追蹤缺口 | 需要 observability |

Senior 回答可以說: 通知 flow 不一定要強一致，但缺資料不能安靜消失，要有 alert 或報表。

## 5. Producer Failure

`KafkaProducerService` async callback 只 log failure。這是簡單、低成本的 best-effort 設計。

若通知重要性提高，可考慮:

- producer failure metric / alert。
- durable outbox table。
- retry job。
- 下游 DLT / replay。
- 用 deterministic key 讓 retry 不重複顯示。
