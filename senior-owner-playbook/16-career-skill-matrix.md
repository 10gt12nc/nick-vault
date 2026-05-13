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

1. 完成 3 條 evidence-backed production flow。
2. 每條 flow 補 failure / consistency。
3. 每條高價值 flow 補 decision-notes。
4. 把 3 條 flow 轉成 3 分鐘面試說法。
5. 每條補 claim boundary，避免履歷誇大。
6. 再補大專案地圖，讓跨 repo flow 不會迷路。

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
