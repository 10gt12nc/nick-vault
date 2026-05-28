# request-log-rabbitmq-admin-consumer Claim Boundary

日期: 2026-05-28

## Step 5 更新

Step 5 已完成單條 flow claim gate。claim 邊界維持保守：本 flow 可作 `antplay-slot-admin-api` project-level supporting evidence 與正式面試 case，但不直接更新 `05 / 08`，也不升級成完整 RabbitMQ platform / exactly-once / money correctness claim。

## 可說

- 參與 AntPlay 後台 RequestLog RabbitMQ consumer 與 audit log 入庫流程。
- 處理 / 理解 message parse、`ptDay` 計算、schema route、查重與 `pt_request_log` insert。
- 能分析 request log async ingestion 的 idempotency、consumer failure、retry / DLQ、observability 風險。
- 能把 admin-api consumer 和 game-api producer 對起來，說明 producer / consumer 邊界。

## 可放履歷但要保守

- 參與 AntPlay 後台 RequestLog RabbitMQ consumer 與 audit log 入庫流程，支援 provider API request / response 後台追查與問題排查。
- 若後續 project-level refresh，可併入「AntPlay 後台 API / control plane / 非同步資料處理」段落，不建議單獨包裝成最大主成果。

## 只能面試講 / 分析講

- DB unique key 是否完整防重: 本輪未確認 DDL，只能說「應確認 / 建議補」。
- retry / DLQ / ack mode: 本輪未看到設定，只能說「未確認 / 建議補」。
- queue lag / metrics / alert: 本輪未看到正式監控，只能說 owner improvement。

## 不可誇大

- 不寫主導完整 AntPlay slot platform。
- 不寫完整 RabbitMQ platform owner。
- 不寫 exactly-once、outbox、DLQ / retry 已完整落地。
- 不寫完整 observability platform owner。
- 不把 request log 說成交易 source of truth 或 money correctness evidence。
- 不寫量化改善，除非後續補 production metric / ticket。

## Evidence 等級

| Claim | 等級 | 備註 |
| --- | --- | --- |
| admin-api request log consumer | 真實開發過 + code-backed | admin-api #774 direct commits |
| game-api producer 對接 | 真實開發過 + code-backed | game-api #774 direct commits + 既有 flow |
| project-level supporting evidence | 可回填 consolidation | 回填時仍要保守，不直接宣稱完整 RabbitMQ owner |
| 05 / 08 direct update | 本輪不更新 | 等 project contribution refresh / rolling resume package |
| complete MQ reliability | 待確認 / 不可宣稱 | 未確認 DLQ / retry / ack / publisher confirm |
| complete idempotency | 待確認 / 不可宣稱 | 未確認 DB unique key |
