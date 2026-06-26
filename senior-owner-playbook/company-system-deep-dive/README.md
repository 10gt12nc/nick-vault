# Company System Deep Dive

狀態：候選第二個 weekly automation 已暫停推進；目前只保留為未來可重啟的學習設計草案，尚未設定排程，也不併入 `Backend Weekly Learning` 當固定 company case lens。

## 一句話定位

`Backend Weekly Learning` 是外部技術學習；本資料夾原本設計為 `Company System Deep Dive`，用公司真實系統當教材，系統性學會 production flow、code reading、data flow、architecture、engineering decision、替代方案、重構思維與面試表達。

這條線不是只做 discovery，也不是整理漂亮文章。Discovery 是最後一節；主體是 deep dive。

最新收斂：先不要推進第二排程。若未來重啟，主軸應從 `Company System Deep Dive` 再修正為更不發散的：

```text
System Capability Deep Dive
通用系統能力深度研究
```

也就是：

```text
通用能力主題
↓
公司 code / legacy system 當案例
↓
抽象成可遷移能力
↓
下間公司也看得懂
```

不要變成：

```text
iwin 有什麼就學什麼
antplay 有什麼就學什麼
五個 repo 都盤完
```

```text
公司系統 / code / git / DB / MQ
↓
理解流程
↓
讀懂程式
↓
學工程判斷
↓
比較替代方案
↓
形成重構思維與面試素材
↓
記錄 0-3 個 new findings
```

## 與 Backend Weekly Learning 的分工

| 項目 | Backend Weekly Learning | Company System Deep Dive |
| --- | --- | --- |
| 來源 | 官方文件、工程文章、通用技術 | 公司 code、既有 KB、git history、DB / SQL、config、architecture |
| 目的 | 補市場通用 Senior Backend 基本功 | 用真實系統訓練 production / architecture / refactor thinking |
| 輸出 | 一週一主題 learning packet | 一次一個公司系統主題 deep-dive packet |
| 新發現 | 可有可無 | 最後列 0-3 個 new findings，不硬湊 |
| 禁止 | 學習債務 | 重複整理既有 KB、全掃 repo、誇大履歷 claim |

目前決策：

```text
Backend Weekly Learning = active
Company System Deep Dive = paused reference
第二排程 = 不做
```

不刪除本資料夾，因為刪掉容易讓未來重新討論一次；也不移到 `archive/`，因為 `archive/` 不是長期資料位置。最乾淨的狀態是留在原處並明確標示暫停。

## 固定章節

每次 deep dive 固定包含：

1. Topic。
2. System Context。
3. Runtime Flow。
4. Code Reading Map。
5. Data Flow / Data Structure。
6. Engineering Thinking。
7. Production Risk。
8. Technology Comparison。
9. If Redesigning Today。
10. Learning Level Check。
11. Interview Value。
12. New Findings。
13. KB Update。

## 學習層級

每次要標示這次學到哪一層：

| Level | 意義 |
| --- | --- |
| L1 Flow | 知道這個功能在系統裡做什麼 |
| L2 Code | 知道 Controller / Service / DAO / MQ / Job 怎麼串 |
| L3 Why | 知道為什麼這樣設計，包含 transaction、retry、timeout、idempotency |
| L4 Alternatives | 知道常見替代方案與 trade-off |
| L5 Redesign | 知道如果今天重構，哪些先做、哪些不該動 |
| L6 Interview | 知道怎麼保守講給面試官聽 |

## 課綱

課綱看 [curriculum.md](curriculum.md)。Prompt 只是執行器；課綱才是長期方向。

大主軸：

- System Architecture / Module Boundary。
- Production Flow。
- Code Reading。
- Database Design。
- Transaction / Consistency。
- MQ / Event / Async。
- Cache / Redis。
- Provider Integration。
- Observability。
- Incident / Troubleshooting。
- Git History / Design Evolution。
- Refactoring。
- Technology Comparison。
- System Design。
- Interview Mapping。

## 選題方式

選題先不跟履歷 / 自傳掛鉤，避免把公司系統學習侷限成求職素材。第一層先看通用能力，再找公司案例，最後才決定是否需要 claim gate。

若未來重啟，優先順序應是：

```text
Universal Capability Curriculum
↓
Company Case Pool
↓
Transferable Pattern
↓
Deep Dive Template
```

目前資料夾裡的 `project-value-map.md` 只作案例池，不作完整 inventory。

舊版 project-first 順序如下，保留作參考但不作主線：

```text
Project Value Map
↓
Project Index
↓
Topic List
↓
Deep Dive Template
```

先看 [project-value-map.md](project-value-map.md)，判斷每個 project 底下哪些系統 / 功能 / 架構可能作案例來源；不要把它當五個 repo 的完整清單。

再做二維選題：

```text
先選一個 Project
再選一個 Deep Dive Topic
```

專案清單看 [project-index.md](project-index.md)，主題清單看 [topic-list.md](topic-list.md)。

啟用前資料結構看 [structure.md](structure.md)。手動跑一次用 [manual-deep-dive-prompt.md](manual-deep-dive-prompt.md)。輸出格式看 [deep-dive-template.md](deep-dive-template.md)。

## 重複既有 KB 的防呆

可以引用既有 KB 避免重掃，但不能只重包既有 KB。

如果某章節既有 flow 已寫過，應該只摘要一兩句，然後補：

- code reading map。
- 更清楚的 call graph。
- data structure。
- technology comparison。
- redesign thinking。
- learning level。
- new findings。

若沒有新 discovery，不代表 deep dive 失敗；但要明確寫：

```text
No new A-level finding this run.
```

## 邊界

不做：

- 不全掃公司 repo。
- 不平均掃所有 module。
- 不保存公司機密、商戶、token、內部 URL、真實交易資料。
- 不把 Deep Dive 預設掛到履歷、自傳、Story、Flow、`04 / 05 / 08 / 17 / 19`；只有 Nick 明確要求回填時才做 claim gate。
- 不把 code-backed / team-context / learning-only 包裝成 Nick direct owner。
- 不把 Spring Cloud / Kafka / K8s / 微服務現代化想像變成自動改造任務。

## 目前下一步

先不用設定 automation。先用 `manual-deep-dive-prompt.md` 手動跑 1-2 次，確認深度、章節、學習層級與安全邊界都符合需求，再決定是否設定排程。
