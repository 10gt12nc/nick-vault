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

整理終點不是「所有 repo 都做完」，而是達到 Senior / Platform Backend 可投遞、可面試、可防追問的證據包。判斷標準是：3-5 個可保守放履歷的 project-level claim、8-10 條能講 3 分鐘的 production flow、每條 claim 的真實開發 / code-backed / 不可誇大邊界清楚，以及 `05 / 08 / 04 / 17` 已對齊。達到後應停止大規模整理，改成投遞、面試練習與依職缺補洞。

> 2026-05-27 KB refresh：`05 / 08 / 04 / 17` 已完成 rolling refresh，已回填 UGSoft admin-api / connector refresh，`08` 已完成 104 投遞欄位檢查，`04 / 面試 case 對齊檢查` 與三條主力 case 90 秒 / 3 分鐘口說稿也已完成。沒有特定 JD 時，不需要重新客製履歷；面試口說練習先暫停，等 Nick 明確要求才開始。

> 2026-05-25 方向校正：這份矩陣不是要求 Nick 一個人扛完整大系統。Senior / Platform Backend 的可面試證據是「代表性 production flows + project-level claims + 可防追問邊界」。完整公司級系統地圖是團隊知識管理工作；個人準備只需要能講清楚核心 flows 與跨系統協作，不需要把所有 repo 全量掃完。

> 2026-05-25 面試準備比例：通用對標用 `70 / 20 / 10` 收斂。70% 放 production case / system design / claim boundary；20% Java / SQL / transaction / Redis / MQ 遇到 case 卡點或面試回饋再補；10% LeetCode / coding test 作投遞前保險。不同角色只調整 case 權重，不改主線。

> 2026-05-26 code-first claim audit：`payment` 在本矩陣中只代表 payment provider / 商戶對接 case，不代表完整金流帳務、wallet、ledger 或 reconciliation owner。若職缺明確要求完整 payment platform / wallet / fintech 帳務 owner，要更小心；Nick 目前更適合 provider integration、遊戲 wallet / bet-settle、MQ / batch、partition / high-traffic data 與 legacy takeover 的組合型 Senior Backend。

可以投：

- Senior Java Backend Engineer
- Backend Engineer 偏 Senior
- Platform Backend Engineer
- Gaming / Provider Integration / Wallet / Platform Backend
- 需要接手複雜系統、整合、維運、事件流、payment provider 對接、遊戲錢包 / 下注結算的職缺

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

- `payment-provider-callback`（provider / 商戶對接，不作完整 payment platform owner）
- `antplay-slot-game-api/slot-bet-settle-rollback` 或 `iwin_gameserver/third-party-transfer-in-out`
- `antplay-slot-game-job/proxy-user-data-report-projection` 或 `request-log-rabbitmq-async`

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
- 參與 payment provider 對接、遊戲錢包、遊戲結算、MQ、排程報表與後台控制面相關開發與維護。
- 具備既有系統逆向、log 追蹤、資料流梳理與 production issue 排查經驗。

避免寫：

- 不得寫成主導完整金流架構。
- 獨立負責全平台交易一致性。
- 改善效能 X%。

## 2. Platform Backend Engineer

### 面試官想看

- 能不能跨服務理解系統。
- 能不能處理 provider integration、事件流、控制面、部署與觀測性。
- 能不能把 feature 看成平台能力，而不是單點 API。

### 你要準備的 case

第一優先：

- `antplay-slot-game-api/transfer-wallet-money-in-out`
- `ug-adapter-provider-gateway`
- `request-log-traceability`
- `k3s-deploy/gameserver-phased-rollout`（interview-only 加分，不作正式 DevOps / SRE owner）

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
- `antplay-slot-game-api/slot-bet-settle-rollback`
- `iwin_gameserver/third-party-transfer-in-out`
- `antplay-slot-game-job/proxy-user-data-report-projection`

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

- 不得寫成全權 owner。
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
- `antplay-slot-game-job/proxy-user-data-report-projection`
- `k3s-deploy/gameserver-phased-rollout`（interview-only）
- `antplay-slot-game-api/bet-record-sharding-schema-route`

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

1. `payment-provider-callback` / `payment-order-provider-request`
2. `antplay-slot-game-api/slot-bet-settle-rollback` 或 `iwin_gameserver/third-party-transfer-in-out`
3. `antplay-slot-game-job/proxy-user-data-report-projection`
4. `request-log-rabbitmq-async` 或 `bet-record-sharding-schema-route`
5. `k3s-deploy/gameserver-phased-rollout`（interview-only 加分）

這 5 條分別覆蓋：

- payment provider callback / request
- 遊戲 wallet / ledger / compensation
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

### Level 2：中等可面，完成 3 條 case

狀態：

- payment provider 對接、遊戲 wallet / bet-settle、MQ / projection 至少各有一條。
- 每條能講 3 分鐘，有 evidence、claim boundary 與 5 個常見追問。
- 能回答入口、source of truth、failure window、state transition、retry / compensation。

結果：

- 可以較有底氣投 10 萬以上 Senior / Platform Backend。
- 遇到強追問仍可能需要回來補洞。

### Level 3：穩過可抗追問，完成 5 條 case

狀態：

- payment provider 對接、遊戲 wallet / bet-settle、MQ / projection、partition / high-traffic data、rollout / observability 都有。
- 每條都有 90 秒、3 分鐘、STAR、failure scenarios、owner decision 與不可誇大邊界。
- 履歷 bullet 已依 evidence 調整。
- 能依 JD 重排案例順序，不靠背稿硬講。

結果：

- Senior / Platform Backend 面試準備度明顯完整。
- 可以挑更重視核心系統與 production ownership 的職缺。

### Level 4：完全對標 Senior / Platform，Lead / Architect 候選能力

狀態：

- 8-10 條 production flow 可依 JD 切換使用。
- 每條 case 都能講 decision trade-off。
- 能提出 phased migration。
- 能講 rollback / monitoring / team maintenance。
- 能區分短期修補與長期架構演進。
- `05 / 08 / 04 / 17` 與最新 claim boundary 全部對齊。

結果：

- 完全對標 Senior / Platform Backend。
- 可以面 Lead / Architect 候選能力職缺。
- 但履歷仍應保守，不要自稱正式 Architect。

## 結論

目前 Nick 的最佳策略不是包裝成「已經是 Architect」。

最佳策略是：

```text
我是一名正在往 Senior / Platform Backend / System Owner 發展的 Java 後端工程師，
強項是高交易平台、既有系統接手、payment provider 對接、遊戲錢包 / 遊戲結算、MQ / Kafka、production flow 分析與可恢復性思維。
```

這個定位真實，而且市場有價值。

接下來只要把 3-5 條 case 做實，就不是空喊 Senior，而是有東西可以打。

如果要再往「0 到 1 架構能力」補強，下一步不是平均掃更多 repo，而是把已掌握的 payment provider / game wallet / bet-settle / MQ / batch / math flows 抽成可重用的 system design template：狀態機、資料表、idempotency key、outbox / inbox、retry、reconciliation、observability、manual repair 與 rollout plan。這是可選加強，不是目前通用投遞稿的前置條件。

建議模板只收斂成 4 份：`Provider Integration template`、`Wallet / Bet-Settle template`、`MQ / Batch / Projection template` 是主力；`Slot Math / RTP Validation template` 是遊戲 / slot / provider JD 的備用差異化。這四份的價值是展示從 production flow 抽象出 system design 的能力，不是宣稱 Nick 主導過完整 0 到 1 大系統。`Provider Integration template v1`、`Wallet / Bet-Settle template v1` 與 `MQ / Batch / Projection template v1` 已完成，位置是 `18-system-design-templates.md`；剩下 Slot Math / RTP Validation 仍是可選。
