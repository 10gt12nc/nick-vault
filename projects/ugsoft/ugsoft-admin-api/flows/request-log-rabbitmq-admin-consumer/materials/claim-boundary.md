# Claim Boundary - request-log-rabbitmq-admin-consumer

日期: 2026-05-27

## Evidence 等級

| Claim | 等級 | 判斷 |
| --- | --- | --- |
| Nick 參與 RequestLog RabbitMQ 非同步化 | 真實開發過 | `10gt12nc` direct commits 覆蓋 listener / mapper / processor |
| admin-api consumer parse / duplicate / insert flow | code-backed | latest `origin/main` code 已檢視 |
| upstream connector producer publish MQ | code-backed context | 本輪只作上游 context，不宣稱 admin-api owner |
| RabbitMQ retry / DLQ / exactly-once | 待確認 | 未驗證 runtime config，不可寫 |
| request-log key / step filter 後續修正 | supervisor / team context | `arnold` commits，不當 Nick direct evidence |

## Step 5 結論

本 flow 可作 `ugsoft-admin-api` project-level claim 的 supporting evidence，但不單獨升級為 `05 / 08` 履歷 bullet。最保守、可用的說法是「參與 RequestLog RabbitMQ 非同步入庫與後台查詢支援」。

## 可放履歷

- 參與 RequestLog RabbitMQ 非同步入庫與後台查詢支援，處理 request / response audit log 的 consumer、分區、重複檢查與 DB insert。
- 維護後台非同步資料處理 flow，關注 MQ consumer idempotency、failure window、partition query 與 observability。

## 可面試講

- async audit log 為什麼要從主流程拆出。
- `ptDay` / `agentId` / `time` / `id` 如何形成 duplicate boundary。
- `@Transactional insert` 不能等於完整 exactly-once。
- catch-and-log 對 RabbitMQ retry / DLQ 的風險。
- 後台查詢 list / count / partition / scope 一致性。

## 不可誇大

- 不說主導完整 RabbitMQ event platform。
- 不說已落地 DLQ / replay / outbox。
- 不說完整 money / wallet correctness。
- 不說完整 provider connector owner。
- 不把 `arnold` commits 包裝成 Nick direct evidence。

## 目前狀態

Step 5 完成。面試稿與單條 flow claim gate 已收斂；正式履歷回填需等 project contribution refresh 或 rolling resume package 判斷。
