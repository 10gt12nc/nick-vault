# Backend Weekly Template

狀態：每週 automation 使用模板。

用途：讓每週學習排程固定輸出少量、高品質、可行動的內容。這不是題庫擴張器、文章剪貼簿，也不是履歷 / 自傳自動改寫工具。

核心定位：

```text
Weekly Senior Backend Capability Builder
```

每週不是單純學一個技術名詞，而是利用該主題訓練 Senior Java Backend / Platform Backend 的 production thinking、trade-off、incident、system design、decision making 與 interview expression。

## 固定規則

- 每週跑一次。
- 每週只聚焦 1 個主題。
- 每週最多 5 個排程項目。
- 每週最多吸收 1-2 個重點；沒讀完不用補，不累積債務。
- 不自動改 `04 / 05 / 08 / 17`、三個故事稿或 flow claim。
- 不把文章全文貼入 KB，只保留核心概念、production 觀點、面試表達、technology landscape、future direction、自然適用時與 Nick 經驗的關聯、待驗證問題。
- 不處理日文，除非 Nick 明確要求。
- 所有新增說法要標註：`已做過`、`參與過`、`分析過`、`可作為目標`、`待驗證`。

## 重複內容檢查

產出本週內容前，先讀：

- `backend-weekly-plan.md`
- `backend-learning-log.md`
- `19-interview-coaching-question-bank.md` 的 30 題核心與三個 Story。

檢查：

1. 本週主題是否曾經跑過。
2. 過去是否已介紹相同概念。
3. 是否曾推薦相同文章。
4. 是否已經有相同面試題或相同 production flow 連結。

如果學過：

- 不重複基礎介紹。
- 增加 Incident 深度。
- 增加 Production 案例。
- 增加 Trade-off。
- 增加 Interview Depth。

目標：

```text
第二次比第一次深。
第三次比第二次更貼 production / interview。
不要重講同樣內容。
```

## 每週 5 個排程項目

1. 本週技術主題學習。
2. 本週 Production Flow 連結。
3. 本週面試題練習。
4. 本週 Incident / Troubleshooting 思考。
5. 本週 KB / 學習紀錄維護。

## 每週必要檢查清單

每週輸出前必須確認：

- 本週輸出類型：依主題選 1 個 weekly mode：`Concept Mode`、`Troubleshooting Mode`、`Trade-off Mode`、`Interview Mode`，避免每週都變成同一種文章包。
- 避免重複檢查：先看 `backend-learning-log.md` 是否已跑過相同主題；若跑過，要加深 incident、production、trade-off 或 interview depth。
- 本週可執行任務：最多 1 項，30 分鐘內完成。
- 能力優先順序：Production Thinking -> Troubleshooting -> Trade-off -> System Design -> Interview -> Known Case（if naturally applicable）-> Resume（optional）。
- 與面試材料關聯：若本週主題自然連到三個 Story、12 條 Flow 或 30 題核心，才做保守連結；若不自然，改連到一般 backend production / system design 場景，不硬貼履歷或 Story。
- Known Production Case Lens：最多 5 bullets；若自然適用，才用既有 notes 連到 Nick 已知 cases；否則使用泛化 production analogy。不掃公司 repo、不補公司系統深讀、不創造 direct ownership。
- Avoid Hallucination：討論 Nick production cases 時，必須區分 `verified from Nick's documented experience`、`inferred from general engineering practice`、`speculative ideas for future improvement`；不得把 assumption 寫成 fact。
- Mini ADR：至少練一個 decision angle，包含 Context、Decision、Alternatives、Consequences、When this decision becomes wrong。
- Observability Anchor：為本週主題定義 1 個 log、1 個 metric、1 個 trace/span、1 個 alert condition、1 個不該 alert 的情況。
- Technology Landscape：建立本週主題的技術地圖，分成 Learn Now、Learn Later、Awareness Only，並說明各技術在什麼情境更適合；不得建議把所有相關技術都學完。
- Knowledge Boundary：用 Must Understand、Should Understand、Can Ignore For Now 收斂本週學習邊界，並說明每項為什麼屬於該分類。
- One Common Misconception：列 1 個最容易誤解的點，用來幫 Nick 記憶與面試避坑。
- Future Direction：只有有意義時才放；用 Senior Backend、Platform Backend、Architect 三層說明未來可能補什麼，每層最多 1 個重點，且必須說明它是 current priority 還是 future-only topic。
- Learning Check：學完後 Nick 至少要能 60 秒說明、說出 1 個 production failure mode、回答 1 題 Senior interview question、判斷何時不該用這個 approach。
- 實戰輸出補強：若主題涉及 Incident、Decision 或 Ownership，必須補一句它對 Technical Communication、Risk Communication、Decision Making 或 Ownership 的幫助。
- AI-Assisted Engineering：若本週使用 AI 協助學習或產出，必須補一句 AI risk review，檢查 Transaction Boundary、Idempotency、BigDecimal、SQL Index、Redis Lock、MQ Retry、Race Condition 或 Security 是否被忽略。
- KB 維護建議：只能提出建議，不大量自動改正式 KB。
- 本週不建議做什麼：明確列出低 ROI 或焦慮型延伸。

## 每週輸出格式

```text
## 本週主題

## Weekly Mode

選 1 個：
- Concept Mode：核心概念 / 機制。
- Troubleshooting Mode：incident diagnosis / production debugging。
- Trade-off Mode：design choice / migration / comparison。
- Interview Mode：answer structure / senior judgment。

## 為什麼這週學這個

請優先說明：
- production 上解決什麼問題
- troubleshooting 時會怎麼用
- trade-off / system design 判斷在哪裡
- Senior Backend 面試為什麼會問
- Nick 既有 Story / Flow：only if naturally applicable，不硬連

## 核心概念

控制在 10-15 分鐘可讀完。

## Beginner-to-Senior 解釋

用三層講清楚：
- Beginner：這個技術是什麼、解決什麼基本問題。
- Mid：實作時最常踩的坑。
- Senior：production flow、failure window、trade-off、observability 怎麼看。

## 小型 code / pseudo-code 範例

提供 1 個短範例即可：
- 優先用 Java / Spring / SQL / pseudo-code。
- 範例只示範核心概念，不建立完整專案。
- 若主題不適合 code，改用資料流 pseudo-code。

## 架構 / Flow 圖

提供 1 個簡單 Mermaid 圖即可：
- 用來說明 request flow、transaction boundary、MQ / retry / projection、incident 排查路徑。
- 不畫大型系統圖。
- 不把未做過的架構畫成 Nick 已經 owner 的系統。

## Production 情境

優先從一般 backend production 情境說明。

若自然適用，再連結：
- Provider Integration
- Payment Callback
- Wallet / Bet-Settle
- MQ / Projection
- Legacy Takeover

若不自然，不要硬連；改用常見 production / incident / system design 場景。

## Known Production Case Lens

最多 5 bullets：
- 這個主題是否自然連到 Nick 已知 case？
- 若自然連到，說明連到哪個 case。
- 若不自然，改用泛化 production 情境，不硬貼 Story / Flow / resume。
- 不掃公司 repo。
- 不新增公司系統 deep dive。
- 不發明 direct ownership。

Clearly distinguish:
- verified from Nick's documented experience
- inferred from general engineering practice
- speculative ideas for future improvement

Never present assumptions as facts.

## 常見錯誤

## Incident / Troubleshooting

至少提供一個 production 角度排查思路。

## Observability Anchor

For this topic:
- 1 useful log:
- 1 useful metric:
- 1 useful trace/span:
- 1 alert condition:
- 1 thing that should not alert:

## Senior 面試怎麼問

提供 3 題。

## Senior 面試怎麼回答

回答要保守，並區分：
- 已做過
- 參與過
- 分析過
- 可作為目標
- 待驗證

## System Design 延伸思考

說明這個主題牽涉哪些 trade-off。

## Mini ADR

- Context:
- Decision:
- Alternatives:
- Consequences:
- When this decision becomes wrong:

## Technology Landscape

For this topic:
- Related technologies:
- Current industry mainstream:
- When each technology is a better fit:
- Learn Now:
- Learn Later:
- Awareness Only:
- Why:

不要建議把所有相關技術都學完。

## Knowledge Boundary

- Must Understand:
- Should Understand:
- Can Ignore For Now:

Explain why each item belongs in that category.

## One Common Misconception

- Misconception:
- Correction:
- Why it matters in production / interview:

## Future Direction

只有有意義時才放；不要每週硬湊 Architect 方向。

If Nick later becomes:
- Senior Backend:
- Platform Backend:
- Architect:

每層最多 1 個重點，並說明它是 current priority 還是 future-only topic；不形成近期 backlog。

## 與我的面試材料如何連結（Only if naturally applicable）

若自然適用，請說明：
1. 對應哪個 Story：Provider Integration / Wallet / Bet-Settle / Legacy Takeover
2. 對應哪條 Flow
3. 對應哪類 30 題核心面試題
4. 可以補強哪個面試弱點
5. 對應哪個軟實力面向：Technical Communication / Risk Communication / Decision Making / Ownership
6. 是否涉及 AI-Assisted Engineering；若有，請說明它只是工作法 / review 能力，不能誇大成 AI Engineer 或 AI platform owner
7. 是否能講進自我介紹；若能，必須保守不誇大

若不自然，直接寫：

```text
本週主題主要補強通用 backend production capability，不硬連履歷 / Story / Flow。
```

## 本週必看

最多 2 篇。優先 1 篇官方文件；第 2 篇只有在提供明顯 practical value 時才放。

優先 stable references。若已有等價官方文件，避免使用低品質 blog。

每篇包含：
- 標題
- 來源
- 實際連結，不能只放佔位文字
- 為什麼值得看
- 對應哪個面試 / Production Flow

## 本週可執行任務

最多 1 項：
- 30 分鐘內完成
- 不建立大型專案
- 不新增複雜 backlog
- 可直接用於面試或 KB

## Learning Check

學完本週 packet 後，Nick 應該能：
1. 用 60 秒說明這個主題。
2. 說出 1 個 production failure mode。
3. 回答 1 題 Senior interview question。
4. 判斷什麼時候不該用這個 approach。

## 本週 KB 維護建議

只能提出建議，不大量自動改 KB：
1. 建議新增
2. 建議補強
3. 建議暫不處理

## 本週不建議做什麼

指出本週應避免的低 ROI 行為。

如果 packet 變得太長，優先保留：
1. production thinking
2. decision making
3. trade-off
4. interview

犧牲 exhaustive technical detail。
```

## 每週資料搜尋規則

每週主題需要查最新資料。資料優先順序：

1. 官方文件。
2. 官方 Blog。
3. 技術團隊 Blog。
4. Conference 分享。
5. 高品質工程文章。

避免：

- 低品質農場文。
- 無來源整理文。
- 過度抽象架構文。
- 與 Java Backend / Platform Backend 無關的文章。
- 只談概念但無 production 價值的文章。
