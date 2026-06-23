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

狀態：`v1.0 定稿`。接下來不要再編題目、加題或重排題庫；只有遇到實際 JD、面試回饋或題目明顯錯誤時才做最小修正。

最終結論：Nick 目前的問題已經不是題目不夠、知識點不夠、還缺哪個技術主題；真正要解決的是能不能把做過的事講清楚、能不能講出 Senior 層級思考、能不能讓面試官相信自己有 ownership。

本題庫目前設計成 `14 個主題 / 診斷池`。前 10 層涵蓋 Senior Backend 通用面試，後 4 層補 Nick 背景最相關的 provider integration、security、troubleshooting 與 architecture evolution。

重點不是把題庫從 150 題擴成 300 題。這份是診斷池，不是全刷清單；實際準備時先用「30 題核心題」收斂，且 30 題要避免太偏 Payment / Provider。Provider Integration 是 Nick 的主戰場專題，Architecture Evolution 是第二輪加分題，Real Troubleshooting 併入 Incident / Legacy Takeover 練習，不作第一輪獨立支線。

14 個主題：

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
| Provider Integration | 15 | Nick 主戰場專題：三方 provider、callback、query、routing、reconciliation；不是新必刷題庫 |
| Security / Auth | 15 | 檢查 API trust boundary、JWT / RBAC、callback 驗簽、PII / log 安全 |
| Real Troubleshooting | 12 | 第八層延伸：練真正線上問題排查，不獨立插隊第一輪 |
| Architecture Evolution | 12 | 第二輪加分：Senior -> Staff 過渡題，不列第一輪主戰場 |

建議練習順序：

1. `30 題核心`
   先把 1 分鐘版與 3 分鐘版練到能講。
2. `履歷與定位`
   確認市場主軸、claim boundary、ownership 口徑。
3. `Production Flow`
   把會做的事講成 Senior case。
4. `Transaction / Consistency`
   補高交易系統的失敗邊界與冪等。
5. `Incident / Troubleshooting`
   合併第八層與第十三層，練線上問題怎麼查。
6. `DB / MQ / Spring 基本功`
   只補 30 題或面試追問打穿的部分。
7. `Provider Integration 專題`
   當差異化武器，不取代基本盤。
8. `System Design`
   從已完成 flows 抽象，不背空泛架構詞。
9. `談薪 / HR`
   練市場定位、弱點、薪資與離職原因。
10. `Architecture Evolution`
    第二輪加分，面 Lead / Staff / Architect 候選再加深。

判斷規則：

- 題庫 v1.0 完成後，最高 ROI 不是繼續整理題目，而是開始做 `30 題核心答案草稿`、`30 題錄音自講`、`最強 3 個 project story 3 分鐘版`，並找機會投遞、面試驗證。
- 第 1 輪若卡住，不往下刷題，先教學、畫圖、代碼演示或打磨回答。
- 第 1 輪若穩，才進入第 2 輪 deep dive。
- 基本功題只補被 production case 打穿的部分，不變成泛用八股題海。
- 若某輪回答形成更好的履歷 / 面試說法，再回填 `04`、對應 flow `career-interview.md` 或 `05 / 08` 的保守素材。

## 30 題核心收斂版

若目標是 8 萬到 10-12 萬的 Senior Java Backend / Platform Backend market check，先不要全刷。優先練這 30 題；它們最貼近 Nick 目前的履歷主軸與常見追問。

### A. 履歷與定位

1. 你現在投 Senior Java Backend / Platform Backend，主軸一句話怎麼講？
2. 你不是正式 Tech Lead，也不是完整系統 owner，那你要怎麼講 ownership 才不失分？
3. 你的三個最強 project-level claim 是什麼？每個 claim 的 evidence 是什麼？
4. 如果面試官問「你現在職稱不是資深，為什麼投資深？」你怎麼回答？
5. 如果面試官問「你主導過什麼？」你怎麼保守但有力地回答？

### B. Production Flow

6. 請用 3 分鐘講一條 payment provider request flow。
7. 請用 3 分鐘講一條 payment callback flow。
8. 請用 3 分鐘講一條 wallet / bet-settle / rollback flow。
9. 請用 3 分鐘講一條 MQ / report projection flow。
10. 你怎麼把 flow 從「功能描述」升級成「Senior 風險分析」？

### C. Consistency / Idempotency

11. DB transaction 成功，但 MQ publish 失敗怎麼辦？
12. callback 重送時，怎麼避免二次入帳或二次扣款？
13. provider timeout 後，為什麼不能直接當失敗？
14. rollback、refund、compensation、reconciliation 差在哪？
15. 你會怎麼設計一個 retry-safe 的 provider callback handler？

### D. Backend 基本盤 + Provider 差異化

16. slow query 出現時，你會怎麼排查？
17. consumer lag 變高，你會怎麼排查？
18. Spring transaction 什麼情境會失效？
19. 多 provider 共用介面怎麼設計？Adapter Pattern 怎麼落地？
20. provider settlement 和平台 settlement 不一致怎麼辦？

### E. Incident / Legacy Takeover

21. 如果線上發生訂單卡住，你第一步查什麼？
22. 如果玩家說錢不見了，你會怎麼查？
23. 如果 callback 大量失敗，你會怎麼查？
24. legacy system 沒文件，你會從哪裡開始反推？
25. 你怎麼補一份交接文件，讓下一個人能維護？

### F. Behavior / 談薪

26. 你為什麼想換工作？
27. 你現在薪資 8 萬，為什麼期待 10 萬以上？
28. 如果對方說你沒完整主導經驗，你怎麼回？
29. 如果對方問你最大的弱點，你怎麼講？
30. 你有什麼問題想問主管，來判斷這個職缺是不是值得去？

優先練這五區：履歷與定位、Production Flow、Transaction / Consistency、Incident / Troubleshooting、Behavior / 談薪。Java / SQL / Redis / MQ / Security / Architecture 題只有在這五區被追問打穿、JD 明確要求，或 Nick 主動想補時才深入。

## 主戰場梯隊

### 第一梯隊：最重要

1. 履歷與自我定位。
2. Production Flow。
3. Transaction / Consistency。
4. Observability / Incident。
5. Behavior / 談薪。

### 第二梯隊：加分與基本盤補強

1. MQ。
2. Database。
3. Spring。
4. Provider Integration。

### 第三梯隊：進階追問

1. System Design。
2. Security。
3. Architecture Evolution。

## 面試官真正想知道

面試官真正想知道的不是 Kafka API 背多少、Redis 指令背多少、JVM 參數背多少，而是：

- 有沒有能力理解 production flow。
- 出事時能不能查。
- 能不能分析風險。
- 能不能獨立負責一塊系統。
- 能不能接手沒文件的系統。
- 能不能跟 PM、QA、商戶或 provider 溝通。

## 對 Nick 最重要的 4 件事

1. 三個 Project Story：例如 Payment Integration、MQ / Projection、Legacy Takeover / Incident。每個都要能講背景、問題、角色、方案、風險、結果，控制在 3-5 分鐘。
2. 四條核心 Flow：Payment Request、Payment Callback、Wallet / Bet / Settle、MQ Projection。要能白板講 source of truth、transaction boundary、retry、idempotency、compensation。
3. Ownership 故事：重點不是說自己是正式 Tech Lead，而是能說「雖然不是正式 owner，但我負責分析 flow、釐清問題、評估風險、推進修復與驗證結果」。
4. 為什麼值 10 萬以上：核心不是工作很多年，而是已經能獨立理解複雜系統、處理 provider 整合、分析交易流程、排查 production 問題，並承擔部分 owner 型責任。

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

### 第一層 90 秒草稿：定位主線

> 2026-06-23 補充：這段是「看稿練習草稿」，不是新的標準答案，也不新增履歷 claim。正式投遞仍以 `08-application-autobiography-zh.md` 的 A 版為主；面試回答只用來維持定位一致：`Java Backend -> Provider Integration -> Production Flow -> Legacy Takeover -> Owner-like production thinking`。不要把 `Owner-like thinking` 說成正式 Tech Lead、Architect 或完整系統 owner。

1. 主軸一句話

   ```text
   我目前主要定位是 Java Backend Engineer，過去幾年工作內容集中在遊戲平台、第三方 provider integration 與高交易 production flow，包含 payment provider、遊戲 provider、wallet / bet-settle、MQ、batch 與 report projection。我比較擅長接手既有系統，透過 code reading、log、DB、MQ 與 git history 理解資料流與狀態轉換，分析線上風險並協助把流程整理到可維護、可追問的狀態；所以目前希望往 Senior Java Backend / Platform Backend 發展。
   ```

2. Ownership 口徑

   ```text
   我目前不是正式 Tech Lead，也不是完整系統 owner，所以我不會把自己包裝成完整平台負責人。不過在幾個專案裡，我做的事情不只是單點功能實作，而是需要先理解整條 flow、釐清需求、分析資料流與狀態轉換、評估修改風險，再協助驗證結果。很多時候我會透過 code reading、log、DB、MQ 與 git history 還原系統行為；所以比較準確的說法是，我已經開始用 owner-like 的角度處理 production flow，但不誇大成正式 owner title。
   ```

3. 三個最強 project-level claim

   ```text
   我目前最有代表性的經驗會收斂成三塊。第一是第三方 provider integration，包含 payment provider 與遊戲 provider 的 request、callback、query、withdraw 或 transfer 類流程。第二是 wallet / bet-settle / MQ / projection 相關資料流，重點是交易狀態、source of truth、非同步投影與補償邊界。第三是 legacy takeover / system reconstruction，在缺乏完整文件時，透過 code reading、log、DB、MQ 與 git history 重建 production flow。這三塊支撐我投 Senior Backend / Platform Backend，而不是靠單一工具或職稱。
   ```

4. 履歷 / 面試 / 不可誇大邊界

   ```text
   我會把能被追問到底、且有本人確認、commit、flow evidence 或 project-level consolidation 支撐的內容放履歷，例如 payment provider integration、遊戲 provider / wallet flow、MQ / projection、legacy system reconstruction。面試時可以補更多脈絡，例如 callback 重送、timeout unknown、compensation、slot math contract 或 system map。但沒有完整拍板、完整架構 owner 或完整平台責任的部分，我不會寫成主導，例如完整 wallet architecture、完整 reconciliation platform、完整 Kafka platform 或完整 DevOps / SRE owner。
   ```

5. Payment 能講與不能講

   ```text
   Payment 我可以講的是第三方金流 provider / 商戶對接，包含 request、callback、query、withdraw、merchant order id、signature、response parsing、timeout unknown 與 order consistency 類問題。不能講的是完整 ledger、完整 accounting、完整 reconciliation owner 或完整金流平台 owner。如果被問到 reconciliation，我會說我有接觸相關風險與流程，也理解查單、callback evidence 與人工補償邊界，但我沒有擔任完整 reconciliation owner。
   ```

6. game_job / MQ / projection 能講與不能講

   ```text
   MQ / projection 我可以講的是 request log、bet record、Kafka / RabbitMQ consumer、Quartz job、report projection、batch summary、重跑與補資料。回答時我會把重點放在資料如何進 MQ、如何被消費、如何更新 projection，以及資料不一致時怎麼排查 source of truth。不能講的是完整 Kafka cluster owner、完整 MQ platform owner、exactly-once 保證或完整 BI platform owner；我會把它定位成參與 job / event processing 與報表 projection 維護。
   ```

7. AntPlay slot / math 差異化

   ```text
   Slot math 不是我的主要職責，所以我不會把自己定位成完整 Slot Math Engineer。但我有參與 slot math core / 多個 math module 的維護與驗證，也接觸過 fixedMultiBet、RTP / reel strip、buy free、jackpot / symbol 與 feature result contract。這讓我在處理遊戲平台時，不只理解 API、wallet、bet-settle 與資料流，也能理解部分遊戲結果產生與驗證機制。它是我的 domain 差異化，不是取代 Java Backend 主線。
   ```

8. 內部產品名處理

   ```text
   正式履歷、104、LinkedIn 或給獵頭的版本，我不會直接堆內部產品名或 repo 名，而會改成泛稱，例如遊戲平台 / gameserver、Slot 遊戲平台、provider connector / 後台控制面平台、第三方遊戲 provider。KB 裡可以保留 iwin、AntPlay、UGSoft 等名稱方便 evidence 回查；真正對外時，除非確認是公開品牌且沒有保密風險，否則用泛稱會比較安全。
   ```

9. 職稱不是資深，為什麼投資深

   ```text
   我知道自己目前職稱不一定是正式 Senior，也沒有正式帶團隊的 title。不過近幾年的工作內容已經不只是單純功能開發，而是開始接觸 provider integration、交易流程、MQ / batch、既有系統接手與 production troubleshooting。我希望透過市場驗證，確認自己在 production flow 理解、風險判斷、跨系統資料流與 owner-like 思考上，是否已經符合 Senior Java Backend / Platform Backend 的要求。
   ```

10. 主導過什麼

    ```text
    如果是整個平台層級的主導，我目前不會說自己有完整 owner 經驗。但在幾個專案裡，我有負責或參與從需求理解、流程分析、實作、驗證到上線支援的完整交付，例如 provider integration、callback flow、MQ projection、wallet / bet-settle 相關維護，以及既有系統的 flow reconstruction。比較準確的說法是，我能 owner 一條 production flow 的理解、風險分析與交付驗證，但不把它擴張成完整平台 owner。
    ```

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

## 第十一層：Provider Integration

這是 Nick 的主戰場專題，不是新必刷題庫。比起空泛微服務題，payment / game provider integration 更能展現你對 timeout、callback、query、routing、settlement、reconciliation 與 trust boundary 的判斷；但第一輪仍要先證明 Java Backend 基本盤穩。

1. Provider 接進來時，從 request、callback、query 到 reconciliation 的完整流程是什麼？
2. Provider callback 不可信時，你會做哪些 trust boundary 檢查？
3. Provider callback 和 query 結果不同時，你相信哪邊？怎麼收斂？
4. Provider timeout 怎麼處理？為什麼不能直接當成功或失敗？
5. Provider API rate limit 怎麼辦？
6. Provider schema 改版時，你怎麼兼容新舊版本？
7. 多 provider 共用介面怎麼設計？
8. Adapter Pattern 在 provider integration 裡怎麼落地？不要只講設計模式名詞。
9. Provider 故障時怎麼切流？哪些流量不能亂切？
10. Provider routing 規則怎麼設計？要不要放 Redis？
11. provider settlement 和平台 settlement 不一致怎麼辦？
12. reconciliation 怎麼做？source of truth 怎麼選？
13. provider replay attack 怎麼防？
14. signature 驗證怎麼做？哪些欄位一定要參與簽名？
15. provider transaction id 和 internal order id 怎麼 mapping？哪個可以當 idempotency key？

## 第十二層：Security / Auth

這層不是要變資安專家，而是 Senior Backend 不能忽略 API trust boundary、身分驗證、權限、敏感資訊與金流 callback 安全。

1. JWT 是什麼？適合解決什麼問題？
2. JWT 有什麼風險？token 洩漏後怎麼辦？
3. refresh token 怎麼設計？要不要 rotating refresh token？
4. RBAC 怎麼設計？
5. 權限表怎麼拆？role、permission、user-role、role-permission 怎麼看？
6. API 怎麼防重放攻擊？
7. API 怎麼防暴力破解？
8. 密碼怎麼存？為什麼不能明文或可逆加密？
9. bcrypt 為什麼不能解密？salt / cost 大概在解決什麼？
10. Session 和 JWT 怎麼選？
11. SSO 流程大概怎麼走？
12. OAuth2 Authorization Code Flow 大概在做什麼？
13. 金流 callback 為什麼一定要驗簽？
14. 什麼資料不能進 log？
15. PII 怎麼處理？正式履歷或面試講內部資料時要注意什麼？

## 第十三層：Real Troubleshooting（第八層延伸）

這層併入第八層一起練，不獨立插隊第一輪。回答重點不是背工具，而是能說出先看什麼、怎麼縮小範圍、如何保護線上、怎麼補資料。

1. API latency 突然變高怎麼查？
2. CPU 100% 怎麼查？
3. JVM memory 持續上升怎麼查？
4. MQ backlog 怎麼查？
5. Redis QPS 爆掉怎麼查？
6. DB connection pool 滿了怎麼查？
7. 某個商戶或 provider 一直 timeout 怎麼查？
8. callback 大量失敗怎麼查？
9. 某玩家說少錢怎麼查？
10. 某筆訂單卡 Pending 怎麼查？
11. provider 故障怎麼切流？
12. 線上事故你怎麼定優先順序？

## 第十四層：Architecture Evolution

這是 Senior 到 Staff / Architect 過渡區，放第二輪加分，不列第一輪主戰場。不是背流行詞，而是判斷什麼該做、什麼不要做、第一階段先改哪裡。

1. Monolith 什麼時候該拆？
2. 為什麼不要亂拆微服務？
3. Redis 要不要上？什麼資料不該上？
4. Kafka 要不要上？什麼情境 RabbitMQ / DB job 反而比較簡單？
5. Event Sourcing 值不值得？
6. CQRS 值不值得？
7. DB Sharding 值不值得？
8. 什麼時候接受 eventual consistency？
9. 哪些系統一定要 strong consistency？
10. 什麼情況不要做 abstraction？
11. 技術債怎麼排優先順序？
12. 你會怎麼做 migration plan？

## 第一輪建議題組

第一輪不要從 Java 八股開始。先用這 5 題診斷主力市場定位：

1. 你用 90 秒介紹自己，主打 Senior Java Backend / Platform Backend。
2. 用 3 分鐘講 payment provider request / callback，並說清楚你不能誇大的邊界。
3. 用 3 分鐘講 wallet / bet-settle / rollback，說明 transaction boundary 與 idempotency。
4. 用 3 分鐘講 MQ / report projection，說明重跑、補資料與 duplicate message。
5. 如果面試官問「你不是完整 owner，為什麼能投 Senior？」你怎麼回答？

如果這 5 題卡住，AI 先教學與打磨，不往下擴張題庫。
