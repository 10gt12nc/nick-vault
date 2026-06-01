# 面試問答包

用途：微達軟體 Senior Java Backend JD-specific 面試準備。

回答原則：

- 用 production flow 講，不背技術名詞。
- 不誇大成完整架構師、正式 Tech Lead、完整金流 / wallet / MQ platform owner。
- 對弱項先承認邊界，再轉回可遷移能力與補強方式。

## 30 秒自我介紹

```text
我是 Java 後端工程師，主要經驗在平台型後端、第三方 provider 串接、payment provider 對接、遊戲錢包 / 下注結算、MQ / Kafka / RabbitMQ / Quartz、排程報表與既有系統維護。我的工作方式比較偏 production flow，會關注資料狀態、冪等、retry、補償、source of truth、log / audit 與線上問題追蹤。目前想往 Senior Java Backend / Platform Backend 發展，這個職缺強調線上穩定性、code review、架構參與與工程品質，和我的方向滿接近。
```

## 90 秒自我介紹

```text
我是 Java 後端工程師，具 4 年以上平台型後端與遊戲 / 博弈系統經驗，主要使用 Java、Spring Boot、MyBatis、MySQL、Redis、MongoDB、Kafka / RabbitMQ / Quartz。過去工作不只是一般 API 維護，也長期接觸第三方金流 provider request / callback / query / withdraw、第三方遊戲 provider、wallet / bet-settle、request log / bet record MQ、report projection、batch job 與既有系統接手。

我比較重視 production flow 的可維護性。像 provider timeout、callback 重送、下注扣款與 rollback、MQ 重複消費、報表和交易真相不一致，這些問題不能只看單一 API 回傳，而要拆 source of truth、transaction boundary、idempotency、retry / compensation 和 observability。我也曾在缺乏完整交接文件時，協助主管梳理兩套平台的服務、部署、DB / Redis / MQ / log flow，讓後續維護和交接更有依據。

我目前想往 Senior Java Backend / Platform Backend 發展。這個職缺提到線上服務穩定性、code review、架構設計與工程文化，我會用過去 provider integration、wallet / bet-settle、MQ / batch 和 legacy takeover 的經驗切入，同時也清楚區分自己實際做過、分析過，以及不能誇大的部分。
```

## HR / 動機 / 薪資

### Q1. 為什麼想投這個職缺？

```text
我看重的是這個職缺不只是寫功能，而是有線上服務穩定性、code review、架構設計、效能優化與工程流程改善。這和我過去接觸的 production flow 很接近，例如第三方 provider、payment callback、wallet / bet-settle、MQ / batch、legacy system reconstruction。我的下一步希望不是只做單點 API，而是往能承擔核心服務可維護性與 owner decision 的 Senior Backend 發展。
```

### Q2. 年資不到 JD 的 7 年，為什麼能勝任？

```text
我理解 JD 寫 7 年以上。我的年資可能不是最長，所以我不會把自己包裝成完整架構師或正式 Tech Lead。但我過去處理的內容不是單純 CRUD，而是第三方 provider、payment callback、wallet / bet-settle、MQ / batch、legacy production flow 這類需要處理狀態一致性、失敗重試、可觀測性與線上排查的工作。這些和職缺的線上穩定性、問題診斷與可維護後端服務高度相關。我會用具體 case 證明能力，也願意補足 Spring Cloud、PostgreSQL 或 DDD 這些職缺加分項。
```

### Q3. 期望薪資？

```text
我會以年薪 150-180 萬作為主要期待，實際會依職務範圍、年終獎金、整體 package 與團隊責任討論。若這個職缺確實需要處理線上服務穩定性、第三方 provider、MQ / Kafka、Redis / MongoDB、legacy system takeover 與 code review，我會希望落在區間中段以上。
```

若 HR 要月薪：

```text
月薪期望大約 110,000-125,000，整體仍會看年終、獎金結構、工時與技術成長性。
```

### Q4. 為什麼離職 / 想轉職？

```text
目前工作讓我接觸很多複雜既有系統和 production flow，也累積了 provider integration、wallet / bet-settle、MQ / batch 與 legacy takeover 經驗。接下來我希望到更重視工程品質、code review、架構討論與團隊成長的環境，把這些經驗轉成更正式的 Senior Backend 職責。不是單純想換公司，而是希望職責、成長與市場定位更對齊。
```

### Q5. 你怎麼看團隊文化？

```text
我喜歡有 code review、能討論取捨、願意把 production issue 轉成可維護改善的團隊。Senior 不是只自己把功能寫完，而是要讓程式碼、文件、log、runbook 和 decision 都能被下一個人理解。我也重視建設性 feedback，因為高交易系統裡很多問題不是靠個人英雄，而是靠團隊共同把邊界、測試與觀測補齊。
```

## 技術 / 架構 / Code Review

### Q1. RabbitMQ / Kafka 如何處理重複消費？

```text
我接觸過 request log / bet record async processing、report projection、proxy user data summary、big-win notification、daily summary 這類 MQ / batch flow。我的判斷是 MQ 不能假設 exactly-once，所以 consumer 要能處理 duplicate。實務上會用 event identity、business key、DB unique key、upsert、狀態機 guard 或 processed record 來做冪等；失敗時要有 retry、DLQ / DLT 或可重跑機制。report projection 也要和交易 source of truth 分開，不能因為 projection 成功就當交易成功。
```

### Q2. Redis 和 DB 不一致怎麼辦？

```text
我會先判斷誰是 source of truth。多數 money / wallet 類 flow 不能讓 Redis 成為唯一真相，Redis 比較像 hot balance、cache 或 runtime projection。若 DB 成功但 Redis 更新失敗，要看能否用 DB 重建 Redis；若 Redis 成功但 DB 失敗，就要避免它對外造成錯誤餘額。設計上會用 transaction boundary、更新順序、version / timestamp、repair job、rebuild script、alert 和人工查詢邊界處理。不會只說加 lock，因為 lock 只能處理部分併發，不能解決跨系統失敗。
```

### Q3. Payment callback 重送怎麼設計？

```text
callback 進來先驗簽、正規化欄位、找到 merchant order id 或 provider transaction id，再看本地 order 狀態。若已終態，不能重複執行上分或下游副作用；若是 pending，才依 allowed transition 轉成功或失敗。callback log 要保留 audit evidence，query API 可處理 timeout unknown。若 DB 成功但 MQ / 上分失敗，要留下 pending / failed 狀態，讓 compensation 或人工補償接手。
```

### Q4. 你怎麼做 Code Review？

```text
我會先看這個 diff 會不會改變 production flow 的狀態與副作用，而不是只看語法。重點包含 transaction boundary、有沒有重複副作用、金額是否用 BigDecimal 且單位正確、SQL 是否有 index、Redis lock 是否有 TTL 和 key 設計、MQ consumer 是否冪等、exception 是否吞掉、log 是否足夠追問題、rollback / retry 是否會造成狀態錯亂。若是 AI 產生的 code，我會更嚴格檢查邊界條件、null、race condition 和測試是否真的覆蓋 production case。
```

### Q5. 你參與過架構設計或技術選型嗎？

```text
我不會把自己說成完整架構 owner，但有參與過 production flow 的設計討論、風險拆解和改善方向整理。我的方式是先從 failure mode 反推選型：如果怕重複 callback，就補 idempotency key 和 order lifecycle；如果怕 MQ 重複消費，就補 consumer 冪等與 DLQ；如果怕報表錯，就拆 source of truth 和 projection；如果怕 rollout 風險，就補 rollback 和 observability gate。我比較擅長把架構問題落到 transaction boundary、state transition、retry / compensation 和可觀測性。
```

### Q6. Spring Cloud 熟嗎？

```text
我目前主力是 Java / Spring Boot、服務間 API / provider integration、MQ / Redis / DB 的 production flow；Spring Cloud 不是我最強主軸，所以我不會誇大。但微服務常見問題，例如 service boundary、timeout、retry、config、observability、provider failure、資料一致性，我在實際系統裡有接觸。若團隊使用 Spring Cloud，我可以把既有的分散式系統與 production troubleshooting 經驗轉過去，並快速補齊框架細節。
```

### Q7. PostgreSQL 熟嗎？

```text
我主要實務經驗是 MySQL，但我熟悉 RDBMS 的 transaction、index、query plan、schema design、分表 / partition、資料狀態排查這些共通概念。PostgreSQL 的語法、locking、index type、explain 細節我會需要依團隊環境補齊，但不會影響我判斷 transaction boundary、查詢效能與資料一致性問題。
```

### Q8. DDD / Clean Architecture 你怎麼理解？

```text
我不會說自己完整落地過 DDD，但我認同它的核心是把 domain boundary、business invariant 和 infrastructure detail 分清楚。以 provider integration 或 wallet / bet-settle 來說，Controller 不應該塞滿簽章、狀態機、DB / Redis / MQ 副作用；domain service 要能表達 order lifecycle、allowed transition、idempotency 和 money mutation，而 provider adapter、MQ、DB repository 是外部細節。Clean Architecture 也是類似精神：讓核心規則不被框架或外部 provider 綁死。
```

## 反問面試官

### HR / 團隊

- 這個職缺是擴編，主要是因為業務成長、系統重構，還是目前團隊負載太高？
- 團隊目前 Java backend 人數、Senior / mid / junior 比例大概如何？
- Code review 是每個 PR 都做，還是依模組 / 風險決定？
- 線上 incident 目前怎麼分級、追蹤與復盤？
- 這個角色入職前三個月，最期待解決什麼問題？

### 技術

- 目前主要架構是 Spring Boot microservices，還是有 Spring Cloud / service discovery / config center？
- MQ 主要使用 RabbitMQ 還是 Kafka？使用場景是 event streaming、task queue、audit log 還是 report projection？
- Redis 主要作 cache、lock、session，還是有 hot data / runtime projection？
- PostgreSQL / MySQL 使用比例如何？是否有分表、partition、讀寫分離或 large table 問題？
- 目前 observability 使用哪些工具？log、metrics、trace 是否完整？

### 薪資 / 制度

- 年薪 120 萬以上的結構是固定月薪 + 年終，還是含績效 / 分紅？
- 薪資調整週期與 Senior 職級評估標準是什麼？
- 10:00-18:00 是否為固定工時？on-call 或 incident 支援怎麼安排？
- 若角色包含 mentor / 架構參與，是否有對應職級、授權與評估方式？

## 投遞前讀法

先讀五條：

1. `payment-order-provider-request` / `payment-provider-callback`
2. `slot-bet-settle-rollback`
3. `transfer-wallet-money-in-out`
4. `proxy-user-data-report-projection`
5. `bet-record-sharding-schema-route`

再讀補充：

- `gameserver-phased-rollout`：只作 rollout / observability 加分。
- `fixed-multi-bet-currency-math-core-compatibility`：只在對方追遊戲 / slot domain 時補。
