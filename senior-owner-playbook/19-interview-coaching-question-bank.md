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
