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
- [01-senior-owner-flow-inventory.md](01-senior-owner-flow-inventory.md)：高價值 flow inventory，依產品與專案分類。
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

## 讀法

先讀：

1. `07-core-positioning.md`
2. `00-operating-rules.md`
3. `02-learning-roadmap.md`
4. `04-interview-casebook.md`
5. `05-resume-master-zh.md`
6. `08-application-autobiography-zh.md`
7. `09-ai-prompt-manual.md`
8. `10-vault-structure-plan.md`
9. `11-senior-interview-readiness.md`
10. `12-role-target-readiness-matrix.md`
11. `13-code-capability-map.md`

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
