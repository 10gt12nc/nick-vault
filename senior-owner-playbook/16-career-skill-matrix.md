# 初階到資深的軟硬實力矩陣

這份是檢查表，不是新的每日學習主線。

主線仍然是：

```text
production flow
-> evidence
-> failure / consistency
-> decision-notes
-> interview / resume
```

這份只用來定期檢查：Nick 從中階 Java Backend 往 Senior / Platform Backend / System Owner / Lead-Architect 候選前進時，還缺哪些硬實力與軟實力。

## 使用方式

- 每完成 1-2 條 flow 後回頭檢查一次。
- 不要一次補全部缺口。
- 缺口要回到具體 flow 裡補，不要變成抽象讀書清單。
- 沒有 evidence 的能力，不直接寫進履歷。
- 依 `70 / 20 / 10` 控制比例：70% 放 production case / system design / claim boundary；20% Java / SQL / transaction / Redis / MQ 基本判斷遇到再補；10% LeetCode / coding test 只作投遞前保險。
- AI 時代 coding 的訓練重點是 review AI 產物是否能進 production，不是把自己訓練成刷題型選手。

## 完整技術結構

這段是 Nick 對標 Senior Java Backend / Platform Backend 的完整技術結構。它不是新主線，也不是要求一次全會；用途是當「基本功到底要補什麼」的固定檢查表。真正學習仍要回到 production flow、面試追問與實際 JD。

能力主線：

```text
Java / Spring 基礎
-> 高交易 Production Flow 掌握
-> Transaction / Idempotency / Consistency
-> MQ / Kafka / Batch / Projection
-> MySQL / Redis / JVM Troubleshooting
-> Observability / Incident Handling
-> Legacy Takeover / Flow Reconstruction
-> System Owner Decision
```

### 1. Java / Spring Backend 基礎主幹

這是基本盤，目標不是炫技，而是能穩定寫出可維護、可讀、可排查的後端服務。

必須維持熟練：

- Java 語法與集合。
- Stream / Optional / Lambda。
- Exception handling。
- Thread / Executor / Future。
- Spring Boot / Spring MVC。
- Spring Transaction。
- MyBatis。
- REST API 設計。
- Validation / DTO / VO / Entity 分層。
- Log / Error Code / Global Exception。

### 2. Production Flow / 業務流程掌握

這是 Nick 最核心定位。要能重建一個 request / event / batch 從入口到資料落地，中間經過哪些服務、狀態、交易邊界與失敗點。

優先掌握：

- Provider Integration。
- Payment Callback / Payment Query。
- Wallet Flow。
- Bet / Settle Flow。
- Game Round Flow。
- Batch Projection。
- Report / Statement Flow。
- Admin / RBAC Flow。
- Third-party API Flow。
- Legacy System Flow。

### 3. Transaction / Consistency / Idempotency

這是高交易系統的核心能力，也是往 Senior 拉開差距的地方。要能回答：失敗時資料會不會重複、錢會不會多扣、重試會不會二次入帳、DB 成功但 MQ 失敗怎麼辦。

必須熟：

- DB Transaction Boundary。
- Spring Transaction 失效場景。
- Isolation Level。
- Lock / Deadlock / Lock Wait。
- Optimistic Lock / Pessimistic Lock。
- Idempotency Key / Request Deduplication。
- Retry Safety。
- Compensation。
- Reconciliation。
- Outbox Pattern。
- 最終一致性。
- Callback 重複通知處理。
- Wallet Balance Consistency。

### 4. MQ / Kafka / Async Event Flow

這是平台型後端核心加分項。重點不是背 Kafka 指令，而是能說清楚 event 發出去後誰消費、失敗怎麼辦、能不能重跑、能不能補資料、資料會不會亂序。

要掌握：

- Producer / Consumer。
- Topic / Partition。
- Consumer Group / Offset。
- Retry / DLQ。
- At-least-once。
- Duplicate Message。
- Message Ordering。
- Event Schema。
- Kafka Lag。
- Batch Consumer。
- Projection。
- Event-driven Integration。
- Transaction + Event 的一致性問題。

### 5. Database / MySQL Performance

高交易後端不能只會查資料，還要能判斷這個查詢在線上高流量下會不會炸。

要熟：

- Index Design / Composite Index。
- EXPLAIN。
- Slow Query。
- Join / Subquery / Pagination。
- Transaction / Lock / Deadlock。
- Partition。
- Batch Insert / Batch Update。
- 大 IN Query 風險。
- Connection Pool。
- Read / Write Pattern。
- Report Query 與 Online Query 分離。

### 6. Redis / Cache / Distributed Lock

核心問題是什麼資料能放 Redis、什麼資料不能只信 Redis，以及 cache 跟 DB 不一致時怎麼處理。尤其 wallet / balance 類資料要非常保守。

要熟：

- Cache Aside。
- Cache Penetration / Breakdown / Avalanche。
- TTL Strategy。
- Hot Key / Big Key。
- Distributed Lock。
- Token / Session。
- Rate Limit。
- Redis + DB Consistency。
- Redis Memory / Eviction。
- Redis Cluster 基礎。

### 7. JVM / Runtime Troubleshooting

這是 Senior 訊號。Nick 不需要一開始變 JVM 專家，但要能在線上服務異常時知道看什麼、怎麼縮小範圍、怎麼提出合理修正。

要能處理：

- Heap / Stack。
- GC / Full GC。
- OOM。
- Thread Dump / Heap Dump。
- CPU 100%。
- Memory Leak。
- Thread Pool Exhaustion。
- Connection Pool Exhaustion。
- Large Object / Large Query Result。
- JVM Parameters 基礎。

### 8. Observability / Production Operation

這是從「會寫功能」到「能扛服務」的差距。每個重要流程都要能查、能追、能補、能解釋。

要熟：

- Structured Logging。
- Trace ID / Request ID。
- Error Code。
- Metrics / Alert / Dashboard。
- Slow Query Log。
- Kafka Lag Monitoring。
- Callback Failure Log。
- Batch Job Result。
- Reconciliation Report。
- Incident Review。

### 9. Legacy System Takeover / Reverse Engineering

這是 Nick 的隱藏強項，要明確包裝成「接手複雜既有系統，重建 production flow，找出資料與穩定性風險」。

要能做到：

- 從 API Entry 找流程。
- 從 DB Table 反推狀態機。
- 從 Log 反推 production behavior。
- 從 MQ Topic 找上下游。
- 從 Batch Job 找資料投影。
- 從錯誤案例找真實邊界。
- 補文件、畫 flow、找風險點、提出改善順序。

### 10. System Design / Owner Decision

下一階段要補的是具體決策能力，不是空談架構。

要能回答：

- 何時同步？何時非同步？
- 何時用 DB transaction？何時用最終一致？
- 何時 retry？何時 compensation？
- 何時用 cache？何時不要 cache？
- 何時拆服務？何時不要拆？
- 如何設計可重跑 batch？
- 如何設計 callback query fallback？
- 如何設計 wallet / ledger flow？
- 如何讓系統可觀測、可補償、可排查？

## 目前最該優先補的 5 個

先不用再擴張太多。對標月薪 10 萬以上 Senior / Platform Backend，最有投遞與面試價值的是把下面 5 個補成「我做過什麼、遇過什麼問題、怎麼判斷、怎麼修、trade-off 是什麼」：

1. Spring Transaction + DB Lock / Consistency。
2. Kafka / MQ Retry / DLQ / Idempotency。
3. MySQL Slow Query / Deadlock / Index。
4. Redis Cache Consistency / Distributed Lock。
5. Production Flow 文件化與案例化。

補法仍然固定：

```text
先讀主力 production flow
-> 從該 flow 裡抽出卡點
-> 補對應基本功
-> 回填 90 秒 / 3 分鐘口說與追問題庫
```

## Senior Java Backend JD 技術關鍵字去重清單

這段彙整目前三份 JD 客製包（微達 Senior Java Backend、糖蛙 Backend Team Lead Java、糖蛙 Senior Java Backend）與一般資深 Java Backend JD 常見要求。它是投遞前檢查表，不是新的學習主線；真正準備仍以主力 production flow、JD-specific 補洞與 `70 / 20 / 10` 為準。

### Java / Spring 主幹

- Java 8+；Java 11 / 17 加分。
- OOP、Collection、Stream / Lambda / Optional。
- Exception handling、Thread / Executor / CompletableFuture。
- Spring Boot、Spring MVC、Spring AOP、Spring Transaction、Spring Security。
- RESTful API、Validation、DTO / VO / Entity 分層。
- Global Exception Handler、Error Code、Logging。

### Spring Cloud / 微服務架構

Spring Cloud 不只背元件名，重點是能講清楚服務怎麼拆、怎麼呼叫、失敗怎麼辦、怎麼觀測。

常見技術：

- Spring Cloud Gateway。
- Spring Cloud OpenFeign。
- Spring Cloud LoadBalancer。
- Spring Cloud Config / Bus。
- Spring Cloud CircuitBreaker / Resilience4j。
- Eureka / Nacos / Consul。
- Sleuth / Micrometer Tracing、Zipkin / Jaeger。
- Spring Cloud Stream。
- Spring Cloud Kubernetes 加分。

微服務核心：

- Service boundary / bounded context。
- API Gateway、Service discovery、Load balancing。
- Config management、Inter-service communication。
- Synchronous call vs Asynchronous event。
- Timeout、Retry、Circuit breaker、Fallback、Bulkhead、Rate limit。
- Distributed transaction、Eventual consistency、Saga / compensation、Outbox pattern。
- Idempotency、Correlation ID / Trace ID、Observability。
- Deployment、rollout、rollback。

面試口徑：

```text
我主要實務是在既有 Java / Spring 系統中處理跨服務與 provider integration 的 production flow。雖然不會把自己包裝成完整 Spring Cloud 平台 owner，但我熟悉微服務場景下的 timeout、retry、idempotency、MQ async、state consistency、observability 與 source of truth 判斷。這些是 Spring Cloud 元件背後真正要解決的問題。
```

### Persistence / ORM

- MyBatis、dynamic SQL。
- MyBatis Plus 加分。
- JPA / Hibernate 加分。
- DAO / Repository pattern。
- Transaction boundary。
- Batch insert / update。
- N+1 query。

### Database

- MySQL、PostgreSQL。
- MongoDB。
- SQL tuning、Index design、Composite index、Covering index。
- EXPLAIN、Slow query。
- Transaction、Isolation level。
- Lock / Deadlock、Optimistic lock / Pessimistic lock。
- Pagination、Large table query。
- Partition、Sharding / schema routing。
- Unique key for idempotency。

### Redis / Cache

- Redis。
- Cache aside、TTL strategy。
- Cache penetration / breakdown / avalanche。
- Hot key / Big key。
- Distributed lock。
- Rate limit、Session / token cache。
- Runtime projection / quota / whitelist cache。
- Redis + DB consistency。
- Source of truth 判斷。

### MQ / Async

- RabbitMQ、Kafka；ActiveMQ 加分。
- Producer / Consumer。
- Exchange / Queue / Routing key。
- Topic / Partition、Consumer group。
- Ack / Nack、Retry / Backoff。
- DLQ / DLT。
- At-least-once。
- Duplicate message handling。
- Message ordering。
- Event-driven flow。
- MQ + DB consistency。
- Projection replay / rebuild。

### Distributed / High Transaction

- Provider integration。
- Payment callback / query。
- Wallet / Bet-settle / Rollback。
- Idempotency key、Request deduplication。
- Compensation、Reconciliation。
- State transition、Failure recovery。
- Source of truth。
- Timeout unknown / query fallback。

### Game / Provider Domain

- Game backend、gaming / gambling platform。
- Third-party provider integration。
- Provider request / callback / query。
- Wallet transfer。
- Bet / settle / rollback。
- Slot job / report projection。
- Runtime RTP / player control 加分。
- Slot math / RTP validation 加分。
- Netty、Elasticsearch 加分；目前只列可補強，不當主軸。

### Batch / Report / Data Flow

- Quartz / scheduled job。
- Batch processing。
- Report projection、Daily summary。
- Request log async、Bet record sync。
- Replay / rebuild、Data repair。
- Audit log、Statement / reconciliation。

### DevOps / Runtime

- Linux。
- Git、Maven。
- Docker、Docker Compose。
- Kubernetes / K8s / K3s。
- Nginx。
- CI/CD、GitLab CI / GitHub Actions。
- JVM basic、GC、OOM、Thread dump、Heap dump。
- Connection pool。
- Graceful shutdown、Health check。

### Observability / Production

- Production troubleshooting。
- Structured logging。
- Trace ID / Request ID。
- Metrics、Alert、Dashboard。
- Slow query log。
- MQ lag monitoring。
- Error tracking。
- Incident handling / Incident review。
- Performance monitoring。
- Log tracing。

### Architecture / Design

- System design。
- API contract。
- Module boundary。
- Clean Architecture / DDD 加分。
- Design patterns。
- Scalability、High availability、Maintainability。
- Security basic。
- Performance optimization。
- Technical decision / trade-off。

### Quality / Team

- Code Review。
- Unit test、Integration test。
- JUnit、Mockito、API testing。
- Automated testing。
- Refactoring、Clean Code。
- Technical documentation、Development standards。
- Technical mentoring、Task breakdown。
- Engineering workflow。

### AI-assisted Engineering

- Claude Code、Codex。
- AI-assisted code reading。
- AI-assisted diff review。
- AI-assisted documentation。
- AI-generated code review。
- Production risk checklist。
- KB / flow / evidence 回填。
- 不讓 AI 取代 transaction / idempotency / consistency 判斷。

### 投遞前最優先穩住的核心

```text
Java / Spring Boot
MyBatis
MySQL
Redis
RabbitMQ / Kafka
Spring Cloud / 微服務失敗處理概念
Transaction
Idempotency
Provider Integration
Wallet / Bet-settle
Batch / Projection
Production Troubleshooting
Code Review
AI-assisted Engineering Workflow
```

## Level 1：初階 Backend

定位：

- 能接明確需求。
- 能在既有框架下完成 API / CRUD / basic integration。
- 需要別人幫忙判斷系統風險。

硬實力：

- Java 基礎語法與集合。
- Spring Boot controller / service / repository。
- SQL CRUD、基本 index 概念。
- HTTP API、JSON、基本錯誤處理。
- Git 基本操作。

軟實力：

- 問清楚需求。
- 能回報進度。
- 能承認不懂。
- 能照既有規範寫。

面試表達：

- 我能完成明確功能。
- 我會依照既有架構開發與測試。

不能誇大：

- 不說自己能扛 production issue。
- 不說自己能設計架構。

## Level 2：中階 Backend

定位：

- 能獨立接一個功能 flow。
- 能理解既有系統。
- 能處理常見 production bug。

硬實力：

- Spring Boot / MyBatis 常見架構。
- transaction boundary 基礎。
- Redis cache / lock 基礎。
- MQ / Kafka 基礎消費與重送概念。
- SQL index、慢查、lock wait 初步排查。
- API integration、callback、基本 idempotency。
- log tracing 與錯誤定位。

軟實力：

- 拆需求。
- 跟前端、QA、營運對接。
- 寫可交接文件。
- 遇到事故能先止血與回報。

面試表達：

- 我能獨立接後端功能，並理解它在系統中的資料流。
- 我能透過 log / DB / code reading 排查問題。

不能誇大：

- 不說全權 owner。
- 不說主導跨服務架構。

## Level 3：資深 Backend

定位：

- 能扛一條 production flow。
- 能理解 state、failure、consistency。
- 能做技術取捨，而不是只寫功能。

硬實力：

- transaction / consistency / source of truth。
- idempotency、retry、compensation、reconciliation。
- DB performance、index、lock、deadlock、批量處理風險。
- Redis projection、cache consistency、stale data。
- Kafka / MQ reliability、consumer idempotency、DLT。
- production observability：log、metric、trace、alert。
- 逆向舊系統與跨 repo flow。

軟實力：

- 主動發現風險。
- 能跟非工程角色講清楚影響。
- 能定義驗證方式與 rollback。
- 能寫 runbook / handover。
- 能帶中階工程師理解 flow。

面試表達：

- 我會從 production flow 角度看功能，不只看 API。
- 我會先確認 source of truth、終態、retry 邊界與補償方式。
- 我能用 evidence 說明系統風險，不靠猜。

不能誇大：

- 沒有正式證據，不說主導完整系統。
- 沒有 metric，不寫改善百分比。

## Level 4：Platform Backend / System Owner

定位：

- 能跨服務理解一條核心能力。
- 能定義 owner boundary、監控、補償、人工處理與對帳。
- 能把散落功能收斂成平台能力。

硬實力：

- provider contract / external integration。
- event-driven architecture。
- outbox / saga / eventual consistency。
- request id / correlation id / audit trail。
- control plane vs data plane。
- 多服務 rollout、backward compatibility、migration。
- report / projection / source of truth 分離。

軟實力：

- 對結果負責。
- 能定義團隊共用規則。
- 能協調上下游。
- 能在風險與成本之間做取捨。
- 能把事故變成改善項目。

面試表達：

- 我能把跨服務 flow 拆成 source of truth、projection、event、補償與觀測性。
- 我不會只說要重構，而會分階段降低風險。

不能誇大：

- 沒有組織授權，不說正式 owner。
- 沒有主導證據，不說主導平台化。

## Level 5：Lead / Architect 候選

定位：

- 能做方向與取捨。
- 能讓系統更可維護。
- 能影響團隊做法，但不一定已有正式 title。

硬實力：

- system design trade-off。
- architecture decision record。
- migration strategy。
- scalability / reliability / operability。
- security / permission / audit 基礎。
- cost / complexity / maintenance 評估。
- technical debt 分級。

軟實力：

- 能帶人思考，不只是派工作。
- 能把模糊問題拆成階段。
- 能對齊業務、產品、工程風險。
- 能說服，也能接受限制。
- 能知道短期不做什麼。

面試表達：

- 我目前不是正式 Architect，但我用 owner 角度分析系統風險與改善路線。
- 我能提出 Option A / B / C，說清楚成本、風險、rollback 與驗證方式。

不能誇大：

- 不寫 Architect title。
- 不寫帶領團隊，除非有正式管理或 mentor evidence。

## Nick 目前最該補的順序

優先順序不是把全部補滿，而是對標 10 萬以上 Senior / Platform Backend 職缺，先補最有面試價值的缺口：

1. 先把 3 條主力 evidence-backed production case 練到能講 3 分鐘：payment provider、wallet / bet-settle、Kafka / report projection。
2. 每條主力 case 補 failure / consistency / claim boundary 與至少 5 個追問。
3. 再補到 5 條「穩過可抗追問」case：加入 high-traffic table / partition，以及 request log MQ 或 rollout / observability。
4. 若要完全對標 Senior / Platform，再整理到 8-10 條可依 JD 切換的 production flow。
5. Java / SQL / Redis / MQ / LeetCode 只從這些 case 的卡點延伸補，不另外開巨大讀書清單。
6. 大專案地圖只作架構視角補強，不是投遞前必做。

## 8 週軟硬實力養成計劃

這份計劃對標 Nick 目前目標：

```text
Senior Java Backend / Platform Backend / System Owner 候選
```

原則：

- 不用先補滿所有技術。
- 每週都要回到 production flow，不做純讀書焦慮。
- 每週同時補硬實力、軟實力與面試輸出。
- 產出比閱讀重要；能講、能查、能證明，才算補到。

### 第 1-2 週：Money Correctness 主力 Flow

主題：

- `payment-provider-callback`
- `payment-order-provider-request`
- `withdrawal-auto-review-refund`

硬實力：

- transaction boundary。
- idempotency key / terminal state guard。
- provider callback retry / timeout。
- request / response sign verification。
- payment / withdraw order state transition。

軟實力：

- 面對金流問題時先分「狀態、責任邊界、可查證資料」。
- 不急著說重構，先說怎麼止血、查證、補償。
- 能向非工程角色說明：為什麼 timeout 不能直接當失敗。

產出：

- 1 份 3 分鐘 `payment` case。
- 5 個追問：重複 callback、DB 成功 MQ 失敗、provider timeout、人工補單、對帳。
- 1 條保守履歷 bullet。

### 第 3 週：Provider / Wallet / Game Runtime

主題：

- `third-party-transfer-in-out`
- `center-http-deposit-withdraw`
- `coupon-redeem-credit-grant`

硬實力：

- wallet mutation hook。
- bet / settle / refund / transfer-in-out。
- provider transaction id / round id / player balance。
- partial success / compensation。
- downstream command / callback boundary。

軟實力：

- 能把多服務流程拆成「入口、下游、source of truth、projection」。
- 能清楚說哪些是自己做過，哪些是 code-backed 分析。
- 被問「這是不是你主導」時保守但不失分。

產出：

- 1 份 third-party provider / wallet case。
- 1 張 state / failure window 對照表。
- 1 段 claim boundary 口說稿。

### 第 4 週：Batch / Projection / Mongo Backup

主題：

- `daily-game-data-summary`
- `third-party-record-mongo-backup`
- `coin-flow-batch-projection`

硬實力：

- batch replay。
- delete + insert 重跑。
- cursor / stream / batch size。
- Redis checkpoint。
- source log vs report projection。

軟實力：

- 能說明報表不等於交易真相。
- 能先確認資料來源，再談修報表。
- 能把「可重跑、可追蹤、可停止」說成 owner decision。

產出：

- 1 份 batch correctness case。
- 5 個追問：重跑、漏資料、重複累加、Mongo 記憶體、checkpoint 已前進但 projection 缺資料。

### 第 5 週：Kafka / MQ Reliability

主題：

- `settled-bets-kafka`
- `bet-record-request-log-mq`
- `request-log-monitor-alert-mq`

硬實力：

- at-least-once。
- consumer idempotency。
- retry / DLQ / compensation。
- ordering / partition key。
- request log / audit / source of truth 差異。

軟實力：

- 面試時不要只說「用 Kafka 解耦」。
- 要能說「解耦後如何觀測、重送、補償」。
- 能說清楚哪些訊息可以重放，哪些副作用要 guard。

產出：

- 1 份 Kafka / MQ reliability case。
- 1 份「MQ 不是保證一致性」的口說稿。

### 第 6 週：K8s / Observability / Rollout

主題：

- `gameserver-phased-rollout`
- `observability-pipeline`
- `k3s-migration-track`

硬實力：

- Docker / K8s 基礎。
- rollout / rollback。
- probe / resource。
- log pipeline / trace / correlation id。
- release provenance。

軟實力：

- 不把自己包裝成 SRE owner。
- 能說明後端工程師需要讀懂部署訊號，才能排 production issue。
- 能把 rollout 風險拆成「功能、流量、資料、觀測」。

產出：

- 1 份 rollout / observability case。
- 1 份「我不是 DevOps owner，但能讀懂部署與 log 訊號」的保守說法。

### 第 7 週：System Design / Owner Decision

主題：

- 選前 3-4 週最強 flow 各補 `decision-notes.md`。

硬實力：

- Option A / B / C。
- trade-off。
- migration strategy。
- rollback / feature flag / compatibility。
- audit / permission / operation risk。

軟實力：

- 能說「短期先補觀測與 guard，長期再重構」。
- 能說服，也能承認限制。
- 能把技術決策翻成風險、成本與驗證方式。

產出：

- 3 個 owner decision 問答。
- 1 份 3 分鐘 career summary。

### 第 8 週：Mock Interview / Resume Calibration

主題：

- 3 條主力 case 串成面試包。
- 05 / 08 / 17 對齊目標職缺。

硬實力：

- 快速定位 flow。
- 快速畫 state / failure。
- 快速回答追問。

軟實力：

- 誠實 claim boundary。
- 不被薪資與自信拉垮。
- 能把「我分析過」與「我做過」說清楚。

產出：

- 30 秒自我介紹。
- 3 分鐘 career summary。
- 3 條 production case。
- 15 個追問。
- 104 欄位版履歷微調。
- 薪資說法：主談、底線、進取。

## 每週固定節奏

每週只做一個主題，建議節奏：

```text
Day 1-2：重讀 flow.md / evidence / career-interview
Day 3：補 failure / consistency / decision-notes
Day 4：整理 3 分鐘口說與 5 個追問
Day 5：回填 05 / 08 / 17 或 claim boundary
Day 6-7：複述、修稿、休息，不硬開新坑
```

## 軟實力養成重點

Nick 目前最需要補的軟實力不是「變外向」，而是以下 6 件事：

1. **需求澄清**：先問 source of truth、終態、異常處理、誰會查。
2. **風險表達**：把技術風險說成營運可理解的狀態風險。
3. **保守承認邊界**：做過、參與、分析、建議要分開。
4. **Owner decision**：能說先做什麼、不做什麼、為什麼。
5. **面試穩定度**：不用背稿，但要能用固定骨架講 flow。
6. **AI 工具控管**：能說自己用 AI 提效，但判斷、驗證、review 由自己負責。

## 硬實力養成重點

Nick 目前最需要補的硬實力不是所有新技術，而是以下 8 件事：

1. transaction / lock / isolation。
2. idempotency / retry / compensation。
3. reconciliation / audit trail。
4. Kafka / RabbitMQ reliability。
5. SQL index / slow query / batch processing。
6. Redis cache / projection / stale data。
7. K8s rollout / observability 基礎。
8. system design trade-off / migration strategy。

## 檢查問題

每完成一條 flow，就問：

- 我能 3 分鐘講清楚這條 flow 嗎？
- 我知道 source of truth 嗎？
- 我知道 transaction boundary 嗎？
- 我知道 retry 會不會重複副作用嗎？
- 我知道出事怎麼查嗎？
- 我知道怎麼補償 / 對帳嗎？
- 我能說 Option A / B 的代價嗎？
- 我能誠實說哪些是我做過、哪些是我分析的嗎？

如果答不出來，回到該 flow 補，不要開新主題。
