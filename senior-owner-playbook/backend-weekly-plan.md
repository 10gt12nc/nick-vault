# Backend Weekly Plan

狀態：48 週核心循環，第 1 輪。

用途：記錄每週主題排程、目前跑到第幾週、下一週主題與排程規則。這不是大型 backlog，不放大量文章內容。

## 排程定位

這份排程服務目前 v1 `Job Search Vault`。它不是要把 52 週全部塞滿，而是用 `48 週核心循環 + 實戰輸出補強池 + 補足主題池` 長期反覆深化：

- Senior Java Backend 面試準備。
- Production Thinking。
- Incident / Troubleshooting。
- System Design Trade-off。
- AI-Assisted Engineering 作為小比例槓桿。
- 市場驗證與談薪準備。

不做：

- 不處理日文。
- 不自動改履歷、自傳、故事稿。
- 不建立巨大 backlog。
- 不把尚未做過的架構寫成既成事實。
- 不把每週未完成內容累積成學習債務。

## 目前進度

```text
Current cycle: Round 1 / 48 weeks
Current week: Week 01
Current topic: Spring Transaction
Next topic: Week 02 Propagation / Isolation / Rollback Rule
```

## 三層排程結構

1. `每週固定產出`：每週固定 5 件事，避免 automation 變成文章摘要或題庫擴張器。
2. `48 週核心輪詢`：涵蓋 Senior Backend Core、Platform / Distributed、Incident / Architecture / Leadership。
3. `實戰輸出補強`：Troubleshooting Playbook、Ownership / Decision、軟實力主題，用來把知識轉成面試可講的 owner 表達。
4. `補足主題池`：Security、K8s / DevOps、Architecture、Communication / Leadership、AI-Assisted Engineering，只在第二輪 / 第三輪或面試回饋需要時插入。

## 能力比例樹

AI 協作開發要追，但不能搶走 Backend 本體。排程比例固定如下：

| Layer | 能力層 | 比例 | 定位 |
| --- | --- | --- | --- |
| 1 | Backend Core | 60% | Java、Spring、Transaction、DB、Redis、MQ 基本盤 |
| 2 | Platform / Distributed | 15% | consistency、projection、provider、wallet、event flow |
| 3 | Incident / Observability | 10% | troubleshooting、logging、metrics、tracing、SLO |
| 4 | Communication / Ownership | 10% | risk communication、decision making、ADR、incident review |
| 5 | AI-Assisted Engineering | 5% | Codex / Claude Code / context engineering / AI risk review |

原則：

- Backend 是本體，AI 是槓桿。
- 不追 AI 新聞，不收集工具清單。
- 只追能改善 production delivery、review 品質、context handoff 與風險檢查的 AI workflow。
- AI 相關素材不得寫成 Nick 已具備 AI 平台 owner 或 AI Engineer 經驗；只能作工作法、效率與 review 能力。

## 30 年能力羅盤

這份 weekly plan 只負責把長期能力樹落成每週小步。完整長期羅盤看 `22-career-industry-kb-evolution-plan.md` 的 `30 年能力主幹`；這裡只保留排程判斷用摘要。

長期 12 條主幹：

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

近期只做前 11 條中和 Senior Backend / Platform Backend 直接相關的部分。Business Thinking、Personal Finance、Health 是長期穩定性，不放進 backend weekly 主線；若要處理，另用生活 / 財務計畫，不混進技術排程。

判斷規則：

- 新主題若能加深 12 條主幹，可以排入第二輪 / 第三輪。
- 新主題若只是新名詞、新工具或短期熱點，先觀察，不排進主線。
- 若主題不能接回 production case、incident、decision、writing 或 interview expression，先不做。

## 每週固定產出

每週固定 5 件事：

1. 本週技術主題學習。
2. 本週 Production Flow 連結。
3. 本週面試題練習。
4. 本週 Incident / Troubleshooting 思考。
5. 本週 KB / 學習紀錄維護。

每週固定要包含：

- 避免重複檢查：先讀 `backend-learning-log.md`，若主題重複，必須加深 incident、production、trade-off 或 interview depth。
- 本週可執行任務：最多 1 項，30 分鐘內完成。
- 與面試材料關聯：至少連到三個 Story、12 條 Flow 或 30 題核心之一。
- KB 維護建議：只能提出建議，不自動改 `04 / 05 / 08 / 17`、三個故事稿或 flow 文件。
- 本週不建議做什麼：明確列低 ROI 或焦慮型延伸。

## 48 週核心輪詢

### 第一輪：Senior Backend Core

| Week | 主題 | 主戰場 | 對應面試 / flow |
| --- | --- | --- | --- |
| 01 | Spring Transaction | Backend 基本盤 | payment / wallet / MQ transaction boundary |
| 02 | Propagation / Isolation / Rollback Rule | Spring / Transaction | callback rollback、wallet state transition |
| 03 | Self Invocation / AOP Proxy | Spring / AOP | legacy code review、transaction 失效 |
| 04 | Async / Thread Pool | Runtime stability | callback / MQ / async job |
| 05 | CompletableFuture | Java async | provider query、batch async、exception handling |
| 06 | JVM Heap / GC / OOM | Runtime troubleshooting | incident / production ops |
| 07 | MySQL Index / B+Tree | DB / Performance | bet-record-sharding-schema-route |
| 08 | Composite Index / Covering Index | DB / Query tuning | report query、provider query |
| 09 | Explain / Slow Query | DB / Troubleshooting | high-traffic table、admin query |
| 10 | Lock / Deadlock / MVCC | DB correctness | wallet / bet-settle consistency |
| 11 | Batch Processing / Pagination | Batch / Report | daily-game-data-summary |
| 12 | Redis Cache Aside | Redis / Cache | provider config、admin query |
| 13 | Redis Cache Consistency | Redis / Consistency | wallet / runtime control |
| 14 | Redis Hot Key / Big Key | Redis / Performance | high traffic query |
| 15 | Redis Distributed Lock | Redis / Concurrency | job lock、補單、wallet 高風險操作 |
| 16 | Kafka Producer / Consumer / Consumer Group | MQ 基本盤 | proxy-user-data-report-projection |

### 第二輪：Platform / Distributed

| Week | 主題 | 主戰場 | 對應面試 / flow |
| --- | --- | --- | --- |
| 17 | Kafka Offset / Ack | MQ reliability | consumer retry、projection lag |
| 18 | Kafka Retry / DLQ | MQ / Projection | proxy-user-data-report-projection |
| 19 | Message Ordering | MQ / State transition | bet -> settle -> rollback |
| 20 | Consumer Idempotency | MQ reliability | request log / bet record MQ |
| 21 | Consumer Lag | MQ / Troubleshooting | projection delay |
| 22 | Projection Rebuild | Report / Rebuild | report flow、batch rerun |
| 23 | Payment Request Flow | Provider Integration | payment-order-provider-request |
| 24 | Payment Callback Flow | Provider Integration | payment-provider-callback |
| 25 | Provider Query / Timeout Unknown | Provider Integration | callback / query fallback |
| 26 | Wallet Transfer | Money correctness | transfer-wallet-money-in-out |
| 27 | Bet / Settle / Rollback | Money correctness | slot-bet-settle-rollback |
| 28 | Reconciliation | Consistency | provider / wallet / report 對帳 |
| 29 | Idempotency Key | Consistency | callback、MQ、wallet operation |
| 30 | Transaction Boundary | Transaction + event | DB / Redis / MQ / provider boundary |
| 31 | Compensation | Failure recovery | rollback、refund、manual repair |
| 32 | Outbox Pattern | Transaction + event | DB success / MQ failed |

### 第三輪：Incident / Architecture / Leadership

| Week | 主題 | 主戰場 | 對應面試 / flow |
| --- | --- | --- | --- |
| 33 | Eventual Consistency | Distributed consistency | projection、callback、query |
| 34 | Saga | Distributed transaction | wallet / provider multi-step flow |
| 35 | CDC / Debezium | Data integration | outbox / projection 可作目標，不誇大 |
| 36 | Contract Testing | Provider / API compatibility | provider adapter、schema change |
| 37 | Event Schema | MQ / Compatibility | MQ schema 改版 |
| 38 | Service Boundary / Bounded Context | Architecture | provider / wallet / report boundary |
| 39 | Circuit Breaker / Retry / Fallback | Resilience | provider timeout、downstream failure |
| 40 | Bulkhead / Rate Limit | Resilience / Protection | provider rate limit、API protection |
| 41 | Graceful Shutdown / Health Check | Runtime / Deployment | rollout / K8s readiness |
| 42 | OpenTelemetry / Tracing | Observability | gameserver-phased-rollout / incident |
| 43 | Structured Logging / Metrics | Observability | order id、trace id、provider txn id |
| 44 | Alert / SLO | Observability / Owner decision | business health dashboard |
| 45 | Incident Review / Postmortem | Incident / Learning | production troubleshooting |
| 46 | Migration Strategy / Legacy Takeover | Legacy / rollout | Legacy Takeover Story |
| 47 | Architecture Trade-off / ADR | Owner decision | 18-system-design-templates |
| 48 | Risk Communication / Technical Decision Making | Leadership | PM 快上線、技術債排序 |

## 實戰輸出補強

這份排程目前對 `Senior Java Backend` 與 `Platform Backend` 已經足夠，不需要再擴新框架或新題庫。若往 `System Owner` 前進，真正缺口不是主題數量，而是把知識轉成可被追問的實戰輸出：

- Incident：出事時如何定位、證明、修復與避免重發。
- Trade-off：為什麼選 A 不選 B，以及成本與風險是什麼。
- Ownership：如何定義問題、成功條件、驗證方式與風險溝通。

因此第二輪 / 第三輪若要插入補強，優先用下面三組，不再另開大型學習線。

### Troubleshooting Playbook

反覆輪詢，但不排成新的長期主線：

- CPU 100%。
- OOM。
- GC 問題。
- Thread Pool 滿。
- DB Connection Pool 滿。
- Slow Query。
- Deadlock。
- Redis Timeout。
- Kafka Lag。
- Consumer 卡死。

輸出要求：每題都要能回答 `影響範圍 -> 卡在哪一層 -> 如何證明根因 -> 如何修復 / 避免重發`，並盡量接回 Payment Callback、Wallet / Bet-Settle、MQ / Projection 或 Legacy Takeover。

### Ownership / Decision

這是從 Senior Backend 往 System Owner 的橋樑：

- 需求不合理怎麼辦。
- 如何評估風險。
- 如何拆任務。
- 如何寫 ADR。
- 如何寫 Incident Review。
- 如何做技術決策。
- 如何和 PM 討論取捨。

輸出要求：不要只講技術名詞，要能說出 `問題、成本、風險、驗證方式、rollback plan`。

### 軟實力主題池

目前排程比例維持 `硬實力 80% / 軟實力 20%`。因為 Nick 目前目標是 Senior Java Backend / Platform Backend / 10 萬以上，不是 Engineering Manager、Director 或 VP Engineering。

優先補：

1. Technical Communication：技術說明、文件撰寫、Incident Communication。
2. Risk Communication：風險分析、風險回報、Escalation。
3. Decision Making：Trade-off、ADR、Architecture Decision。
4. Ownership：Requirement Clarification、Success Criteria、Validation Plan。

排後面：

5. Mentoring：Code Review、Knowledge Sharing。
6. Stakeholder Management：PM、QA、Other Teams。

Mentoring、Hiring、People Management 先不當主線；等面試回饋或職缺明確要求 Lead / Manager 再補。

## 補足主題池

這些不是每週主排程；第二輪、第三輪或面試回饋需要時才插入。

### Security

- Spring Security。
- OAuth2。
- OIDC。
- JWT。
- RBAC。
- API Authentication。
- Service-to-Service Auth。
- Signature Verification。
- Input Validation。
- OWASP Top 10。
- PII / Log Masking。
- Secret Management。
- Audit Log。

### K8s / DevOps 必要理解

- Deployment。
- Rollout。
- Rollback。
- Liveness Probe。
- Readiness Probe。
- Startup Probe。
- Resource Request / Limit。
- ConfigMap / Secret。
- Graceful Shutdown。
- Log Collection。
- Basic Monitoring。

### Architecture

- Modular Monolith。
- Microservice Boundary。
- DDD Strategic Design。
- API Gateway。
- Service Discovery。
- Config Management。
- Backward Compatibility。
- Technical Debt Ranking。
- Migration Plan。
- ADR。
- Cost / Complexity / Maintainability。

### Communication / Leadership

- Technical Writing。
- Decision Memo。
- Risk Communication。
- Stakeholder Alignment。
- Prioritization。
- Code Review Standard。
- Mentoring。
- Cross-team Communication。
- Incident Communication。
- Handover / Runbook。
- Hiring / Interview Signal。

### AI-Assisted Engineering

這是固定小比例主題池，不取代 Java / Transaction / MQ / DB / Incident。它的價值是讓 Nick 比只會寫 Java 的候選人更懂 AI 時代的 backend delivery 與 risk review。

#### Level 1：工具使用

- Codex。
- Claude Code。
- Gemini CLI。
- Cursor。
- Windsurf。

#### Level 2：Context Engineering

- `CLAUDE.md`。
- `AGENTS.md`。
- Project Memory。
- KB Structure。
- Handoff Pattern。

#### Level 3：Workflow

- Plan。
- Implement。
- Review。
- Refactor。
- Agent 分工：Architect、Planner、Coder、Reviewer。

#### Level 4：AI Risk Review

AI 產物最容易在 production 風險上出包，因此每次導入 AI 工作流，都要能 review：

- Transaction Boundary。
- Idempotency。
- BigDecimal。
- SQL Index。
- Redis Lock。
- MQ Retry。
- Race Condition。
- Security。

#### Level 5：AI Team Lead

先作長期能力觀察，不排進近期主線：

- 如何拆任務給 Agent。
- 如何驗收 Agent。
- 如何設計 Skill。
- 如何設計 Workflow。
- 如何做 Context Compression。

### 趨勢追蹤邊界

要追趨勢，但只追和 Backend / Platform / AI delivery 直接相關的方向。

Backend 趨勢：

- Java LTS。
- Spring Boot。
- Kafka。
- OpenTelemetry。
- PostgreSQL / MySQL。
- Redis。
- K8s。

Architecture 趨勢：

- Modular Monolith。
- Event Driven。
- Platform Engineering。

AI Engineering 趨勢：

- Agent Workflow。
- Context Engineering。
- Harness Engineering。
- AI Code Review。
- AI Testing。

不追：

- 每週 AI 新聞。
- 無法落到 production review 的工具比較。
- 把 AI 工具熟悉度包裝成正式 backend 架構能力。

## 面試材料對應列表

每週輸出都要至少對應一項。

### 三個故事

1. Provider Integration Story。
2. Wallet / Bet-Settle Story。
3. Legacy Takeover Story。

### Production Flow

1. Payment Request。
2. Payment Callback。
3. Provider Query。
4. Wallet Transfer。
5. Bet / Settle / Rollback。
6. MQ Projection。
7. Report Flow。
8. Reconciliation。
9. Admin Operation。
10. Request Log / Audit Log。
11. Legacy Reconstruction。
12. Incident / Troubleshooting Flow。

### 面試題分類

1. Java / JVM。
2. Spring / Transaction。
3. MySQL / DB。
4. Redis。
5. Kafka / MQ。
6. Distributed System。
7. System Design。
8. Production Incident。
9. Legacy Takeover。
10. Behavioral / Ownership。

## 不排進主線

這些先不要放進每週主排程：

- 日文。
- 新框架收集。
- 過度 DDD。
- 過度 K8s。
- 雲端證照。
- 大型 side project。
- KB 大重構。
- 把沒做過的技術包裝成已做過。

日文若要維持，只能是每週 `30-60 分鐘` 的獨立輕量項目，不併入 Senior Backend 主排程。

## 每週固定檢查

每週開始前：

1. 讀 `backend-weekly-template.md`。
2. 讀 `backend-learning-log.md`，避免重複。
3. 查本週主題最新官方 / 高品質資料。
4. 對應 12 條 flow、三個 Story、30 題核心題。
5. 只產出 1 個 30 分鐘內可完成任務。

每週結束後：

1. 更新 `backend-learning-log.md` 的本週 checkpoint。
2. 更新本檔 `Current week / Next topic`。
3. 只提出 KB 維護建議，不大量改正式 KB。
