# Backend Weekly Plan

狀態：16 週核心循環，第 1 輪。

用途：記錄每週主題排程、目前跑到第幾週、下一週主題與排程規則。這不是大型 backlog，不放大量文章內容。

## 排程定位

這份排程服務目前 v1 `Job Search Vault`：

- Senior Java Backend 面試準備。
- Production Thinking。
- Incident / Troubleshooting。
- System Design Trade-off。
- 市場驗證與談薪準備。

不做：

- 不處理日文。
- 不自動改履歷、自傳、故事稿。
- 不建立巨大 backlog。
- 不把尚未做過的架構寫成既成事實。
- 不把每週未完成內容累積成學習債務。

## 目前進度

```text
Current cycle: Round 1 / 16 weeks
Current week: Week 01
Current topic: Spring Transaction
Next topic: Week 02 Payment Callback / Idempotency
```

## 第一輪 16 週核心輪替

| Week | 主題 | 主戰場 | 對應面試 / flow |
| --- | --- | --- | --- |
| 01 | Spring Transaction | Backend 基本盤 | payment / wallet / MQ transaction boundary |
| 02 | Payment Callback / Idempotency | Provider Integration | payment-provider-callback |
| 03 | Kafka Retry / DLQ | MQ / Projection | proxy-user-data-report-projection |
| 04 | MySQL Index / EXPLAIN | DB / Performance | bet-record-sharding-schema-route |
| 05 | Redis Lock / Cache Consistency | Redis / Consistency | wallet / runtime control |
| 06 | Incident Troubleshooting | Legacy Takeover | Legacy Takeover Story |
| 07 | OpenTelemetry / Tracing | Observability | gameserver-phased-rollout / incident |
| 08 | System Design Trade-off | Owner Decision | 18-system-design-templates |
| 09 | Wallet / Bet-Settle Consistency | Money correctness | slot-bet-settle-rollback |
| 10 | Consumer Idempotency / Duplicate Message | MQ reliability | request log / bet record MQ |
| 11 | Deadlock / Lock Wait | DB correctness | high-traffic table / wallet |
| 12 | Thread Pool / Async | Runtime stability | callback / MQ / async job |
| 13 | JVM / GC / OOM | Runtime troubleshooting | incident / production ops |
| 14 | Outbox Pattern | Transaction + event | DB success / MQ failed |
| 15 | Migration Strategy / Legacy Takeover | Legacy / rollout | Legacy Takeover Story |
| 16 | Architecture Trade-off / Platform Backend | Platform / Lead 候選 | System design / owner decision |

## 第二輪候選加深

第二輪不先排日期；第一輪跑完後，依面試回饋與 KB 缺口決定。

- Saga。
- CDC / Debezium。
- Contract Testing。
- Event Schema。
- Service Boundary。
- Bounded Context。
- Circuit Breaker。
- Retry / Fallback / Bulkhead。
- Graceful Shutdown。
- SLO / Alert。
- K8s Readiness / Liveness。
- ADR。
- Technical Decision Making。
- Risk Communication。
- Mentoring / Code Review Standard。

## 每週固定檢查

每週開始前：

1. 讀 `backend-weekly-template.md`。
2. 讀 `backend-learning-log.md`，避免重複。
3. 查本週主題最新官方 / 高品質資料。
4. 對應 12 條 flow、三個 Story、30 題核心題。
5. 只產出 1 個 30 分鐘內可完成任務。

每週結束後：

1. 更新 `backend-learning-log.md` 的本週 checkpoint。
2. 更新本檔 `Current week / Next topic`。
3. 只提出 KB 維護建議，不大量改正式 KB。
