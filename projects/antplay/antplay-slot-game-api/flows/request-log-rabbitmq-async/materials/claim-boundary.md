# request-log-rabbitmq-async Claim Boundary

日期: 2026-05-21

## 1. Step 3 Claim 狀態

本 flow 目前是 Step 3，可作 code-backed 面試素材與 project-level async log evidence。正式履歷 claim 仍等 Step 5。

## 2. 可以說

- Nick / `10gt12nc` 有 #774 request log RabbitMQ 非同步化 direct commits。
- 我參與 / 維護過 provider API request log 非同步化，理解 producer、message DTO、RabbitMQ routing、consumer、dedupe insert 與 audit boundary。
- request log 是 observability，不是交易 source of truth；MQ 失敗不應直接影響 money flow。

## 3. 暫時只能面試講

- producer / consumer 的 failure window。
- at-least-once message 與 idempotent insert。
- publisher confirm、DLQ、retry、queue lag、alert 的 owner 改善方向。

## 4. 不能說

- 不能說 Nick 主導完整 RabbitMQ architecture。
- 不能說 exactly-once / outbox 已落地。
- 不能說完整 event-driven platform owner。
- 不能說 request log 是交易一致性的保證。
- 不能直接把 Step 3 寫入 `05 / 08`。

## 5. Step 4 要補

- 30 秒 / 90 秒 / 3 分鐘面試說法。
- STAR。
- Failure scenarios。
- Senior / Lead 追問。
- 可回填 project-level consolidation 的保守 bullet。

## 6. 下一步

```text
antplay antplay-slot-game-api request-log-rabbitmq-async Step 4
```
