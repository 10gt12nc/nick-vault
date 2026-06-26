# Career / Industry KB Evolution Plan

狀態：草案，待 Nick 調整；目前結論是先不升級 v2。

落地狀態：

- Backend weekly learning 的 KB 支撐檔已建立在 `senior-owner-playbook/`：
  - `backend-weekly-plan.md`
  - `backend-learning-log.md`
  - `backend-weekly-template.md`
  - `backend-weekly-writing-guideline.md`
- 第一版只做 Senior Backend / Platform Backend 每週 capability builder；日語暫不排入主學習排程。
- automation 若正式啟用，預設只維護上述 weekly 檔案，不改 `04 / 05 / 08 / 17`、三個故事稿或 production flow 文件，除非 Nick 明確要求。
- 2026-06-23 決策：公司電腦才是工作學習與 KB 維護主機；家裡電腦原則上不跑工作學習 automation，避免休息時間被工作排程打斷。
- `backend-weekly-learning` 的實際啟用狀態以該台電腦的 Codex app / `~/.codex/automations/.../automation.toml` 為準；換電腦時用本檔 `Canonical automation prompt` 重建，不假設舊電腦狀態會同步。
- 2026-06-24 確認：目前這台是公司電腦，`backend-weekly-learning` 可保持 `ACTIVE`；目前本機設定為每週一 09:00 執行。
- 2026-06-26 候選第二條排程暫停推進。前面曾收斂出 `Company System Deep Dive`，資料夾在 `company-system-deep-dive/`，但最新結論是先不要設定第二排程，也不要再沿著 project inventory 擴張。若未來重啟，方向應調整為 `System Capability Deep Dive`：topic-first、company-code-second。也就是先選通用系統能力，例如 transaction boundary、idempotency、MQ retry、projection rebuild、cache consistency、service discovery、rollout、observability、slow query、auth / RBAC、legacy refactor，再用 iwin / antplay / ugsoft / DevOps / usproject 的 code 或 legacy system 當案例，最後抽象成下間公司也能用的 transferable pattern。`project-value-map.md` 只保留為案例池 / learning value map，不是完整 inventory、不是必讀清單、不是履歷 claim map、不是待辦 backlog。
- 2026-06-26 後續決策：目前不把 `company-system-deep-dive` 併入 `Backend Weekly Learning`，也不做固定 company code deep dive。`Backend Weekly Learning` 維持外部通用技術 + production / interview 的小型 weekly capability builder，但可加入最多 5 bullets 的 `Known Production Case Lens`：只用既有 notes 或泛化 production analogy，不掃公司 repo、不創造 direct ownership、不形成每週必做項目。`company-system-deep-dive/` 不刪、不移到 archive，只保留在原位作 paused reference，避免未來重新討論一次。
- 2026-06-26 補強：`Backend Weekly Learning` 的真正目標不是技術快訊，而是 `Weekly Senior Backend Capability Builder`。每週用單一主題練 production、trade-off、incident、system design、decision making 與 interview expression。來源數量從最多 3 篇收斂到最多 2 篇：優先 1 篇官方文件，第 2 篇只有在提供明顯 practical value 時才放，且正式輸出必須有實際連結，不得只放佔位文字。新增 `Technology Landscape`、`Knowledge Boundary`、`One Common Misconception` 與條件式 `Future Direction`；用 Learn Now / Learn Later / Awareness Only 和 Must / Should / Ignore 管住廣度，避免公司沒有 Kafka / Nacos / K8s 就完全沒概念，也避免把所有相關技術都變成本月待辦。

本檔先記錄兩個想法：

1. 用 Codex automation 定期推送少量高品質材料，降低 Nick 主動找資料的成本。
2. 未來如果進新公司，`nick-vault` 是否能從求職履歷庫，升級成長期職涯 / 工作復盤 / 產業 KB / skill source。

這份不是自動開工授權，也不是新的必做主線。正式建立 automation、改目錄規格、或把工作內容納入 KB 前，都要 Nick 再確認。

## 一句話結論

可以做，但要守住四條線：

1. 自動化只推少量材料，不製造資訊焦慮。
2. Vault 可以升級，但不要變成每日流水帳。
3. 新公司工作內容只能抽象化、去識別化、證據分層，不放公司機密。
4. 在拿到下一份工作前，`nick-vault` 維持 v1 `Job Search Vault`；v2 結構只保留為到職後候選，不現在開工。

目前優先順序：

1. `nick-vault` 收口，不再大改結構。
2. 三個故事熟悉。
3. 12 條 flow 熟悉。
4. 30 題核心面試題。
5. 開始投遞，取得市場回饋。

任何新建議都要先問：

- 是否直接提升面試勝率？
- 是否直接提升履歷品質？
- 是否直接提升薪資談判能力？

若否，預設列為可選加強或暫不建議，不得自動升級為必做事項。

## Part 0：30 年能力主幹

這份能力樹是長期羅盤，不是新增待辦。未來 20-30 年不要一直替能力樹加枝葉；原則是反覆深化主幹，把知識轉成 production case、文件、決策與面試表達。

### 終身底層能力

這些能力不分職級，會長期累積：

- Learning System：KB、Learning Log、Weekly Review、技術文章篩選、不累積學習債務、能把知識轉成案例與回答。
- AI 協作：Prompt Engineering、Context Engineering、Harness Engineering、Agent Workflow、AI Code Review、AI Risk Review、AI-assisted Documentation / Debugging / Testing。
- Writing：ADR、Design Doc、Incident Report、Handover、Runbook、Proposal、Decision Memo。
- 個人穩定性：健康、睡眠、財務穩定、緊急預備金、長期投資、情緒韌性、長期職涯節奏。

### 30 年能力主幹

濃縮成 12 條：

1. Backend Core。
2. Database / Cache / MQ。
3. Transaction / Consistency。
4. Production Flow。
5. Incident / Troubleshooting。
6. Observability / Reliability。
7. Distributed System。
8. Architecture / Migration。
9. Security。
10. AI-assisted Engineering。
11. Communication / Ownership / Decision Making。
12. Business Thinking / Personal Stability。

### 分階段方向

2026-2027：

- Senior Backend 面試。
- 三個故事。
- 12 條 Flow。
- Transaction / MQ / DB / Redis / Incident。
- 市場驗證。
- 緊急預備金。

2028-2030：

- Platform Backend。
- System Design。
- Observability。
- Reliability。
- Ownership。
- Cross-team Impact。

2030 以後：

- System Owner。
- Tech Lead。
- Business Thinking。
- Strategy。
- Organization。
- 更高階決策。

### 不追的東西

這些只能觀察，不得變成主線：

- 每個新框架。
- 每個新語言。
- 每個 AI Model。
- 每個雲端服務。
- 每個熱門架構名詞。

真正該追的是：

- Backend 趨勢：Java LTS、Spring Boot、Kafka、OpenTelemetry、PostgreSQL / MySQL、Redis、K8s。
- Architecture 趨勢：Modular Monolith、Event Driven、Platform Engineering。
- AI Engineering 趨勢：Agent Workflow、Context Engineering、Harness Engineering、AI Code Review、AI Testing。

### 近期邊界

目前仍以 `Senior Java Backend -> Platform Backend` 為主。Engineering Manager、Business Owner、組織管理與高階商業決策只作長期方向，不排入近期週排程。Personal Finance 與健康屬於長期穩定性，會影響 20 年競爭力，但不放進 backend weekly 主線。

### 學位 / 證照 / 高階管理進修邊界

2026-06-26 校正：Nick 近期不用把資工碩士、MBA、PMP 或架構師證照變成主線。它們可以是長期選項，但不能壓過 Senior Backend 的 production case、system design、incident 與 platform capability。

分階段看：

- 未來 3-5 年：先站穩 Senior Java Backend / Platform Backend。主投資放在 production flow、系統設計、分散式、一致性、JVM / MySQL / Redis / Kafka、英文文件閱讀與面試表達。
- 5-10 年：若開始帶人、負責跨部門決策、預算、技術 roadmap，再補 Engineering Manager / Director 需要的管理能力。PMP 仍是可選，不是必要門票。
- 10 年以上：若確定要走 GM / BU Head / 高階經營管理，再評估 MBA 或企業管理進修。此時重點會變成 P&L、商業策略、財務、法務風險、人才與組織管理。

CTO 與 GM 的路線不同：

- CTO / Architect 路線：技術深度、架構決策、團隊技術領導、系統穩定性是核心。MBA 通常不是必要。
- Engineering Manager / VP Engineering 路線：招募、績效、預算、跨部門協作與組織規劃開始重要。
- GM / 經營線：P&L、業務、行銷、財務、法務、組織與商業決策變成核心。MBA 的價值比技術線高，但仍不是決定性條件。

判斷規則：

```text
證照 / 學位只有在能直接幫助下一份工作、符合目標公司要求、或補足正在承擔的職責時，才排入行動。
否則只放在長期能力樹，不進本月待辦。
```

### Capability Tree by Level

這份能力樹是 `Capability Coverage`，不是新的學習 backlog。用途是每 3-6 個月檢查一次長期能力是否偏科，不是看到缺口就立刻重寫 48 週課綱。

#### Level 1：Junior Backend

目標：把功能做好，不拖累團隊。

- Java 基礎：Java Language、OOP、Collection、Exception、IO / NIO、JUC 基礎、Lambda / Stream、Reflection、Generics。
- Spring 生態：Spring Core、Spring Boot、Spring MVC、Spring AOP、Spring Transaction、Validation、Spring Security 基礎。
- Database：SQL、Index、Transaction、Lock、MVCC、Explain、Query Optimization。
- Redis：Data Structure、Cache Aside、Expire、Hot Key、Cache Penetration、Cache Breakdown、Cache Avalanche。
- MQ：RabbitMQ / Kafka 基礎、Producer、Consumer、Retry、DLQ、Ordering、Idempotency。
- API：REST、Versioning、Pagination、Error Code、DTO、Validation、OpenAPI。
- Git：Branch、Merge、Rebase 理解、PR。
- Testing：Unit Test、Integration Test、Mock。
- Linux：基本指令、Log、Process、Network。
- 基本軟實力：溝通、問問題、Code Review、文件閱讀。

#### Level 2：Senior Backend

目標：負責一個核心服務，能處理 production。這是 Nick 目前主線。

- Production Thinking：Transaction Boundary、State Machine、Idempotency、Retry、Compensation、Failure Window、Reconciliation、Timeout、Circuit Breaker。
- System Design：High Availability、Scalability、Consistency、CAP 理解、CQRS 理解、Outbox、Inbox、Saga 理解、Service Boundary。
- Distributed System：Service Discovery、Config、Load Balance、Cache Cluster、DB Replication、Partition、Sharding。
- Observability：Logging、Metrics、Tracing、Alert、SLI / SLO 理解、OpenTelemetry 理解與基本使用。
- Performance：JVM、GC、Thread Pool、CPU、Memory、IO、DB Performance、Redis Performance。
- Incident：RCA、Postmortem、Debugging、Production Troubleshooting、Monitoring。
- Architecture：Layered、Hexagonal 理解、Event Driven、Modular Monolith、Microservice Trade-off。
- Security：Authentication、Authorization、JWT、OAuth2 理解、Signature、Replay Attack、Secret Management。
- Code Quality：Refactoring、Legacy Takeover、Technical Debt、ADR、Design Pattern 適度。
- AI 協作：Prompt、Context、Spec、AI Review、AI Pair Programming。
- 軟實力：Ownership、Decision Making、Risk Communication、Technical Writing、Cross-team Communication、Estimation。

#### Level 3：Staff / Lead

目標：跨 team 影響力。

- 技術：Architecture Evolution、Migration、Platform、Standards、Engineering Productivity、Developer Experience。
- 組織：Mentoring、Technical Review、Hiring、Planning、Roadmap。
- 決策：Trade-off、Cost、ROI、Buy vs Build、Technical Strategy。

#### Level 4：Engineering Manager / 技術主管

目標：把團隊做好。

- 人：Coaching、Feedback、Hiring、Performance Review、Conflict Resolution。
- 團隊：Team Topology、Delivery、Priority、Risk Management、Process Improvement。
- 商業：Product Thinking、Cost Awareness、Budget、Stakeholder Management。

#### 全職涯硬實力

```text
Java
Spring
Database
Redis
MQ
Linux
API
Transaction
Distributed System
System Design
Observability
Performance
Security
Testing
CI/CD
Container（Docker / Kubernetes 必要理解）
Architecture
AI-assisted Engineering
```

#### 全職涯軟實力

```text
Communication
Writing
Reading
Ownership
Decision Making
Risk Thinking
Troubleshooting
Prioritization
Learning Ability
Business Thinking
Mentoring
Leadership
```

#### Nick 每天看的 8 大能力版

完整清單只作 coverage，不作日常焦慮來源。日常只看 8 大能力：

| 能力 | 目前優先 |
| --- | --- |
| Java / Spring | 最高 |
| Database / Redis / MQ | 最高 |
| Production & Incident | 最高 |
| System Design & Distributed | 高 |
| Observability & Performance | 高 |
| Security & API | 中 |
| AI-assisted Engineering | 中 |
| Communication / Ownership | 高 |

### 學習型能力 vs 責任型能力

30 年能力樹裡有兩種能力，準備方式不同。

第一種是可以靠學習補到 `70-80%` 的能力，例如：

- Java。
- JVM。
- Spring Transaction。
- MySQL。
- Redis。
- Kafka。
- OAuth2。
- OpenTelemetry。
- Outbox Pattern。
- Saga。
- K8s 基礎。

這類能力可以透過 `讀書 -> 文章 -> Lab -> Side Project` 補強，適合放進 weekly learning。

第二種是不做過永遠只有理論的能力，例如：

- Incident Handling。
- Ownership。
- Decision Making。
- Risk Communication。
- Cross-team Collaboration。
- Leadership。
- Business Thinking。

這類能力需要真實責任，不能只靠讀文章補完。可以先記、先看、先建立 vocabulary，但不能把它包裝成已具備成熟 owner 能力。真正成長路徑是：

```text
做過
↓
踩坑
↓
整理
↓
面試表達
↓
市場驗證
```

職級越高，經驗比重越高：

| 階段 | 學習 | 經驗 |
| --- | --- | --- |
| Junior | 70% | 30% |
| Mid | 50% | 50% |
| Senior | 30% | 70% |
| Owner | 20% | 80% |

因此近期最有價值的不是再擴主題清單，而是把現有經驗整理成四種輸出：

1. Story。
2. Flow。
3. Incident。
4. Decision。

然後拿去市場驗證。這比多學一個框架更能往 Senior Backend / Platform Backend 前進。

### A / B / C 時間分層

Nick 的盲點不是不努力，而是容易把 `未來 10 年需要的能力` 和 `未來 6 個月需要的能力` 混在一起。長期能力可以記，但近期時間要分層。

#### A 級：80% 時間

直接影響下一份工作，2026 下半年主力放這裡：

- 面試：定位、履歷、自傳、三個故事、12 條 Flow、30 題核心。
- Backend Core：Transaction、MySQL、Redis、Kafka、MQ、JVM。
- Production：Callback、Wallet、Projection、Idempotency。
- Incident：排查、止血、根因分析。
- System Design：Trade-off、Boundary、Migration。

#### B 級：15% 時間

知道即可，作為 Platform Backend 加分：

- Platform：Outbox、Saga、CDC、Contract Testing。
- Observability：OpenTelemetry、Metrics、SLO。
- AI 協作：Codex、Context Engineering、Agent Workflow。

#### C 級：5% 時間

現在不用投入大量時間，可以收藏與觀察：

- Lead：Mentoring、Hiring、Code Review Strategy。
- Manager：1:1、Performance Review、Budget。
- Business：P&L、Pricing、Strategy。
- GM：組織、文化、營運。

這些有價值，但不是 2026-2027 Senior Backend 求職主線。可以記在能力樹，不要變成本月待辦。

2026 下半年健康比例：

```text
80% Senior Backend / 面試 / Production / Incident / System Design
15% Platform Backend / AI 協作
5% Lead / Business / GM 能力
```

判斷句：

```text
想太遠不是問題。
問題是把三年後、五年後、十年後要學的東西，全部搬到今天的待辦清單。
```

### 懂很多 vs 能扛系統

2026-06-26 校正：Nick 容易把「懂很多技術」誤認成「已經資深」。實際上，Senior Backend / Platform Backend 的市場訊號不是懂多少框架，而是能不能扛住代表性 production flow。

資深感不是：

- 把公司 code 全部背熟。
- 私下把單體改成 Spring Cloud / microservices。
- 本地做完整 K8s / CI/CD / observability lab。
- 追完整新架構名詞。

資深感是：

- 看懂一條 flow 的 source of truth。
- 說清 state transition 和 terminal state。
- 找出 failure window。
- 出事時知道先查哪個 key、哪張表、哪段 log、哪個 MQ / job。
- 能提出止血、retry、compensation、reconciliation、rollback 或人工處理邊界。
- 能保守說明自己負責到哪裡，不把 code-backed / learning-only 誇大成完整 owner。

2026 下半年求職主線仍是證明 Nick 能扛 3 條代表性 production flow：

1. Provider Integration。
2. Wallet / Bet-Settle / MQ。
3. Legacy Takeover / Troubleshooting。

本地架構練習可以做，但只佔 `10% lab`。如果要做，應縮成單條 flow 的最小 lab，例如 payment callback / wallet / projection / outbox / observability，而不是重構整個公司系統或把投遞延後到微服務全跑通。

## Part A：自動化推送規劃

### 目標

自動化的目標不是讓 AI 每週產大量文件，而是讓 Nick 週期性收到「少量、可讀、可行動」的材料。

更精準地說，automation 是：

```text
每週篩選器 / 提醒器，不是主線學習系統。
```

合格的推送應該能回答：

- 這週我該看什麼？
- 為什麼跟工作、面試或日語有關？
- 我只花 20-40 分鐘能做什麼？
- 哪些不用現在做？

不合格的推送：

- 一次塞 20 篇文章。
- 產生長篇摘要但沒有行動建議。
- 把基本功、刷題、日語、產業文章全部變成壓力。
- 自動寫入 KB，造成未確認內容污染正式文件。

### 建議先開 1 個 automation

第一階段只建議先開一個：

```text
每週 Backend / 面試 / 日語輕量週報
```

頻率：

- 每週一次。
- 建議週一早上或週日晚間。
- 不建議一開始每天推送。

輸出長度：

- 3-5 個 backend / platform / AI coding 重點。
- 3 題面試小練習。
- 5-10 個日語工作句。
- 1 個本週建議行動。
- 1 個明確「本週不做」。

消化規則：

- 每週最多吸收 1-2 個主題。
- 沒讀完不用補，不累積債務。
- 不把未讀項目滾成下一週 backlog。
- 不自動改 KB，除非 Nick 明確要求「這點記 KB」。

建議輸出三分法：

```text
本週必看：最多 2 個
可選補充：最多 5 個
暫不建議：避免分心
```

### 推送內容模組

#### 1. Backend / Platform 知識週報

主題範圍：

- Java / Spring Boot / Spring transaction
- MySQL / PostgreSQL / index / slow query
- Redis consistency / lock / cache
- MQ / Kafka duplicate consume / retry / ordering
- payment / wallet / provider callback / idempotency
- observability / incident / rollback / reconciliation
- AI-assisted coding review / Codex / Claude Code 實務

篩選規則：

- 優先挑跟 Nick 既有 production cases 能接上的內容。
- 每週最多 3-5 個重點，但正式 weekly packet 仍以 canonical prompt 的最多 2 篇 references 為準。
- 每個重點要附「跟 Nick 哪條 flow / 面試 case 有關」。
- 如果只是流行技術名詞，先略過。

輸出格式：

```text
本週 Backend 重點
1. 主題
   - 為什麼重要
   - 對應 Nick case
   - 只要先懂哪一點
   - 不用現在深挖什麼
```

#### 2. 面試題小練習

頻率：

- 跟週報一起推即可。
- 不建議獨立變成每天刷題。

每週題型：

1. Java / Spring / transaction 1 題。
2. SQL / Redis / MQ 1 題。
3. Production case 追問 1 題。

原則：

- 題目要能接回 `19-interview-coaching-question-bank.md` 或主力 flow。
- 不新增泛用 300 題題庫。
- 答不出才回補 `interview-practice/` 或 `19`，不要每題都寫正式 KB。

範例：

```text
本週 3 題
1. 為什麼 self-invocation 會讓 @Transactional 失效？
2. MQ consumer 重複消費時，你會用哪個欄位做 idempotency key？
3. payment callback 已上分，但 provider 又重送一次，你怎麼判斷與阻擋？
```

#### 3. 日語工作推送

目標：

- 服務未來工作、遠端協作、日商 / 外商環境、日文面試或 Slack / meeting 溝通。
- 先求能用，不做完整日語課程。

每週內容：

- 5-10 個工作常用句。
- 3 個會議 / Slack / 報告情境。
- 1 小段自我介紹、進度回報或問題回報修正。

範例主題：

- 進度回報：今天完成什麼、卡在哪、明天做什麼。
- 問問題：我不確定規格，想確認 A/B。
- 風險回報：這段如果今天上線，可能有資料不一致風險。
- Code review：這裡我擔心 retry 後會重複副作用。

日語推送不需要寫進正式履歷 KB；除非 Nick 明確要建立日語面試稿或工作日語包。

### Automation 建立前確認項

正式建立前要 Nick 確認：

1. 頻率：每週一次或每週兩次。
2. 時間：週一早上、週日晚間、或其他時間。
3. 內容比例：Backend / 面試 / 日語 是否都放同一封。
4. 是否只推送在 Codex thread，不自動改檔。
5. 是否需要查網路最新文章；若需要，推送時要附來源與日期。

建議第一版：

```text
每週一早上，推送 Backend + 面試 + 日語輕量週報；只回在 Codex thread，不自動寫入 KB。
```

### 公司電腦 automation 建議

Nick 已決定：

- 公司電腦是 KB 維護與工作學習主機。
- 家裡電腦原則上不跑工作學習 automation；若短期測試啟用，需另外確認並避免變成長期工作提醒。
- 公司電腦每週一執行 backend weekly learning packet；目前本機設定是 09:00，若工作節奏需要可改為 08:00。
- 公司電腦可以維護 `backend-learning-log.md` 與 `backend-weekly-plan.md`，但仍不得碰履歷、自傳、故事稿、正式 flow 或 project 文件，除非 Nick 明確要求。
- 預設可以 commit，但不 push；push 仍由 Nick 明確要求。

公司電腦建立 automation 時可貼；時間可依實際作息設定，目前本機用每週一 09:00：

```text
每週一執行 Nick 的 backend weekly learning packet，並維護 nick-vault KB。

先讀：
- AGENTS.md
- senior-owner-playbook/README.md
- senior-owner-playbook/backend-weekly-template.md
- senior-owner-playbook/backend-weekly-plan.md
- senior-owner-playbook/backend-learning-log.md
- senior-owner-playbook/19-interview-coaching-question-bank.md
- senior-owner-playbook/22-career-industry-kb-evolution-plan.md

依 template 產出一週一主題的 Senior Java Backend / Platform Backend 學習週報。

規則：
- 每週只聚焦 1 個主題
- 最多 2 篇高品質來源，優先 1 篇官方文件，第 2 篇只有在提供明顯 practical value 時才放
- 依 `backend-weekly-template.md` 輸出，不要用極短 checkpoint 覆蓋 template
- 包含 beginner-to-senior 解釋、1 個小型 code / pseudo-code 範例、1 個簡單 Mermaid 架構 / flow 圖
- 不處理日文
- 不建立巨大 backlog
- 不改履歷、自傳、故事稿、正式 flow 或 project 文件，除非 Nick 明確要求
- 所有內容標註：已做過 / 參與過 / 分析過 / 可作為目標 / 待驗證
- 只在安全且符合 KB 規則時，更新 backend-learning-log.md 與 backend-weekly-plan.md
- 更新後執行 Relationship Check 與 git diff --check
- 可以 commit
- 不 push，除非 Nick 明確要求

輸出：
- 本週摘要
- 最多 2 篇必看來源
- Beginner-to-Senior 解釋
- 小型 code / pseudo-code 範例
- Mermaid 架構 / flow 圖
- Senior 面試題
- 30 分鐘任務
- 更新了哪些 KB 檔案
- 下一週主題
- 本週不建議做什麼
```

### Canonical automation prompt

若換電腦或重建 Codex automation，直接使用下方英文 prompt；排程時間依該台電腦用途調整，目前公司電腦可用 `每週一 09:00`。家裡電腦預設不啟用工作學習 automation；實際是否 ACTIVE / PAUSED 以該台電腦的 Codex app 狀態為準。

2026-06-26 後的封版方向：Backend Weekly Learning 是 `capability layer`，不是履歷 / 自傳 / Story 產生器。每週優先建立 `Production Thinking -> Troubleshooting -> Trade-off -> System Design -> Interview`。Known production case、Story、Flow、Resume relevance 只在自然適用時才連結；若不自然，使用一般 backend production / system design 情境即可。避免把 OpenTelemetry、Rate Limit、Service Boundary、API Design 等通用能力硬塞回 Provider / Wallet / Legacy，導致學習被既有履歷框住。

2026-06-26 writing guideline 補強：Prompt、Template 與 Plan 先封版；後續若 weekly packet 出現模板味、重複講同一組 production concept、或每週重新介紹 beginner concept，優先調整 `backend-weekly-writing-guideline.md`，不要繼續膨脹 prompt / template。四個檔案職責固定為：`backend-weekly-plan.md` 決定學什麼；`backend-weekly-template.md` 決定輸出哪些章節；`backend-weekly-writing-guideline.md` 決定每一週怎麼寫；`backend-learning-log.md` 累積每週真正值得保留的內容。

```text
Run Nick's weekly Senior Java Backend / Platform Backend capability builder packet for nick-vault.

Read only as needed:
- AGENTS.md
- senior-owner-playbook/README.md
- senior-owner-playbook/backend-weekly-template.md
- senior-owner-playbook/backend-weekly-writing-guideline.md
- senior-owner-playbook/backend-weekly-plan.md
- senior-owner-playbook/backend-learning-log.md
- senior-owner-playbook/19-interview-coaching-question-bank.md
- senior-owner-playbook/22-career-industry-kb-evolution-plan.md

Follow senior-owner-playbook/backend-weekly-template.md and senior-owner-playbook/backend-weekly-writing-guideline.md.
Do not replace the template with a short checkpoint.
Treat each week as the next chapter of the same book: build on previous weeks, avoid re-explaining recently covered beginner concepts, and introduce at least one genuinely new production insight.

Produce one focused weekly capability builder packet with:
1. this week's topic from backend-weekly-plan.md
2. why this matters in production
3. at most 2 high-quality references
   - Prefer one official document.
   - The second may be an engineering blog or conference talk only if it adds significant practical value.
   - Each reference must include a real URL, source name, and why it is worth reading.
   - Prefer stable references. Avoid low-quality blogs if equivalent official documentation exists.
4. beginner-to-senior explanation
5. 1 small Java / Spring / SQL / pseudo-code example
6. 1 simple Mermaid architecture / flow diagram
7. 3 focused learning points
8. 3 Senior interview questions
9. 1 suggested 30-minute action
10. 1 explicit non-goal

Also paste the full weekly learning packet in the chat response, not only a summary.

Before producing the packet, choose ONE weekly mode:
- Concept Mode: core concept / mechanism
- Troubleshooting Mode: incident diagnosis / production debugging
- Trade-off Mode: design choice / migration / comparison
- Interview Mode: answer structure / senior judgment

Add these sections to the weekly packet:

1. Known Production Case Lens
Keep this section concise, maximum 5 bullet points.
If this topic naturally connects to Nick's documented production experience, explain the connection.
Otherwise, connect it to general backend production scenarios or common system design situations.
Do not force a resume, Story, Flow, or known-case connection.
Do not scan company repo.
Do not turn this into company-system deep dive.
Do not invent direct ownership.
Clearly distinguish:
- verified from Nick's documented experience
- inferred from general engineering practice
- speculative ideas for future improvement
Never present assumptions as facts.

2. Mini ADR
Summarize one decision angle:
- Context
- Decision
- Alternatives
- Consequences
- When this decision becomes wrong

3. Observability Anchor
For this topic, define:
- 1 useful log
- 1 useful metric
- 1 useful trace/span
- 1 alert condition
- 1 thing that should not alert

4. Technology Landscape
For this topic:
- related technologies
- current industry mainstream
- when each technology is a better fit
- Learn Now
- Learn Later
- Awareness Only
Explain why.
Never recommend learning every related technology.

5. Knowledge Boundary
For this topic:
- Must Understand
- Should Understand
- Can Ignore For Now
Explain why each item belongs in that category.

6. One Common Misconception
For this topic:
- one common misconception
- correction
- why it matters in production or interviews

7. Future Direction
Only include this section if meaningful.
If Nick later becomes:
- Senior Backend
- Platform Backend
- Architect
What additional knowledge would become useful?
Limit to at most three items.
Explain whether each item is a current priority or a future-only topic.

8. Learning Check
After this packet, Nick should be able to:
- explain the topic in 60 seconds
- name one production failure mode
- answer one Senior interview question
- decide when not to use this approach

Respect the current priority rules:
- A level 80%: Senior Backend, interview, Production, Incident, System Design
- B level 15%: Platform Backend, Observability, AI-assisted Engineering
- C level 5%: Lead / Manager / Business / GM topics only as optional context
- Do not create backlog or learning debt.

Backend Weekly Learning is the capability layer, not the resume/story layer.
Do not force every topic into resume wording, autobiography, Story, Flow, or Nick-specific production cases.
Prioritize:
- production thinking
- troubleshooting
- trade-off
- system design
- interview answer quality
- known case connection only when naturally applicable
- resume relevance only when genuinely useful and conservative

Only if useful and safe, update:
- senior-owner-playbook/backend-learning-log.md
- senior-owner-playbook/backend-weekly-plan.md

Do not modify resume, autobiography, story drafts, production flow files, 04 / 05 / 08 / 17 / 19, project claim files, or formal interview materials unless Nick explicitly asks.

If files are updated, run Relationship Check and git diff --check. Commit local KB updates when safe, but do not push unless Nick explicitly asks.

Keep the output actionable, concise, focused, and suitable for long-term weekly learning.

If the packet becomes too long, prioritize:
- production thinking
- decision making
- trade-off
- interview
over exhaustive technical detail.

Whenever possible, explain not only "how", but also:
- why this design exists
- what production problem it solves
- what new problems it introduces
- when NOT to choose it
```

## Part B：Vault 升級方向

### 目前定位

目前 `nick-vault` 的主定位是：

```text
Senior Java Backend / Platform Backend 求職、履歷、自傳、production flow、面試 evidence package。
```

換句話說，現在它是：

```text
Job Search Vault
```

它的價值是：

- 讓履歷 claim 有證據邊界。
- 讓面試 case 有 flow / evidence / claim boundary。
- 讓 Nick 不必每次從零整理經歷。

### 可升級方向

未來可以升級成四層。

#### 1. 職涯證據庫

用途：

- 記錄 Nick 做過什麼類型的工作。
- 哪些能寫履歷。
- 哪些只能面試講。
- 哪些只是 learning-only。

內容型態：

- project contribution consolidation
- role / responsibility boundary
- claim boundary
- interview story
- resume bullet source

不放：

- 每日工作流水帳。
- 公司內部細節。
- 未去識別化的 issue / ticket / 客戶資料。

#### 2. 工作復盤庫

用途：

- 把重要 issue、技術決策、排查方法、事故風險整理成可重用案例。

適合記：

- 某類 callback 重送如何避免重複副作用。
- 某次 batch partial failure 如何重跑。
- 某段 legacy code 怎麼靠 git history / log / DB / MQ 重建風險。
- 某次 rollout 如何控制 blast radius。

不適合記：

- 今天做了什麼。
- 主管說了什麼。
- 公司內部系統名、客戶名、URL、IP、token。
- 可識別 production incident 的敏感細節。

建議格式：

```text
問題類型
背景抽象化
風險
排查路徑
決策
結果
可面試講法
不可誇大
敏感資訊處理
```

#### 3. 產業 KB

用途：

- 長期累積 gaming / payment / wallet / provider / platform / AI coding 的產業理解。

主題範圍：

- Payment / Provider Gateway
- Wallet / Ledger / Bet-Settle
- Game Backend / Slot / RTP / Math Contract
- MQ / Batch / Projection / Report
- Platform / Observability / Incident
- AI-assisted Engineering Workflow

原則：

- 產業 KB 要從實際 production case 或可信來源長出來。
- 外部文章必須標成 `外部案例 / non-local`。
- 不把外部案例寫成 Nick 做過。

#### 4. Skill Source

用途：

- 把可重複的工作法整理成 prompt / checklist / skill。

候選 skill：

- Git History Debugging / Risk Reconstruction
- Callback Idempotency Review
- Payment Provider Integration Review
- MQ Duplicate Consume Review
- SQL Slow Query Triage
- AI Code Review for Production Risk
- Interview Story Builder

Skill 化條件：

- 已重複使用 3 次以上。
- 有明確 input / output。
- 有安全邊界。
- 不依賴公司機密。
- 可以被下一個 AI 或 Nick 自己重用。

不建議太早 skill 化：

- 還沒穩定的個人想法。
- 只用過一次的 prompt。
- 含公司內部流程或敏感細節的方法。
- 只是因為 prompt 看起來可重用，但沒有高品質 evidence 或判斷框架。

長期更有價值的不是 1000 個 skill，而是少數高品質 framework：

- Flow Analysis Framework
- Incident Analysis Framework
- Provider Integration Framework
- Resume Claim Framework
- System Design Framework

候選 skill 只有當它已經能抽象成 framework、且 input / output / safety boundary 清楚時才整理。

### v1 / v2 時間線

| 時間點 | Vault 目標 | 建議 |
| --- | --- | --- |
| 現在到拿到下一份工作 | 求職 Vault | 維持現狀，收口、投遞、面試、市場驗證 |
| 到職後 3-6 個月 | Incident Library 候選 | 只有累積新公司去識別化 incident / 排查 evidence 後才考慮 |
| 到職後 6-12 個月 | Decision Library / Ownership Evidence 候選 | 只有有實際 technical decision / ownership case 後才考慮 |
| 到職後 1-2 年 | Career / Industry KB 候選 | 累積足夠高品質案例後再升級 |

結論：

```text
下一家公司不是先升級 KB，而是先累積新的 evidence。
```

## Part C：新公司資料安全線

如果 Nick 到新公司後要把經驗整理進 vault，預設只能寫抽象化版本。

### 可以寫

- 問題類型：callback duplicate、transaction boundary、MQ retry、slow query。
- 技術風險：idempotency、consistency、timeout unknown、partial failure。
- 個人方法：如何查 log、看 git history、比對 DB / MQ、設計補償。
- 面試可講的抽象案例。
- 去識別化後的決策原則。

### 不可以寫

- token / password / private key。
- internal IP / production URL。
- 客戶名、供應商名、商戶名。
- 公司內部 repo 名稱、未公開架構圖。
- 可識別的 incident 時間、金額、流量、影響範圍。
- 公司 code、SQL dump、log 原文。
- 還沒公開的產品、商業策略、薪資或人事資訊。

### 證據層級

新公司內容也要套用 evidence label：

- `真實開發過 / 本人確認`：Nick 實際參與，但仍需去識別化。
- `工作復盤 / 去識別化`：可作個人成長與面試素材，不暴露公司資料。
- `公司機密 / 不入庫`：只能本機腦內或公司系統，不進 vault。
- `外部案例 / non-local`：文章、公開文件、面試題、產業資料。

## Part D：建議目錄演化

目前先不改目錄。若未來真的升級，建議仍保持簡單。

候選演化：

```text
senior-owner-playbook/
  22-career-industry-kb-evolution-plan.md
  interview-practice/
  applications/

projects/
  {domain}/{project}/...

career/
  work-retrospectives/
  industry-kb/
  skills/
```

但 `career/` 不要現在建立。只有當 Nick 到新公司後，真的開始累積抽象化工作復盤與產業 KB，再決定是否新增。

目前明確不建立：

- `career/`
- `incident/`
- `decision-log/`
- `architecture/`
- `industry-kb/`
- `skill-library/`

如果新增 `career/`，它的責任應該是：

- `work-retrospectives/`：去識別化工作復盤，不是每日流水帳。
- `industry-kb/`：產業主題整理，不是文章剪貼簿。
- `skills/`：可重複 prompt / checklist / skill source，不是一次性筆記。

仍不建議新增：

- `ai-notes/`
- `docs/`
- `.claude/`
- `.codex/`
- `resources/`
- `daily-log/`
- `work-log/`
- `records/`

## Part E：明天調整時的決策問題

明天 Nick 可以只回答這幾題：

1. 自動化要不要先開？
2. 週報頻率要每週一次還是兩次？
3. Backend / 面試 / 日語要放同一份，還是分開？
4. 自動化只推送 thread，還是允許產出候選 KB 草稿？
5. Vault 升級要先停在規劃，還是先加 `career/` 目錄？
6. 新公司工作復盤要不要先設模板？
7. skill source 要先收哪一個：git history、callback idempotency、AI code review，還是 interview story？

建議答案：

```text
先不改大目錄。
先開每週一次輕量週報。
只推送在 Codex thread，不自動寫 KB。
等新公司或實際工作復盤需求出現，再新增 career/。
第一個 skill source 候選先放 Git History Debugging / Risk Reconstruction。
```

若只看目前求職階段，更保守的建議答案是：

```text
先不建立 automation。
先不新增 career/。
先不新增 skill library。
先把三個故事、12 條 flow、30 題核心題和投遞節奏跑起來。
```

## Part F：最小下一步

明天若要正式調整，可以用這句：

```text
讀 22，幫我決定 automation 要怎麼開，以及 vault 要不要先新增 career/ 結構；先不要建立 automation，也先不要 push。
```

如果要直接建立 automation，可以改用：

```text
讀 22，幫我建立每週一早上的 Backend / 面試 / 日語輕量週報 automation，只推送在這個 thread，不自動改 KB。
```
