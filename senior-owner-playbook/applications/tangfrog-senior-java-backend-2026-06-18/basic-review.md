# JD-specific 基本功複習清單

用途：糖蛙「資深JAVA後端工程師」的 20% 基本功最小複習，不建立泛用八股題庫。

比例：

```text
70% production case / system design / claim boundary
20% JD-specific Java / Spring / MySQL / Redis / RabbitMQ / Linux / AI review 基本功
10% coding test / LeetCode 保險
```

## 1. Java / Spring / MyBatis

### 必會回答

- Spring transaction propagation、rollback 條件、checked / unchecked exception 差異。
- `@Transactional` self-invocation 為什麼失效。
- Controller / Service / Repository / Adapter 的責任切分。
- MyBatis mapper、dynamic SQL、N+1、batch insert / update 注意事項。
- Maven dependency conflict / profile / build 基本概念。

### 對 Nick 的轉譯

- 從 payment callback、wallet bet-settle、RabbitMQ consumer 說 transaction boundary。
- 從 provider adapter / connector 說 adapter boundary，不把 provider detail 塞進 Controller。

## 2. MySQL

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

- Cache aside、TTL、cache penetration / breakdown / avalanche。
- Redis lock 必須有 key granularity、TTL、owner token、unlock check。
- DB / Redis 不一致時誰是 source of truth。
- Hot key / big key / cache stampede 基本處理。
- Session、quota、runtime projection、whitelist 類資料的風險。

### 對 Nick 的轉譯

- 遊戲 wallet / runtime projection 不要讓 Redis 變成唯一真相。
- 白名單 / quota / provider config 要講 rebuild / audit / fallback。

## 4. RabbitMQ / MQ

### 必會回答

- Exchange、queue、routing key、ack / nack、prefetch。
- At-least-once 意味著 consumer 必須冪等。
- Retry、DLQ、backoff、poison message。
- Message ordering 不能跨 queue / partition 隨便保證。
- Projection 不等於 source of truth。

### 對 Nick 的轉譯

- 用 request log / bet record async 說 audit side effect。
- 用 proxy report projection / daily summary 說 replay / rebuild / duplicate。
- 不說完整 RabbitMQ architecture owner。

## 5. Linux / 維運 / 效能

### 必會回答

- `top` / `htop` / `free` / `df` / `du` / `tail` / `grep` / `journalctl` 基本用途。
- CPU high、memory high、disk full、connection pool exhausted 的初步排查。
- Java thread / GC / heap / connection timeout 基本觀念。
- Log、metric、trace、alert 的差異。
- Batch size、cursor / stream、一次載入大量資料的記憶體風險。

### 對 Nick 的轉譯

- Mongo backup batch size、daily summary、report projection 可以講 performance。
- K3s / OpenObserve 只作維運理解加分，不說完整 SRE owner。

## 6. AI Review / 技術文件

### 必會回答

AI 產 code 或整理文件時，優先檢查：

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

## 7. Netty / Elasticsearch 邊界

### 建議說法

```text
Netty / Elasticsearch 不是我目前最強主軸，所以我不會誇大。我的主力是 Java / Spring Boot、MyBatis、MySQL、Redis、RabbitMQ、provider integration、wallet / bet-settle、batch / report projection 與 production troubleshooting。若團隊有 Netty 或 Elasticsearch，我可以用既有的高交易系統、資料狀態、效能排查與服務邊界經驗快速補齊框架細節。
```
