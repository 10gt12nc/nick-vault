# JD-specific 基本功最小複習

用途：微達軟體 Senior Java Backend JD 的 `20% 基本功`。這份不是八股題庫，也不是要讀完整 Spring 原始碼；只補 JD 與主力 production cases 可能追問的最小答案。

比例：

```text
70%：payment provider、wallet / bet-settle、MQ / projection 等 production cases
20%：本檔 Java / Spring / SQL / Redis / MQ / microservice 基本功
10%：coding test / LeetCode 保險
```

## Spring AOP / Proxy

最小答案：

```text
Spring AOP 主要靠 proxy。JDK dynamic proxy 針對 interface，CGLIB 針對 class 建子類。方法呼叫進 proxy 後，會依 advisor / pointcut 找到要套用的 advice。@Transactional、@Cacheable、@Async 這類常見能力都和 proxy / interceptor 有關。限制是 self-invocation 不會經過 proxy，所以同 class 內部方法呼叫可能不會觸發 transaction / AOP。
```

要會追問：

- JDK proxy vs CGLIB 差異。
- self-invocation 為什麼讓 `@Transactional` 失效。
- final class / final method 對 CGLIB 的限制。
- AOP 適合 cross-cutting concerns，不適合塞 business state transition。

連回 production case：

- payment callback / wallet mutation 的 transaction boundary 不能只相信 annotation，要確認呼叫是否真的經過 proxy。
- code review 時看到同 class 內部呼叫 transactional method，要特別小心。

## Spring Transaction

最小答案：

```text
@Transactional 本質是透過 proxy 在 method 前後開 transaction、commit 或 rollback。常見失效場景包含 self-invocation、method 不是 public、exception 被 catch 掉、checked exception 預設不 rollback、不同 propagation 設錯、transaction 包住外部 HTTP / MQ 造成邊界過大。
```

要會追問：

- checked vs unchecked rollback。
- propagation：`REQUIRED`、`REQUIRES_NEW` 基本差異。
- isolation：dirty read、non-repeatable read、phantom read。
- 為什麼不要把 provider HTTP call 長時間包在 DB transaction 裡。

連回 production case：

- payment order 建單與 provider request 要拆清楚：DB order 寫入、外部 call、callback / query 補償，不一定能放同一個 transaction。
- wallet / bet-settle 要確認 money mutation 與 bet record state 的 commit boundary。

## Java 基本功

優先補：

- `BigDecimal`：金額不可用 floating point；注意 scale、rounding、compareTo vs equals。
- `HashMap / ConcurrentHashMap`：hash、bucket、resize、thread safety 基本概念。
- Thread pool：core / max / queue / rejection policy；避免無界 queue 和過度 retry。
- Exception：不要吞 exception；要保留 context log；區分可重試 / 不可重試。
- null / Optional：避免 NPE，API boundary 要明確。

連回 production case：

- 金額單位、provider amount、wallet balance 一律要小心 BigDecimal / long cents / scale。
- MQ consumer retry 不可無限制堆 thread 或重複副作用。

## SQL / MySQL / PostgreSQL

最小答案：

```text
我主要實務是 MySQL，但 RDBMS 的 transaction、index、query plan、schema design、locking、large table query 是共通能力。PostgreSQL 語法與 index type 可依團隊環境補，但不影響我判斷 transaction boundary 與查詢效能。
```

要會追問：

- index 最左前綴、selectivity、covering index。
- `EXPLAIN` 看 rows、type、key、Extra。
- large table pagination：避免深 offset，考慮 cursor / seek pagination。
- deadlock：固定更新順序、縮短 transaction、重試策略。
- unique key 作 idempotency guard。

連回 production case：

- payment merchant order id / provider transaction id 可用 unique key 防重。
- bet record / request log / report projection 要考慮 partition key、schema routing、query filter。

## Redis

最小答案：

```text
Redis 常用在 cache、lock、session、runtime projection。money / wallet 類 flow 通常不能讓 Redis 當唯一 source of truth；要先判斷 DB、wallet transaction、bet record、provider transaction 哪個才是真相。
```

要會追問：

- cache aside：讀 cache miss 查 DB，再回填 cache。
- cache penetration / breakdown / avalanche。
- distributed lock：key、TTL、owner token、unlock safety。
- DB / Redis consistency：DB 成功 Redis 失敗、Redis 成功 DB 失敗要怎麼 repair。

連回 production case：

- transfer wallet / slot bet flow 若同時改 DB wallet 與 Redis hot balance，要能說明 source of truth 與 repair。
- code review 看到 Redis lock 沒 TTL、key 太粗或 unlock 沒 owner check，要指出風險。

## MQ / Kafka / RabbitMQ

最小答案：

```text
MQ 實務上通常要以 at-least-once 思考，不能假設 exactly-once。consumer 要能處理 duplicate，靠 event id、business key、DB unique key、upsert、狀態機 guard 或 processed record 做 idempotency。失敗要有 retry、DLQ / DLT、alert 或可重跑機制。
```

要會追問：

- at-least-once / at-most-once / exactly-once 概念。
- retry storm 與 backoff。
- DLQ / DLT 何時進、誰處理。
- ordering：同 key ordering、partition key。
- report projection 不是交易 source of truth。

連回 production case：

- request log / bet record async 失敗不應直接改變主交易真相。
- proxy report projection / daily summary 要可重跑，並明確 delete + insert / upsert 邊界。

## Microservice / Distributed System

最小答案：

```text
微服務最重要的是 service boundary、timeout、retry、idempotency、observability。外部 provider 或下游 service timeout 時，不應直接假設成功或失敗；要保留 unknown / pending 狀態、查詢入口、補償與 alert。
```

要會追問：

- timeout / retry / circuit breaker。
- retry storm。
- idempotency key。
- correlation id / trace id。
- config / rollout / rollback。

連回 production case：

- payment provider timeout unknown。
- gameserver wallet mutation 與 adapter audit 不一致。
- K3s rollout / observability 只作加分，不包裝成完整 SRE owner。

## 不建議現在做

- 不讀完整 Spring 原始碼。
- 不建立 Java / Spring / SQL 300 題題庫。
- 不把 AOP / transaction 背誦放到 70% 主線前面。
- 不因為怕被問難題，就停止投遞或延後 market check。
