# request-log-rabbitmq-async Career / Interview

日期: 2026-05-21

## 0. 定位

- Flow: `request-log-rabbitmq-async`
- 狀態: Step 3 已完成，待 Step 4 面試 case
- 證據層級: 真實開發過 + code-backed；Nick / `10gt12nc` 有 game-api producer 與 admin-api consumer 相關 direct commits
- 履歷狀態: 可作 `antplay-slot-game-api` project-level async audit / observability evidence；不直接單獨寫進 05 / 08

## 1. 可說

- 參與 AntPlay slot game API request log 非同步化，將 provider API request / response / error log 從主流程同步 DB 寫入改成 RabbitMQ 投遞。
- 能說明 producer、exchange / queue / routing key、message DTO、admin-api consumer、dedupe check 與 `pt_request_log` 落庫邊界。
- 能說明 request log 是 audit / observability，不是 bet / wallet / provider transaction source of truth。

## 2. 面試主軸

這條 case 不要講成「我會用 RabbitMQ」。要講成:

- 主交易流程和 audit log 寫入解耦。
- MQ failure 不應讓 provider API 主流程 rollback。
- audit loss 需要告警、重試、DLQ 或補償策略。
- at-least-once 消費下，consumer 要做 idempotent insert / dedupe。
- message schema、queue key、routing key 需要 producer / consumer 對齊。

## 3. 履歷保守 bullet 候選

可回填 project-level consolidation:

- 參與 AntPlay slot game API request log RabbitMQ 非同步化，將 provider API audit log 從主交易流程同步寫庫改為 MQ 投遞與下游 consumer 落庫，降低主流程與 audit DB 的耦合，並整理 message duplication、consumer delay 與 observability loss 邊界。

不建議單獨寫成完整主成果；可併入「game runtime / betting settlement / transfer wallet / async log」大 bullet。

## 4. 不可誇大

- 不寫主導完整 RabbitMQ architecture。
- 不寫 exactly-once / outbox 已完成。
- 不寫完整 event-driven platform owner。
- 不寫 request log 能保證交易一致性；它只是 audit / observability。
- 不寫有完整 DLQ / retry / alert，除非 Step 4 / Step 5 補到更強 evidence。

## 5. 下一步

```text
antplay antplay-slot-game-api request-log-rabbitmq-async Step 4
```
