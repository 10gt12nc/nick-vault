# Company System Study

狀態：候選第二個 weekly automation；先建立資料夾、清單與輸出規則，尚未設定排程。

## 一句話定位

`Backend Weekly Learning` 是每週學一個後端主題；`Company System Study` 是每週讀一小塊既有系統，把 code、git history、架構、資料流與痛點整理成可面試、可排查、可做 decision 的知識。

這條線採用二維選題：

```text
先選一個 Project
再選一個 Topic
```

專案清單看 [project-index.md](project-index.md)，主題清單看 [topic-list.md](topic-list.md)。

啟用前資料結構看 [structure.md](structure.md)。手動跑一次用 [manual-run-prompt.md](manual-run-prompt.md)。每週輸出格式看 [packet-template.md](packet-template.md)。

這條線不是要把公司系統完整背熟，也不是要私下重寫公司架構。它的價值是訓練 Nick 從既有系統讀出：

- production flow 怎麼跑。
- source of truth 在哪。
- failure window 在哪。
- 為什麼當初這樣設計。
- 如果今天重做，哪些地方值得改，哪些不值得。
- 面試時如何保守講成 troubleshooting / system design / ownership 思考。

## 與 Backend Weekly Learning 的差異

| 項目 | Backend Weekly Learning | Company System Study |
| --- | --- | --- |
| 來源 | 官方文件、工程文章、通用知識 | 本地 KB、既有系統、code path、git history、flow evidence |
| 目的 | 補 Senior Backend 基本功 | 訓練 production 系統閱讀、決策與排查 |
| 輸出 | 一週一主題 learning packet | 一週一個系統切片 study packet |
| 風險 | 讀太多文章、學習債務 | 變成公司系統流水帳、誇大履歷 claim |
| 邊界 | 不改履歷 / flow | 不碰公司機密、不產生未證實 claim |

## 每週輸出模板

每次只做一個很小的 topic，不建立 backlog。

```text
1. Project：本週讀哪個專案
2. Topic：本週系統切片
3. Local path：本週只碰哪個路徑
4. 為什麼值得讀
5. What：架構 / module / API / DB / MQ / cache / integration 事實
6. Why：目前設計的推測原因與證據
7. Pain：可能的 incident / performance / consistency 風險
8. Decision：當時選擇、替代方案、trade-off、migration cost
9. Git history signal：重要 commit / diff / rollback / hotfix 線索
10. 如果今天重做：保守的改善方向，不假裝已經實作
11. 面試可用 takeaway
12. Evidence boundary
13. explicit non-goal
```

必要時可以附：

- 一張 Mermaid flow / architecture diagram。
- 一小段 pseudo-code / SQL / config example。
- 一段 60-90 秒面試說法。

## 優先級規則

### A 級：80%

直接支撐 2026-2027 Senior Java Backend / Platform Backend 求職：

- Provider callback / timeout / query / reconciliation。
- Wallet / bet-settle / rollback。
- MQ / projection / report rebuild。
- slow query / DB lock / consumer lag / Redis timeout。
- legacy takeover / git history risk reconstruction。

### B 級：15%

Platform Backend 加分，但不能搶主線：

- Outbox / Saga / CDC / Contract Testing。
- OpenTelemetry / metrics / tracing / SLO。
- Kafka vs RabbitMQ、Zookeeper vs modern service discovery、monolith vs microservice 的 trade-off。
- AI-assisted code review / risk review。

### C 級：5%

只作 optional context：

- Lead / Manager / Business / GM 題目。
- Hiring、budget、P&L、organization、culture。
- 大型重構想像題。

## 收錄標準

值得記入本線的內容必須至少符合一項：

- 能支撐面試回答。
- 能提升 production thinking。
- 能提升 troubleshooting thinking。
- 能形成 system design trade-off。
- 能保守說明 Nick 看過 / 維護過 / 分析過的系統邊界。

不收錄：

- 公司機密、商戶、token、真實交易資料。
- 每日工作流水帳。
- 單純 class summary。
- 沒有連到 production 風險的新技術名詞。
- 把他人 commit 或 code-backed 素材包裝成 Nick direct owner。

## 現代化比較規則

可以比較新舊技術，但只能當 decision thinking，不是自動升級任務。

例如：

- 看到 Zookeeper，可以比較 Eureka / Spring Cloud / Kubernetes service discovery。
- 看到 RabbitMQ，可以比較 Kafka。
- 看到 monolith，可以比較 modular monolith / microservice。
- 看到 polling job，可以比較 event-driven / CDC / outbox。

但每次都要回答：

```text
現有痛點是什麼？
替代方案解決哪個問題？
會增加什麼成本？
有哪些服務 / DB / MQ / deployment 會被影響？
現在值得做嗎？
```

如果答不出現有痛點，就只記成 `觀察 / 暫不建議`。

## Automation 邊界草案

未來若設定排程，prompt 應該要求：

- 讀本資料夾與必要 KB。
- 先讀 `project-index.md`，選一個 project；再讀 `topic-list.md`，選一個 topic。
- 每週只選 topic-list 的一個 topic。
- 每週只碰一個 project 的一小段 scope。
- 不全掃公司 repo。
- 不修改履歷、自傳、Story、正式 flow、`04 / 05 / 08 / 17 / 19`。
- 若讀公司 code，必須去識別化、標 evidence level，避免 secret。
- 可以更新本資料夾的 study log / topic progress。
- 如果產生有面試價值的句子，只列 `KB 建議`，不自動回填正式面試材料。

## 目前下一步

先不用設定 automation。先用 `manual-run-prompt.md` 手動跑 1-2 次，確認 packet 大小、深度與安全邊界都符合需求，再決定是否設定排程。
