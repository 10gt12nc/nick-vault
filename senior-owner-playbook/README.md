# Senior / Owner Playbook

這是 `nick-vault` 重新開始後的新入口。

舊資料已整理進 `senior-owner-playbook/` 與 `projects/`，`archive/` 已依 Nick 指示清空，只保留 `.gitkeep` 佔位。後續不再依賴 archive 作為必要參考來源。

## 目標

對標：

- Senior Java Backend Engineer
- Platform Backend Engineer
- System Owner
- Lead / Architect 候選能力

核心不是「產很多文件」，而是把 Nick 過去碰過的遊戲平台、金流、錢包、注單、報表、MQ、K8s / K3s、觀測性與後台控制流程，整理成能讀、能問、能面試、能轉成履歷的學習系統。

## 兩條主線

本 playbook 同時維護兩條線，不要混在一起：

- `Flow Track`：整理系統 flow 與面試 case。固定主線是 Step 1 找 candidate flows、Step 2 排序、Step 3 單條 flow 深掃、Step 4 轉面試 case、Step 5 單條 flow claim gate。
- `Career Track`：整理履歷、自傳與 project-level 經驗。正式更新 `05-resume-master-zh.md` / `08-application-autobiography-zh.md` 前，要先做 project contribution claim consolidation，掃 Nick / `10gt12nc` commits、branches、重要 diff、本人確認、既有 flow evidence、`projects/` 與 KB 內履歷素材。`archive/` 目前已清空，不再列為必要來源。

Flow Step 5 可以提供「這條 flow 能不能作履歷 / 面試素材」的證據，但不能直接代表整個 project 的履歷結論。當 Nick 追問「我是不是做過很多」、「履歷怎麼寫」、「這個 project 經驗怎麼放」時，下一步優先走 Career Track。

## 目前檔案

- [00-operating-rules.md](00-operating-rules.md)：這套資料以後怎麼維護，避免再次變亂。
- [01-senior-owner-flow-inventory.md](01-senior-owner-flow-inventory.md)：flow dashboard，記錄目前 Step、下一步、履歷邊界與近期 queue。
- [02-learning-roadmap.md](02-learning-roadmap.md)：從 0 開始讀的順序，不一次吞 189 條。
- [03-flow-learning-package-template.md](03-flow-learning-package-template.md)：每條 flow 要怎麼做成完整學習包。
- [04-interview-casebook.md](04-interview-casebook.md)：Senior / Lead / Architect 面試案例與問答方向。
- [05-resume-master-zh.md](05-resume-master-zh.md)：唯一履歷自傳 master，一、工作經驗 二、專長 三、自傳 四、自我推薦。
- [06-todo.md](06-todo.md)：下一步清單。
- [07-core-positioning.md](07-core-positioning.md)：整套學習包的核心定位：從功能工程師到高交易 Production System Owner。
- [08-application-autobiography-zh.md](08-application-autobiography-zh.md)：正式投遞用自傳版本，含超精簡版、一般版與 30 秒自我介紹。
- [09-ai-prompt-manual.md](09-ai-prompt-manual.md)：之後請 AI 掃專案、整理 flow、更新履歷時使用的提示詞。
- [10-vault-structure-plan.md](10-vault-structure-plan.md)：nick-vault 後續資料夾規劃與維護方式。
- [11-senior-interview-readiness.md](11-senior-interview-readiness.md)：檢查是否已具備 10 萬以上 Senior / Owner 面試準備度。
- [12-role-target-readiness-matrix.md](12-role-target-readiness-matrix.md)：Senior / Platform / Owner / Lead-Architect 四種目標職缺對標矩陣。
- [13-code-capability-map.md](13-code-capability-map.md)：根據 `/Users/nick/Git` 高階掃描建立的本地專案能力地圖與外部案例標注規則。
- [14-technical-decision-hard-skills.md](14-technical-decision-hard-skills.md)：技術硬底子、決策比較、技術選型與 trade-off 教學模板。
- [15-project-architecture-map-system.md](15-project-architecture-map-system.md)：大專案 / 子專案地圖與跨 repo 架構圖規則。
- [16-career-skill-matrix.md](16-career-skill-matrix.md)：初階到資深、Lead 候選的軟硬實力檢查表、完整技術結構、優先補強 5 項與 8 週養成計劃。
- [17-salary-negotiation.md](17-salary-negotiation.md)：104 / 面試用薪資談判定位、期望待遇區間與口頭說法。
- [18-system-design-templates.md](18-system-design-templates.md)：可選架構加強，把已完成 production flows 萃取成 0 到 1 system design 面試模板；目前四份 v1 均已完成，Slot Math / RTP Validation 維持遊戲 / slot / provider JD 的差異化素材。
- [19-interview-coaching-question-bank.md](19-interview-coaching-question-bank.md)：互動式 Senior Backend 問答診斷、教學題庫與評分規則；當 Nick 想用問答確認實力、請 AI 追問 / 教學 / 畫圖 / 代碼演示時，從這份開始。
- [interview-practice/](interview-practice/)：面試練習成果庫。每輪問答後只記題目、Nick 原答摘要、評分、修正版與下次追問，不保存聊天逐字稿。
- [20-game-backend-architecture-selection.md](20-game-backend-architecture-selection.md)：遊戲後端架構選型與整合策略；目前 side project 暫不做，本檔只作面試架構問答、商業化卡牌 / 回合制 / 放置遊戲選型、單體 vs 微服務與新遊戲服務器整合備忘。
- [21-zero-to-one-project-strategy-report.md](21-zero-to-one-project-strategy-report.md)：0 到 1 可容器化遊戲交易 backend lab 策略報告；重讀 `nick-vault`、AntPlay、iwin、UGSoft、DevOps 的技術棧與架構線索後，整理面試展示版、可部署 portfolio 版與商業化 pivot 的邊界。這不是新履歷 claim，也不是 side project 開工授權。
- [22-career-industry-kb-evolution-plan.md](22-career-industry-kb-evolution-plan.md)：30 年能力主幹、Codex automation 週報、工作 / 面試 / 日語推送，以及 `nick-vault` 未來升級成職涯證據庫、工作復盤、產業 KB 與 skill source 的草案；目前只是規劃，不是自動開工授權。
- [backend-weekly-plan.md](backend-weekly-plan.md)：48 週 Senior Java Backend / Platform Backend 核心循環 + 實戰輸出補強池；每週只聚焦 1 個主題，不累積學習債務，並把 Incident、Decision、Ownership 與小比例 AI-Assisted Engineering 轉成可面試表達。
- [backend-learning-log.md](backend-learning-log.md)：每週學習 checkpoint；只記核心概念、production 觀點、面試題與 KB 建議，不是流水帳。
- [backend-weekly-template.md](backend-weekly-template.md)：每週 automation 輸出模板與重複內容檢查規則。
- [company-system-deep-dive/](company-system-deep-dive/)：候選第二條 weekly automation 的課綱、資料夾、清單、模板、手動 prompt 與啟用前資料結構。用途是每次依 `project-index.md` 先選一個專案，再依 `topic-list.md` 選一個公司系統主題，用真實 code / git / DB / MQ / config 學 System Context、Runtime Flow、Code Reading、Data Flow、Engineering Thinking、Technology Comparison、Redesign、Interview Value 與 0-3 個 New Findings；目前尚未設定排程，也不是授權全掃公司 repo。
- [applications/](applications/)：特定 JD 客製投遞包。沒有明確 JD 時不用讀；有明確職缺時，才把 08 / 04 / 17 的通用素材重排成該職缺的履歷、自傳、HR / 技術問答與薪資口徑。
- 是否急著跳槽、低壓公司能否當準備跳板、market check 節奏，也統一看 [17-salary-negotiation.md](17-salary-negotiation.md)。

## 讀法

### 投遞前衝刺讀法

目標是「讀完、練完，就開始投 Senior Java Backend / Platform Backend，對標 10 萬以上」。不要再平均掃 repo，照這份一頁版清單看。

#### 主線閱讀

1. [README.md](README.md)：投遞前衝刺讀法。先看整體順序，不要亂掃。
2. [11-senior-interview-readiness.md](11-senior-interview-readiness.md)：判斷目前是中等可面、穩過可抗追問，還是完全對標 Senior；先用這份校正焦慮。
3. [08-application-autobiography-zh.md](08-application-autobiography-zh.md)：104 可貼版，包含工作經驗、專長、自傳、自我推薦；投遞主要看這份。
4. [05-resume-master-zh.md](05-resume-master-zh.md)：履歷母稿 / 證據池；用來確認哪些可以寫、哪些不能誇大。
5. [04-interview-casebook.md](04-interview-casebook.md)：面試 case 主檔；90 秒、3 分鐘、追問都從這裡練。
6. [18-system-design-templates.md](18-system-design-templates.md)：四份 system design template。重點是架構思路，不是可直接上線 spec。
7. [17-salary-negotiation.md](17-salary-negotiation.md)：談薪、期望待遇、底線；要開履歷或接獵頭前看。
8. [19-interview-coaching-question-bank.md](19-interview-coaching-question-bank.md)：進入互動問答診斷；AI 每輪問 3-5 題，依回答直接評估、教學、追問或打磨口說。

補充查詢：

- [12-role-target-readiness-matrix.md](12-role-target-readiness-matrix.md)：判斷要投 Senior Backend、Platform Backend、System Owner 或 Lead / Architect 候選。
- [01-senior-owner-flow-inventory.md](01-senior-owner-flow-inventory.md)：flow dashboard；只用來查某條 flow 做到哪、能不能放履歷。
- [06-todo.md](06-todo.md)：收斂狀態與可選加強；不要把它當無限待辦。
- [applications/](applications/)：只有已貼明確 JD 並完成客製時才讀；例如糖蛙 Backend Team Lead Java、糖蛙 Senior Java Backend、微達軟體 Senior Java Backend 客製包。
- [19-interview-coaching-question-bank.md](19-interview-coaching-question-bank.md)：不是投遞前必讀全文；目前題庫 v1.0 已定稿，不再加題。它保留 14 個主題的診斷池，已收斂成 30 題核心題，並補完三個 project story 與面試準備閱讀順序。下一步是錄音自講、三個 story 練到 `30 秒 / 90 秒 / 3 分鐘`、主力 7 條 flow 摘要、30 題核心題與投遞 / 面試驗證。

目前投遞前的準備比例固定是 `70 / 20 / 10`：

- 70%：production case / system design / claim boundary。
- 20%：Java / SQL / transaction / Redis / MQ 基本判斷，遇到 case 或面試回饋再補。
- 10%：LeetCode / coding test 保險，投遞前或收到面試邀約後再補手感。

面試題也不要做成泛用題海；第一輪仍從 payment provider、wallet / bet-settle、Kafka / report projection 三條主力 case 長出追問、回答要點與 case-specific 基本功。Provider Integration 是主戰場專題，但不取代 Java Backend 基本盤；Security / Auth、Real Troubleshooting、Architecture Evolution 只有 JD、面試追問或回答卡住時才抽題，其中 Real Troubleshooting 併入 Incident 練，Architecture Evolution 放第二輪加分。AI 時代 coding 的重點是能 review AI 產物是否能進 production，而不是把刷題變成新的主線。

轉職月份策略看 [17-salary-negotiation.md](17-salary-negotiation.md)：台灣一般主旺季是 2-4 月，2026 特殊狀況可延到 4-5 月；9-10 月是第二波，11-1 月適合準備 / 卡位。月份只影響投遞節奏，不取代 case 準備。

投遞前優先練五類 case；完整 8-10 條通用排序以 [04-interview-casebook.md](04-interview-casebook.md) 的「完全對標 Senior / Platform」為準。

1. Payment provider：`payment-order-provider-request` / `payment-provider-callback`。
2. Wallet / bet-settle：`antplay-slot-game-api/slot-bet-settle-rollback` 或 `iwin_gameserver/third-party-transfer-in-out`。
3. MQ / projection：`antplay-slot-game-job/proxy-user-data-report-projection` 或 `game_job/daily-game-data-summary`。
4. High-traffic / partition：`bet-record-sharding-schema-route` 或 `db-partition-job-report-routing`。
5. Rollout / observability 加分：`k3s-deploy/gameserver-phased-rollout`，只作 interview-only 加分，不放正式 DevOps owner。

### 投遞前 Flow 閱讀清單

不要全部平均讀。先讀主力 flows 1-7；8-10 是 Senior / Platform 追問加強；差異化 flows 只在投遊戲 / slot / provider JD 或想強化 B 版履歷時讀。

#### 主力 flows

1. [payment-order-provider-request](../projects/iwin/payment/flows/payment-order-provider-request/flow.md)：Provider request / 查單 / timeout unknown。金流對接主 case，但不要講成完整金流 owner。
2. [payment-provider-callback](../projects/iwin/payment/flows/payment-provider-callback/flow.md)：Callback / 重送 / order state / 下游副作用。搭配 provider request 讀。
3. [third-party-transfer-in-out](../projects/iwin/iwin_gameserver/flows/third-party-transfer-in-out/flow.md)：第三方遊戲 provider / wallet mutation / bet-settle 邊界。Senior 深度主力。
4. [slot-bet-settle-rollback](../projects/antplay/antplay-slot-game-api/flows/slot-bet-settle-rollback/flow.md)：AntPlay bet / settle / rollback。遊戲錢包與狀態機主 case。
5. [transfer-wallet-money-in-out](../projects/antplay/antplay-slot-game-api/flows/transfer-wallet-money-in-out/flow.md)：Transfer wallet。補 wallet source of truth、idempotency、partial success。
6. [proxy-user-data-report-projection](../projects/antplay/antplay-slot-game-job/flows/proxy-user-data-report-projection/flow.md)：Kafka / Quartz / report projection。MQ / batch / projection 主 case。
7. [daily-game-data-summary](../projects/iwin/game_job/flows/daily-game-data-summary/flow.md)：Daily summary / BI projection / batch 重跑。報表可信度 case。
8. [bet-record-sharding-schema-route](../projects/antplay/antplay-slot-game-api/flows/bet-record-sharding-schema-route/flow.md)：高流量資料表 / schema routing。補 partition / table governance。
9. [db-partition-job-report-routing](../projects/antplay/antplay-slot-game-job/flows/db-partition-job-report-routing/flow.md)：DB partition job / report routing。高流量與維運補充。
10. [gameserver-phased-rollout](../projects/iwin/k3s-deploy/flows/gameserver-phased-rollout/flow.md)：Rollout / rollback / observability。interview-only 加分，不放正式 DevOps owner。

#### 差異化 flows

1. [fixed-multi-bet-currency-math-core-compatibility](../projects/antplay/star-math/flows/fixed-multi-bet-currency-math-core-compatibility/flow.md)：Slot math contract / fixedMultiBet / currency。遊戲 domain 差異化。
2. [rtp-reel-strip-simulation-validation](../projects/antplay/star-math/flows/rtp-reel-strip-simulation-validation/flow.md)：RTP / reel strip / simulation validation。高風險 domain validation。
3. [buy-free-scatter-rtp3-result-contract](../projects/antplay/star-math/flows/buy-free-scatter-rtp3-result-contract/flow.md)：Buy free / RTP_3 / result contract。投遊戲職缺再讀深。
4. [jackpot-symbol-hit-and-prize-scaling](../projects/antplay/star-math/flows/jackpot-symbol-hit-and-prize-scaling/flow.md)：Jackpot symbol / prize scaling。只能講 result contract，不講 jackpot pool owner。
5. [special-wild-feature-state-transform](../projects/antplay/star-math/flows/special-wild-feature-state-transform/flow.md)：Special Wild / extraData / feature state。遊戲面試補充。

最小讀法：

```text
README -> 11 -> 08 -> 05 -> 04
```

面 Senior 更穩：

```text
README -> 11 -> 08 -> 05 -> 04 -> 主力 flows 1~7 -> 18 -> 17
```

投遞通路三線一起跑：

- 104 主動投遞：用 `08` 通用版先投，不要等完美 JD。
- LinkedIn / 獵頭：提高曝光，讓懂金流、遊戲錢包、provider、MQ、legacy takeover 的獵頭協助對職缺。
- 內推：有熟人 / 前同事時優先，Senior 缺更吃信任。

策略不是只靠獵頭，而是 104 保持投遞量、LinkedIn / 獵頭提高曝光、內推優先吃高品質機會。

### 全套維護讀法

先讀：

1. `00-operating-rules.md`
2. `09-ai-prompt-manual.md`
3. `03-flow-learning-package-template.md`
4. `07-core-positioning.md`
5. `02-learning-roadmap.md`
6. `04-interview-casebook.md`
7. `05-resume-master-zh.md`
8. `08-application-autobiography-zh.md`
9. `10-vault-structure-plan.md`
10. `11-senior-interview-readiness.md`
11. `12-role-target-readiness-matrix.md`
12. `13-code-capability-map.md`
13. `14-technical-decision-hard-skills.md`
14. `15-project-architecture-map-system.md`
15. `16-career-skill-matrix.md`
16. `17-salary-negotiation.md`
17. `18-system-design-templates.md`
18. `19-interview-coaching-question-bank.md`
19. `20-game-backend-architecture-selection.md`
20. `21-zero-to-one-project-strategy-report.md`
21. `22-career-industry-kb-evolution-plan.md`

主軸仍然是 production flow。`14`、`15`、`16`、`17`、`18`、`19`、`20`、`21`、`22` 是輔助層：卡住或要補全局 / 硬底子 / 職涯缺口 / 談薪策略 / system design 口說 / 互動問答診斷 / 遊戲後端選型 / 0 到 1 backend lab 策略 / 長期職涯與產業 KB 規劃時再讀，不要拿來取代 flow。

## AI 自動維護規則

這套規則是全 vault 共用，不是單一專案專用。

Nick 之後只要下 project / flow / Step 任務，例如：

```text
payment step1
app_bi step2
某 flow Step 3
下一步
繼續
```

或只貼：

```text
讀kb
下一步
```

AI 必須自動：

- 重讀 KB、project 既有文件與相關 code 最新狀態。
- 檢查既有 Step / flow 是否過舊、缺 evidence 或不符合目前 KB。
- 判斷本次是 Level 1 / Level 2 / Level 3。
- 判斷本次是 Flow Track 還是 Career Track；牽涉履歷、自傳、Nick 真實貢獻時，不得只用單條 flow Step 5 代替 project contribution claim consolidation。
- 寫清楚已掃、未掃、推測與待確認。
- 如果上一個 Step 不乾淨，先建議重整，不直接跳下一步。
- 自動判斷是否要維護 project README、Step 文件、flow evidence、claim boundary、todo 或共用 KB。
- 自動給下一步建議。

`讀kb / 下一步` 是省字入口，不是覆蓋舊指令。Nick 明確下 `project / flow / Step N` 時照 Step 主線做；Nick 只貼 `讀kb / 下一步` 時，AI 才自己判斷應走 Flow Track、Career Track、Resume / 104、Domain Map、Decision Notes / 口說材料，或是否已收斂只需自由提問。Teaching Notes 預設先不補，只有 Nick 讀某條 flow 卡住、面試回饋指出基本功缺口，或 Nick 明確要求補教學時才補。

若 Nick 明確說「專案先不下一步」、「先只更新 KB」、「先不要推 project / flow」，本輪視為 KB-only 維護：AI 只修正規則 / 索引 / todo / readiness 的一致性，完成後不附 project flow 下一步 prompt，也不自動執行 todo 中的任何 flow。

Nick 不需要每次提醒「重讀 KB / 重讀 code / 維護規則」。

AI 不會背景定期自動掃 repo。改檔後的 git 流程依 `00-operating-rules.md`：日常模式預設只在 `main` 開發，且同一時間只允許一個 session 具備寫入 / commit 權限；其他 session 只能只讀。例外使用 project / submodule branch 時，要定期同步 `main` 才能讀到最新 KB。自查通過後自動 commit，但 commit 前必須確認 staged 內容沒有混入其他 session / 其他 project；若本輪需要 push，AI 要直接執行 `git push` 觸發 approval 視窗，讓 Nick 按 Yes / No。只有 Nick 明確說「不要 push / 只 commit / 先停在本地」時，才停在本地 commit。

投遞前不要讀全部 flow。以本檔上方 `投遞前 Flow 閱讀清單` 為準：先讀主力 flows 1-7；8-10 是 Senior / Platform 追問加強；差異化 flows 只在投遊戲 / slot / provider JD 或想強化 B 版履歷時讀。

## Claim 原則

履歷與面試只使用保守說法：

- 可說：參與、維護、分析、梳理、優化、協助、設計改善方向。
- 沒有 MR / commit / ticket / metric / 本人確認前，不說：主導、獨立完成、改善 X%、架構師、owner 全權負責。
- 面試可以講 owner 思考，但要清楚區分「實際做過」與「分析後提出的改善方案」。
