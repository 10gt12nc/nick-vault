# Company System Study Topic List

狀態：候選清單，不是自動開工授權。

## 使用規則

- 每週最多選 1 個 topic。
- 每個 topic 只產 1 份小型 study packet。
- 先看 production / interview / troubleshooting 價值，再決定要不要讀 code 或 git history。
- 讀公司 code 時要去識別化，不寫 secret、商戶、真實交易資料。
- 不把分析素材誇大成 Nick direct owner。

## 第一輪：Production Flow Decision

1. Provider request：order 建立、provider mapping、timeout unknown。
2. Provider callback：signature、idempotency、terminal state、副作用。
3. Provider query / reconciliation：狀態收斂、差異清單、人工處理邊界。
4. Wallet transfer：source of truth、partial success、retry / compensation。
5. Bet / settle / rollback：money correctness、state transition、duplicate handling。
6. MQ projection：source of truth vs projection、lag、rebuild。
7. Report batch：可重跑性、checkpoint、資料範圍與補資料。
8. Request log / audit log：async 化、查問題能力、敏感資訊遮罩。

## 第二輪：Incident / Troubleshooting

9. Order pending：查 order、provider、callback、query、MQ、batch。
10. Player says money missing：查 wallet transaction、bet、settle、provider record、log。
11. Slow query：EXPLAIN、index、資料量、online vs report。
12. DB lock / deadlock：transaction 範圍、更新順序、lock wait。
13. Consumer lag：producer spike、consumer error、DB / provider bottleneck。
14. Redis timeout / hot key：cache role、fallback、source of truth。
15. Thread pool / CPU / memory：runtime symptom 到 root cause。
16. Batch failed halfway：idempotent rerun、checkpoint、manual repair。

## 第三輪：Architecture / Trade-off

17. Monolith vs modular monolith vs microservice。
18. RabbitMQ vs Kafka。
19. Zookeeper / service discovery vs modern platform discovery。
20. Polling job vs event-driven vs CDC。
21. Cache aside vs DB source of truth。
22. Outbox / transaction boundary。
23. Saga / compensation：何時值得，何時過度。
24. Observability：log、metric、trace、dashboard、SLO。

## 第四輪：Git History / Legacy Reconstruction

25. 用 git history 重建 feature context。
26. 用 path-specific history 找風險修改。
27. hotfix / rollback / revert 的訊號。
28. 主管 / team commit 與 Nick direct evidence 的邊界。
29. legacy system map：API -> DB -> MQ -> batch。
30. handover / runbook：讓下一個人能查問題。

## 評分規則

每個 topic 讀完後，只標三種結果：

| 結果 | 意義 |
| --- | --- |
| High | 直接支撐面試、production thinking、troubleshooting 或 system design trade-off |
| Medium | 有背景價值，但不是近期投遞主力 |
| Low | 只是新技術或好奇心，暫不投入 |

## 暫不排入

- 完整重構公司系統。
- 私下把單體改成 Spring Cloud 作為主線。
- 全面畫公司大架構圖。
- 平均掃所有 repo。
- 每週新增新技術清單。
- 把公司 code 內容變成履歷 claim。
