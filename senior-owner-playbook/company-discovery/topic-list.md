# Company Discovery Target List

狀態：候選 discovery target，不是自動開工授權。

## 使用規則

- 每次必須先依 `project-index.md` 選 1 個 project，再選 1 個 discovery target。
- 每次最多保存 0-3 個 discovery。
- 只找新 evidence；不重講既有 KB。
- 如果只找到既有內容，回報 `No new high-value knowledge discovered.`。
- 讀公司 code 時要去識別化，不寫 secret、商戶、內部 URL、真實交易資料。
- 不把分析素材誇大成 Nick direct owner。

## 第一輪：Production Risk Discovery

1. MQ publish fail / callback accepted but not processed。
2. Duplicate callback / duplicate refund / duplicate settle guard。
3. DB transaction boundary vs external HTTP / MQ boundary。
4. Unknown state / timeout handling。
5. Manual repair / admin operation boundary。
6. Retry count / retry stop condition / DLQ-like behavior。
7. Compensation / rollback / refund edge cases。
8. Source of truth mismatch。

## 第二輪：Code / DB / Config Discovery

9. SQL / index / unique key assumptions。
10. Partition / sharding / table routing edge cases。
11. Redis key / lock / TTL / cache consistency risks。
12. Config default / feature flag / routing risk。
13. TODO / FIXME / suspicious comments。
14. Duplicated provider logic。
15. Dead code / unreachable branch。
16. Exception swallowed / only logged。

## 第三輪：Git History Discovery

17. Hotfix commit that reveals production pain。
18. Revert / rollback commit。
19. Commit touching retry / idempotency / status guard。
20. Commit touching DB schema / index / migration。
21. Historical refactor that reveals legacy constraint。
22. Path-specific history around a risky method。
23. Nick direct evidence vs team context distinction。
24. Commit message that should influence interview boundary。

## 第四輪：Architecture Decision Discovery

25. Hidden service boundary。
26. Unusual data flow between services。
27. Legacy constraint blocking clean design。
28. Why current MQ / scheduler / cache exists。
29. Modernization opportunity with real pain evidence。
30. Migration cost that makes a tempting refactor not worth it.

## 暫不排入

- Flow happy path summary。
- 30 秒 / 90 秒面試稿重寫。
- 已有 KB 的 trade-off 重述。
- 完整重構公司系統。
- 私下把單體改成 Spring Cloud 作為主線。
- 全面畫公司大架構圖。
- 平均掃所有 repo。
- 每週新增新技術清單。
- 把公司 code 內容變成履歷 claim。
