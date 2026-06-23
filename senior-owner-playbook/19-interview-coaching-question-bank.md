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

### 第二層 90 秒草稿：Production Flow 講法

> 2026-06-23 補充：這段是「看稿練習草稿」，用來把 flow 從功能描述升級成 Senior 面試說法。回答時固定沿著 `業務目的 -> 主要資料流 -> 狀態轉換 -> failure window -> 觀測 / 排查 -> claim boundary` 講；不要只背 Controller / API / Kafka 名詞，也不要把 code-backed 或 analysis-only 的 flow 說成自己完整主導。

通用模板：

```text
這條 flow 的業務目的，是解決什麼 production 問題。
正常路徑上，request / event 從哪裡進來，經過哪些 service、DB、Redis、MQ 或 external provider。
接著要說清楚狀態在哪裡改變，哪裡是 source of truth，哪裡只是 cache、projection 或 audit log。
再補 failure window：timeout、retry、callback 重送、MQ duplicate、projection 落後、rollback 重複執行或下游失敗。
最後補觀測與邊界：出事時用哪些 log / id / DB / MQ / provider evidence 查；我實際參與、維護或分析到哪裡，哪些不能誇大。
```

1. Payment provider request flow

   ```text
   以充值或 provider request 為例，這條 flow 的目的不是單純呼叫 API，而是建立一筆可追蹤、可查單、可補償的 payment order。正常路徑會先驗證參數、商戶與 provider 設定，建立本地 payment order，產生 internal order id 或 merchant order id，再透過 provider adapter 組 request、做簽章與金額單位轉換，送到第三方 provider。provider 回應後，本地通常只能先判斷 request 是否受理或是否進入未知狀態，不能把單次 HTTP response 當成交易最終真相。我會特別看 order 是否成功建立、provider request 是否送出、timeout 要如何標示，以及 provider transaction id 和 internal order id 的 mapping 是否能支撐後續 callback / query / 補償。
   ```

2. Payment callback flow

   ```text
   Payment callback 的核心是 idempotency 和 order consistency。provider 完成支付後會 callback，本地收到後要先驗簽與檢查來源，再用 merchant order id 或 provider transaction id 找到本地訂單，確認目前狀態是否允許轉換。若訂單已經是成功、失敗或人工補單後的終態，就不能因為 provider 重送 callback 而重複入帳或覆蓋舊狀態。這類 flow 最常見的 failure window 是 callback 重送、timeout unknown、人工補單後 callback 又進來、DB 成功但下游副作用失敗。所以我會優先講終態保護、idempotency key、callback audit log、查單與人工補償邊界；但不把它誇大成完整 reconciliation owner。
   ```

3. Wallet / bet-settle / rollback flow

   ```text
   Wallet / bet-settle / rollback flow 的重點是 money correctness，而不是 API 有沒有回傳成功。玩家下注時，系統會驗證玩家狀態與餘額，建立 bet record，執行扣款或 transfer wallet 動作；遊戲結果產生後，settle flow 會依結果計算派彩、更新 wallet / transaction record，並寫入後續 report 或 log。若 provider 回傳 rollback，或中途發生 timeout / partial failure，就要依 transaction id、round id 或 provider action key 判斷能不能回滾。這裡最怕的是重複 settle、重複 rollback、扣款成功但 bet record 寫失敗、report projection 和 wallet truth 不一致。所以我會先拆清楚 wallet / bet record 是交易真相，request log / report 多半是 audit 或 projection，再講 idempotency、狀態機與補償。
   ```

4. MQ / report projection flow

   ```text
   MQ / report projection flow 的目的，是把線上交易和報表查詢解耦。線上交易完成後，系統透過 Kafka、RabbitMQ 或 job event 發送資料，consumer 或 projection service 消費後更新報表表、統計表或查詢用資料。這裡要先講清楚 source of truth：交易系統、wallet、order 或 bet record 才是主資料，report projection 只是查詢與營運分析用的衍生資料。風險主要是 MQ 延遲、duplicate message、consumer failure、DLQ、projection 落後或 batch 重跑造成重複寫入。所以我會關心 event identity、consumer idempotency、重跑範圍、補資料方式、lag / backlog 告警，以及報表和交易真相不一致時怎麼查。
   ```

5. Partition / high-traffic data flow

   ```text
   像 bet record、request log、transaction log 這類高流量資料，問題通常不是單筆寫入，而是資料量成長後查詢、報表、備份與維運會變慢。常見做法是依時間、schema、table suffix 或 routing key 做 partition / sharding。寫入時要先決定 routing rule，寫到正確的實體表；查詢時則依時間區間、商戶、玩家或業務條件縮小查詢範圍。這類 flow 的風險是 route miss、ThreadLocal / schema context 沒還原、跨表查詢效能差、補資料漏表、migration / backfill 不完整。所以我會把重點放在統一路由規則、query boundary、migration / backfill 檢查與監控，而不是只說「有分表」。
   ```

6. Controller -> DB / Redis / MQ / external provider 拆層

   ```text
   我會從 request 入口開始拆，而不是一開始就跳到工具。Controller 負責接 request、做基本 validation 與轉交；Service 負責業務判斷與流程編排；transaction boundary 內處理必須一致提交的 DB 狀態；Redis 通常只作 cache、config、routing 或 lock，不應取代交易 source of truth；MQ 用來處理 async audit、projection、notification 或補資料；external provider 則是外部副作用，必須保留 request / response / callback / query evidence。這樣拆可以說清楚狀態在哪裡改變、哪裡可能失敗，以及失敗時要從 DB、log、MQ 還是 provider evidence 查。
   ```

7. Source of truth / cache / projection / audit log

   ```text
   Source of truth 是業務真相，例如 payment order、wallet transaction、bet record 或 provider transaction mapping；Cache 是為了效能或快速讀設定，例如 Redis config / routing cache；Projection 是為了查詢和報表效率，例如 report table、summary table；Audit log 是為了追蹤與排查，例如 request log、callback log、MQ consume log。這四者用途不同，不能混用。面試時如果報表和交易不一致，我會先回 source of truth 查 order / wallet / bet record，再看 projection 是否延遲或 consumer 失敗，而不是直接相信報表。
   ```

8. 下游 service 沒掃過怎麼講

   ```text
   如果下游 service 不是我負責或沒有完整掃過，我會明確切邊界。我可以描述我確認過的入口、輸入輸出契約、request / response、DB evidence、MQ topic 或 callback evidence，也可以說明資料會往下游流動以及可能的 failure window；但下游內部實作如果我沒有讀過，就不會包裝成我完整掌握。這樣回答不是扣分，反而比較符合 Senior 的 trust boundary：我知道系統邊界在哪，也知道哪些是已確認、哪些是待確認。
   ```

9. Code-backed 但不是直接開發

   ```text
   如果一段 flow 不是我直接開發，但我因為維護、排查或交接需要讀過，我會說它是 code-backed analysis，而不是我的 direct implementation。我可以透過 code reading、log、DB、MQ、git history 和重要 diff 描述資料流、狀態轉換與風險點；但不會把它寫成我主導或獨立完成。比較安全的說法是：這段流程我有分析與維護脈絡，能講清楚風險與改善方向；若要放履歷，仍要回到 project-level contribution evidence。
   ```

10. 從功能描述升級成 Senior 風險分析

    ```text
    我會要求自己每講完一條 flow，都補一句「這條 flow 最大風險是什麼」。Junior 說法可能只停在這個 API 會寫 DB、發 MQ；Senior 說法要能指出 callback 重送、provider timeout、DB 成功但 MQ 失敗、MQ duplicate、projection 落後、wallet 不一致、rollback 重複執行或補資料漏資料。接著要能說怎麼降低風險，例如 idempotency、狀態機 guard、retry policy、DLQ、reconciliation、alert、runbook 或人工補償。這樣才會從功能描述變成 production risk analysis。
    ```

優先看稿練習順序：

```text
Payment Callback -> Wallet / Bet-Settle / Rollback -> MQ / Projection -> Legacy Takeover / flow reconstruction
```

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

### 第三層 90 秒草稿：Consistency / Idempotency 講法

> 2026-06-23 補充：這段是「看稿練習草稿」，用來把高交易 flow 的追問收斂成一致性思考。回答時先講 `風險在哪 -> 怎麼避免 -> 出事怎麼補救`。可以用 Outbox、retry、compensation、reconciliation 作設計選項，但不要說成 Nick 已主導完整 outbox / reconciliation platform。

通用模板：

```text
這題我會先看不一致風險在哪裡。
如果只在同一個 DB transaction 裡，transaction 可以解一部分問題；但只要跨 MQ、Redis、provider、gameserver 或其他 service，就要把狀態拆成已確認、未知、待補償。
避免方式通常是 idempotency、狀態機 guard、唯一 key、終態保護、retry policy 或 outbox / repair table。
出事後則靠 audit log、query、補償 job、reconciliation 或人工修復收斂，不能假裝一次 API response 就代表最終真相。
```

1. 只靠 `@Transactional` 不夠的情境

   ```text
   `@Transactional` 只能保證同一個 DB transaction 內的操作一致。只要流程跨到 MQ、Redis、第三方 provider、gameserver 或其他 service，就已經超出單一 transaction 邊界。例如本地 DB commit 成功，但 MQ publish 失敗，或 provider request 已送出但本地 timeout，DB transaction 都不能自動把外部副作用回滾。這時需要把狀態設計成可恢復，例如 retry、補償 job、outbox / repair table、查單、callback audit 或 reconciliation。我的回答重點會放在 transaction boundary，而不是只說加 `@Transactional`。
   ```

2. DB 成功，但 MQ publish 失敗

   ```text
   這是典型半成功狀態：DB 已經 commit，但後續 event 沒送出去，consumer、報表或通知就不會發生。第一步要承認系統已經不一致，不能假裝 MQ 一定成功。常見做法是 outbox pattern，把要發的 event 先和 business state 一起落 DB，再由 background publisher 發送；或至少要有 retry / repair job 掃描 pending event。實務上我會特別關心這種半成功能不能被觀測，例如 event status、publish error log、補送次數與告警，讓它能被重送或人工處理。
   ```

3. MQ publish 成功，但 consumer 失敗

   ```text
   MQ publish 成功但 consumer 失敗時，通常要用 at-least-once 的心態設計。重點不是幻想訊息只會被處理一次，而是讓 consumer 可以 retry 且具備 idempotency。失敗可以透過 retry queue、DLQ、backoff 或 job replay 收斂；但 consumer 寫 DB、發通知、補資料這些副作用要用 event id、business key 或 unique constraint 避免重複。面試時我會說，真正要保護的是重試不造成二次副作用，而不是完全避免重送。
   ```

4. Provider timeout 不能直接當失敗

   ```text
   Provider timeout 只代表本地沒有在時間內收到結果，不代表對方沒有執行成功。以 payment 或 transfer request 來說，request 送出後 provider 可能已經扣款或受理，只是 response 沒回來；如果本地直接當失敗並讓使用者重送，可能造成重複訂單、重複扣款或狀態不一致。所以 timeout 應該視為 unknown / pending state，後續透過 provider query、callback、statement 或人工補償收斂。這題我會特別強調：unknown 是一種狀態，不是失敗的同義詞。
   ```

5. Callback 重送避免二次入帳 / 扣款

   ```text
   Callback 不能假設只來一次，所以處理前一定要先做 idempotency 與狀態檢查。通常會用 internal order id、merchant order id 或 provider transaction id 找到本地訂單，再確認目前狀態是否允許轉換。如果訂單已經是成功、失敗、退款完成或人工補單後的終態，就應該只記錄 audit log 或直接回 provider success，不再執行入帳、扣款、派彩或通知副作用。真正的關鍵是：終態保護要在副作用之前，而不是副作用做完才檢查。
   ```

6. Idempotency key 放哪裡

   ```text
   Idempotency key 要看業務邊界。Request id 偏技術追蹤，一次 HTTP request 可能有一個，但不一定代表業務唯一性；order id 或 merchant order id 比較適合代表一筆本地業務訂單；provider transaction id 則是第三方世界的唯一識別，適合 callback 去重、查單與對帳。provider integration 裡常常要同時保存 internal order id 和 provider transaction id，因為本地狀態機和外部 statement 需要靠 mapping 收斂。不能只用 trace id 這類技術 id 當 money flow 的 idempotency key。
   ```

7. 終態訂單能不能覆蓋

   ```text
   一般情況下，成功、失敗、退款完成、rollback 完成這類終態不應被普通 callback 或 retry 覆蓋，否則容易造成重複副作用或狀態倒退。例外是 reconciliation、人工補單、人工修正或特殊錯帳修復，但這些應該走明確流程，有 audit log、操作人、原因、前後狀態與可追蹤 evidence。我的原則是：自動流程不能隨意覆蓋終態；如果業務真的需要修正終態，就要把它當作人工 / 對帳修復，而不是一般 callback state transition。
   ```

8. Rollback / refund / compensation / reconciliation 差別

   ```text
   Rollback 通常是撤銷同一筆交易或回復同一個業務動作，例如 bet rollback；refund 是資金退回，常常是一筆新的資金動作；compensation 是為了修復跨系統不一致而做的補償行為，可能是補發 MQ、補寫 projection、補退款或補狀態；reconciliation 則是對帳，用多方資料比對出最終真相。面試時我會把它們分開講，因為 rollback 不等於 reconciliation，refund 也不一定代表原交易不存在。
   ```

9. Optimistic lock vs pessimistic lock

   ```text
   我會先看衝突頻率和業務風險。讀多寫少、衝突可接受、失敗可 retry 的場景，我傾向 optimistic lock，例如 version 欄位或 update rows check；如果是 wallet balance、庫存、同一筆訂單狀態轉移這類衝突成本很高的場景，可能考慮 pessimistic lock 或更嚴格的 serial execution。但 lock 不是唯一答案，還要搭配 idempotency、狀態機與 unique key；否則只加 lock 可能降低併發，卻沒有解決重送或外部副作用。
   ```

10. Isolation level 實務關注

    ```text
    我不會只背四種 isolation level，而會先講業務會遇到什麼讀寫異常。實務上我會關心 dirty read、non-repeatable read、phantom read、lost update 或併發下重複處理同一筆狀態。大部分系統會先用預設 isolation level 搭配 row lock、unique constraint、version、狀態條件 update 來控制風險；只有當業務真的需要更強一致性，才評估提高 isolation level，因為它會影響 lock、throughput 和 deadlock 風險。
    ```

11. Wallet 扣款成功，但 bet record 寫入失敗

    ```text
    這是 money flow 裡很嚴重的不一致：玩家餘額已改，但交易紀錄不完整。第一步要先確認 wallet 是否是 source of truth，以及扣款是否真的成功，不能只補 bet record 或只看報表。如果 wallet mutation 和 bet record 不在同一個 transaction，就需要補償或 repair 流程，例如用 wallet log、provider transaction、round id 或 request log 重建狀態，再決定補寫 bet record、退款、rollback 或標記人工處理。這題我會強調：先確認錢的真相，再修衍生紀錄。
    ```

12. Bet settle 成功，但 projection 沒更新

    ```text
    如果 projection 只是報表或查詢用資料，bet settle 成功代表交易主線已完成，source of truth 仍然是 bet record、wallet transaction 或 provider transaction，而不是 projection。這時不應該為了報表落後去回滾交易，而是補 projection，例如重送 MQ、重跑 consumer、用 batch 補資料或掃 source table rebuild derived table。這題的重點是分清交易 truth 和報表 truth：報表落後是可補資料問題，不一定是交易失敗。
    ```

13. 人工補單後 callback 又進來

    ```text
    人工補單和 callback 必須共用同一套終態檢查與 idempotency 規則，否則人工修正後 callback 又進來，可能造成重複入帳或狀態覆蓋。我的做法會是：人工補單時把訂單推進明確終態，留下 audit log、操作人與原因；後續 callback 進來先查狀態，如果已終態就只補 callback evidence 或回 provider success，不再執行上分、扣款、派彩等副作用。這樣才能讓人工修復和自動 callback 不互相打架。
    ```

14. BigDecimal production 風險

    ```text
    金額計算不能用 double，因為會有精度問題；BigDecimal 也不是用了就安全。常見風險包括 scale 不一致、rounding mode 不一致、`equals` 和 `compareTo` 行為不同、中途轉 double、不同 provider 的金額單位不同，例如元、分或不同幣別 precision。production 裡需要統一金額單位、scale、rounding mode 和比較方式，provider request / callback / DB 儲存也要一致。這題我會把 BigDecimal 連回 payment provider amount unit 和 money correctness，而不是只背 API。
    ```

15. Retry-safe callback handler

    ```text
    Retry-safe callback handler 我會分幾步設計。第一，先驗簽和檢查 provider trust boundary；第二，用 internal order id、merchant order id 或 provider transaction id 找到本地訂單；第三，在任何副作用前先做終態與 idempotency 檢查；第四，用狀態機限制 allowed transition，避免終態被覆蓋；第五，更新狀態時保留 callback audit log；第六，後續 MQ、通知或 projection 要能 retry 且 idempotent。即使 provider 重送多次，結果也應該是同一筆訂單只被收斂一次，不造成重複入帳或重複扣款。
    ```

優先看稿練習順序：

```text
Provider Timeout -> Callback Idempotency -> Wallet 扣款成功 / Bet Record 失敗 -> Projection 落後 -> Retry-safe Callback Handler
```

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

### 第四層 90 秒草稿：MQ / Batch 講法

> 2026-06-23 補充：這段是「看稿練習草稿」。Nick 的定位不是 Kafka expert 或 MQ platform owner，而是在高交易系統中使用 MQ 處理 request log、bet record、projection、batch flow，因此重點要放在資料一致性、重試、補資料與觀測性。不要說成完整 Kafka / RabbitMQ platform、exactly-once 或完整 replay / DLQ governance owner。

通用模板：

```text
我會先說為什麼用 MQ：解耦主流程、降低同步延遲、隔離報表 / audit / 通知或支援補資料。
接著講風險：duplicate message、ordering、consumer failure、lag、DLQ、schema 不相容、projection 落後。
最後講補救：consumer idempotency、retry / DLQ、replay / backfill、unique key / upsert、lag alert、error log 與可重跑 runbook。
```

1. At-least-once

   ```text
   At-least-once 代表訊息至少會送達一次，但可能送達多次。它選擇的是不要輕易丟資料，但代價是 consumer 不能假設只處理一次。實務上我會把 consumer 設計成 idempotent，例如用 order id、transaction id、event id、provider bet id 或 projection key 判斷是否已處理過；如果已處理，就直接忽略或回成功。這題我會連回 request log、bet record、report projection：MQ 重送是正常風險，重點是重送不能造成重複入帳、重複報表累加或重複通知。
   ```

2. Duplicate message

   ```text
   Duplicate message 不應該只靠 MQ 保證不發生，而要由 consumer 和業務 key 防守。常見做法是用 event id、business id、order id、transaction id 或 provider bet id 查重，搭配 DB unique key、processed table、upsert 或狀態機 guard。如果是 report projection，要避免重複累加；如果是 request log，要避免同一筆 audit 重複污染查詢；如果是 money-like side effect，更要先做 idempotency 再執行副作用。我的口徑會是：可以接受重送，但不能接受重複副作用。
   ```

3. Message ordering

   ```text
   不是所有訊息都需要強排序。若事件之間有狀態依賴，例如同一筆 bet 的 bet -> settle -> rollback，或同一 wallet / order 的狀態轉移，ordering 就很重要；通常會用同一個 partition key 或同一條序列處理。相反地，request log、audit log、部分通知或可重建 projection，通常不需要全域強排序。強排序會犧牲吞吐與擴展性，所以我會先問：這個業務真的需要排序嗎？需要的是同一實體內排序，還是全系統排序？
   ```

4. Kafka partition key

   ```text
   Partition key 要依業務邊界選，不只是為了平均分散。若同一個 wallet、order、player、round 或 provider transaction 的事件需要保持相對順序，就應該用能代表該實體的 key，讓相關事件進同一 partition。若目標是 report projection，key 可能是 playerId、agentId、dataDay、currency 或 bet id，取決於更新粒度。選錯 key 可能造成同一筆業務被多個 consumer 併發更新，也可能造成 hot partition，所以要在 ordering 和 load distribution 間取捨。
   ```

5. Consumer lag 變高

   ```text
   Consumer lag 變高時，我會先分三類看：producer 突然變多、consumer 變慢、下游依賴異常。接著看 consumer processing time、error rate、retry 次數、DB slow query、外部 API timeout、thread pool、connection pool、CPU / memory 和 partition 分布。如果只是短期尖峰，可以先水平擴容或加快批次；如果是單筆處理慢，就要找 DB / provider / lock / serialization bottleneck；如果是 poison message，一直 retry 反而會拖垮整個 consumer，要進 DLQ 或隔離處理。
   ```

6. DLQ

   ```text
   DLQ 是 Dead Letter Queue，用來保存多次重試仍無法正常處理、或不適合繼續阻塞主 consumer 的訊息。資料格式錯、必填欄位缺失、mapping 找不到、業務狀態不允許、重試多次仍失敗，都可能進 DLQ。暫時性錯誤，例如 DB 短暫 timeout 或 network jitter，通常先 retry，不一定直接進 DLQ。DLQ 不是垃圾桶，還需要告警、查詢、修資料、replay / skip 流程，否則只是把問題藏起來。
   ```

7. Retry 放哪裡

   ```text
   Retry 要看錯誤類型和副作用邊界。Producer retry 適合處理 publish MQ 失敗，但要避免重複 publish 造成 duplicate；consumer retry 適合暫時性錯誤，例如 DB timeout，但 consumer 必須 idempotent；queue-level retry / DLQ 適合集中管理重試與隔離壞訊息；batch job retry 適合補資料、重建 projection 或修復時間窗資料。資料錯誤或 schema 不相容時，盲目 retry 沒意義，應該進 DLQ 或人工修正。
   ```

8. Batch job 重跑避免重複寫入

   ```text
   Batch job 一開始就要設計可重跑性，不能假設只會成功跑一次。常見方式是用 deterministic key、unique constraint、upsert、先標記批次範圍、staging table、versioned snapshot，或在可接受的 report projection 場景中先刪除指定範圍再重建。最大風險是重複寫入、漏資料或半批次成功。重跑前要清楚 source range、data day、currency、商戶 / provider 維度與 processed count；重跑後要能對 expected count / actual count。
   ```

9. Projection 跟 online transaction 分開

   ```text
   Online transaction 追求交易正確性和快速回應，projection 追求查詢效率和營運分析。交易資料、wallet、order、bet record 才是 source of truth；report projection 可以延遲、可以補、可以重建。如果把報表寫入綁死在主交易裡，報表 DB 慢、consumer 失敗或統計邏輯錯，都可能拖垮交易主流程。比較安全的做法是透過 MQ 或 batch 非同步更新 projection，但要有 lag / error / DLQ / replay 觀測，避免 projection 落後變成 silent failure。
   ```

10. Quartz job 跑到一半失敗

    ```text
    我會先問這個 job 是否可重跑，以及它有沒有 checkpoint。如果是 projection / summary / backup 類 job，通常可以根據時間窗、batch id 或 source range 重跑；如果它涉及資金、狀態轉移或外部副作用，就要先確認執行到哪個階段，是否已寫 DB、是否已發 MQ、是否已呼叫 provider，避免重跑造成重複副作用。比較好的設計是記錄 start / end / processed / failed count、batch status、checkpoint 和錯誤明細，讓重跑有邊界。
    ```

11. MQ schema 改版

    ```text
    MQ schema 改版最大的風險是 producer 和 consumer 不相容。例如新增欄位、改欄位語意、改 enum、移除欄位或金額單位改變，都可能讓舊 consumer parse 失敗或誤解資料。安全做法是向後相容，先讓 consumer 能接受新舊格式，再切 producer；新增欄位要有 default，移除欄位要分階段，語意改變要版本化。高交易資料尤其要注意 amount、currency、provider transaction id、data day 這類欄位的 contract。
    ```

12. Consumer 水平擴容與 race condition

    ```text
    Consumer 水平擴容可能提升吞吐，但也可能造成 race condition。若同一個 order、wallet、player 或 projection key 的事件被不同 consumer 同時處理，就可能發生重複更新、狀態覆蓋或累加錯誤。常見防守方式是選好 partition key，讓同一實體事件進同一 partition；或在 DB 層用 unique key、version、狀態條件 update、lock 或 idempotency table 控制。不能只說加 consumer，還要確認資料競爭邊界。
    ```

13. Request log async 化

    ```text
    Request log async 化的好處，是把 audit log 寫入從主交易同步路徑拆出來，降低線上 request latency，也避免 log DB 抖動直接拖慢 provider API。但它的風險是 MQ publish failure、consumer lag、duplicate log、routing mismatch 或 log 延遲出現。這類 request log 應定位成 audit / observability data，不是交易 source of truth；主流程不應因 audit log 失敗就 rollback，但要有 publisher failure log、DLQ / retry、queue lag alert 和補寫策略，否則事故排查時會缺 evidence。
   ```

14. Bet record 從 API 進 MQ 再進 admin query 的落差

   ```text
   Bet record 從 API 進 MQ 再到 admin query，中間可能有 MQ 延遲、consumer failure、projection 落後、batch 尚未完成、schema routing 錯誤或查詢時間窗不一致。因此玩家交易可能已成功，但後台暫時查不到或報表還沒更新。這時不能直接判斷交易失敗，要先回 source of truth 查 wallet / bet record / provider transaction，再看 MQ offset、consumer log、projection table、DLQ 和 batch status。面試時我會強調：admin query 是查詢面，不一定是交易真相。
   ```

15. 可補資料、可重跑、可觀測 projection flow

   ```text
   我會把交易資料和 projection 明確分離，交易資料是 source of truth，projection 是可重建的 derived state。event 進 MQ 時要有 event id、business key、source range 或 data day；consumer 更新 projection 時要用 upsert、unique key 或 deterministic projection key 保證 idempotency。失敗時可以 retry、進 DLQ、replay 特定時間窗或用 batch rebuild。觀測上要看 consumer lag、error rate、DLQ 數量、processed count、projection delay、replay result 和資料對帳差異，確保資料落差能被發現與補救。
   ```

優先看稿練習順序：

```text
At-Least-Once -> Consumer Lag -> Projection vs Source of Truth -> Request Log Async 化 -> 可補資料 / 可重跑 / 可觀測 Projection Flow
```

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

### 第五層 90 秒草稿：Database / SQL / Performance 講法

這段是看稿練習草稿。Nick 的定位不是 DBA、MySQL Expert 或 Sharding Architect，而是能在高交易 Backend Flow 中處理 Bet Record、Request Log、Report Projection、Partition Table 與查詢風險的 Backend Engineer。回答重點不是資料庫理論背誦，而是 `Production Risk + 排查思路`。

回答這區題目時，先固定用這個順序：

> 我會先看為什麼會有這個問題：資料量、查詢模式、索引、鎖、分表路由或報表 / 線上混用。接著看怎麼發現：Slow Query Log、EXPLAIN、rows、DB Metrics、Error Log、Timeout、使用者或營運回報。然後說怎麼處理：加或調整 Index、改 Query Pattern、分批、Cursor Pagination、Projection、Rate Limit、Partition / Routing、Batch Size 或重跑策略。最後補風險：Index 寫入成本、Route Miss、跨表漏查、交易主線被報表拖垮、Batch 重複或漏資料。

#### 1. 怎麼判斷一個查詢需要 index？

> 我通常不會一開始就直接加 Index，而是先看這支 Query 的使用頻率、資料量、執行時間與是否在高流量 API 上。如果 Slow Query 或 EXPLAIN 顯示掃描 rows 很大、沒有命中合適 Index，而且查詢條件相對固定，才會考慮新增或調整 Index。不過 Index 不是越多越好，它會增加寫入成本與維護成本，所以我會先確認 Query Pattern 與 production 風險，再決定是否加。

#### 2. composite index 欄位順序怎麼決定？

> Composite Index 我會先回到實際 Query Pattern，看 Where 條件、排序、範圍查詢與查詢頻率。一般會優先考慮常出現在條件裡、選擇性較高、能縮小掃描範圍的欄位，同時確認排序或覆蓋索引是否有幫助。重點不是背固定公式，而是用 EXPLAIN 驗證 Index 是否真的降低 rows 與排序成本。

#### 3. `EXPLAIN` 你最先看哪幾個欄位？

> 我通常先看 type、key、rows 與 Extra。type 可以判斷查詢型態是否太差，例如接近全表掃描；key 看有沒有命中預期 Index；rows 看資料庫估計要掃多少資料；Extra 則會留意 Using Filesort、Using Temporary 這類可能造成成本升高的訊號。看完後還要回到實際資料量與查詢頻率判斷，不是只看一個欄位就下結論。

#### 4. 大分頁查詢為什麼危險？

> Offset 很大時，資料庫通常還是要先掃過前面大量資料，再丟掉不需要的部分，所以 page 越後面成本越高。這在 Bet Record、Request Log 或後台報表查詢特別容易出問題。高流量或大資料量場景下，我會考慮 Cursor Pagination、依 ID 範圍查詢，或限制可查時間區間，避免把 DB 拖進大範圍掃描。

#### 5. 大 `IN` query 有什麼風險？

> IN 條件太大時，SQL 會變長，執行計畫可能變差，也可能增加記憶體與解析成本。即使有 Index，也不代表一定有效率。我會先看資料量與 EXPLAIN，如果風險高，可能改成分批查詢、暫存表或 Join，並控制每批大小，避免一次把 DB 壓力打滿。

#### 6. join、subquery、分批查詢你會怎麼取捨？

> 我不會固定說哪一種一定最好，主要看資料量、Index、查詢模式與線上風險。Join 在資料庫層處理通常有效率，但複雜 Join 或大表 Join 可能成本很高；Subquery 要看 optimizer 實際怎麼處理；如果資料量大或風險不確定，分批查詢有時比較可控。Production 上我會用 EXPLAIN 和實際資料量來決定。

#### 7. slow query 出現時，你會怎麼排查？

> 我會先確認是哪一支 SQL、從哪個 API 或 Job 來、執行時間多長、資料量多大，以及最近是否有版本或資料量變化。接著用 EXPLAIN 看是否沒命中 Index、掃描 rows 過大、Join 不合理、排序或 temporary table 成本太高。如果 SQL 本身看起來合理，再看 DB 資源、鎖等待、連線池或下游壓力。重點是先定位瓶頸，不會只靠直覺加 Index。

#### 8. deadlock 發生時，你會看什麼？

> 我會先拿 Deadlock Log，確認是哪幾個 Transaction、哪些 SQL、哪些資料列互相等待。Deadlock 通常不是單一 SQL 慢，而是兩邊更新順序或鎖範圍產生循環等待。處理方向可能是統一更新順序、縮小 Transaction Scope、減少不必要的查詢與更新，或調整 Lock 策略。

#### 9. lock wait timeout 跟 deadlock 差在哪？

> Lock Wait Timeout 是某個 Transaction 一直等不到鎖，等到超時後失敗；Deadlock 是兩個或多個 Transaction 形成循環等待，資料庫偵測後會主動中止其中一方。Deadlock 通常代表更新順序或交易設計需要檢查；Lock Wait Timeout 可能是鎖競爭太高、Transaction 太長，或某個慢操作卡住資源。

#### 10. online query 和 report query 為什麼要分離？

> Online Query 直接影響交易流程，例如 payment、wallet、bet-settle 這類流程，要求低延遲與穩定性。Report Query 通常資料量大、條件複雜，目標是查詢與分析。我的判斷會是交易資料作為 Source of Truth，報表盡量透過 Projection、Batch 或獨立查詢路徑處理，避免報表查詢拖垮線上交易。

#### 11. 分表 / partition 解決什麼問題？不能解決什麼問題？

> Partition 主要解決單表資料量過大後的查詢與維護問題，例如 Bet Record、Request Log、Transaction Log 這類高成長資料。它可以讓查詢依時間或路由條件縮小掃描範圍，也讓維護、清理或補資料更可控。但 Partition 不是萬能，如果 Query Pattern 不合理，或查詢條件沒有打到 partition key，一樣可能很慢。

#### 12. 每日 / 每月分表建立 job 有哪些風險？

> 這類 Job 的風險主要是建表失敗、路由規則錯誤、程式和實際資料表不同步，以及跨表查詢時漏查。尤其在日期切換或月切時，錯誤容易被放大。我會關心建表監控、失敗告警、路由規則是否集中管理、查詢範圍是否正確，以及補資料或重跑時會不會漏表。

#### 13. batch insert / batch update 有哪些 transaction 與 memory 風險？

> Batch 可以減少 DB round trip，提高處理效率，但 Batch 太大會讓 Transaction 變長，增加 Lock 時間、Rollback 成本與記憶體壓力。如果是 Projection 或報表類資料，我會特別注意可重跑性、唯一鍵或 Upsert 規則；如果涉及交易狀態，就要更小心 Idempotency 與重複副作用。

#### 14. 如果查單 API 被 provider 或商戶高頻打，你怎麼保護 DB？

> 我會先確認這個查單是否真的需要每次直接打 Source of Truth。如果可以接受短暫延遲，可能透過 Cache、Projection 或 Rate Limit 降低 DB 壓力；如果一定要查 DB，就要確認 Index、查詢條件、時間範圍與流量保護。Provider Query 或訂單查詢尤其要注意 timeout、重試放大與高頻查詢造成的 DB 壓力。

#### 15. 你怎麼設計 bet record sharding / routing 的查詢邊界？

> 我會先看資料成長速度、主要查詢條件與保留週期。像 Bet Record 通常會依時間，例如日或月，或依業務 routing key 做分表。寫入時要有一致的 routing rule，查詢時則依時間區間或條件決定要查哪些表。最大風險是路由錯誤、跨表漏查與查詢範圍過大，所以 routing rule、查詢邊界與補資料機制都要清楚。

這區優先練的 5 題是：

```text
Slow Query 怎麼查 -> Online Query vs Report Query -> Partition 解決什麼 -> 查單 API 高頻 -> Bet Record Sharding / Routing
```

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

### 第六層 90 秒草稿：Redis / Cache / Distributed Lock 講法

這段是看稿練習草稿。Nick 的定位不是 Redis Expert、Distributed Lock Expert 或 Cache Architect，而是在 Payment、Provider、Wallet、Request Log、Projection 場景中使用 Redis，並理解快取、一致性與分散式風險的 Backend Engineer。回答重點是先問 `Source of Truth 是誰`，再談效能、降 DB 壓力與失效風險。

回答這區題目時，先固定用這個順序：

> 我會先說 Redis 為什麼存在：提升效能、降低 DB 壓力、降低延遲或做短期狀態控制。接著看最大風險：Cache 不一致、Hot Key、Big Key、Cache Miss、Redis 掛掉或 Lock 失效。最後一定要問 Source of Truth 是 Redis 還是 DB / 交易資料，因為交易正確性不能只靠 Cache 或 Lock 保證。

#### 1. 什麼資料可以 cache？什麼資料不應只信 cache？

> 我通常會快取讀多寫少、允許短時間不一致的資料，例如 Provider Config、白名單、商戶設定或部分查詢結果。交易訂單、Wallet Balance 這類高一致性資料可以快取來加速查詢，但不應只信 Cache，真正 Source of Truth 還是 DB、交易紀錄或狀態機。面試時我會把 Cache 定位成加速層，不會把它講成交易真相。

#### 2. cache aside 是什麼？更新 DB 後 cache 怎麼處理？

> Cache Aside 是先查 Cache，沒有命中再查 DB，查到後回填 Cache。更新時通常先更新 DB，再刪除或更新 Cache，讓下一次查詢重新回源。這種模式比較容易維護，但仍可能有短暫不一致，所以要看業務能不能接受 Eventual Consistency；如果是交易核心狀態，就不能只靠 Cache 結果做決策。

#### 3. cache penetration、breakdown、avalanche 差在哪？

> Penetration 是一直查不存在的資料，導致請求穿透 Cache 打到 DB；Breakdown 是熱門 Key 過期，瞬間大量請求回源；Avalanche 是大量 Key 同時失效，造成整體 DB 壓力暴增。三者本質都是 Cache 保護層失效，但觸發原因不同，所以處理方式也不同，例如空值快取、互斥回源、過期時間打散或限流。

#### 4. hot key / big key 會造成什麼問題？

> Hot Key 是單一 Key 流量過大，容易形成 Redis 熱點，讓延遲上升或打爆單點資源。Big Key 是單一 Key 資料量太大，會影響記憶體、網路傳輸與操作耗時。兩者都不只是效能問題，也會影響穩定性，所以要透過監控、拆分 Key、限制資料大小或調整資料模型處理。

#### 5. Redis distributed lock 什麼情境適合？什麼情境不適合？

> 我認為 Redis Distributed Lock 適合控制少量高價值、短時間的競爭操作，例如避免排程重複執行、特殊資源競爭或補單流程。不適合拿來解決所有併發問題。像 Wallet、Order 這類核心交易，我會更傾向依賴 DB Transaction、唯一鍵、狀態機與 Idempotency，而不是把正確性完全壓在 Redis Lock 上。

#### 6. lock 過期時間怎麼設？

> Lock 過期時間要大於正常執行時間，並保留一些 buffer，但也不能太長，避免任務異常後鎖長時間不釋放。若任務耗時不固定，就要考慮續約機制或把任務拆小。更重要的是，即使有 Lock，業務層也要有狀態檢查與 Idempotency，不能只靠過期時間保證正確性。

#### 7. 如果 lock 還沒處理完就過期，會怎樣？

> 如果 Lock 已經過期，但原本任務還在執行，新的執行緒或節點可能再次取得 Lock，導致同一個業務被執行兩次。這會造成重複扣款、重複補單或重複更新的風險。所以 Lock 只是併發控制的一層保護，真正避免副作用還要靠 Idempotency、狀態機與終態檢查。

#### 8. wallet balance 能不能放 Redis？為什麼？

> 可以放，但不能只信 Redis。Redis 可以用來提升查詢效能或降低 DB 壓力，但 Wallet Balance 屬於高一致性資料，真正 Source of Truth 應該是交易紀錄、DB 或錢包狀態。若 Redis 與 DB 不一致，應該回到交易真相判斷，而不是用 Cache 覆蓋真實餘額。

#### 9. Redis 和 DB 不一致時怎麼處理？

> 第一件事是確認 Source of Truth。如果 DB 或交易資料才是真相，就應該以 DB 為準，重新回填或刪除 Cache。如果 Redis 被設計成主資料來源，問題會複雜很多，也會增加恢復風險。因此我通常傾向把 Redis 當加速層，讓不一致時可以安全回源修復。

#### 10. provider routing / config 放 Redis 有什麼風險？

> 好處是查詢快、切換快，可以降低 DB 壓力，也適合 Provider Routing 或商戶配置這類讀多寫少資料。風險是 Cache 與 DB 不一致、Redis 更新失敗、版本不一致或路由錯誤。比較安全的做法是保留配置版本、Reload 機制、Audit Log 與回退能力，避免錯誤配置直接影響交易路由。

#### 11. rate limit 你會怎麼設計？

> 常見做法是用 Redis Counter、Sliding Window 或 Token Bucket。重點不是只背演算法，而是先定義限制邊界，例如依 User、IP、Merchant、Provider 或 API 類型限制，避免單一來源打爆系統。對 payment query 或 provider callback 這類流量，也要區分正常重試和異常流量，避免誤傷主流程。

#### 12. session / token / auth state 放 Redis 要注意什麼？

> Redis 很適合管理 Session、Token 或 Auth State，因為查詢快，也方便設定過期時間。但要注意 TTL、登出同步、主從切換、大量 Session 回收與 Redis 掛掉後的使用者影響。如果 Redis 不可用，要先決定登入服務是 fail open、fail close，還是降級處理。

#### 13. Redis cluster 或主從切換時，lock / cache 可能有什麼問題？

> 主從切換期間可能出現資料尚未同步完成的情況。對一般 Cache 來說，最多可能是短暫 miss 或資料變舊，通常可以回源修復；但對 Distributed Lock 風險比較高，因為鎖資訊可能遺失或被重複取得。所以核心交易正確性不能只靠 Redis Lock，要有業務層 Idempotency 和狀態保護。

#### 14. 如果 Redis 掛了，系統要 fail open 還是 fail close？

> 要看業務風險。像 Config Cache、報表查詢或一般查詢，可以考慮 Fail Open，讓系統回源 DB，但要保護 DB 不被打爆。像風控、登入、限流或可能影響資金安全的流程，可能需要 Fail Close 或降級。重點不是固定答案，而是判斷業務是寧可慢、寧可不服務，還是寧可承擔短暫不一致。

#### 15. 你怎麼把 Redis 風險講成 owner decision？

> 我不會只討論 Redis 技術，而是先確認業務風險。例如 Redis 掛掉後，系統是寧可慢一點回源 DB，還是停止某些高風險操作？Wallet Balance 能不能接受短暫不一致？Provider Routing 配置錯誤時能不能回退？這些都是 Owner Decision，因為它們牽涉交易正確性、可用性與風險承擔，不只是 Cache 實作問題。

這區優先練的 5 題是：

```text
Wallet Balance 能不能放 Redis -> Redis 與 DB 不一致 -> Provider Routing Config 放 Redis -> Redis 掛掉 Fail Open / Fail Close -> Redis 風險如何講成 Owner Decision
```

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

### 第七層 90 秒草稿：Java / Spring / Runtime 講法

這段是看稿練習草稿。Nick 的定位不是 JVM Expert，也不是 Spring Framework Contributor，而是理解 Spring Transaction、AOP、Exception Handling、Thread Pool 與 Runtime 問題在 Production 上會造成什麼影響的 Java Backend Engineer。這區不要背八股到太深，回答重點是 `是什麼 -> 為什麼 -> Production 會怎麼壞 / 怎麼查`。

回答這區題目時，先固定用這個順序：

> 我會先用一句話說這個機制是什麼，再說它為什麼會這樣運作，最後一定補 production 風險。例如 transaction 沒生效會造成資料沒 rollback、thread pool queue 太大會造成延遲堆積、exception 被吃掉會讓錯誤不可觀測。面試官通常不是要我背原始碼，而是要確認我遇到 production 問題時知道往哪裡查。

#### 1. Spring transaction 什麼情境會失效？

> `@Transactional` 通常是透過 Spring Proxy 生效，所以如果呼叫沒有經過 Proxy，就可能失效。常見情況包括 self-invocation、private method、final method、物件不是 Spring Bean，或直接 new 出來的物件。另外如果 Exception 被 catch 掉沒有往外拋，也可能導致預期中的 rollback 沒發生。Production 上風險就是你以為有 transaction，但其實資料已經部分寫入。

#### 2. self-invocation 為什麼會讓 transaction / AOP 失效？

> 因為 Spring Transaction 和 AOP 主要是透過 Proxy 攔截方法呼叫。如果同一個 class 裡面直接呼叫自己的另一個方法，實際上是 this.method，不會經過 Proxy，所以 AOP advice 和 Transaction 都不會被觸發。Production 上最容易踩到的是把交易邏輯拆成內部方法後，以為加了 `@Transactional`，但 rollback 沒有生效。

#### 3. checked exception / runtime exception 對 rollback 有什麼影響？

> Spring 預設對 RuntimeException 和 Error rollback，Checked Exception 預設不 rollback。如果業務需要 Checked Exception 也 rollback，要用 `rollbackFor` 明確指定。Production 上要注意的是，不同 Exception 類型會影響 transaction 結果，不能只看有沒有 throw exception，還要看它是否符合 rollback rule。

#### 4. `@Transactional` 放在 private method 有用嗎？

> 通常沒有用。因為 Spring Proxy 無法攔截 private method，private method 也不是外部透過 Proxy 呼叫的入口，所以 transaction 不會照預期生效。比較安全的做法是把 transaction boundary 放在 public service method 上，並讓呼叫路徑經過 Spring 管理的 Bean。

#### 5. AOP 大致怎麼攔截？JDK proxy 和 CGLIB 差在哪？

> AOP 本質上是在方法前後插入額外邏輯，例如 Transaction、Log、權限驗證或監控。JDK Proxy 依賴 interface 產生 proxy，CGLIB 則透過繼承產生子類 proxy。實務上最重要的是理解 Spring 很多功能依賴 Proxy，只要呼叫沒有經過 Proxy，AOP 就不會生效。

#### 6. filter、interceptor、AOP 的差別是什麼？

> Filter 在 Servlet 層，最靠近 HTTP request / response，適合處理跨框架的請求過濾。Interceptor 在 Spring MVC 層，常用於權限、登入狀態、request context。AOP 在 method 層，通常關注 service method，例如 transaction、log、監控或共用業務切面。三者作用層級不同，所以要依問題位置選工具。

#### 7. DTO / VO / Entity / BO 你怎麼分？

> DTO 主要用於跨層或跨服務傳輸資料，Entity 對應資料庫結構，BO 偏業務處理中的物件，VO 常用於回傳給前端或呈現層。實務上我最在意的是不要把 DB Entity 直接暴露給外部 API，避免資料欄位外洩、耦合 DB schema，也避免更新 API 時影響 persistence model。

#### 8. Global exception handler 要注意什麼？

> Global Exception Handler 的好處是統一錯誤格式、錯誤碼與 log。要注意不要把所有 Exception 都吃掉，也不要把內部 stack trace 或敏感訊息直接回給前端。另外要保留 trace id、request id、order id 這類排查線索，讓 production 問題能追回實際請求。

#### 9. thread pool 參數怎麼看？queue 太大有什麼風險？

> 我主要會看 core size、max size、queue size、keep alive 和 rejection policy。Queue 太大看起來可以吸收流量，但也可能把問題藏起來，造成請求延遲持續累積，甚至 OOM。Production 上我會關心任務耗時、流量尖峰、是否有 timeout、拒絕策略是否明確，以及監控能不能看到 queue backlog。

#### 10. Future / CompletableFuture 使用時要注意什麼？

> 我會注意它使用哪個 Thread Pool、Exception 怎麼處理、Timeout 有沒有設，以及結果是否真的被等待或觀測。CompletableFuture 很方便，但如果大量使用預設 common pool，或 exception 沒有處理，production 問題會很難查。非同步不是免費的，還是要考慮資源、timeout 與失敗邊界。

#### 11. Java Stream 什麼情境不適合？

> Stream 適合資料轉換和簡單 pipeline，但不適合複雜流程控制、大量副作用、需要清楚 debug 的交易流程，或資料量很大但沒有控制記憶體的情境。過度使用 Stream 可能讓程式可讀性變差。Production code 我會優先選擇可讀、可排查、錯誤邊界清楚的寫法。

#### 12. Optional 什麼情境會被濫用？

> Optional 適合表示 method return value 可能為空，提醒呼叫端處理空值。不太建議放在 Entity 欄位、DTO 欄位，或大量巢狀使用，因為會讓序列化、ORM 或可讀性變複雜。Optional 的目的不是消滅所有 null，而是讓重要邊界的空值語意更清楚。

#### 13. OOM 你會怎麼排查？

> 我會先確認是 Java Heap、Metaspace、Direct Memory 還是 container memory 問題，再看 GC log、記憶體使用趨勢與發生時間點。接著透過 heap dump 分析物件分佈，找是否有大量集合、cache 未清、ThreadLocal、byte array 或物件引用未釋放。Production 上重點是先止血，再找出是流量、資料量、程式 leak 還是設定問題。

#### 14. CPU 100% 你會怎麼排查？

> 我會先確認是哪個 process，再找高 CPU thread，透過 thread dump 對照 stack trace，看是否有無限迴圈、頻繁重試、鎖競爭、大量序列化、加密簽章或計算邏輯。接著回到對應程式碼與近期變更確認原因。CPU 100% 不一定是機器不夠，也可能是程式進入錯誤重試或 hot path 寫壞。

#### 15. thread dump / heap dump 你會看什麼？

> Thread Dump 主要看 thread state、blocked / waiting、deadlock、高 CPU thread 對應的 stack，以及 thread pool 是否塞滿。Heap Dump 則看物件數量、占用大小、引用鏈與是否有異常集合或 cache。實務上我不會從頭看所有內容，而是先根據 production symptom 找最異常的 thread 或物件。

這區優先練的 5 題是：

```text
Spring Transaction 失效 -> Self Invocation -> Thread Pool -> OOM -> CPU 100%
```

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

### 第八層 90 秒草稿：Observability / Incident / Legacy Takeover 講法

這段是看稿練習草稿。Nick 這區的差異化不是自稱最懂 Payment、Kafka 或 Redis，而是能在 Legacy Takeover、Production Troubleshooting 與 Flow Reconstruction 裡，把問題定位、證明、整理成下一個人能接手的系統理解。回答重點是 `怎麼發現 -> 怎麼定位 -> 怎麼證明 -> 怎麼避免下次再發生`。

回答這區題目時，先固定用這個順序：

> 我會先說問題怎麼被發現，例如告警、使用者回報、營運回報或報表異常。接著定位卡在哪一層：API、DB、Provider、MQ、Batch、Projection 或人工流程。再用資料證明，例如 OrderId、TraceId、Log、DB 狀態、MQ 消費結果與 Git History。最後補預防方式，例如補 log、補監控、補重跑能力、補交接文件或把 Flow 邊界整理清楚。

#### 1. 一條重要 production flow 上線前，你會加哪些 log？

> 我會優先記錄 Flow 的關鍵節點，而不是每一行程式。通常會包含 request entry、外部 Provider request / response、重要狀態轉換、MQ publish、consumer 處理結果、exception 與最終結果。重點是讓我可以透過 log 還原整條 flow，知道資料卡在哪一段，而不是單純堆很多看不懂的 log。

#### 2. trace id / request id / order id / provider transaction id 怎麼串？

> TraceId 用來串整條請求鏈路，RequestId 代表一次 API 請求，OrderId 是內部業務識別，Provider Transaction Id 是第三方世界的識別。我希望重要 log 裡能同時看到這些欄位，這樣從玩家回報、營運查單，到 Provider 對帳或 callback 排查，都能串回同一筆業務。

#### 3. callback failure log 要記什麼？

> 我至少會記錄 callback payload、order id、provider transaction id、驗簽結果、目前訂單狀態、錯誤訊息、stack trace、處理耗時與回應結果。重點不是只記 exception，而是讓後續能重建當時發生什麼事，判斷是資料錯誤、簽章錯誤、狀態不允許，還是系統處理失敗。

#### 4. batch job result 要記什麼？

> 我會記錄 job 開始時間、結束時間、處理資料範圍、處理筆數、成功數、失敗數、錯誤原因與執行耗時。如果是 Projection 或補資料類 Job，還要記最後處理位置、是否可重跑，以及重跑時如何避免重複副作用。這樣失敗時才知道該補哪一段。

#### 5. Kafka lag / MQ backlog 要怎麼告警？

> 我不會只看 lag 數字，而是看業務影響。通常會監控 lag / backlog 數量、consumer error rate、處理延遲、DLQ 數量與 projection 落後時間。如果 lag 持續上升，或超過業務可接受的同步延遲，就應該告警。因為報表延遲和交易卡住的嚴重度不一樣，要先分清楚影響層級。

#### 6. 如果線上發生訂單卡住，你第一步查什麼？

> 我第一步不會直接看程式碼，而是先拿 OrderId 查目前訂單狀態、最後更新時間與最近 log。接著判斷 flow 卡在哪個節點：Provider request、callback、query、MQ、batch、projection 還是人工流程。先定位卡點，再往下查原因，避免一開始就迷失在整個 codebase 裡。

#### 7. 如果玩家說錢不見了，你會怎麼查？

> 我會先確認交易真相，也就是 wallet transaction、order 或 bet / settle record。接著比對玩家餘額、扣款紀錄、派彩紀錄、provider 回傳、callback / query log 與相關 trace。目標是先回答「錢到底有沒有動過」，再回答「為什麼玩家看到的結果不一致」。這類問題不能先相信報表或畫面，要先回到 Source of Truth。

#### 8. 如果報表和實際交易不一致，你會怎麼查？

> 我會先確認哪邊是 Source of Truth。通常交易資料才是真相，報表只是 Projection。接著檢查 MQ 是否延遲、consumer 是否失敗、batch 是否漏跑、projection 是否落後或查詢範圍是否錯誤。如果交易資料正確而報表錯，我會優先修復 projection 或重跑資料，而不是改交易資料。

#### 9. legacy system 沒文件，你會從哪裡開始反推？

> 我通常會從 API entry 開始找，先確認 Controller、Service、主要 DB table、MQ topic、batch job 與外部 provider 邊界。接著透過 log、資料流、git history 和實際資料狀態，把整條 production flow 串起來。我不會一開始平均讀所有 class，而是先建立系統地圖和關鍵 flow。

#### 10. 從 API entry、DB table、log、MQ topic、batch job 反推 flow 的順序是什麼？

> 我通常從入口開始追。先看 API entry 和 request 參數，再看主要 DB table 的狀態如何改變，接著用 log 對應每個狀態轉換。如果有 MQ，就追 event publish 與 consumer；如果有 batch 或 projection，就確認它們怎麼讀資料、更新報表或補資料。目標是建立完整資料流，而不是理解每個 class 的細節。

#### 11. 你怎麼補一份交接文件，讓下一個人能維護？

> 我不會只寫 API 文件，而是先寫系統地圖。內容會包含核心 flow、資料表、MQ topic、重要 batch job、source of truth、狀態轉換、常見異常、排查入口與不能誇大的邊界。好的交接文件應該讓下一個人知道出問題時從哪裡開始查，而不是只知道有哪些 endpoint。

#### 12. 你怎麼區分「系統真的壞了」和「報表 projection 還沒同步」？

> 我會先確認交易資料是否正確。如果 order、wallet transaction 或 bet / settle record 正確，只是報表沒更新，那比較像 projection 延遲或失敗。如果交易本身缺資料、狀態錯誤或金額不一致，才是核心交易系統問題。先分清楚出錯層級，才不會把報表問題誤判成交易壞掉。

#### 13. incident review 你會寫哪些項目？

> 我會寫事件時間線、影響範圍、發現方式、根因、處理過程、資料修復方式、使用者或業務影響，以及後續預防措施。重點不是找誰犯錯，而是讓團隊下次更快發現、更快定位、更安全修復。對我來說 incident review 也是把 production knowledge 留下來的一種方式。

#### 14. 如果主管要你救一個沒交接的系統，你怎麼拆第一週工作？

> 第一週我不會急著改功能。我會先盤點 service、DB、Redis、MQ、batch、外部 provider 與部署方式，建立最小系統地圖。接著挑最重要的 production flow，確認 source of truth、主要資料流、常見異常和排查入口。目標是先恢復可理解性與基本可維護性，再決定哪些地方需要補強。

#### 15. 這類 legacy takeover 經驗你要怎麼講才不像抱怨前人？

> 我會避免說前人沒寫文件，而是說當時系統文件相對不足，所以我花比較多時間透過 code reading、log、DB、MQ、git history 與資料流整理系統脈絡。這段經驗讓我學到如何在資訊不完整的情況下建立可靠的系統理解，也讓後續排查和交接更穩定。

這區優先練的 5 題是：

```text
訂單卡住怎麼查 -> 玩家說錢不見了怎麼查 -> 報表與交易不一致 -> Legacy System 沒文件怎麼反推 -> 第一週接手沒交接系統
```

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

### 第九層 90 秒草稿：System Design / Owner Decision 講法

這段是看稿練習草稿。這區最容易答太滿，Nick 目前不能把自己包裝成完整架構師、從零設計大型平台的人，或完整 Wallet Ledger Owner。回答原則是用看過、維護過、分析過的 production flow 抽象回答，不發明 Google 級架構。核心固定看四件事：`Source of Truth 在哪 -> State Transition 怎麼走 -> Failure 怎麼收斂 -> 怎麼觀測`。

回答這區題目時，先固定用這個順序：

> 我會先定義 Source of Truth，例如 order、wallet transaction、bet / settle record 或交易資料。接著說狀態怎麼轉，例如 pending、success、failed、rollback。然後處理 failure：timeout、duplicate callback、MQ failure、projection delay、provider mismatch。最後補觀測與 owner decision：哪些 log / dashboard / retry / compensation / reconciliation 可以讓風險被發現與收斂。

#### 1. 請設計一個 provider integration 系統，包含 request、callback、query、timeout、retry、compensation。

> 我會把 Provider Integration 拆成 Request、Callback、Query 三條路徑。Request 先建立內部 order，再呼叫 provider；Callback 是主要狀態更新來源，但不能直接相信，要先驗簽、驗證 order mapping 與狀態轉移；Query 是 fallback，用在 timeout 或狀態不確定時收斂結果。Timeout 我會視為 Unknown State，不直接當成功或失敗。整體核心是 Order State Machine、Idempotency、Retry、Compensation 與完整 audit log。

#### 2. 請設計一個 wallet / bet-settle / rollback 系統，說明 source of truth 與 transaction boundary。

> 我會先定義 Wallet Transaction Record 或交易紀錄作為 Source of Truth。Bet 階段驗證玩家狀態與餘額，扣款並建立 bet record；Settle 階段依遊戲結果派彩；Rollback 則依原 transaction id 做反向或取消操作。DB Transaction 可以保證單系統內的一致性，但跨 provider、MQ 或 projection 時，需要靠 idempotency、補償與對帳收斂。最重要的是避免重複 settle、重複 rollback 或 wallet 和 bet record 不一致。

#### 3. 請設計一個 MQ / batch / projection 系統，支援重跑、補資料、DLQ 與 observability。

> 我會把 Transaction System 和 Projection 分開，交易資料是 Source of Truth，Projection 只是查詢模型。交易完成後送 event 到 MQ，Projection Service 消費後更新報表。每個 event 需要唯一識別，consumer 要 idempotent，失敗時進 retry 或 DLQ。Batch 或 replay 要能依時間或 event id 補資料。觀測上要看 lag、error rate、DLQ、projection delay 與重跑結果。

#### 4. 請設計一個 slot math / RTP validation flow，說明 result contract 與 simulation validation。

> 我不會把自己定位成 Math Owner，但如果設計驗證流程，我會把 Math Module 與 Runtime 分開。Math Module 負責輸出穩定的 result contract，例如盤面、獎項、倍數與必要 metadata；Runtime 依 contract 執行遊戲流程與資料落地。RTP 驗證則透過大量 simulation 檢查結果分布是否符合預期。重點是 contract 穩定、可驗證、可回溯，而不是讓 Runtime 混入過多數學細節。

#### 5. 同步 API 和非同步 event 你怎麼選？

> 如果業務需要立即結果或立即一致性，例如支付建單、查詢餘額、交易狀態確認，我會偏向同步 API。如果是報表、通知、audit log、projection 更新或後續非核心流程，我傾向非同步 event。核心考量不是技術喜好，而是使用者或業務是否需要當下知道結果，以及失敗時能不能接受 eventual consistency。

#### 6. 什麼時候拆服務？什麼時候不要拆？

> 我不會因為技術潮流就拆服務。通常會看業務邊界是否清楚、部署節奏是否不同、團隊是否能獨立維護、資料 ownership 是否能切開，以及擴展需求是否真的存在。如果系統高度耦合、團隊規模不大、資料一致性要求高，我反而會傾向先維持 monolith 或 modular monolith，把 flow 和邊界整理清楚再談拆分。

#### 7. 什麼時候用 DB transaction？什麼時候接受 eventual consistency？

> 資金、訂單狀態、wallet transaction 這類核心交易，我會優先用 DB transaction 或明確的狀態機保證一致性。報表、通知、projection、audit log 這類可以延遲的資料，可以接受 eventual consistency。重點是先確認業務風險和 source of truth，而不是所有資料都追求強一致，也不是所有流程都丟 MQ。

#### 8. 什麼時候用 cache？什麼時候寧可打 DB？

> 讀多寫少、允許短暫不一致的資料適合 cache，例如 provider config、白名單、商戶設定或部分查詢結果。交易真相、wallet transaction、order state 這類資料應該以 DB 或交易紀錄為主。Cache 可以加速，但不能取代 Source of Truth。如果 cache 掛掉或不一致，系統要能安全回源或降級。

#### 9. 什麼時候 retry？什麼時候人工補償？

> 暫時性錯誤適合 retry，例如網路抖動、provider timeout、MQ publish 短暫失敗。資料錯誤、狀態不允許、簽章錯誤或業務規則衝突，retry 通常沒有意義，應該進 DLQ、異常清單或人工補償。關鍵是判斷錯誤是否可能自行恢復，並避免 retry 造成重複副作用。

#### 10. 你怎麼設計 reconciliation？

> 我會先定義要比對的資料來源，例如內部 order、wallet transaction、provider record 或 bet / settle record，並確認哪邊是 Source of Truth。接著定期比對金額、狀態、transaction id、完成時間等欄位，產生差異清單。Reconciliation 本身不是直接修資料，而是找出不一致，再依規則決定自動修復、補償或人工確認。

#### 11. 你怎麼設計 callback query fallback？

> Callback 是主要狀態更新路徑，但 timeout、callback 遺失或 callback 不可信時，需要 query fallback 收斂狀態。Request timeout 後我會把訂單保留在 unknown 或 pending 狀態，再透過 query 或後續 callback 更新終態。如果 callback 和 query 結果不同，會回到 provider transaction id、internal order id、狀態機與 audit log 交叉驗證，避免只相信單一來源。

#### 12. 你怎麼設計可灰度、可 rollback 的 rollout？

> 我會透過 feature flag、白名單商戶、指定 provider、指定幣別或部分流量逐步放量。Rollback 要能快速切回舊路徑、舊 provider 或關閉新功能。上線前要確認 log、dashboard、告警與回退操作都準備好。比起只追求快速上線，我更在意出問題時能不能快速縮小影響範圍。

#### 13. 你怎麼設計 system owner 的 dashboard？

> 我會優先看業務健康度，而不是只看 CPU。像 payment / provider flow，我會看訂單成功率、provider timeout rate、callback failure、unknown order 數量、MQ lag、projection delay、DLQ、異常訂單數量與人工補單數量。System Owner Dashboard 的目標是讓風險早點浮出來，而不是等使用者回報才知道。

#### 14. 如果 PM 要快上線，但你看到 consistency 風險，你怎麼溝通？

> 我不會只說不行，而是把風險具體化。例如目前缺少 idempotency、retry、reconciliation 或 rollback path，上線後可能造成重複入帳、訂單卡 unknown、報表不一致或需要大量人工補單。我會提出最小必要保護和可接受的 scope cut，讓 PM 做知情決策，而不是把技術風險藏在工程端。

#### 15. 如果重新設計現有系統，你第一階段只改哪裡，為什麼？

> 我通常不會第一階段就說全部重寫。對 legacy system，我會先改善觀測能力與資料流可見度，例如補 log、補 dashboard、補 flow 文件、整理 source of truth 與狀態轉移。因為如果系統看不清楚，重寫也可能只是重新製造問題。先讓系統可觀測、可排查，再決定是否拆服務、改架構或重構核心流程。

這區優先練的 5 題是：

```text
Provider Integration Design -> Wallet / Bet-Settle / Rollback -> MQ / Projection -> Reconciliation -> 重設計現有系統先改什麼
```

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

### 第十層 90 秒草稿：Behavior / HR / 談薪 / 團隊協作 講法

這段是看稿練習草稿。這區最容易失分的不是技術，而是 Nick 如何描述自己。原則是不要講不爽主管、公司很爛、沒調薪、同事很雷，即使它們有部分真實。面試官要聽的是 `你在追求什麼`，不是 `你在逃離什麼`。主軸固定成：具備 Provider Integration、Production Flow 與 Legacy Takeover 經驗，正在往 Senior Backend / Platform Backend 成長的工程師。

回答這區題目時，先固定用這個順序：

> 我會先承認現況，不把自己包裝過頭；接著說明近幾年工作內容已經從功能開發延伸到 provider integration、wallet、MQ、projection、legacy takeover 與 production troubleshooting；最後把動機放在成長、職責、系統規模與市場驗證，而不是抱怨原公司。

#### 1. 你為什麼想換工作？

> 我目前的工作其實有不少優點，例如工時穩定，也讓我有機會接觸 Provider Integration、Wallet、MQ、Projection 與既有系統維護。不過近幾年我開始希望往 Senior Backend 或 Platform Backend 發展，希望接觸更大的系統規模、更完整的 ownership 與更高複雜度的問題。因此我想透過市場機會，看看下一個成長階段。

#### 2. 你現在薪資 8 萬，為什麼期待 10 萬以上？

> 我不會單純用年資談薪資，而是看工作內容、職責與市場價值。近幾年的工作內容已經不只是功能開發，而是開始接觸 Provider Integration、Wallet Flow、MQ、Projection、Legacy Takeover 與 Production Troubleshooting。如果職缺責任符合 Senior Backend 或 Platform Backend 的期待，我認為 10 萬以上是合理區間，也希望用市場標準重新驗證自己的價值。

#### 3. 如果對方問你是不是管理職，你怎麼回答？

> 不是。我目前主要還是 Individual Contributor，工作重心在系統分析、開發、維護與問題排查。不過在一些專案裡，我會協助釐清流程、整理脈絡、讓同事理解系統，也開始接觸部分 owner-like 的責任。但我不會把自己包裝成正式管理職或 Tech Lead。

#### 4. 你怎麼描述自己不是正式資深，但具備資深潛力與 ownership？

> 我知道自己不是正式資深職稱，也沒有完整帶團隊經驗。不過近幾年的工作內容已經逐漸從功能開發，延伸到 Provider Integration、交易流程、MQ、Legacy Takeover 與 Production Troubleshooting。我目前最強的是能理解 production flow、分析資料狀態與排查風險，所以希望透過市場驗證確認自己是否已經具備 Senior Backend 所需要的能力。

#### 5. 你怎麼處理 code review disagreement？

> 我通常會先討論問題，而不是討論人。如果只是程式風格，我會尊重團隊規範。如果涉及效能、可維護性、一致性或 production 風險，我會提出具體理由、案例或替代方案。最後還是以團隊共識與系統利益為優先，不會把 code review 變成個人輸贏。

#### 6. 你怎麼帶 junior 或協助同事？

> 我沒有正式帶人的職稱，但有協助同事理解系統與 flow 的經驗。我的方式通常不是直接叫對方背 API，而是先說明業務目的、資料流、狀態轉換與常見風險，再帶他看程式碼。因為我覺得理解 flow 後，才比較能安全修改既有系統。

#### 7. 你怎麼向主管說明技術債風險？

> 我不會只說技術債很醜或程式很難看，而是把它轉成業務風險。例如目前沒有 idempotency、retry、對帳或足夠 log，未來可能造成重複入帳、訂單卡住、資料不一致或排查時間過長。讓主管知道成本、風險與影響範圍後，再一起討論優先順序。

#### 8. 你怎麼在不加班文化和職涯成長之間取捨？

> 我認為兩者不一定衝突。我很重視工作與生活平衡，因為長期穩定輸出比短期燃燒更重要。不過我也會利用工作外時間整理經驗、補技術觀念、準備面試與回顧 production case。我追求的是長期穩定成長，而不是只靠長時間加班累積經驗。

#### 9. 公司縮編、你想跳但不捨，面試時要怎麼講？

> 我會說目前公司有不少值得感謝的地方，例如讓我接觸複雜系統，也累積很多實務經驗。但我也開始希望看看市場上更大的挑戰與成長空間，所以才開始了解新的機會。這樣講的重點是保留感謝與專業，不把轉職原因講成情緒或抱怨。

#### 10. 如果獵頭問期望薪資，你怎麼講？

> 我目前主要看職責、系統規模與成長空間。如果職缺內容符合 Senior Backend 或 Platform Backend 的期待，我希望落在 10 萬以上的區間。不過我也會綜合考量工作內容、團隊成熟度、技術挑戰與長期發展，而不是只看單一數字。

#### 11. 如果 HR 壓薪，你怎麼守底線？

> 我理解每家公司都有預算考量，也願意理解職缺的實際責任。不過我也會根據目前工作內容、市場行情與職缺要求評估。如果差距過大，我可能會優先考慮更符合預期的機會。語氣上我會保持禮貌，但不把自己的價值講低。

#### 12. 如果對方說你沒完整主導經驗，你怎麼回？

> 如果是完整平台層級的 Owner，我確實還沒有，這點我不會誇大。不過在 Provider Integration、Wallet Flow、MQ Projection 與 Legacy Takeover 等主題上，我有從需求理解、流程分析、實作、驗證到排查的完整參與經驗。我目前正在累積更多 owner 視角與決策能力。

#### 13. 如果對方問你最大的弱點，你怎麼講？

> 我過去有點偏向過度準備，會希望把很多東西都搞懂才開始行動。近幾年我在調整這件事，開始透過實際交付、市場驗證與面試回饋來校準能力，而不是一直停留在準備階段。這也讓我更重視把複雜問題整理成可行的下一步。

#### 14. 如果對方問你未來 2-3 年想成為什麼，你怎麼講？

> 我希望往 Senior Backend / Platform Backend 發展，持續累積高交易系統、Provider Integration、MQ、Consistency、Production Troubleshooting 與 System Design 經驗。短期目標是成為能獨立負責核心 production flow 的工程師；中長期希望具備更完整的 owner 能力，可以判斷風險、設計邊界並協助團隊穩定交付。

#### 15. 你有什麼問題想問主管，來判斷這個職缺是不是值得去？

> 我會問和系統、ownership、incident、legacy 與 senior 定義相關的問題。例如：目前團隊最核心的 production flow 是什麼？這個職位預期負責到什麼程度？新人加入後最難理解的系統是哪一塊？最近一年最大的線上事故是什麼？團隊如何定義 Senior Backend？這些問題比只問福利更能判斷職缺是否適合我。

這區優先練的 5 題是：

```text
為什麼換工作 -> 為什麼不是資深還投資深 -> 沒完整主導經驗怎麼回答 -> 最大弱點 -> 未來 2-3 年規劃
```

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

### 第十一層 90 秒草稿：Provider Integration 講法

這段是看稿練習草稿。Provider Integration 是 Nick 的主戰場之一，但回答邊界要穩：Nick 是做過 Provider Integration 的 Backend Engineer，不是完整 Payment Architect，也不是整個金流平台 Owner。回答時固定抓五件事：`Order 在哪 -> State 怎麼流 -> Trust Boundary 相信誰 -> Failure 怎麼收斂 -> Source of Truth 在哪`。

回答這區題目時，先固定用這個順序：

> 我會先建立 internal order 或 transaction record，確認內部業務識別。接著說狀態如何從 pending 走到 success、failed 或 unknown。Provider callback 不能直接相信，要驗簽、驗證欄位與狀態轉移。Timeout 或不一致時透過 query、retry、compensation 或 reconciliation 收斂。最後一定回到 source of truth 與 idempotency，避免重複副作用。

#### 1. Provider 接進來時，從 request、callback、query 到 reconciliation 的完整流程是什麼？

> 我通常把 Provider Flow 分成 Request、Callback、Query 與 Reconciliation 四條路徑。Request 階段先建立 internal order，再組 provider request 並送出；Callback 是主要狀態更新來源，但要驗簽、驗證欄位與狀態轉移；Query 用於 timeout 或狀態不確定時收斂結果；Reconciliation 則定期比對 internal order 與 provider record，找出不一致訂單。整體核心是 state machine、idempotency 與最終一致性。

#### 2. Provider callback 不可信時，你會做哪些 trust boundary 檢查？

> 我不會直接相信 callback。第一步是驗簽，確認來源可信；接著驗證 merchant id、internal order id、provider transaction id、amount、currency、timestamp 等關鍵欄位；再確認目前訂單狀態是否允許轉移。如果欄位不符、簽章錯誤或狀態不允許，我不會直接更新訂單結果，只會記錄 audit log 並進入異常處理。

#### 3. Provider callback 和 query 結果不同時，你相信哪邊？怎麼收斂？

> 我不會簡單說永遠相信其中一邊，而是先看系統定義的收斂規則與 source of truth。通常會比對 internal order、provider transaction id、金額、狀態與時間順序。有些系統會把 query 視為最終確認來源，但仍要受狀態機保護。重點是規則要一致、可追溯，不要每次靠人工直覺判斷。

#### 4. Provider timeout 怎麼處理？為什麼不能直接當成功或失敗？

> Timeout 只代表我沒有收到結果，不代表 provider 沒有執行成功。如果直接當失敗，可能重送造成重複訂單或重複扣款；如果直接當成功，也可能造成內部狀態錯誤。所以我會把 timeout 視為 unknown state，保留訂單並透過 callback、query 或 reconciliation 收斂終態。

#### 5. Provider API rate limit 怎麼辦？

> 如果 provider 有 rate limit，我會控制 query 頻率、retry 策略與流量分配，避免大量重試把問題放大。必要時可以透過 queue、batch 或 backoff 平滑流量。重點是區分哪些請求可以延後，哪些交易狀態需要優先收斂，同時要有告警讓團隊知道 provider 已接近限制。

#### 6. Provider schema 改版時，你怎麼兼容新舊版本？

> 我會優先追求向後相容，例如新增欄位而不是直接改變欄位意義。Consumer 先能接受新舊格式，再逐步切換 producer 或 provider 設定。對外部 provider 來說，最怕一方改版造成另一方解析失敗，所以我會保留版本標記、相容 parser、測試樣本與 rollback plan。

#### 7. 多 provider 共用介面怎麼設計？

> 我會先定義內部 domain interface，例如 login、balance、transfer、bet、settle、rollback 或 payment request / callback / query。Provider 差異封裝在 adapter 裡，上層業務只看 internal request / response，不直接依賴 provider 特殊格式。這樣可以降低 provider 差異對核心流程的影響，但也不能抽象過頭，把真的不同的業務語意硬塞成同一種。

#### 8. Adapter Pattern 在 provider integration 裡怎麼落地？不要只講設計模式名詞。

> 具體來說，A Provider 可能用 amount，B Provider 用 coin；A 回 success code，B 回 status；簽章欄位和錯誤碼也不同。Adapter 的工作是把不同 provider 的 request / response、錯誤碼、狀態與金額單位轉成系統內部統一格式。上層 service 不需要知道 provider 細節，只處理 internal model 和 state transition。

#### 9. Provider 故障時怎麼切流？哪些流量不能亂切？

> Query 或查詢類流量通常比較容易切換或降級；資金流、transfer、wallet、bet-settle 這類流量要非常小心，因為切流可能造成交易狀態不一致。切流前要先確認既有交易是否已收斂、pending 訂單如何處理、callback 是否還會回來，以及新舊 provider 的 order mapping 是否清楚。不能只看 provider 掛了就立刻切所有流量。

#### 10. Provider routing 規則怎麼設計？要不要放 Redis？

> Routing 規則可以依 merchant、幣別、provider health、流量比例或白名單決定。Redis 可以作為 routing config 的快取，因為讀取頻率高、延遲低；但 source of truth 應該在 DB 或 config service。需要版本、reload、audit log 與 fallback，避免 Redis 異常或錯誤配置讓交易路由失控。

#### 11. provider settlement 和平台 settlement 不一致怎麼辦？

> 我會先確認 internal order、wallet transaction、bet / settle record 與 provider settlement record，再比對 amount、currency、status、transaction id 與完成時間。差異來源可能是 callback 遺失、query 延遲、retry 重複執行、provider 延遲或人工補單。最後透過 reconciliation 產生差異清單，再依規則自動修復或人工處理。

#### 12. reconciliation 怎麼做？source of truth 怎麼選？

> Reconciliation 的本質是找出不一致，不是直接修資料。首先要定義內部資料與 provider record 各自代表什麼，並確認哪個欄位是業務真相。接著定期比對 order id、provider transaction id、amount、currency、status 與完成時間。發現差異後產生異常清單，再決定自動補償、人工確認或等待下一輪資料收斂。

#### 13. provider replay attack 怎麼防？

> 常見做法是 signature 驗證、timestamp 驗證、nonce 或 idempotency 檢查。即使 callback 被重送，也不能產生重複副作用。實務上我會讓 callback handler 先檢查簽章與時間，再依 order id 或 provider transaction id 做終態與 idempotency 檢查，確保重送只會被安全忽略或回傳成功。

#### 14. signature 驗證怎麼做？哪些欄位一定要參與簽名？

> 我會依 provider 約定組簽名字串，通常會包含 merchant id、order id、provider transaction id、amount、currency、timestamp 與 secret key 相關規則。重點不是只驗證格式，而是確保 payload 沒被竄改，尤其是金額、幣別、訂單與商戶識別。驗簽失敗不能更新訂單狀態，只能記錄並拒絕或進異常流程。

#### 15. provider transaction id 和 internal order id 怎麼 mapping？哪個可以當 idempotency key？

> Internal Order Id 是系統內部業務識別，Provider Transaction Id 是 provider 世界的識別，兩者需要明確 mapping。Idempotency 要看處理邊界：內部訂單狀態轉移通常以 internal order id 為主；callback 去重和 provider 對帳會同時看 provider transaction id。重點是唯一性規則要清楚，避免同一筆 provider transaction 被錯誤對到多筆內部訂單。

這區優先練的 5 題是：

```text
完整 Provider Flow -> Timeout -> Callback vs Query -> Reconciliation -> 多 Provider Adapter
```

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
