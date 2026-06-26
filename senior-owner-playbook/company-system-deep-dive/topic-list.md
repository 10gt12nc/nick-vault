# Company System Deep Dive Topic List

狀態：候選 topic，不是自動開工授權。

## 使用規則

- 每次必須先依 `project-index.md` 選 1 個 project，再選 1 個 topic。
- 每次選題前先看 `project-value-map.md`，避免只因履歷 / 自傳或既有 KB 熟悉度而重複選 Payment。
- 每次只研究 1 個 topic。
- 每次都要對應 `curriculum.md` 的至少 1 個 curriculum area。
- 目標是理解系統與工程判斷；不是只找 discovery，也不是只 summary existing KB。
- 讀公司 code 時要去識別化，不寫 secret、商戶、內部 URL、真實交易資料。
- 不把分析素材誇大成 Nick direct owner。

## 第一輪：A 級 Production / Money Flow

1. Bet / settle / rollback。
2. Wallet transfer。
3. MQ projection。
4. Report batch / rebuild。
5. Third-party transfer in-out。
6. Runtime architecture / service communication inventory。
7. Payment request / callback / query，僅在要補 code / DB / git history 新 evidence 時優先。
8. Request log / audit log。

## 第二輪：A 級 Consistency / Incident

9. Duplicate callback / duplicate refund / duplicate settle。
10. DB transaction boundary vs external HTTP / MQ boundary。
11. Unknown state / timeout handling。
12. Manual repair / admin operation boundary。
13. Retry stop condition / DLQ-like behavior。
14. Compensation / rollback / refund edge cases。
15. Source of truth mismatch。
16. Incident timeline reconstruction。

## 第三輪：B 級 Code / DB / Redis / Observability

17. SQL / index / unique key assumptions。
18. Partition / sharding / table routing。
19. Redis key / lock / TTL / cache consistency。
20. Config default / feature flag / routing。
21. Provider adapter duplication。
22. Module boundary / API boundary。
23. Structured logging / trace key。
24. Alert / dashboard / SLO candidate。

## 第四輪：B 級 Architecture / Refactor / Technology Comparison

25. Monolith vs modular monolith vs microservice。
26. RabbitMQ / XXL-MQ vs Kafka / RocketMQ / Pulsar。
27. ZooKeeper vs Eureka / Nacos / Consul / K8s service discovery。
28. cron / XXL-Job vs event-driven / CDC。
29. direct DB update vs outbox / inbox。
30. local transaction vs Saga / compensation。
31. Strangler-style migration candidate。
32. Refactor candidate with rollback plan。

## 暫不排入

- 只重寫 30 秒 / 90 秒面試稿。
- 只摘要已完成 flow happy path。
- 完整重構公司系統。
- 私下把單體改成 Spring Cloud 作為主線。
- 全面畫公司大架構圖。
- 平均掃所有 repo。
- 每週新增新技術清單。
- 把公司 code 內容變成履歷 claim。
