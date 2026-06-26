# Company System Deep Dive Curriculum

狀態：候選課綱；不是自動開工授權。

## 目的

這份課綱回答：

```text
未來一年，我希望透過公司專案學會哪些能力？
```

Prompt 只是執行器。每次 deep dive 都應該回到本課綱，確認本週學的是哪一種通用能力，而不是隨機挖 code。

最新收斂：若未來重啟第二排程，本課綱要採 `topic-first, company-code-second`：

```text
先選通用能力
再找公司案例
最後抽象成 transferable pattern
```

## Year-1 Core Curriculum

### 1. System Architecture / Module Boundary

- 系統在整體產品中的位置。
- module / service / repo 邊界。
- upstream / downstream。
- sync vs async boundary。
- C4-style context / container / component 視角。

### 2. Production Flow

- 使用者 / provider / internal actor。
- happy path。
- failure path。
- state transition。
- terminal state。
- manual operation boundary。

### 3. Code Reading

- Controller / entry。
- Service / domain logic。
- DAO / mapper / repository。
- MQ producer / consumer。
- Scheduler / batch。
- 外部 API client。
- call graph。

### 4. Database Design

- main table。
- important columns。
- index / unique key。
- partition / sharding / routing。
- online query vs report query。
- migration / schema evolution。

### 5. Transaction / Consistency

- transaction boundary。
- cross-system boundary。
- idempotency key。
- retry / duplicate。
- timeout / unknown state。
- rollback / refund / compensation。
- reconciliation。

### 6. MQ / Event / Async

- why async。
- producer / consumer。
- ordering。
- retry / DLQ-like handling。
- consumer lag。
- event schema。
- projection rebuild。

### 7. Cache / Redis

- cache role。
- source of truth。
- key design。
- TTL / invalidation。
- lock / rate limit。
- hot key / big key。
- Redis failure mode。

### 8. Provider Integration

- request / callback / query。
- trust boundary。
- signature。
- mapping。
- timeout unknown。
- reconciliation。
- adapter boundary。

### 9. Observability

- trace key。
- structured log。
- metric。
- alert。
- dashboard。
- SLO / error budget thinking。
- incident timeline reconstruction。

### 10. Incident / Troubleshooting

- symptom。
- impact range。
- first query / first key。
- root cause evidence。
- stop bleeding。
- repair / replay。
- postmortem。

### 11. Git History / Design Evolution

- feature history。
- hotfix / revert。
- path-specific history。
- refactor reason。
- legacy constraint。
- evidence boundary。

### 12. Refactoring

- minimal safe improvement。
- medium refactor。
- large migration。
- strangler-style migration。
- rollback plan。
- compatibility。
- migration cost。

### 13. Technology Comparison

- RabbitMQ / XXL-MQ vs Kafka / RocketMQ / Pulsar。
- ZooKeeper vs Eureka / Nacos / Consul / K8s service discovery。
- cron / XXL-Job vs event-driven / CDC。
- direct DB update vs outbox / inbox。
- local transaction vs Saga / compensation。
- monolith vs modular monolith vs microservice。

### 14. System Design

- source of truth。
- state machine。
- failure handling。
- scalability。
- observability。
- migration。
- owner dashboard。

### 15. Interview Mapping

- 能保守講什麼。
- 需要 direct evidence 才能講什麼。
- 不能誇大什麼。
- 對應 Senior interview questions。
- 30 秒 / 90 秒 takeaway，只在真的有新角度時產出。

## Priority For 2026-2027

Deep Dive 的 priority 先按學習價值，不先按履歷 / 自傳可用性。履歷 claim 是後續 claim gate，不是本課綱的第一層篩選。

A 級：

- Wallet / transfer。
- Bet / settle / rollback。
- MQ / projection。
- Report batch / rebuild。
- Service communication / runtime architecture inventory。
- Payment / provider callback，僅在需要補 code / DB / git history 新 evidence 時優先。
- Idempotency / transaction boundary。
- Incident / troubleshooting。

B 級：

- DB index / partition。
- Redis / cache。
- Observability。
- Auth / permission。
- Deployment / rollout。

C 級：

- Utility。
- DTO / mapper。
- Enum。
- Config-only topic。

## Completion Signal

不是「看完所有 repo」才完成。

每個主力 topic 只要能做到：

```text
L1 Flow
L2 Code
L3 Why
L4 Alternatives
L5 Redesign
L6 Interview
```

就算這次 deep dive 有價值。
