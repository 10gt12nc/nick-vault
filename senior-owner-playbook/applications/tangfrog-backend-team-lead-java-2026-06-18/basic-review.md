# JD-specific 基本功複習清單

用途：糖蛙 Backend Team Lead (Java) 的 20% 基本功最小複習，不建立泛用八股題庫。

比例：

```text
70% production case / system design / claim boundary
20% JD-specific Java / Spring / SQL / Redis / MQ / microservice / AI review 基本功
10% coding test / LeetCode 保險
```

## 1. Java / Spring Boot / Spring Cloud

### 必會回答

- Spring transaction propagation、rollback 條件、checked / unchecked exception 差異。
- `@Transactional` self-invocation 為什麼失效。
- Controller / Service / Repository / Adapter 的責任切分。
- Timeout / retry / circuit breaker 的差異與風險。
- Spring Boot service 如何做 graceful shutdown、health check、config 管理。

### 對 Nick 的轉譯

- 從 payment callback、wallet bet-settle、MQ consumer 說 transaction boundary。
- Spring Cloud 不要硬背全家桶；先能講 service boundary、timeout、retry、config、observability。

## 2. MySQL / PostgreSQL / Database Design

### 必會回答

- B+Tree index、複合索引最左前綴、covering index。
- transaction isolation：read committed、repeatable read、phantom read。
- unique key 如何支撐 idempotency。
- 大表查詢如何看 explain、如何避免 full scan。
- 分表 / partition / schema routing 的 trade-off。

### 對 Nick 的轉譯

- 用 bet record / request log / report projection / daily summary 說大表與重跑。
- 用 provider transaction id / merchant order id 說 unique key 與 callback idempotency。

## 3. Redis

### 必會回答

- Cache aside、write-through、write-behind 的差異。
- Redis lock 必須有 key granularity、TTL、owner token、unlock check。
- DB / Redis 不一致時誰是 source of truth。
- Hot key / big key / cache stampede 基本處理。
- Session、quota、runtime projection、whitelist 類資料的風險。

### 對 Nick 的轉譯

- 遊戲 wallet / runtime projection 不要讓 Redis 變成唯一真相。
- 白名單 / quota / provider config 要講 rebuild / audit / fallback。

## 4. RabbitMQ / Kafka / MQ

### 必會回答

- At-least-once 意味著 consumer 必須冪等。
- Retry、DLQ / DLT、backoff、poison message。
- Kafka partition key 如何影響 ordering。
- RabbitMQ exchange / queue / routing key 基本概念。
- Projection 不等於 source of truth。

### 對 Nick 的轉譯

- 用 request log / bet record async 說 audit side effect。
- 用 proxy report projection / daily summary 說 replay / rebuild / duplicate。
- 不說完整 Kafka / RabbitMQ architecture owner。

## 5. Scalability / Performance / Security

### 必會回答

- Scalability：stateless API、DB bottleneck、cache、MQ async、read model。
- Performance：SQL index、N+1、batch size、cursor / stream、GC / memory pressure。
- Security：auth / RBAC / JWT、signature verification、input validation、secret management。
- Observability：structured log、trace id、metrics、alert、audit log。

### 對 Nick 的轉譯

- payment provider sign / callback 可以講 security。
- Mongo backup batch size、daily summary、report projection 可以講 performance。
- legacy takeover / K3s rollout analysis 可以講 observability，但不說完整 SRE owner。

## 6. Code Review / AI Review

### 必會回答

AI 產 code 或同事 PR 時，優先檢查：

- BigDecimal / 金額單位 / rounding。
- Transaction boundary / rollback。
- Callback / MQ idempotency。
- Redis lock TTL / unlock token / key design。
- SQL index / query plan。
- Null / race condition / concurrency。
- 重複副作用。
- Exception handling 是否吞錯。
- Log / audit / trace id 是否足夠。
- Test 是否覆蓋 failure path，而不是只有 happy path。

### 對 Nick 的轉譯

這份 JD 明確要求 Claude Code / Codex。面試時重點不是「我會用 AI 寫 code」，而是：

```text
我能把 AI 放進可控流程，讓 code reading、diff review、文件同步和測試檢查更快，但不讓 AI 取代 production 判斷。
```

## 7. Team Lead 最小準備

### 必會回答

- 如何拆 task：先拆風險、入口、資料狀態、交付順序。
- 如何做 Code Review：用 checklist + PR discussion + 範例。
- 如何帶新人：先給 flow map，再給小修，再 review failure mode。
- 如何處理品質不穩：具體化問題，建立標準，pairing / 文件 / review，反覆失敗才升級。
- 如何處理 incident：止血、定位、修復、補償、復盤、回填 runbook。

### 對 Nick 的邊界

不能說已正式管理 5-8 人。可以說：

```text
我目前偏 hands-on tech lead candidate，具備任務拆解、風險檢查、文件化、交接、AI-assisted review 與 production flow ownership 的基礎；正式人員管理會依公司制度補齊。
```
