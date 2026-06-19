# 面試問答包

用途：糖蛙線上娛樂「資深JAVA後端工程師」JD-specific 面試準備。

回答原則：

- 用 Senior IC / Game Backend 口徑，不套 Team Lead 管理說法。
- 用 production flow 講，不背技術名詞。
- AI 工具要講 review / verification / documentation，不講自動完成 production 開發。
- 不誇大成完整架構師、完整 DevOps / SRE、完整 payment / wallet / MQ platform owner。

## 30 秒自我介紹

```text
我是 Java 後端工程師，主要經驗在遊戲 / 博弈平台、第三方 provider 串接、payment provider 對接、遊戲錢包 / 下注結算、RabbitMQ / Kafka / Quartz、排程報表與既有系統維護。我習慣用 production flow 角度拆問題，會關注資料狀態、冪等、retry、補償、source of truth、log / audit 與線上穩定性。近期也把 Codex / Claude Code 類 AI 工具放進 code reading、diff review、文件同步與驗證流程。這個職缺強調遊戲後端、維運監控、技術文件和 AI 工具，和我的方向滿接近。
```

## 90 秒自我介紹

```text
我是 Java 後端工程師，具 5 年以上平台型後端與遊戲 / 博弈系統經驗，主要使用 Java、Spring Boot、MyBatis、MySQL、Redis、MongoDB、RabbitMQ / Kafka / Quartz。過去工作不只是一般 API 維護，也長期接觸第三方金流 provider request / callback / query / withdraw、第三方遊戲 provider、wallet / bet-settle、request log / bet record MQ、report projection、batch job 與既有系統接手。

我比較重視 production flow 的可維護性。像 provider timeout、callback 重送、下注扣款與 rollback、MQ 重複消費、報表和交易真相不一致，這些問題不能只看單一 API 回傳，而要拆 source of truth、transaction boundary、idempotency、retry / compensation 和 observability。我也曾在缺乏完整交接文件時，協助主管梳理兩套平台的服務、部署、DB / Redis / MQ / log flow，讓後續維護和交接更有依據。

這個職缺提到遊戲後端架構、核心功能、維運監控、技術問題排除與文件標準。我會用遊戲 provider、wallet / bet-settle、RabbitMQ / Redis / MySQL、batch projection 和 AI-assisted workflow 的經驗切入，同時也清楚區分自己實際做過、分析過，以及不能誇大的部分。
```

## HR / 動機 / 薪資

### Q1. 為什麼想投這個職缺？

```text
我看重的是這個職缺明確偏遊戲後端核心功能、系統穩定性、維運監控、技術問題排除和技術文件。這和我過去接觸的 production flow 很接近，例如第三方 provider、payment callback、wallet / bet-settle、RabbitMQ / batch、legacy system reconstruction。我的下一步希望不是只做單點 API，而是往能承擔遊戲後端核心服務可維護性與穩定性的 Senior Backend 發展。
```

### Q2. 期望薪資？

```text
我會以月薪 100,000-110,000 作為期待，實際依年終、獎金、工時、職責深度與成長空間討論。這個區間主要是因為我的經驗不只是一般 Java API，而是長期接觸遊戲平台、第三方 provider、payment provider、wallet / bet-settle、RabbitMQ / Redis / MySQL、既有系統維護與 AI-assisted engineering workflow，和這個職缺要求的遊戲後端架構、穩定性、維運排查與技術文件直接相關。
```

若 HR 說上限是 100,000：

```text
我理解職缺區間上限是 100,000。若整體 package、年終、工時和技術成長性都符合，我可以以接近上限作為討論基準。
```

### Q3. 為什麼離職 / 想轉職？

```text
目前工作讓我接觸很多複雜既有系統和 production flow，也累積了 provider integration、wallet / bet-settle、RabbitMQ / batch、slot job、math module 和 legacy takeover 經驗。接下來我希望到更重視遊戲後端穩定性、架構討論、技術文件和工程品質的環境，把這些經驗轉成更正式的 Senior Backend 職責。不是單純想換公司，而是希望職責、成長與市場定位更對齊。
```

## 技術 / 維運 / AI

### Q1. 遊戲後端你最重視什麼？

```text
我會先看狀態正確性和可恢復性。遊戲後端常見問題不是單一 API 寫錯，而是 provider timeout、玩家餘額異動、bet / settle / rollback、MQ audit、report projection 之間狀態不一致。對這類 flow，我會先確認 source of truth、transaction boundary、idempotency key、retry / compensation、log / audit 和人工查詢邊界。穩定性不是只靠多加 server，而是要讓錯誤發生時能定位、能補償、能重跑、能交接。
```

### Q2. RabbitMQ 如何處理重複消費？

```text
我接觸過 request log / bet record async processing、report projection、daily summary 這類 MQ / batch flow。我的判斷是 MQ 不能假設 exactly-once，所以 consumer 要能處理 duplicate。實務上會用 event identity、business key、DB unique key、upsert、狀態機 guard 或 processed record 來做冪等；失敗時要有 retry、DLQ 或可重跑機制。report projection 也要和交易 source of truth 分開，不能因為 projection 成功就當交易成功。
```

### Q3. Redis 和 DB 不一致怎麼辦？

```text
我會先判斷誰是 source of truth。多數 money / wallet 類 flow 不能讓 Redis 成為唯一真相，Redis 比較像 hot balance、cache 或 runtime projection。若 DB 成功但 Redis 更新失敗，要看能否用 DB 重建 Redis；若 Redis 成功但 DB 失敗，就要避免它對外造成錯誤餘額。設計上會用 transaction boundary、更新順序、version / timestamp、repair job、rebuild script、alert 和人工查詢邊界處理。不會只說加 lock，因為 lock 只能處理部分併發，不能解決跨系統失敗。
```

### Q4. 你怎麼做效能排查？

```text
我會先確認問題是 CPU、memory、DB、Redis、MQ、外部 provider 還是批次窗口。DB 會看慢查詢、explain、index、資料量和查詢條件；batch 會看 batch size、cursor / stream、一次載入資料量、重跑策略；MQ 會看 consumer lag、retry、poison message；Java 端會看 GC、thread、connection pool 和 timeout。排查時會先止血和縮小範圍，再決定是改 SQL、加 index、調 batch、拆 async，還是補 log / metric。
```

### Q5. 你怎麼使用 Claude Code 或 Codex？

```text
我會把 AI 工具定位成工程輔助，不是自動取代工程判斷。實務上會用在 code reading、需求拆解、diff review、測試檢查、文件同步、commit 收斂和 KB 回填。比如接手一條 payment 或 wallet flow，我會讓 AI 幫忙整理入口、service、DB / Redis / MQ、log 和可能 failure window，但最後一定會自己回到 code、git history、測試與 production risk 檢查。

如果在團隊或專案中使用，我會先建立使用邊界：不能貼 secret、不能讓 AI 自動決定 production 行為、AI 產出的 diff 必須經過 reviewer 檢查，尤其是 BigDecimal、transaction boundary、callback idempotency、Redis lock、SQL index、MQ retry、null / race condition 和重複副作用。
```

### Q6. Netty / Elasticsearch 熟嗎？

```text
Netty / Elasticsearch 不是我目前最強主軸，所以我不會誇大。我的主力是 Java / Spring Boot、MyBatis、MySQL、Redis、RabbitMQ、provider integration、wallet / bet-settle、batch / report projection 與 production troubleshooting。若團隊有 Netty 或 Elasticsearch，我可以用既有的高交易系統、資料狀態、效能排查與服務邊界經驗快速補齊框架細節。
```

## 反問面試官

- 這個職缺主要負責新遊戲後端、既有平台維護，還是 provider / wallet 類核心服務？
- 目前服務架構是 Spring Boot monolith、microservices，還是有其他 game server framework？
- RabbitMQ 主要用在 request log、bet record、task queue、report projection，還是遊戲事件？
- Redis 主要作 cache、session、lock、hot data，還是 runtime projection？
- 目前效能監控工具有哪些？log、metrics、trace、alert 是否完整？
- 技術文件與開發標準目前是希望新建立，還是既有文件需要整理？
- 團隊目前是否已使用 Claude Code / Codex？公司對 AI 工具和資安邊界有什麼規範？
- 月薪區間上限 100,000 是否另有年終、績效或分紅？

## 投遞前讀法

先讀五條：

1. `third-party-transfer-in-out`
2. `slot-bet-settle-rollback`
3. `payment-provider-callback` / `payment-order-provider-request`
4. `request-log-rabbitmq-async`
5. `proxy-user-data-report-projection` / `daily-game-data-summary`

再讀補充：

- `bet-record-sharding-schema-route`：MySQL / schema routing / high-traffic data。
- `iwin-workspace` / `math-workspace` contribution consolidation：AI-assisted workflow supporting evidence。
- `gameserver-phased-rollout`：只作維運 / observability 加分。
