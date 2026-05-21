# request-log-rabbitmq-async Claim Boundary

日期: 2026-05-21

## 1. Step 5 Claim 狀態

本 flow 已完成 Step 5，可作正式面試 case，也可回填 `antplay-slot-game-api` project-level async audit / observability claim。仍不直接單獨寫進 `05 / 08`，輸出版履歷要吃 project-level consolidation。

## 2. 可以說

- Nick / `10gt12nc` 有 #774 request log RabbitMQ 非同步化 direct commits。
- 我參與 / 維護過 provider API request log 非同步化，理解 producer、message DTO、RabbitMQ routing、consumer、dedupe insert 與 audit boundary。
- 我處理過 game-api producer 與 admin-api consumer 兩側的 request log async flow；可說明 current code 仍保留 producer publish、consumer parse、查重與 `pt_request_log` insert path。
- request log 是 observability，不是交易 source of truth；MQ 失敗不應直接影響 money flow。

## 3. 可作面試 case

- producer / consumer 的 failure window。
- at-least-once message 與 idempotent insert。
- publisher confirm、DLQ、retry、queue lag、alert 的 owner 改善方向。
- request log 是 RCA evidence，不是 money source of truth。

## 4. 不能說

- 不能說 Nick 主導完整 RabbitMQ architecture。
- 不能說 exactly-once / outbox 已落地。
- 不能說完整 event-driven platform owner。
- 不能說 request log 是交易一致性的保證。
- 不能把 2026-01-21 routing key / queue key 最終格式修正寫成 Nick 完成；該修正是他人 context evidence。
- 不能直接把單條 Step 5 寫入 `05 / 08`；正式履歷仍以 project-level consolidation / rolling resume package 為準。

## 5. Step 4 已補

- 30 秒 / 90 秒 / 3 分鐘面試說法。
- STAR。
- Failure scenarios。
- Senior / Lead 追問。
- 可回填 project-level consolidation 的保守 bullet。

## 6. Step 5 已補

- 追 #774 producer / consumer 重要 diff。
- 確認 current behavior 仍與 Step 3 判斷一致。
- 確認這條 flow 可升級成 flow-level claim，並回填 project-level consolidation。
- 收斂不可誇大邊界：完整 RabbitMQ owner、exactly-once、outbox、DLQ / retry / alert 仍不可寫。

## 7. 下一步

```text
rolling resume package
```
