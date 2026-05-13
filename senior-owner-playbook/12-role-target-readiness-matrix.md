# 職缺目標對標矩陣

這份用來區分四種目標：

1. Senior Backend Engineer
2. Platform Backend Engineer
3. System Owner
4. Lead / Architect 候選能力

不要把它們混成同一種職缺。每一種面試重點不同，能講的案例也不同。

## 總結

目前 Nick 最合理的主線是：

```text
Senior Java Backend
-> Platform Backend
-> Core Service Owner
-> Lead / Architect 候選
```

不是直接跳成正式 Architect。

可以投：

- Senior Java Backend Engineer
- Backend Engineer 偏 Senior
- Platform Backend Engineer
- Gaming / Payment / Wallet / Provider Integration Backend
- 需要接手複雜系統、整合、維運、事件流、金流 / 錢包的職缺

要小心：

- 明確要求正式 Architect title
- 明確要求帶 5 人以上團隊
- 明確要求主導完整金流帳務架構
- 明確要求金融級帳務稽核制度設計
- 明確要求 SRE / K8s 全權 owner

## 1. Senior Backend Engineer

### 面試官想看

- 能不能獨立接 flow。
- 能不能處理 production issue。
- 能不能講清楚資料狀態與 failure window。
- 能不能寫出可維護、可追蹤、可恢復的後端流程。

### 你要準備的 case

第一優先：

- `payment-provider-callback`
- `transfer-wallet-transfer-in-out`
- `settled-bets-kafka`

第二優先：

- `bet-settlement`
- `request-log-reconciliation-tooling`
- `admin-rbac-operations`

### 必須講得出來

- API 到 DB / Redis / MQ 的完整 flow。
- 交易狀態怎麼轉。
- 哪裡要冪等。
- retry 會造成什麼副作用。
- timeout 怎麼判斷。
- 如何補償與對帳。

### 履歷語氣

可以寫：

- 參與 / 維護 / 分析 / 優化高交易平台後端流程。
- 參與金流、錢包、遊戲結算、MQ、排程報表與後台控制面相關開發與維護。
- 具備既有系統逆向、log 追蹤、資料流梳理與 production issue 排查經驗。

避免寫：

- 主導完整金流架構。
- 獨立負責全平台交易一致性。
- 改善效能 X%。

## 2. Platform Backend Engineer

### 面試官想看

- 能不能跨服務理解系統。
- 能不能處理 provider integration、事件流、控制面、部署與觀測性。
- 能不能把 feature 看成平台能力，而不是單點 API。

### 你要準備的 case

第一優先：

- `transfer-wallet-transfer-in-out`
- `ug-adapter-provider-gateway`
- `request-log-traceability`
- `observability-pipeline`

第二優先：

- `admin-reporting-reconciliation`
- `provider-whiteip-superagent-sync`
- `ci-template-release-provenance`

### 必須講得出來

- 上下游邊界。
- provider contract。
- request id / trace id 怎麼串。
- 哪些資料是 source of truth。
- 哪些資料是 projection / cache / report。
- 平台共用能力怎麼避免變成散落 helper。

### 履歷語氣

可以寫：

- 熟悉平台型後端、第三方 provider 串接、事件流與後台控制面。
- 能透過 code reading 與 log analysis 梳理跨服務 production flow。
- 具備 API、MQ、Redis、DB、排程與營運查詢之間的整合理解。

避免寫：

- 設計完整平台架構。
- 主導微服務治理。
- 主導全公司平台化。

## 3. System Owner

### 面試官想看

- 你是否會對一條核心 flow 的結果負責。
- 你是否知道上線後會怎麼壞。
- 你是否能定義監控、補償、對帳、人工處理邊界。

### 你要準備的 case

第一優先：

- `payment-provider-callback`
- `transfer-wallet-ledger`
- `bet-settlement`
- `settled-bets-kafka`

第二優先：

- `quartz-job-control`
- `scheduled-bi-aggregation`
- `request-log-reconciliation-tooling`

### 必須講得出來

- 這條 flow 的成功定義。
- 哪個狀態是終態。
- 哪個步驟可以 retry。
- 哪個步驟只能人工介入。
- 監控看什麼。
- 出事時怎麼止血。
- 修復後怎麼對帳。

### 履歷語氣

可以寫：

- 以 production flow 角度梳理核心交易、錢包、事件流與營運查詢風險。
- 參與補償、對帳、retry、log traceability 等可恢復性議題分析與維護。
- 能將複雜系統整理成可交接、可追蹤、可面試說明的 flow。

避免寫：

- 全權 owner。
- 對完整交易系統負責。
- 主導事故管理制度。

## 4. Lead / Architect 候選能力

### 面試官想看

- 你不是只會改 code，而是能做取捨。
- 你知道技術方案的成本、風險、遷移方式與 rollback。
- 你能把模糊問題拆成漸進式改善。

### 你要準備的 case

第一優先：

- `payment-provider-callback`
- `observability-pipeline`
- `k3s-migration-track`
- `admin-rbac-operations`

第二優先：

- `ci-template-release-provenance`
- `config-cache-refresh`
- `provider-whiteip-superagent-sync`

### 必須講得出來

- Option A / B / C。
- 每個方案的代價。
- migration risk。
- rollback plan。
- monitoring。
- 如何分階段落地。
- 哪些事情短期不做，為什麼。

### 履歷語氣

可以寫：

- 具備 Lead / Architect 候選所需的系統邊界、取捨、風險控制與可維護性思維。
- 能針對核心 flow 提出漸進式改善方向。
- 持續補強系統設計、可靠性、觀測性與技術帶人能力。

避免寫：

- Architect。
- Lead。
- 帶領團隊。
- 主導技術決策。

除非 Nick 有正式職稱、組織授權、專案證據或管理證據。

## 四種職缺共用的 5 條核心 Case

如果只能準備 5 條，順序如下：

1. `payment-provider-callback`
2. `transfer-wallet-transfer-in-out`
3. `settled-bets-kafka`
4. `bet-settlement`
5. `observability-pipeline`

這 5 條分別覆蓋：

- 金流 callback
- wallet / ledger / compensation
- Kafka / MQ reliability
- 遊戲交易 / 結算 / rollback
- observability / rollout / production tracing

## 面試準備分級

### Level 0：只有履歷

狀態：

- 有履歷文字。
- 有自傳。
- 有方向。
- 沒有 code-backed case。

結果：

- 可以投一般 Backend。
- 面 Senior 容易被追問打穿。

### Level 1：完成 1 條 case

狀態：

- 有一條完整 flow。
- 能講 3 分鐘。
- 有 evidence。

結果：

- 可以開始面偏 Senior 職缺。
- 但抗追問能力還不穩。

### Level 2：完成 3 條 case

狀態：

- money / wallet / MQ 各有一條。
- 能回答 failure、state、trade-off。

結果：

- 可以較有底氣投 10 萬以上 Senior / Platform Backend。

### Level 3：完成 5 條 case

狀態：

- money、wallet、MQ、settlement、observability 都有。
- 每條都有 5 個追問。
- 履歷 bullet 已依 evidence 調整。

結果：

- Senior / Platform Backend 面試準備度明顯完整。
- 可以挑更重視核心系統與 production ownership 的職缺。

### Level 4：Lead / Architect 候選

狀態：

- 每條 case 都能講 decision trade-off。
- 能提出 phased migration。
- 能講 rollback / monitoring / team maintenance。
- 能區分短期修補與長期架構演進。

結果：

- 可以面 Lead / Architect 候選能力職缺。
- 但履歷仍應保守，不要自稱正式 Architect。

## 結論

目前 Nick 的最佳策略不是包裝成「已經是 Architect」。

最佳策略是：

```text
我是一名正在往 Senior / Platform Backend / System Owner 發展的 Java 後端工程師，
強項是高交易平台、既有系統接手、金流 / 錢包 / 遊戲結算、MQ / Kafka、production flow 分析與可恢復性思維。
```

這個定位真實，而且市場有價值。

接下來只要把 3-5 條 case 做實，就不是空喊 Senior，而是有東西可以打。
