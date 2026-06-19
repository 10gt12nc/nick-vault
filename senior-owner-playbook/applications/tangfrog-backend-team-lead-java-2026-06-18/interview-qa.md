# 面試問答包

用途：糖蛙線上娛樂 Backend Team Lead (Java) JD-specific 面試準備。

回答原則：

- 用 production flow 講，不背技術名詞。
- 強調 hands-on lead candidate，不假裝已正式管理 5-8 人 2 年。
- AI 工具要講 review / verification / boundary，不講自動完成 production 開發。
- 不誇大成完整架構師、完整 DevOps / K8s owner、完整金流 / wallet / MQ platform owner。

## 30 秒自我介紹

```text
我是 Java 後端工程師，主要經驗在遊戲 / 博弈平台、第三方 provider 串接、payment provider 對接、遊戲錢包 / 下注結算、MQ / Kafka / RabbitMQ / Quartz、排程報表與既有系統維護。我習慣用 production flow 角度拆問題，會關注資料狀態、冪等、retry、補償、source of truth、log / audit 與線上穩定性。近期也把 Codex / Claude Code 類 AI 工具放進 code reading、diff review、文件同步與驗證流程。這個職缺強調 hands-on、帶人、穩定性與 AI 工具，我覺得和我下一步想走的 Backend Lead 方向很接近。
```

## 90 秒自我介紹

```text
我是 Java 後端工程師，具 5 年以上平台型後端與遊戲 / 博弈系統經驗，主要使用 Java、Spring Boot、MyBatis、MySQL、Redis、MongoDB、Kafka / RabbitMQ / Quartz。過去工作不只是一般 API 維護，也長期接觸第三方金流 provider request / callback / query / withdraw、第三方遊戲 provider、wallet / bet-settle、request log / bet record MQ、report projection、batch job 與既有系統接手。

我比較重視 production flow 的可維護性。像 provider timeout、callback 重送、下注扣款與 rollback、MQ 重複消費、報表和交易真相不一致，這些問題不能只看單一 API 回傳，而要拆 source of truth、transaction boundary、idempotency、retry / compensation 和 observability。我也曾在缺乏完整交接文件時，協助主管梳理兩套平台的服務、部署、DB / Redis / MQ / log flow，讓後續維護和交接更有依據。

我目前想往 Senior Backend / Hands-on Tech Lead 發展。這個職缺提到帶 3-8 人、Code Review、AI 工具、架構優化和線上穩定性。我會誠實說，我目前不是純管理職主管，但我的工作方式已經偏向把複雜問題拆成可交接、可 review、可驗證的 flow。若進入正式組長角色，我會先從任務拆解、Code Review 標準、AI 使用邊界、事故追蹤與知識庫交接開始建立團隊節奏。
```

## HR / 動機 / 薪資

### Q1. 為什麼想投這個職缺？

```text
我看重的是這個職缺不是純管理，也不是只寫功能，而是同時要求 hands-on、Code Review、系統穩定性、架構優化、流程改善和 AI 工具落地。這和我過去接觸的 production flow 很接近，例如第三方 provider、payment callback、wallet / bet-settle、MQ / batch、legacy system reconstruction。我的下一步希望不是只做單點 API，而是往能承擔核心服務可維護性、review discipline 和團隊工程品質的 Backend Lead 發展。
```

### Q2. 你有正式管理 5-8 人的經驗嗎？

```text
我會誠實說，目前我不是純管理職主管，也不會把自己包裝成已經正式管理 5-8 人 2 年。我的強項比較偏 hands-on tech lead 的基礎能力：拆解複雜 production flow、整理 code path / DB / Redis / MQ / log、協助交接與文件化、檢查 diff 的 production risk，以及用 Codex / Claude Code 類工具輔助 code reading、diff review、測試檢查與 KB 回填。

如果進入正式組長角色，我會先從最能落地的地方開始：建立任務拆解方式、Code Review checklist、AI 使用邊界、事故追蹤格式和知識庫交接節奏。人的管理、排程與績效我會需要依公司制度學習，但技術帶領和工程品質的底層方法，我已經有不少實務累積。
```

### Q3. 你怎麼看「速度 vs 品質」？

```text
我不會把品質理解成什麼都做滿。我的判斷會先看 failure cost：如果是金流、wallet、bet-settle、MQ retry、資料修復這類錯了會造成錢錯或資料錯的 flow，品質要優先，至少要把 transaction boundary、idempotency、retry / compensation、log / audit 和人工處理邊界守住。如果是低風險後台顯示或內部工具，可以用較輕量的驗證先交付，再用觀測與後續迭代補強。速度和品質的取捨要跟風險等級綁在一起，而不是每件事都套同一套流程。
```

### Q4. 期望薪資？

```text
我會以月薪 120,000-140,000 作為主要期待，實際依管理範圍、on-call / incident 責任、獎金結構與整體 package 討論。若這個角色需要實際管理 5-8 人、負責 Code Review、線上穩定性、AI 工具導入與跨部門推進，我會希望待遇能反映 Backend Team Lead 的責任範圍。
```

若 HR 壓低：

```text
如果職責比較接近 Senior IC 或準 Lead，而不是完整管理 5-8 人，那薪資可以依實際授權與成長路徑討論；但我會希望至少能對齊核心後端、遊戲 / provider domain、MQ / batch、legacy takeover 和 AI-assisted workflow 的綜合價值。
```

### Q5. 為什麼離職 / 想轉職？

```text
目前工作讓我接觸很多複雜既有系統和 production flow，也累積了 provider integration、wallet / bet-settle、MQ / batch、slot job、math module 和 legacy takeover 經驗。接下來我希望到更重視工程品質、Code Review、AI 工具落地、架構討論與團隊成長的環境，把這些經驗轉成更正式的 Senior Backend / Hands-on Lead 職責。不是單純想換公司，而是希望職責、成長與市場定位更對齊。
```

## AI / Code Review / Team Lead

### Q1. 你怎麼使用 Claude Code 或 Codex？

```text
我會把 AI 工具定位成工程輔助，不是自動取代工程判斷。實務上會用在 code reading、需求拆解、diff review、測試檢查、文件同步、commit 收斂和 KB 回填。比如接手一條 payment 或 wallet flow，我會讓 AI 幫忙整理入口、service、DB / Redis / MQ、log 和可能 failure window，但最後一定會自己回到 code、git history、測試與 production risk 檢查。

如果在團隊導入，我會先建立使用邊界：不能直接貼 secret、不能讓 AI 自動決定 production 行為、AI 產出的 diff 必須經過 reviewer 檢查，尤其是 BigDecimal、transaction boundary、callback idempotency、Redis lock、SQL index、MQ retry、null / race condition 和重複副作用。
```

### Q2. 你怎麼做 Code Review？

```text
我會先看這個 diff 會不會改變 production flow 的狀態與副作用，而不是只看語法。重點包含 transaction boundary、有沒有重複副作用、金額是否用 BigDecimal 且單位正確、SQL 是否有 index、Redis lock 是否有 TTL 和 key 設計、MQ consumer 是否冪等、exception 是否吞掉、log 是否足夠追問題、rollback / retry 是否會造成狀態錯亂。若是 AI 產生的 code，我會更嚴格檢查邊界條件、null、race condition 和測試是否真的覆蓋 production case。
```

### Q3. 如果團隊成員一直寫出品質不穩的 code，你會怎麼處理？

```text
我會先把問題具體化，而不是只說品質不好。比如是 transaction boundary 常漏、SQL 沒 index、MQ consumer 不冪等、exception handling 亂、還是測試只測 happy path。接著把常見問題整理成 review checklist，挑 1-2 個真實 PR 一起 review，讓成員知道 production 風險是怎麼發生的。若是能力缺口，就用 pairing、範例、文件和小任務建立節奏；若是態度或反覆無視風險，才需要升級到主管制度處理。
```

### Q4. 你會怎麼導入 AI 工具到團隊？

```text
我會先從低風險場景導入，例如 code reading 摘要、測試案例草稿、PR review checklist、文件同步和 migration impact analysis，而不是一開始就讓 AI 改核心交易流程。接著建立幾個規則：不能貼 secret / token / production URL；AI 產出的 code 必須由人 review；高風險 flow 要特別檢查 money、transaction、MQ、Redis、SQL 和 observability；最後把好用的 prompt、踩過的坑和 review 結果回填到團隊 KB。目標不是追求產碼速度，而是讓團隊更快理解系統、更穩地交付。
```

## 技術 / 架構

### Q1. RabbitMQ / Kafka 如何處理重複消費？

```text
我接觸過 request log / bet record async processing、report projection、proxy user data summary、big-win notification、daily summary 這類 MQ / batch flow。我的判斷是 MQ 不能假設 exactly-once，所以 consumer 要能處理 duplicate。實務上會用 event identity、business key、DB unique key、upsert、狀態機 guard 或 processed record 來做冪等；失敗時要有 retry、DLQ / DLT 或可重跑機制。report projection 也要和交易 source of truth 分開，不能因為 projection 成功就當交易成功。
```

### Q2. Payment callback 重送怎麼設計？

```text
callback 進來先驗簽、正規化欄位、找到 merchant order id 或 provider transaction id，再看本地 order 狀態。若已終態，不能重複執行上分或下游副作用；若是 pending，才依 allowed transition 轉成功或失敗。callback log 要保留 audit evidence，query API 可處理 timeout unknown。若 DB 成功但 MQ / 上分失敗，要留下 pending / failed 狀態，讓 compensation 或人工補償接手。
```

### Q3. Redis 和 DB 不一致怎麼辦？

```text
我會先判斷誰是 source of truth。多數 money / wallet 類 flow 不能讓 Redis 成為唯一真相，Redis 比較像 hot balance、cache 或 runtime projection。若 DB 成功但 Redis 更新失敗，要看能否用 DB 重建 Redis；若 Redis 成功但 DB 失敗，就要避免它對外造成錯誤餘額。設計上會用 transaction boundary、更新順序、version / timestamp、repair job、rebuild script、alert 和人工查詢邊界處理。不會只說加 lock，因為 lock 只能處理部分併發，不能解決跨系統失敗。
```

### Q4. 你參與過架構設計或技術選型嗎？

```text
我不會把自己說成完整架構 owner，但有參與過 production flow 的設計討論、風險拆解和改善方向整理。我的方式是先從 failure mode 反推選型：如果怕重複 callback，就補 idempotency key 和 order lifecycle；如果怕 MQ 重複消費，就補 consumer 冪等與 DLQ；如果怕報表錯，就拆 source of truth 和 projection；如果怕 rollout 風險，就補 rollback 和 observability gate。我比較擅長把架構問題落到 transaction boundary、state transition、retry / compensation 和可觀測性。
```

### Q5. Spring Cloud 熟嗎？

```text
我目前主力是 Java / Spring Boot、服務間 API / provider integration、MQ / Redis / DB 的 production flow；Spring Cloud 不是我最強主軸，所以我不會誇大。但微服務常見問題，例如 service boundary、timeout、retry、config、observability、provider failure、資料一致性，我在實際系統裡有接觸。若團隊使用 Spring Cloud，我可以把既有的分散式系統與 production troubleshooting 經驗轉過去，並快速補齊框架細節。
```

### Q6. PostgreSQL 熟嗎？

```text
我主要實務經驗是 MySQL，但我熟悉 RDBMS 的 transaction、index、query plan、schema design、分表 / partition、資料狀態排查這些共通概念。PostgreSQL 的語法、locking、index type、explain 細節我會需要依團隊環境補齊，但不會影響我判斷 transaction boundary、查詢效能與資料一致性問題。
```

## 反問面試官

### 角色 / 團隊

- 這個 Backend Team Lead 是新職缺還是接替既有角色？
- 目前後端團隊 3-8 人的 senior / mid / junior 比例如何？
- 這個角色實際會負責績效與人事管理，還是偏 technical lead / task lead？
- 入職前三個月，最期待這個角色解決什麼問題？
- 團隊目前最痛的是交付速度、品質、線上穩定性、架構債，還是人員成長？

### 工程流程

- Code Review 是每個 PR 必做，還是依模組 / 風險決定？
- 目前 CI/CD、自動化測試與 release gate 的成熟度如何？
- 線上 incident 目前怎麼分級、追蹤與復盤？
- 團隊是否已經使用 Claude Code / Codex？目前最大的痛點是 prompt、review、資安，還是導入流程？

### 技術

- 主要架構是 Spring Boot monolith、microservices，還是 Spring Cloud ecosystem？
- MQ 主要使用 RabbitMQ 還是 Kafka？使用場景是 event streaming、task queue、audit log 還是 report projection？
- Redis 主要作 cache、lock、session，還是有 hot data / runtime projection？
- PostgreSQL / MySQL 使用比例如何？是否有分表、partition、讀寫分離或 large table 問題？
- 目前 observability 使用哪些工具？log、metrics、trace 是否完整？

### 薪資 / 制度

- 月薪區間 80,000-140,000 是否另有年終、績效或分紅？
- Team Lead 是否有明確職級、授權與薪資 band？
- 是否有 on-call 或 incident 支援？補休 / 津貼制度如何？
- 若角色包含 AI 工具導入，是否已有公司資安規範或工具授權？

## 投遞前讀法

先讀五條：

1. `payment-order-provider-request` / `payment-provider-callback`
2. `third-party-transfer-in-out` / `slot-bet-settle-rollback`
3. `request-log-rabbitmq-async`
4. `proxy-user-data-report-projection`
5. `bet-record-sharding-schema-route`

再讀補充：

- `iwin-workspace` / `math-workspace` contribution consolidation：AI-assisted workflow supporting evidence。
- `gameserver-phased-rollout`：只作 rollout / observability 加分。
- `rtp-reel-strip-simulation-validation` 或 `fixed-multi-bet-currency-math-core-compatibility`：只在對方追遊戲 / slot domain 時補。
