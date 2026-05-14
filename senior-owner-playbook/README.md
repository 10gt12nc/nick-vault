# Senior / Owner Playbook

這是 `nick-vault` 重新開始後的新入口。

舊資料全部視為參考來源，位置在 `archive/`。本資料夾不是複製舊檔，也不是搬移舊檔，而是從 archive 與專案 code 線索整理、去重、合併後重寫的新版本。

## 目標

對標：

- Senior Java Backend Engineer
- Platform Backend Engineer
- System Owner
- Lead / Architect 候選能力

核心不是「產很多文件」，而是把 Nick 過去碰過的遊戲平台、金流、錢包、注單、報表、MQ、K8s / K3s、觀測性與後台控制流程，整理成能讀、能問、能面試、能轉成履歷的學習系統。

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
- [16-career-skill-matrix.md](16-career-skill-matrix.md)：初階到資深、Lead 候選的軟硬實力檢查表。

## 讀法

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

主軸仍然是 production flow。`14`、`15`、`16` 是輔助層：卡住或要補全局 / 硬底子 / 職涯缺口時再讀，不要拿來取代 flow。

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

AI 必須自動：

- 重讀 KB、project 既有文件與相關 code 最新狀態。
- 檢查既有 Step / flow 是否過舊、缺 evidence 或不符合目前 KB。
- 判斷本次是 Level 1 / Level 2 / Level 3。
- 寫清楚已掃、未掃、推測與待確認。
- 如果上一個 Step 不乾淨，先建議重整，不直接跳下一步。
- 自動判斷是否要維護 project README、Step 文件、flow evidence、claim boundary、todo 或共用 KB。
- 自動給下一步建議。

Nick 不需要每次提醒「重讀 KB / 重讀 code / 維護規則」。

AI 不會背景定期自動掃 repo。改檔後的 git 流程依 `00-operating-rules.md`：自查通過後自動 commit；若本輪需要 push，AI 要直接執行 `git push` 觸發 approval 視窗，讓 Nick 按 Yes / No。只有 Nick 明確說「不要 push / 只 commit / 先停在本地」時，才停在本地 commit。

暫時不要讀全部 flow。先挑一條做完整，例如：

- `payment-provider-callback`
- `transfer-wallet-transfer-in-out`
- `bet-settlement`
- `settled-bets-kafka`
- `observability-pipeline`

## Claim 原則

履歷與面試只使用保守說法：

- 可說：參與、維護、分析、梳理、優化、協助、設計改善方向。
- 沒有 MR / commit / ticket / metric / 本人確認前，不說：主導、獨立完成、改善 X%、架構師、owner 全權負責。
- 面試可以講 owner 思考，但要清楚區分「實際做過」與「分析後提出的改善方案」。
