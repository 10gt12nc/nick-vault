# 初階到資深的軟硬實力矩陣

這份是檢查表，不是新的每日學習主線。

主線仍然是：

```text
production flow
-> evidence
-> failure / consistency
-> decision-notes
-> interview / resume
```

這份只用來定期檢查：Nick 從中階 Java Backend 往 Senior / Platform Backend / System Owner / Lead-Architect 候選前進時，還缺哪些硬實力與軟實力。

## 使用方式

- 每完成 1-2 條 flow 後回頭檢查一次。
- 不要一次補全部缺口。
- 缺口要回到具體 flow 裡補，不要變成抽象讀書清單。
- 沒有 evidence 的能力，不直接寫進履歷。
- 依 `70 / 20 / 10` 控制比例：70% 放 production case / system design / claim boundary；20% Java / SQL / transaction / Redis / MQ 基本判斷遇到再補；10% LeetCode / coding test 只作投遞前保險。
- AI 時代 coding 的訓練重點是 review AI 產物是否能進 production，不是把自己訓練成刷題型選手。

## Level 1：初階 Backend

定位：

- 能接明確需求。
- 能在既有框架下完成 API / CRUD / basic integration。
- 需要別人幫忙判斷系統風險。

硬實力：

- Java 基礎語法與集合。
- Spring Boot controller / service / repository。
- SQL CRUD、基本 index 概念。
- HTTP API、JSON、基本錯誤處理。
- Git 基本操作。

軟實力：

- 問清楚需求。
- 能回報進度。
- 能承認不懂。
- 能照既有規範寫。

面試表達：

- 我能完成明確功能。
- 我會依照既有架構開發與測試。

不能誇大：

- 不說自己能扛 production issue。
- 不說自己能設計架構。

## Level 2：中階 Backend

定位：

- 能獨立接一個功能 flow。
- 能理解既有系統。
- 能處理常見 production bug。

硬實力：

- Spring Boot / MyBatis 常見架構。
- transaction boundary 基礎。
- Redis cache / lock 基礎。
- MQ / Kafka 基礎消費與重送概念。
- SQL index、慢查、lock wait 初步排查。
- API integration、callback、基本 idempotency。
- log tracing 與錯誤定位。

軟實力：

- 拆需求。
- 跟前端、QA、營運對接。
- 寫可交接文件。
- 遇到事故能先止血與回報。

面試表達：

- 我能獨立接後端功能，並理解它在系統中的資料流。
- 我能透過 log / DB / code reading 排查問題。

不能誇大：

- 不說全權 owner。
- 不說主導跨服務架構。

## Level 3：資深 Backend

定位：

- 能扛一條 production flow。
- 能理解 state、failure、consistency。
- 能做技術取捨，而不是只寫功能。

硬實力：

- transaction / consistency / source of truth。
- idempotency、retry、compensation、reconciliation。
- DB performance、index、lock、deadlock、批量處理風險。
- Redis projection、cache consistency、stale data。
- Kafka / MQ reliability、consumer idempotency、DLT。
- production observability：log、metric、trace、alert。
- 逆向舊系統與跨 repo flow。

軟實力：

- 主動發現風險。
- 能跟非工程角色講清楚影響。
- 能定義驗證方式與 rollback。
- 能寫 runbook / handover。
- 能帶中階工程師理解 flow。

面試表達：

- 我會從 production flow 角度看功能，不只看 API。
- 我會先確認 source of truth、終態、retry 邊界與補償方式。
- 我能用 evidence 說明系統風險，不靠猜。

不能誇大：

- 沒有正式證據，不說主導完整系統。
- 沒有 metric，不寫改善百分比。

## Level 4：Platform Backend / System Owner

定位：

- 能跨服務理解一條核心能力。
- 能定義 owner boundary、監控、補償、人工處理與對帳。
- 能把散落功能收斂成平台能力。

硬實力：

- provider contract / external integration。
- event-driven architecture。
- outbox / saga / eventual consistency。
- request id / correlation id / audit trail。
- control plane vs data plane。
- 多服務 rollout、backward compatibility、migration。
- report / projection / source of truth 分離。

軟實力：

- 對結果負責。
- 能定義團隊共用規則。
- 能協調上下游。
- 能在風險與成本之間做取捨。
- 能把事故變成改善項目。

面試表達：

- 我能把跨服務 flow 拆成 source of truth、projection、event、補償與觀測性。
- 我不會只說要重構，而會分階段降低風險。

不能誇大：

- 沒有組織授權，不說正式 owner。
- 沒有主導證據，不說主導平台化。

## Level 5：Lead / Architect 候選

定位：

- 能做方向與取捨。
- 能讓系統更可維護。
- 能影響團隊做法，但不一定已有正式 title。

硬實力：

- system design trade-off。
- architecture decision record。
- migration strategy。
- scalability / reliability / operability。
- security / permission / audit 基礎。
- cost / complexity / maintenance 評估。
- technical debt 分級。

軟實力：

- 能帶人思考，不只是派工作。
- 能把模糊問題拆成階段。
- 能對齊業務、產品、工程風險。
- 能說服，也能接受限制。
- 能知道短期不做什麼。

面試表達：

- 我目前不是正式 Architect，但我用 owner 角度分析系統風險與改善路線。
- 我能提出 Option A / B / C，說清楚成本、風險、rollback 與驗證方式。

不能誇大：

- 不寫 Architect title。
- 不寫帶領團隊，除非有正式管理或 mentor evidence。

## Nick 目前最該補的順序

優先順序不是把全部補滿，而是對標 10 萬以上 Senior / Platform Backend 職缺，先補最有面試價值的缺口：

1. 先把 3 條主力 evidence-backed production case 練到能講 3 分鐘：payment provider、wallet / bet-settle、Kafka / report projection。
2. 每條主力 case 補 failure / consistency / claim boundary 與至少 5 個追問。
3. 再補到 5 條「穩過可抗追問」case：加入 high-traffic table / partition，以及 request log MQ 或 rollout / observability。
4. 若要完全對標 Senior / Platform，再整理到 8-10 條可依 JD 切換的 production flow。
5. Java / SQL / Redis / MQ / LeetCode 只從這些 case 的卡點延伸補，不另外開巨大讀書清單。
6. 大專案地圖只作架構視角補強，不是投遞前必做。

## 8 週軟硬實力養成計劃

這份計劃對標 Nick 目前目標：

```text
Senior Java Backend / Platform Backend / System Owner 候選
```

原則：

- 不用先補滿所有技術。
- 每週都要回到 production flow，不做純讀書焦慮。
- 每週同時補硬實力、軟實力與面試輸出。
- 產出比閱讀重要；能講、能查、能證明，才算補到。

### 第 1-2 週：Money Correctness 主力 Flow

主題：

- `payment-provider-callback`
- `payment-order-provider-request`
- `withdrawal-auto-review-refund`

硬實力：

- transaction boundary。
- idempotency key / terminal state guard。
- provider callback retry / timeout。
- request / response sign verification。
- payment / withdraw order state transition。

軟實力：

- 面對金流問題時先分「狀態、責任邊界、可查證資料」。
- 不急著說重構，先說怎麼止血、查證、補償。
- 能向非工程角色說明：為什麼 timeout 不能直接當失敗。

產出：

- 1 份 3 分鐘 `payment` case。
- 5 個追問：重複 callback、DB 成功 MQ 失敗、provider timeout、人工補單、對帳。
- 1 條保守履歷 bullet。

### 第 3 週：Provider / Wallet / Game Runtime

主題：

- `third-party-transfer-in-out`
- `center-http-deposit-withdraw`
- `coupon-redeem-credit-grant`

硬實力：

- wallet mutation hook。
- bet / settle / refund / transfer-in-out。
- provider transaction id / round id / player balance。
- partial success / compensation。
- downstream command / callback boundary。

軟實力：

- 能把多服務流程拆成「入口、下游、source of truth、projection」。
- 能清楚說哪些是自己做過，哪些是 code-backed 分析。
- 被問「這是不是你主導」時保守但不失分。

產出：

- 1 份 third-party provider / wallet case。
- 1 張 state / failure window 對照表。
- 1 段 claim boundary 口說稿。

### 第 4 週：Batch / Projection / Mongo Backup

主題：

- `daily-game-data-summary`
- `third-party-record-mongo-backup`
- `coin-flow-batch-projection`

硬實力：

- batch replay。
- delete + insert 重跑。
- cursor / stream / batch size。
- Redis checkpoint。
- source log vs report projection。

軟實力：

- 能說明報表不等於交易真相。
- 能先確認資料來源，再談修報表。
- 能把「可重跑、可追蹤、可停止」說成 owner decision。

產出：

- 1 份 batch correctness case。
- 5 個追問：重跑、漏資料、重複累加、Mongo 記憶體、checkpoint 已前進但 projection 缺資料。

### 第 5 週：Kafka / MQ Reliability

主題：

- `settled-bets-kafka`
- `bet-record-request-log-mq`
- `request-log-monitor-alert-mq`

硬實力：

- at-least-once。
- consumer idempotency。
- retry / DLQ / compensation。
- ordering / partition key。
- request log / audit / source of truth 差異。

軟實力：

- 面試時不要只說「用 Kafka 解耦」。
- 要能說「解耦後如何觀測、重送、補償」。
- 能說清楚哪些訊息可以重放，哪些副作用要 guard。

產出：

- 1 份 Kafka / MQ reliability case。
- 1 份「MQ 不是保證一致性」的口說稿。

### 第 6 週：K8s / Observability / Rollout

主題：

- `gameserver-phased-rollout`
- `observability-pipeline`
- `k3s-migration-track`

硬實力：

- Docker / K8s 基礎。
- rollout / rollback。
- probe / resource。
- log pipeline / trace / correlation id。
- release provenance。

軟實力：

- 不把自己包裝成 SRE owner。
- 能說明後端工程師需要讀懂部署訊號，才能排 production issue。
- 能把 rollout 風險拆成「功能、流量、資料、觀測」。

產出：

- 1 份 rollout / observability case。
- 1 份「我不是 DevOps owner，但能讀懂部署與 log 訊號」的保守說法。

### 第 7 週：System Design / Owner Decision

主題：

- 選前 3-4 週最強 flow 各補 `decision-notes.md`。

硬實力：

- Option A / B / C。
- trade-off。
- migration strategy。
- rollback / feature flag / compatibility。
- audit / permission / operation risk。

軟實力：

- 能說「短期先補觀測與 guard，長期再重構」。
- 能說服，也能承認限制。
- 能把技術決策翻成風險、成本與驗證方式。

產出：

- 3 個 owner decision 問答。
- 1 份 3 分鐘 career summary。

### 第 8 週：Mock Interview / Resume Calibration

主題：

- 3 條主力 case 串成面試包。
- 05 / 08 / 17 對齊目標職缺。

硬實力：

- 快速定位 flow。
- 快速畫 state / failure。
- 快速回答追問。

軟實力：

- 誠實 claim boundary。
- 不被薪資與自信拉垮。
- 能把「我分析過」與「我做過」說清楚。

產出：

- 30 秒自我介紹。
- 3 分鐘 career summary。
- 3 條 production case。
- 15 個追問。
- 104 欄位版履歷微調。
- 薪資說法：主談、底線、進取。

## 每週固定節奏

每週只做一個主題，建議節奏：

```text
Day 1-2：重讀 flow.md / evidence / career-interview
Day 3：補 failure / consistency / decision-notes
Day 4：整理 3 分鐘口說與 5 個追問
Day 5：回填 05 / 08 / 17 或 claim boundary
Day 6-7：複述、修稿、休息，不硬開新坑
```

## 軟實力養成重點

Nick 目前最需要補的軟實力不是「變外向」，而是以下 6 件事：

1. **需求澄清**：先問 source of truth、終態、異常處理、誰會查。
2. **風險表達**：把技術風險說成營運可理解的狀態風險。
3. **保守承認邊界**：做過、參與、分析、建議要分開。
4. **Owner decision**：能說先做什麼、不做什麼、為什麼。
5. **面試穩定度**：不用背稿，但要能用固定骨架講 flow。
6. **AI 工具控管**：能說自己用 AI 提效，但判斷、驗證、review 由自己負責。

## 硬實力養成重點

Nick 目前最需要補的硬實力不是所有新技術，而是以下 8 件事：

1. transaction / lock / isolation。
2. idempotency / retry / compensation。
3. reconciliation / audit trail。
4. Kafka / RabbitMQ reliability。
5. SQL index / slow query / batch processing。
6. Redis cache / projection / stale data。
7. K8s rollout / observability 基礎。
8. system design trade-off / migration strategy。

## 檢查問題

每完成一條 flow，就問：

- 我能 3 分鐘講清楚這條 flow 嗎？
- 我知道 source of truth 嗎？
- 我知道 transaction boundary 嗎？
- 我知道 retry 會不會重複副作用嗎？
- 我知道出事怎麼查嗎？
- 我知道怎麼補償 / 對帳嗎？
- 我能說 Option A / B 的代價嗎？
- 我能誠實說哪些是我做過、哪些是我分析的嗎？

如果答不出來，回到該 flow 補，不要開新主題。
