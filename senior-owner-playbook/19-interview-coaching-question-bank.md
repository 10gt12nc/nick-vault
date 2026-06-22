# Senior Backend 互動問答診斷題庫

這份用來把焦慮轉成可驗證的問答。目的不是一次背完所有題，而是讓 Nick 逐題回答，AI 依回答狀況判斷：

- 目前是 `中等可面`、`穩過可抗追問`，還是仍需補基礎。
- 哪些是會做但講不清楚。
- 哪些是概念不足，需要直接教學、畫圖或代碼演示。
- 哪些回答可以回填 `04-interview-casebook.md`、`05-resume-master-zh.md`、`08-application-autobiography-zh.md` 或特定 flow 的 `career-interview.md`。

## 維護規則

- 本檔不是流水帳，不記錄每次聊天逐字稿。
- 每輪問答只萃取三種結果：
  - `能力判斷`：能不能講清楚、能不能抗追問、是否有誇大風險。
  - `補強主題`：需要補 transaction、MQ、SQL、Redis、JVM、system design 或某條 flow。
  - `可回填素材`：若 Nick 的回答形成更好的 90 秒 / 3 分鐘說法，再回填 `04` 或對應 flow。
- 每次實際問答練習後，要把有價值的診斷結果整理到 `interview-practice/`；不保留逐字稿，只保留題目、Nick 原答摘要、評分、修正版、下次追問與可回填素材。
- 題目必須從 Senior Java Backend / Platform Backend 的 production case 長出來，不建立泛用八股題海。
- AI 可以依 Nick 回答深度切換模式：
  - `診斷模式`：追問、打分、指出漏洞。
  - `教學模式`：直接講解概念、畫結構圖、補代碼演示。
  - `打磨模式`：把 Nick 的回答壓成 90 秒、3 分鐘或 STAR。
  - `反問模式`：模擬面試官連續追問。
- 每輪最多問 3-5 題。不要一次把下面全部題目丟給 Nick 作答。

## 題庫規模與使用輪次

本題庫目前設計成 `10 個大主題 / 145 題`，但實務上不是 145 題全刷。它是診斷池，AI 依 Nick 的回答狀態挑題、追問、教學或打磨。

10 個大主題：

| 主題 | 題數 | 目的 |
| --- | ---: | --- |
| 履歷與自我定位 | 10 | 確認市場主軸、claim boundary、ownership 口徑 |
| Production Flow 表達 | 10 | 確認會開發的內容能不能講成 3 分鐘 case |
| Transaction / Consistency / Idempotency | 15 | 檢查高交易系統的正確性與失敗邊界 |
| MQ / Kafka / RabbitMQ / Batch | 15 | 檢查 event-driven、projection、retry、DLQ、重跑 |
| Database / SQL / Performance | 15 | 檢查 index、slow query、lock、partition、report query |
| Redis / Cache / Distributed Lock | 15 | 檢查 cache / lock / consistency / fail mode 判斷 |
| Java / Spring / Runtime 基本功 | 15 | 檢查 transaction、AOP、thread pool、JVM troubleshooting |
| Observability / Incident / Legacy Takeover | 15 | 檢查排查、log、trace、交接文件與系統救援能力 |
| System Design / Owner Decision | 15 | 檢查從 flow 抽象成架構、選型與 trade-off |
| Behavior / HR / 談薪 / 團隊協作 | 15 | 檢查資深定位、薪資、弱點、合作與主管溝通 |

建議輪次：

1. `第 1 輪：5 題診斷主力市場定位`
   先看 Nick 是否能講出 Senior 主軸、payment、wallet、MQ 與 ownership 邊界。
2. `第 2 輪：10-15 題 production deep dive`
   從第 1 輪卡住的地方追，例如 payment callback、transaction、MQ retry。
3. `第 3 輪：10-15 題基本功補洞`
   只補被追問打穿的 Spring transaction、SQL index、Redis、MQ、JVM。
4. `第 4 輪：5-10 題 system design / owner decision`
   練從 production flow 抽象成設計，不背空泛架構詞。
5. `第 5 輪：5-10 題 HR / 談薪 / 弱點`
   練不是完整 owner 但有資深潛力、為什麼 10 萬以上、如何保守但有力。

判斷規則：

- 第 1 輪若卡住，不往下刷題，先教學、畫圖、代碼演示或打磨回答。
- 第 1 輪若穩，才進入第 2 輪 deep dive。
- 基本功題只補被 production case 打穿的部分，不變成泛用八股題海。
- 若某輪回答形成更好的履歷 / 面試說法，再回填 `04`、對應 flow `career-interview.md` 或 `05 / 08` 的保守素材。

## 評分方式

每題用 0-3 分判斷：

| 分數 | 判斷 | 代表狀態 |
| --- | --- | --- |
| 0 | 答不出或答非所問 | 需要教學 |
| 1 | 知道名詞，但講不出 production 邊界 | 概念鬆散 |
| 2 | 能講流程與主要風險，但細節追問會晃 | 中等可面 |
| 3 | 能講 trade-off、failure window、改善方案與保守 claim | 穩過可抗追問 |

總體判斷：

- `中等可面`：三條主力 case 的核心題平均 2 分以上。
- `穩過可抗追問`：五類 case 都有至少 2 分，且 payment / wallet / MQ 任兩類達 3 分。
- `完全對標 Senior / Platform`：8-10 條 production flow 可切換，system design 題能講 trade-off，不靠背稿。

## 回答格式

Nick 每題不用長篇作文，先用這個格式回答即可：

```text
我理解：

流程：

風險：

如果重做 / 改善：

我實際做過 / 分析過的邊界：
```

AI 回覆格式：

```text
評分：
目前判斷：
你講得好的地方：
會被追問打穿的地方：
我直接教你：
下一題：
```

## 第一層：履歷與自我定位

這一層先確認 Nick 不是把履歷背熟，而是知道自己在市場上要賣什麼。

1. 你現在投 Senior Java Backend / Platform Backend，主軸一句話怎麼講？
2. 你不是正式 Tech Lead，也不是完整系統 owner，那你要怎麼講 ownership 才不失分？
3. 你的三個最強 project-level claim 是什麼？每個 claim 的 evidence 是什麼？
4. 哪些經驗可以放正式履歷？哪些只能面試講？哪些不能講成主導？
5. payment 你能講什麼？不能講什麼？
6. game_job / MQ / projection 你能講什麼？不能講什麼？
7. AntPlay slot / math 你要怎麼當差異化，而不是把履歷弄雜？
8. iwin / antplay / ugsoft 這些內部產品名正式外投要怎麼處理？
9. 如果面試官問「你現在職稱不是資深，為什麼投資深？」你怎麼回答？
10. 如果面試官問「你主導過什麼？」你怎麼保守但有力地回答？

## 第二層：Production Flow 表達

這一層確認「會開發」能不能轉成「講得清楚」。

1. 請用 3 分鐘講一條 payment provider request flow。
2. 請用 3 分鐘講一條 payment callback flow。
3. 請用 3 分鐘講一條 wallet / bet-settle / rollback flow。
4. 請用 3 分鐘講一條 MQ / report projection flow。
5. 請用 3 分鐘講一條 partition / high-traffic data flow。
6. 一個 request 從 Controller 到 DB / Redis / MQ / external provider，你會怎麼拆層講？
7. 你怎麼說明 source of truth、cache、projection、audit log 的差別？
8. 如果 flow 裡有下游 service，你怎麼避免把沒掃過的下游講成自己懂？
9. 如果 flow 是 code-backed 但不是你直接開發，你要怎麼講？
10. 你怎麼把 flow 從「功能描述」升級成「Senior 風險分析」？

## 第三層：Transaction / Consistency / Idempotency

這是高交易後端的核心追問區。

1. 什麼情境下只靠 `@Transactional` 不夠？
2. DB transaction 成功，但 MQ publish 失敗怎麼辦？
3. MQ publish 成功，但 consumer 失敗怎麼辦？
4. provider timeout 後，為什麼不能直接當失敗？
5. callback 重送時，怎麼避免二次入帳或二次扣款？
6. idempotency key 應該放在哪裡？request id、order id、provider transaction id 差在哪？
7. 終態訂單是否可以被覆蓋？什麼情境可以，什麼情境不行？
8. rollback、refund、compensation、reconciliation 差在哪？
9. optimistic lock 和 pessimistic lock 你會怎麼選？
10. isolation level 你實務上會關心哪幾種問題？
11. 如果玩家錢包扣款成功，但 bet record 寫入失敗，怎麼辦？
12. 如果 bet settle 成功，但報表 projection 沒更新，怎麼辦？
13. 如果人工補單後 callback 又進來，怎麼避免重複副作用？
14. BigDecimal 在金額計算有哪些 production 風險？
15. 你會怎麼設計一個 retry-safe 的 provider callback handler？

## 第四層：MQ / Kafka / RabbitMQ / Batch

這一層看你能不能處理 event-driven 與非同步一致性。

1. at-least-once 代表什麼？對 consumer 設計有什麼影響？
2. duplicate message 怎麼處理？
3. message ordering 什麼時候重要？什麼時候不用強求？
4. Kafka partition key 你會怎麼選？
5. consumer lag 變高，你會怎麼排查？
6. DLQ 是什麼？什麼訊息應該進 DLQ？
7. retry 要放在 producer、consumer、queue 還是 job 裡？
8. batch job 重跑時，怎麼避免重複寫入？
9. report projection 跟 online transaction 為什麼要分開看？
10. Quartz job 跑到一半失敗怎麼恢復？
11. MQ schema 改版會有什麼風險？
12. 如果 consumer 水平擴容，會不會造成 race condition？
13. request log async 化的好處和風險是什麼？
14. bet record 從 API 進 MQ 再進 admin query，中間可能有哪些資料落差？
15. 你怎麼設計一個可補資料、可重跑、可觀測的 projection flow？

## 第五層：Database / SQL / Performance

這一層不求 DBA 等級，但要能判斷線上會不會炸。

1. 怎麼判斷一個查詢需要 index？
2. composite index 欄位順序怎麼決定？
3. `EXPLAIN` 你最先看哪幾個欄位？
4. 大分頁查詢為什麼危險？
5. 大 `IN` query 有什麼風險？
6. join、subquery、分批查詢你會怎麼取捨？
7. slow query 出現時，你會怎麼排查？
8. deadlock 發生時，你會看什麼？
9. lock wait timeout 跟 deadlock 差在哪？
10. online query 和 report query 為什麼要分離？
11. 分表 / partition 解決什麼問題？不能解決什麼問題？
12. 每日 / 每月分表建立 job 有哪些風險？
13. batch insert / batch update 有哪些 transaction 與 memory 風險？
14. 如果查單 API 被 provider 或商戶高頻打，你怎麼保護 DB？
15. 你怎麼設計 bet record sharding / routing 的查詢邊界？

## 第六層：Redis / Cache / Distributed Lock

這一層確認你不會把 Redis 當萬能解。

1. 什麼資料可以 cache？什麼資料不應只信 cache？
2. cache aside 是什麼？更新 DB 後 cache 怎麼處理？
3. cache penetration、breakdown、avalanche 差在哪？
4. hot key / big key 會造成什麼問題？
5. Redis distributed lock 什麼情境適合？什麼情境不適合？
6. lock 過期時間怎麼設？
7. 如果 lock 還沒處理完就過期，會怎樣？
8. wallet balance 能不能放 Redis？為什麼？
9. Redis 和 DB 不一致時怎麼處理？
10. provider routing / config 放 Redis 有什麼風險？
11. rate limit 你會怎麼設計？
12. session / token / auth state 放 Redis 要注意什麼？
13. Redis cluster 或主從切換時，lock / cache 可能有什麼問題？
14. 如果 Redis 掛了，系統要 fail open 還是 fail close？
15. 你怎麼把 Redis 風險講成 owner decision？

## 第七層：Java / Spring / Runtime 基本功

這是 20% 基本功，不取代 production case，但面試會被抽問。

1. Spring transaction 什麼情境會失效？
2. self-invocation 為什麼會讓 transaction / AOP 失效？
3. checked exception / runtime exception 對 rollback 有什麼影響？
4. `@Transactional` 放在 private method 有用嗎？
5. AOP 大致怎麼攔截？JDK proxy 和 CGLIB 差在哪？
6. filter、interceptor、AOP 的差別是什麼？
7. DTO / VO / Entity / BO 你怎麼分？
8. Global exception handler 要注意什麼？
9. thread pool 參數怎麼看？queue 太大有什麼風險？
10. Future / CompletableFuture 使用時要注意什麼？
11. Java Stream 什麼情境不適合？
12. Optional 什麼情境會被濫用？
13. OOM 你會怎麼排查？
14. CPU 100% 你會怎麼排查？
15. thread dump / heap dump 你會看什麼？

## 第八層：Observability / Incident / Legacy Takeover

這是把你從功能工程師拉到 Senior 訊號的區域。

1. 一條重要 production flow 上線前，你會加哪些 log？
2. trace id / request id / order id / provider transaction id 怎麼串？
3. callback failure log 要記什麼？
4. batch job result 要記什麼？
5. Kafka lag / MQ backlog 要怎麼告警？
6. 如果線上發生訂單卡住，你第一步查什麼？
7. 如果玩家說錢不見了，你會怎麼查？
8. 如果報表和實際交易不一致，你會怎麼查？
9. legacy system 沒文件，你會從哪裡開始反推？
10. 從 API entry、DB table、log、MQ topic、batch job 反推 flow 的順序是什麼？
11. 你怎麼補一份交接文件，讓下一個人能維護？
12. 你怎麼區分「系統真的壞了」和「報表 projection 還沒同步」？
13. incident review 你會寫哪些項目？
14. 如果主管要你救一個沒交接的系統，你怎麼拆第一週工作？
15. 這類 legacy takeover 經驗你要怎麼講才不像抱怨前人？

## 第九層：System Design / Owner Decision

這一層用來練 0 到 1 或架構追問，但仍要從現有 flow 抽象，不亂喊業界標準。

1. 請設計一個 provider integration 系統，包含 request、callback、query、timeout、retry、compensation。
2. 請設計一個 wallet / bet-settle / rollback 系統，說明 source of truth 與 transaction boundary。
3. 請設計一個 MQ / batch / projection 系統，支援重跑、補資料、DLQ 與 observability。
4. 請設計一個 slot math / RTP validation flow，說明 result contract 與 simulation validation。
5. 同步 API 和非同步 event 你怎麼選？
6. 什麼時候拆服務？什麼時候不要拆？
7. 什麼時候用 DB transaction？什麼時候接受 eventual consistency？
8. 什麼時候用 cache？什麼時候寧可打 DB？
9. 什麼時候 retry？什麼時候人工補償？
10. 你怎麼設計 reconciliation？
11. 你怎麼設計 callback query fallback？
12. 你怎麼設計可灰度、可 rollback 的 rollout？
13. 你怎麼設計 system owner 的 dashboard？
14. 如果 PM 要快上線，但你看到 consistency 風險，你怎麼溝通？
15. 如果重新設計現有系統，你第一階段只改哪裡，為什麼？

## 第十層：Behavior / HR / 談薪 / 團隊協作

這一層確認能不能被 Senior 職缺接受，不只技術。

1. 你為什麼想換工作？
2. 你現在薪資 8 萬，為什麼期待 10 萬以上？
3. 如果對方問你是不是管理職，你怎麼回答？
4. 你怎麼描述自己不是正式資深，但具備資深潛力與 ownership？
5. 你怎麼處理 code review disagreement？
6. 你怎麼帶 junior 或協助同事？
7. 你怎麼向主管說明技術債風險？
8. 你怎麼在不加班文化和職涯成長之間取捨？
9. 公司縮編、你想跳但不捨，面試時要怎麼講？
10. 如果獵頭問期望薪資，你怎麼講？
11. 如果 HR 壓薪，你怎麼守底線？
12. 如果對方說你沒完整主導經驗，你怎麼回？
13. 如果對方問你最大的弱點，你怎麼講？
14. 如果對方問你未來 2-3 年想成為什麼，你怎麼講？
15. 你有什麼問題想問主管，來判斷這個職缺是不是值得去？

## 第一輪建議題組

第一輪不要從 Java 八股開始。先用這 5 題診斷主力市場定位：

1. 你用 90 秒介紹自己，主打 Senior Java Backend / Platform Backend。
2. 用 3 分鐘講 payment provider request / callback，並說清楚你不能誇大的邊界。
3. 用 3 分鐘講 wallet / bet-settle / rollback，說明 transaction boundary 與 idempotency。
4. 用 3 分鐘講 MQ / report projection，說明重跑、補資料與 duplicate message。
5. 如果面試官問「你不是完整 owner，為什麼能投 Senior？」你怎麼回答？

如果這 5 題卡住，AI 先教學與打磨，不往下擴張題庫。
