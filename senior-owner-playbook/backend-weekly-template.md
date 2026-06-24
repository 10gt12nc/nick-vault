# Backend Weekly Template

狀態：每週 automation 使用模板。

用途：讓每週學習排程固定輸出少量、高品質、可行動的內容。這不是題庫擴張器、文章剪貼簿，也不是履歷 / 自傳自動改寫工具。

## 固定規則

- 每週跑一次。
- 每週只聚焦 1 個主題。
- 每週最多 5 個排程項目。
- 每週最多吸收 1-2 個重點；沒讀完不用補，不累積債務。
- 不自動改 `04 / 05 / 08 / 17`、三個故事稿或 flow claim。
- 不把文章全文貼入 KB，只保留核心概念、production 觀點、面試表達、與 Nick 經驗的關聯、待驗證問題。
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

- 避免重複檢查：先看 `backend-learning-log.md` 是否已跑過相同主題；若跑過，要加深 incident、production、trade-off 或 interview depth。
- 本週可執行任務：最多 1 項，30 分鐘內完成。
- 與面試材料關聯：至少連到三個 Story、12 條 Flow 或 30 題核心之一。
- 實戰輸出補強：若主題涉及 Incident、Decision 或 Ownership，必須補一句它對 Technical Communication、Risk Communication、Decision Making 或 Ownership 的幫助。
- KB 維護建議：只能提出建議，不大量自動改正式 KB。
- 本週不建議做什麼：明確列出低 ROI 或焦慮型延伸。

## 每週輸出格式

```text
## 本週主題

## 為什麼這週學這個

請連結到：
- Senior Backend 面試
- Production
- System Design
- Nick 既有 Story / Flow

## 核心概念

控制在 10-15 分鐘可讀完。

## Production 情境

優先連結：
- Provider Integration
- Payment Callback
- Wallet / Bet-Settle
- MQ / Projection
- Legacy Takeover

## 常見錯誤

## Incident / Troubleshooting

至少提供一個 production 角度排查思路。

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

## 與我的面試材料如何連結

請說明：
1. 對應哪個 Story：Provider Integration / Wallet / Bet-Settle / Legacy Takeover
2. 對應哪條 Flow
3. 對應哪類 30 題核心面試題
4. 可以補強哪個面試弱點
5. 對應哪個軟實力面向：Technical Communication / Risk Communication / Decision Making / Ownership
6. 是否能講進自我介紹；若能，必須保守不誇大

## 本週必看

最多 3 篇。每篇包含：
- 標題
- 來源
- 連結
- 為什麼值得看
- 對應哪個面試 / Production Flow

## 本週可執行任務

最多 1 項：
- 30 分鐘內完成
- 不建立大型專案
- 不新增複雜 backlog
- 可直接用於面試或 KB

## 本週 KB 維護建議

只能提出建議，不大量自動改 KB：
1. 建議新增
2. 建議補強
3. 建議暫不處理

## 本週不建議做什麼

指出本週應避免的低 ROI 行為。
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
