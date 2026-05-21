# request-log-rabbitmq-async Decision Notes

日期: 2026-05-21

## 1. Source of Truth

request log 不是交易 source of truth。

| 資料 | 責任 |
| --- | --- |
| bet record / wallet / provider response | 交易事實與 money correctness |
| request log | audit / observability / troubleshooting |
| RabbitMQ message | request log 投遞載體，不是最終資料 |
| `pt_request_log` | 後台查詢與排查用落庫資料 |

## 2. Sync DB vs Async MQ

| 方案 | 優點 | 代價 |
| --- | --- | --- |
| 同步寫 DB | 寫完即可查，語意直覺 | DB 慢 / 分表錯 / 連線問題會拖累主流程 |
| MQ 非同步 | 主流程與 audit DB 解耦 | 會有延遲、重複、consumer failure、監控成本 |

## 3. Delivery Semantics

本輪可保守說是 at-least-once 傾向:

- producer 盡力 publish。
- consumer 有查重。
- 未確認 exactly-once、outbox、publisher confirm、DLQ。

面試時不要把 RabbitMQ 講成自動保證不丟、不重、不延遲。

## 4. Owner 改善方向

優先順序:

1. Publisher confirm / mandatory return：知道 message 是否真的進 broker / exchange。
2. DLQ / retry policy：consumer parse 或 DB insert 失敗時不要無聲消失。
3. DB unique key：讓 dedupe 不只靠先查再 insert。
4. Queue lag / consumer error metrics：後台查不到 log 時能判斷是正常延遲還是故障。
5. Schema version：避免 producer / consumer message 欄位變更互相打爆。

## 5. 面試切法

不要講成「我會用 RabbitMQ」，而要講成:

- 我把非核心 audit side effect 從主交易流程拆出去。
- 我知道拆出去後會帶來 eventual consistency。
- 我能定義哪些 failure 不該影響主流程，哪些 failure 要告警與 repair。

## 6. Step 4 面試決策主線

Step 4 已把本 flow 收斂成三個 owner decision:

1. **Source of truth decision**: request log 不是交易事實；bet record、wallet state、provider response 才是 money correctness 的依據。
2. **Failure isolation decision**: MQ / audit DB 失敗不應 rollback bet / settle，但必須被觀測與補償。
3. **Async governance decision**: MQ 解耦後要補 dedupe、DLQ / retry、queue lag、consumer error 與 schema compatibility；否則只是把同步風險換成無聲 audit loss。

Step 5 需要追重要 diff 與 final behavior，確認這些 decision 哪些是實際落地、哪些只是 owner 改善方向。

## 7. Step 5 Final Decision Boundary

Step 5 追 #774 diff / blame 後，claim gate 收斂如下:

已落地 / 可說:

- 實際把 request log 從 game-api 同步 service path 改成 producer publish。
- admin-api consumer 解析 message、查重後 insert `pt_request_log`。
- Producer failure 被 catch，只記錄錯誤，不反向拖垮 provider API 主流程。
- Current code 仍是 `request-log.direct` exchange、`request-log` queue / routing key、consumer 落庫。

只作 owner 改善方向:

- Publisher confirm / mandatory return。
- Consumer retry / DLQ / requeue policy。
- Queue lag / consumer error alert。
- DB unique key / idempotent upsert。
- Outbox / durable relay。

Context evidence:

- request-log key 格式最終由後續他人 commit 修正；面試可拿來講 producer / consumer contract 風險，但不能寫成 Nick 完成。
