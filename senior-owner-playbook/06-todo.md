# Todo

## 使用規則

本檔是候選待辦與收斂清單，不是自動開工授權。

- Nick 明確下 `flow Step N`、`project contribution claim consolidation`、`rolling resume package` 時，AI 才開始對應深掃與改 project 文件。
- Nick 只要求「KB、待辦、缺口、優先順序、專案先不下一步」時，AI 只能更新本檔與相關 KB，一律不自動執行下面任何 flow。
- `必做收口` 是收斂排序，不代表每次回答都要推專案下一步；若當下是 KB-only 模式，回報 KB 維護結果即可。

## 候選待辦：2026-06-22 星期一重掃清單

狀態：候選待辦，不是自動開工授權。

目的：把投遞準備、基本功焦慮、flow 閱讀困難、`git log` 風險重建與近期 JD 客製包集中到本檔。這份清單只負責「下次看什麼、怎麼判斷要不要補」，不直接改 `04 / 05 / 08 / 17`，也不直接改任何 flow。

非目標：

- 不重掃全部 repo。
- 不新增泛用 Java / SQL / Redis / MQ 題庫。
- 不把基本功變成新主線。
- 不把 `git log` 的他人 commit 說成 Nick direct evidence。
- 不因 flow 難讀就重寫全部 `flow.md`。
- 不把 Team Lead JD 的管理說法覆蓋到通用履歷。

### 焦慮與收斂狀態

已確認：

- Nick 的焦慮點包含：基本功忘記、怕面試考、flow 太難讀、擔心是否需要一個人懂完整大系統。
- KB 目前規則是：先處理收斂與焦慮，不用新 backlog 回避問題。
- 目前方向不是再掃全部 repo，而是投遞、讀熟主力 flows、練口說、依 JD 補洞。

重掃時要確認：

- 是否仍維持 `70 / 20 / 10`：70% production case、20% 基本功、10% coding test。
- 是否有面試邀約或新 JD；若有，才做 JD-specific 補洞。
- 若 Nick 只是焦慮，先回到「已足夠投遞 / 可選補強 / 暫不建議」三段式，不新增大規模任務。

### 基本功最小掃描

目前結論：

```text
工作遇到再查可以；面試不能完全等遇到再查。需要一輪面試最低防穿透基本功。
```

只掃 12 題，不擴張：

1. `@Transactional` 什麼時候 rollback？
2. self-invocation 為什麼 transaction 會失效？
3. DB 成功但 MQ 發送失敗怎麼辦？
4. MQ 重複消費怎麼處理？
5. Redis lock 要注意什麼？
6. Redis 和 DB 不一致怎麼辦？
7. MySQL index 怎麼設計？
8. `EXPLAIN` 先看什麼？
9. callback 重送如何避免重複上分？
10. timeout 不能確定成功或失敗時怎麼辦？
11. batch 跑一半失敗怎麼重跑？
12. AI 產出的 code 要 review 哪些 production 風險？

掃描方式：

- 每題綁一條 production case。
- 每題練 60-90 秒，不追求教科書完整定義。
- 若答不出，再補 `19-interview-coaching-question-bank.md` 對應主題，不新開泛用題庫。

### Flow 聊天版 / 面試人話版

目前結論：

- `flow.md` 是研究報告與 evidence，不是面試聊天稿。
- `career-interview.md` 已有面試素材，但仍可能偏整理稿。
- `19-interview-coaching-question-bank.md` 的 `A ~ N` 題庫已封版；除非有實際 JD、面試回饋或明顯錯誤，不再新增分類或擴題。
- 三個可口說的專案故事稿已完成並寫入 `19`：`Legacy Takeover / Troubleshooting Story`、`Wallet / Bet-Settle / MQ Story`、`Provider Integration Story`。
- 不建議新增每條 flow 的新檔，也不建議繼續擴張題庫；接下來改成練三個故事稿，每個練 `30 秒版 / 90 秒版 / 3 分鐘版`。

建議格式：

```text
30 秒版
90 秒版
3 分鐘版
面試官追問時怎麼接
不要這樣講
```

優先練習順序：

1. Legacy Takeover / Troubleshooting Story：legacy reconstruction、production troubleshooting、flow reconstruction、git history / log / DB / MQ 排查經驗。
2. Wallet / Bet-Settle / MQ Story：`slot-bet-settle-rollback` / `third-party-transfer-in-out` / MQ projection 經驗。
3. Provider Integration Story：`payment-provider-callback` / `payment-order-provider-request` / provider connector 經驗。

判斷方式：

- 若 Nick 覺得故事稿口吻可用，先直接練口說；只有當它被升級成正式 casebook 入口時，再回填 `04-interview-casebook.md` 或對應 flow 的 `career-interview.md`。
- 若覺得太長，先只保留 `30 秒版 + 90 秒版 + 不要這樣講`。

閱讀順序：

1. 先讀 `19` 最終敘事總結，確認市場定位。
2. 再讀 `08` A 版與 `05` claim / boundary，知道履歷自傳怎麼對外呈現。
3. 再讀三個 Story：先 30 秒版，再 90 秒版，最後看 3 分鐘版。
4. 再讀 Flow：先主力 7 條，再補到 10 條，最後依 JD 補到 12 條。
5. 再讀 30 題核心題標題與草稿。
6. A-N 題庫只作補洞，不從頭刷。
7. 最後才 QA，一次 3-5 題，依回答缺口回填。

最小可 QA 門檻：`19` 最終敘事總結、`08` A 版、三個 Story 的 30 秒與 90 秒、主力 7 條 Flow 摘要、30 題核心標題。

### Git Log / Diff 風險重建

目前結論：

- KB 已有 `git log` / path-specific history / important diff 的 evidence 規則。
- 很多 flow 的 `materials/evidence.md` 已實際記錄 path-specific history、代表 commit 與重要 diff。
- 目前較缺的是把這件事整理成面試可講的能力，而不只是 claim evidence。

建議面試能力名稱：

```text
Git History Debugging / Risk Reconstruction
```

可講能力：

- 用 `git log --all --author` 找 Nick / `10gt12nc` direct evidence。
- 用 path-specific history 看某段 code 當初為何修改。
- 用 important diff 判斷當時是在修 bug、補 idempotency、處理 timeout，還是修資料一致性。
- 區分 Nick direct evidence、主管 / team context、current behavior context。
- 避免把他人 commit 說成自己的成果。

建議人話版：

```text
我接手既有系統時，不會只看目前程式碼。我會一起看 git history 和重要 diff，確認這段邏輯當初為什麼改、是修 bug、補 idempotency、處理 provider timeout，還是修資料一致性問題。這樣比較能避免只看 current code 卻誤判風險。
```

若要正式回填，優先補到 `04-interview-casebook.md` 或 `19-interview-coaching-question-bank.md`；不建議為此新增大量新文件。

### 12 條 Flow 閱讀組

主力 1-7：投遞前優先讀熟。

1. `payment-order-provider-request`
2. `payment-provider-callback`
3. `third-party-transfer-in-out`
4. `slot-bet-settle-rollback`
5. `transfer-wallet-money-in-out`
6. `proxy-user-data-report-projection`
7. `daily-game-data-summary`

補到 8-10：Senior / Platform 追問加強。

8. `bet-record-sharding-schema-route`
9. `db-partition-job-report-routing`
10. `gameserver-phased-rollout`

遊戲 / slot / provider JD 加讀 11-12：

11. `fixed-multi-bet-currency-math-core-compatibility`
12. `rtp-reel-strip-simulation-validation`

糖蛙 JD 優先順序：

```text
1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 8 -> 11 -> 12
```

### 近期 JD 與投遞規劃

已建立客製包：

- `applications/tangfrog-backend-team-lead-java-2026-06-18/`
- `applications/tangfrog-senior-java-backend-2026-06-18/`
- `applications/weida-senior-java-backend-2026-05-26/`

目前優先順序：

1. 糖蛙 Backend Team Lead：測上限，定位 `Senior Java Backend / Hands-on Backend Tech Lead Candidate`，不誇大正式管理 3-8 人。
2. 微達 Senior Java Backend：通用 Senior / Platform Backend market check。
3. 糖蛙 Senior Java Backend：偏 Senior IC / Game Backend，薪資上限 `100,000` 偏保守，可作保底 / market check。

重掃時確認：

- 是否已投遞。
- 是否有 HR 回覆。
- 是否需要針對某份 JD 進入口說練習。
- 若沒有回覆，不改主線，持續投遞 + 練 3 條主力 case。

### 建議執行順序

1. 先看是否有面試邀約 / HR 回覆。
2. 若有明確面試：讀對應 `applications/*/interview-qa.md`。
3. 若沒有面試：做基本功 12 題中的前 3 題。
4. 補一條 flow 的聊天版，優先 `slot-bet-settle-rollback` 或 payment callback。
5. 視 Nick 狀態決定要不要把 `Git History Debugging / Risk Reconstruction` 回填到 `04` 或 `19`。

### 收口標準

這份重掃清單完成，不代表所有 flow 完成。只要達到以下狀態即可收口：

- Nick 知道今天要讀哪 1-2 條 flow。
- Nick 知道今天要補哪 2-3 題基本功。
- Nick 不再把「基本功忘記」解讀成要重學全部 Java。
- Nick 能用一句話說明 `git log` 如何支撐風險重建。
- 若要改 KB，只改最小必要檔案，不開大規模重構。

## 已完成

- 2026-06-26：已將候選第二條週排程收斂為 `company-system-deep-dive/`，包含啟用前資料夾、`README.md`、`curriculum.md`、`project-value-map.md`、`project-index.md`、`topic-list.md`、`structure.md`、`deep-dive-template.md`、`manual-deep-dive-prompt.md`、`deep-dive-log.md` 與五個 project 子資料夾。定位不是 summary existing KB，也不是只做 discovery，而是用公司真實系統當教材，先按每個 project 的高 / 中 / 低學習價值選題，再從 code、git history、DB / SQL、config、architecture 與既有系統學 System Context、Runtime Flow、Code Reading、Data Flow、Engineering Thinking、Technology Comparison、Redesign、Interview Value 與 0-3 個 New Findings。此線只作候選 automation 支撐，尚未設定排程；不全掃公司 repo、不記公司機密、不預設改履歷 / 自傳 / Story / Flow，也不把公司 code 分析直接變成履歷 claim。
- 2026-06-26：第二排程相關討論已再收斂：先暫停推進，不設定 automation。若未來重啟，方向不應是 project-first 的完整 repo inventory，而應是 `System Capability Deep Dive`：topic-first、company-code-second、transferable-pattern third。`project-value-map.md` 只保留為案例池 / learning value map，不是完整 inventory、不是必讀清單、不是履歷 claim map、不是待辦 backlog。
- 2026-06-26：第二排程後續決策已補齊：暫不把 company case lens 加進 `Backend Weekly Learning`，避免 weekly learning 變成公司 repo 深掃或新壓力；`company-system-deep-dive/` 不刪、不移到 archive，只保留在原位置作 paused reference。當前分工是 `Backend Weekly Learning = active`、`company-system-deep-dive = paused reference`、`第二排程 = 不做`。
- 2026-06-26：已補上「懂很多 vs 能扛系統」校正到 `22-career-industry-kb-evolution-plan.md` 與 `16-career-skill-matrix.md`。結論：Senior Backend / Platform Backend 的市場訊號不是背熟所有公司 code、私下把單體改 Spring Cloud 或本地跑完整微服務，而是能扛 3 條代表性 production flow：Provider Integration、Wallet / Bet-Settle / MQ、Legacy Takeover / Troubleshooting。架構 / microservice / deployment lab 可做，但只能作 10% 加分練習，不得取代 `08 A 版定位`、三個 Story、Flow 六點、30 題 QA 與市場驗證。
- 2026-06-24：已更新 `backend-weekly-learning` automation 與 `22-career-industry-kb-evolution-plan.md` 的 canonical prompt，新增 `Also paste the full weekly learning packet in the chat response, not only a summary.`。後續每週排程除了維護 `backend-learning-log.md` / `backend-weekly-plan.md` 外，也應在聊天視窗直接貼完整 weekly learning packet，方便 Nick 當場閱讀與追問；有價值的追問仍只沉澱到 learning log / KB 建議，不自動改履歷、自傳、story、flow 或正式面試材料。
- 2026-06-24：已把面試準備閱讀順序在 `19-interview-coaching-question-bank.md` 去重、整併並重排成正式入口。現在以 `定位 -> 履歷自傳 -> 三個 Story -> 12 條 Flow -> 30 題核心 -> A-N 題庫補洞 -> QA -> 投遞 / 面試回饋` 為唯一大順序，並補齊最小版 / 完整版讀法、入口檔案、主力 7 條 Flow、補到 10 / 12 條 Flow、A-N 補洞順序、QA 開始條件、2026/07-2027/05 時間節奏與市場旺季區分。這是閱讀策略收口，不代表新增題庫或新增 flow backlog。
- 2026-06-24：已補強 Backend weekly template 與 automation prompt 邊界：weekly automation 不是只產 6 點短 checkpoint，而是依 `backend-weekly-template.md` 產一週一主題 learning packet；每週仍保持小而可行動，但要包含最多 3 篇高品質來源、Beginner-to-Senior 解釋、1 個小型 code / pseudo-code 範例、1 個簡單 Mermaid 架構 / flow 圖、Senior 面試題與 30 分鐘任務。仍不得改履歷 / 自傳 / story / 正式 flow / `04 / 05 / 08 / 17 / 19`，除非 Nick 明確要求。
- 2026-06-24：已補上 `A / B / C 時間分層` 到 `22-career-industry-kb-evolution-plan.md` 與 `backend-weekly-plan.md`。2026 下半年比例固定為：A 級 80% 放 Senior Backend、面試、Production、Incident、System Design；B 級 15% 放 Platform Backend、Observability、AI 協作；C 級 5% 放 Lead / Manager / Business / GM 能力。Hiring、Budget、P&L、Org Design、Culture、Business Strategy 可收藏與觀察，但不得搬成本月待辦，也不得搶走 Transaction、MQ、DB、Incident、故事與 Flow 的時間。
- 2026-06-24：已補上 `學習型能力 vs 責任型能力` 的比例規則到 `22-career-industry-kb-evolution-plan.md`，並同步 `backend-weekly-plan.md` 防過度準備規則。結論：Java / Spring / DB / Redis / MQ / Security / Observability / K8s 基礎可以靠學習補到一定程度；Ownership、Decision Making、Incident Handling、Risk Communication、Business Thinking 需要真實責任，近期只能先建立 vocabulary 與回答框架，主力仍是把現有經驗整理成 Story / Flow / Incident / Decision 並市場驗證。
- 2026-06-24：已把「30 年能力主幹」收斂進 `22-career-industry-kb-evolution-plan.md`，並同步 `backend-weekly-plan.md`、`README.md`。結論：未來不要一直擴能力樹，而是反覆深化 12 條主幹：Backend Core、Database / Cache / MQ、Transaction / Consistency、Production Flow、Incident / Troubleshooting、Observability / Reliability、Distributed System、Architecture / Migration、Security、AI-assisted Engineering、Communication / Ownership / Decision Making、Business Thinking / Personal Stability。近期仍以 Senior Java Backend / Platform Backend 為主；Business Thinking、Personal Finance、Health 是長期穩定性，不混入 backend weekly 主線。
- 2026-06-23：已確認 Backend weekly automation 的裝置分工：公司電腦才是工作學習與 KB 維護主機，家裡電腦原則上不跑工作學習 automation；`backend-weekly-learning` 的實際啟用狀態以該台電腦的 Codex app / `~/.codex/automations/.../automation.toml` 為準。2026-06-24 Nick 確認目前這台是公司電腦，因此本機 `backend-weekly-learning` 可保持 ACTIVE，目前設定每週一 09:00 執行；canonical prompt 與限制已記錄在 `22-career-industry-kb-evolution-plan.md`。預設只維護 `backend-learning-log.md` / `backend-weekly-plan.md`，可 commit，不 push，且不得改履歷 / 自傳 / 故事稿 / 正式 flow，除非 Nick 明確要求。
- 2026-06-23：已建立 Backend weekly automation 的 KB 支撐檔：`backend-weekly-plan.md`、`backend-learning-log.md`、`backend-weekly-template.md`，並產出 Week 01 `Spring Transaction` 內容。2026-06-24 已調整為 `48 週核心循環 + 實戰輸出補強池`：每週固定 5 件事，涵蓋 Senior Backend Core、Platform / Distributed、Incident / Architecture / Leadership；Troubleshooting Playbook、Ownership / Decision、Technical Communication / Risk Communication / Decision Making / Ownership 作為 System Owner 補強，不另開大型主線；AI-Assisted Engineering 固定只佔 5%，作為 Codex / context engineering / AI risk review 槓桿，不取代 Backend 本體；Security、K8s / DevOps、Architecture、Communication / Leadership 作補足池，不排進主線。此線只作每週輕量 learning packet，不處理日文、不建立巨大 backlog、不改履歷 / 自傳 / 三個故事稿；每週最多 1 個主題、1 個 30 分鐘任務，並先做重複內容檢查。
- 2026-06-23：已吸收外部 GPT 對 `nick-vault` 的 review 建議，補強「可投遞成果優先」與 v2 延後規則。結論：目前 `nick-vault` 維持 v1 `Job Search Vault`，不新增 `career/`、`incident/`、`decision-log/`、`industry-kb` 或 skill library；automation 只能作每週篩選器 / 提醒器，不是主線學習系統，也不自動改 KB。新增大量待辦、候選 flow、知識整理、system design、skill、automation 或新目錄前，必須先判斷是否直接提升面試勝率、履歷品質或薪資談判能力；若否，列為可選加強或暫不建議。
- 2026-06-23：已建立 `senior-owner-playbook/22-career-industry-kb-evolution-plan.md` 草案，先記錄 Codex automation 每週 Backend / 面試 / 日語輕量週報、`nick-vault` 未來升級成職涯證據庫 / 工作復盤 / 產業 KB / skill source 的規劃、安全線與明天決策問題。此檔只是候選規劃，不代表已建立 automation、不代表新增 `career/` 目錄，也不自動把新公司工作內容寫進 KB；若後續正式啟用 automation 或新增長期目錄，需 Nick 再確認。
- 2026-06-23：已完成第一輪 `market calibration` 投遞準備。檢查 `05 / 08 / 17 / 06` 後，結論是 `08` 的 A 版 104 欄位可直接作第一輪通用投遞稿；本輪不改正式履歷主 claim、不新增大型 KB、不重掃 flow。已在 `17-salary-negotiation.md` 補「第一輪 Market Calibration 投遞策略」，整理 5 類第一批適合投的職缺：Senior Java Backend / 高交易後端、Platform Backend / B2B 內部平台、Payment / Wallet / Provider Gateway Backend、Game Backend / Slot / Provider Backend、Hands-on Backend Tech Lead / Senior IC Plus；並同步 `08` 標明第一輪只做最小客製，投後依市場回饋補洞。
- 2026-06-23：已完成 `nbt / personal reference boundary` 一致性檢查。重讀 `projects/usproject/nbt-overview.md`、`projects/usproject/README.md`、`projects/source-repo-inventory.md`、`projects/README.md`、`projects/CONVENTIONS.md`、`00 / 09 / AGENTS` 後，未發現會把 `nbt` 誤升級成已上線 production claim 的矛盾；只補齊 `test001_RenPy` 與 `/Users/nick/Git/nick/*` personal reference 規則到 `AGENTS.md`、`00-operating-rules.md`、`09-ai-prompt-manual.md`。本輪不更新 `05 / 08 / 04 / 17`，也不新增正式履歷 claim。
- 2026-06-22：已完成 `nick-vault KB 最小防誤判補丁`。新增 `projects/usproject/nbt-overview.md`，並同步 `projects/usproject/README.md`、`projects/source-repo-inventory.md`、`projects/README.md`、`projects/CONVENTIONS.md`：`nbt` 只定位為 active migration / AI-assisted reconstruction / local validation 素材，目前未標成已上線 production flow，不新增正式履歷 claim；`/Users/nick/Git/nick/*` 只作 personal reference / AI workflow / side project 方法論參考，不當公司專案 evidence。本輪不做完整 Step 1-5、不改 `05 / 08 / 04 / 17`。
- 2026-06-22：已依 Nick 指示把原分散待辦資料夾內的星期一重掃候選清單整併回本檔，避免待辦分散；後續不再另設待辦資料夾作長期資料位置。
- 2026-06-20：已建立星期一重掃候選清單，內容已保留在本檔「候選待辦：2026-06-22 星期一重掃清單」。此清單不是流水帳，也不是自動開工授權；執行前需先看 HR / 面試回覆，再決定是否進入口說練習、基本功補洞或最小 KB 回填。
- 2026-06-19：已建立 `applications/tangfrog-senior-java-backend-2026-06-18/`，針對糖蛙線上娛樂股份有限公司「資深JAVA後端工程師」產出 JD fit analysis、104 客製工作經驗 / 專長 / 自傳 / 自我推薦、HR / 技術 / AI / 維運監控問答與 JD-specific 基本功最小複習清單。此包是 JD-specific，不覆蓋通用 `05 / 08 / 17 / 04`；投遞定位為 `Senior Java Backend / Game Backend Engineer`，主打遊戲 / 博弈 provider、wallet / bet-settle、RabbitMQ / Redis / MySQL、legacy takeover、技術文件與 AI-assisted workflow；不得套用 Team Lead 管理說法，也不得誇大成完整架構師、完整 DevOps / SRE、完整遊戲平台 owner 或完整 payment / wallet / MQ platform owner。此職缺薪資上限 `100,000` 偏保守，優先順序低於同公司 Backend Team Lead 缺，可作 market check / 保底機會。
- 2026-06-19：已建立 `applications/tangfrog-backend-team-lead-java-2026-06-18/`，針對糖蛙線上娛樂股份有限公司「JAVA後端組長 / Backend Team Lead (Java)」產出 JD fit analysis、104 客製工作經驗 / 專長 / 自傳 / 自我推薦、HR / Team Lead / AI / 技術問答與 JD-specific 基本功最小複習清單。此包是 JD-specific，不覆蓋通用 `05 / 08 / 17 / 04`；投遞定位為 `Senior Java Backend / Hands-on Backend Tech Lead Candidate`，主打遊戲 / 博弈 provider、wallet / bet-settle、MQ / batch、legacy takeover、AI-assisted workflow 與 Code Review / 技術帶人潛力；不得誇大成已正式管理 3-8 人 2 年、完整架構師、完整 DevOps / K8s owner 或完整金流 / wallet / MQ platform owner。
- 2026-06-05：已確認 `/Users/nick/Git` 底下不再保留 `ai-notes/`。先前 `/Users/nick/Git/antplay/ai-notes` 只是本地 AI session notes / logs，已依 Nick 指示刪除；`nick-vault` 規則維持不新增 `ai-notes/`、不保留流水帳、不把外部 workspace notes 搬進 `projects/`。
- 2026-06-05：已修正 `teaching-notes` 策略為「先不補 / 不批量補」。目前 49 條既有 flow 不因沒有 `materials/teaching-notes.md` 就列為缺口；只有 Nick 讀某條 flow 卡住、面試回饋暴露基本功缺口，或 Nick 明確要求補該 flow 教學時，才建立 teaching notes。投遞前主線仍是讀熟主力 flows、履歷 case、口說與 JD-specific 補洞。
- 2026-06-05：已完成 `讀kb / 下一步` 入口一致性深掃與補強。`AGENTS.md`、`senior-owner-playbook/README.md`、`projects/README.md` 已同步標明：這是新增省字入口，不覆蓋既有 `project / flow / Step N` 指令；明確 Step 仍照原主線做，只有 Nick 只貼 `讀kb / 下一步` 時，AI 才自動判斷 Flow Track / Career Track / Resume / Domain Map / Decision Notes / 口說材料或自由提問狀態。Teaching Notes 不列預設下一步，只在卡住、面試回饋或明確要求時補。
- 2026-06-05：已補 `讀kb / 下一步` 自動化規則。Nick 之後可以只貼兩行，AI 必須自動重讀 KB、掃 `projects/` 的 Flow Track / Career Track / Domain Map / Decision Notes / 口說材料狀態，校正已完成與未完成，再只推薦一件最值得做的下一步；不得自動開工多個 flow、不得把候選缺口包裝成必做 backlog、不得讓 teaching notes / system map / side project 插隊 active flow。
- 2026-06-05：已補 `flow-first teaching` 規則：flow 本身必須 code-backed 深掃；SQL / Spring / Kafka / Redis / transaction 等教學只能從該 flow 的實際 code path 長出來，每條 flow 最多補 3-5 個必要技術點。新增 `materials/teaching-notes.md` 定位，專門放本 flow 的最小基本功補洞；共用技術原則回收進 `14-technical-decision-hard-skills.md`，避免每條 flow 變成教科書或泛用題庫。2026-06-05 已再修正為先不補 / 不批量補；既有 flow 沒有 teaching notes 不算缺口。
- 2026-06-01：已深掃 `/Users/nick/Git/antplay/math-workspace` 新流程作參考。結論是部分跟進，不整套搬：可吸收入口索引、狀態儀表板、source / KB / git log 分層、non-goals、scope、success criteria、handoff / reviewer gate、先 baseline 再變更、不留流水帳；不採用 `math-workspace` 的多 agent 目錄、per-project `CLAUDE.md`、`docs/projects/{module}/kb/catalog/` 長期開發結構、GDD / RTP / JAR / optimizer / deploy / child repo 規則。已回填 `00-operating-rules.md` 與 `09-ai-prompt-manual.md`，之後參考外部 workspace 新流程時一律先做 `可採 / 不採 / 轉譯` 判斷；若只是評估先不改規格，若 Nick 要維護 KB 才更新最小必要規則檔，避免又把 `nick-vault` 變成開發 workspace。
- 2026-06-05：已完成 `0 to 1 Project Strategy Report`，位置是 `senior-owner-playbook/21-zero-to-one-project-strategy-report.md`。本輪重讀 `nick-vault`、AntPlay、iwin、UGSoft、DevOps 的技術棧、package descriptor、代表 code path、workspace flow 與近期 git log，結論是 side project 仍不列必做；若未來要做，先做 `containerized-game-transaction-lab` 的 Interview MVP blueprint，而不是完整商業遊戲 / K8s / 全微服務。2026-06-05 已再補 ROI 判斷：目前實作 side project 的 CP 值偏低，最高 CP 值是讀熟主力 flows、講熟、能工作、能面試；本地 k3s、既有 DB、既有專案整合只能作未來技術 lab 素材，不是當前主線。此報告只作 system design / side project strategy 素材，不新增履歷 claim，也不代表已授權開工。
- 2026-06-01：已建立 `applications/weida-senior-java-backend-2026-05-26/`，針對微達軟體「資深 Java 後端工程師【擴編】」產出 JD fit analysis、客製履歷 / 自傳 / 自我推薦、HR / 薪資 / 文化 / 技術問答，並補上 JD-specific 20% 基本功最小複習清單。此包是 JD-specific，不覆蓋通用 `05 / 08 / 17 / 04`；若後續真的投遞或面試，再依回饋回填通用素材或補洞。
- 已將外層舊整理資料歸入 `archive/` 參考區，之後可由 Nick 人工審查是否刪除。
- 2026-05-25：Nick 已確認 `archive/` 不需要保留，已清空內容，只保留 `.gitkeep` 佔位；後續 KB 不再把 archive 當必要來源。
- 從待刪區重新建立 `senior-owner-playbook/`。
- 建立新入口、維護規則、flow inventory、學習路線、flow 模板、面試案例與唯一履歷自傳。
- 已補 `08-application-autobiography-zh.md` 的 104 欄位版：工作經驗、專長、自傳、自我推薦；`05` 維持母稿 / 證據池定位，`08` 維持投遞可貼定位。
- 已完成 2026-05-25 `104 投遞欄位檢查`：`08-application-autobiography-zh.md` 的工作經驗、專長、自傳、自我推薦已標示為通用 Senior Java Backend / Platform Backend 可貼版；沒有加入未證實主導、完整 owner、量化改善或事故修復 claim。
- 已完成 2026-05-28 `Resume / 104 final check`：`05 / 08 / 04 / 17 / 11` 已對齊 `antplay-slot-admin-api` contribution refresh 與 iwin / AntPlay / UGSoft system maps；system maps 僅作架構與面試視角，不新增正式履歷 claim。`08` A 版仍是公開主版本，正式外投前替換內部產品 / repo / provider 名稱即可。
- 已新增 `17-salary-negotiation.md`：維護 104 / 面試用薪資談判定位、期望待遇區間、底線、口頭說法與不可誇大邊界；正式談 offer 前仍需重查當下行情。
- 已依 2026-05-20 最新 104 職缺快照與舊履歷補強後定位，重新上修 `17-salary-negotiation.md`：104 建議填月薪 115,000-135,000 / 年薪 160-190 萬，面試主談年薪 160-180 萬，底線月薪 100,000。
- 已補讀 Nick 舊 104 PDF 履歷，吸收可用素材：兩套缺乏交接文件平台的 legacy takeover / system reconstruction、早期高頻線上問題支援、ActiveMQ + Redis + Quartz 內部分享；負面主管 / 流程描述與未驗證強 claim 不放正式投遞稿。
- 已補 `*-workspace` 的 AI-assisted engineering workflow 定位；2026-06-03 已重查 `iwin-workspace` payment KB catalog，並對齊 `projects/iwin/payment` Top 5 flows：workspace 補的是讀 KB / 規格 / 相似商戶、feature branch、本地 / SIT / simulator、payment + third-party / simulator + app_bi / center / DB / log / balance 檢查與 KB 回填，不取代 flow 深掃，也不新增完整金流 owner claim。2026-06-03 也已重查 `math-workspace` 最新 KB，並對齊 `projects/antplay/star-math` 五條 representative flows：workspace 補的是 GDD / 相似底包 / 固定盤面 / result contract / RTP 長測 / optimizer / final valid / KB 回填驗證鏈；`sdt-lab-math` 只作 lab / testing reference，不自動升級成所有 math flow 的預設規則。這些放在工程方法 / 自我推薦 / 談薪支撐，不作 standalone production 主成果。
- 已補求職現實判斷：`進取` 是特定對口職缺的談判上限，不是保證；Nick 可以投 10 萬以上職缺，但要靠 3 條以上 production flow case 證明。近期優先把 payment callback、third-party provider / wallet、batch / projection flow 練熟，並採邊待邊投節奏。
- 已補目前 `80,000` 薪資判讀：這是目前公司 / 組織 / 職級 / 薪資帶定價，不等於外部市場最終定價；是否能脫離 8 萬錨點，要靠投遞與面試驗證，若拿到 `100,000-120,000` offer 即可證明主要是組織定價問題。
- 已補對標 Senior Java Backend / Platform Backend / System Owner 的 8 週軟硬實力養成計劃：每週以 production flow 為主題，同時補硬實力、軟實力、3 分鐘 case、追問與 claim boundary。
- 已建立全 vault 共用自動維護規則：每次 project / flow / Step 任務前自動重讀 KB、既有文件與相關 code 最新狀態，完成後自動判斷是否維護 README、Step 文件、evidence、claim boundary、todo 或共用 KB。
- 已補上舊文件自動排查規則：AI 必須自己檢查既有 Step / flow 是否過舊、缺 evidence 或不符合目前 KB；上一個 Step 不乾淨時，優先重整舊文件，不直接跳下一步。
- 已補上技術硬底子與決策比較模組：每條高價值 flow 可新增 `decision-notes.md`，整理技術選型、差異比較、trade-off、owner decision 與面試追問。
- 已補上大專案 / 子專案地圖規則：地圖只用來定位 repo 與 flow，不取代 production flow。
- 已補上初階到資深的軟硬實力矩陣：作為定期檢查表，不作為新的發散學習主線。
- 已重整 `app_bi` Step 1 / Step 2，並把已完成 flow 收斂成新版 `flow.md + career-interview.md + materials/` 結構。
- 已修正 Step 主線規則：AI 不可自行把「下游定位 / 補 evidence / 補 decision-notes / 架構圖」升級成新下一步；Step 3 乾淨且未牽涉履歷 / claim 風險時，預設進 Step 4。
- 已完成 `app_bi point-control-admin-operation Step 4`，轉成保守面試 case，未更新履歷。
- 已完成 `app_bi point-control-admin-operation Step 5` 的「不更新履歷 / 自傳」判定；這不是履歷深掃，也不是 Nick 開發痕跡確認。未補 Nick 本人 MR / ticket / commit / production issue 前，只保留為面試分析 case，不放入正式履歷。
- 已完成 `app_bi admin-config-redis-sync Step 1-5`，已遷移為新版結構；目前只作後台設定同步 Redis 的分析與面試素材，不更新履歷。
- 已完成 `app_bi point-control-admin-operation Step 1-5`，已遷移為新版結構；目前只作後台控制操作的分析與面試素材，不更新履歷。
- 已補上 flow 可讀性規則：每份 `flow.md` 前半要先有白話導讀、Code 分層、最小架構圖、正常流程圖與逐步說明，後半才進 Senior / Owner 分析。
- 已補上 push approval 防錯規則：需要 push 時，AI 必須直接觸發 `git push` approval 視窗，不再只用文字回覆本地已提交、等待 Nick 另外要求推送。
- 已新增 `projects/source-repo-inventory.md`，記錄本機來源 repo 索引；這只是導航，不是 code evidence 或履歷 claim。
- 已重整 `01-senior-owner-flow-inventory.md` 為 flow dashboard，避免下一步亂跳；完整分析仍放各 flow 的 `flow.md`。
- 已完成 `app_bi daily-game-record-summary Step 3`，確認 app_bi 查詢端與 game_job producer；目前只作報表 projection / 批次一致性分析素材，不更新履歷。
- 已完成 `app_bi daily-game-record-summary Step 4`，轉成保守面試 case；目前仍不更新履歷 / 自傳。
- 已完成 `app_bi daily-game-record-summary Step 5` 的「不更新履歷 / 自傳」判定；沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認前，只保留為面試分析素材。
- 已完成 `app_bi game-round-record-query Step 3`，確認 app_bi 查詢端、每日 `log_reel` 分表與 iwin_gameserver log writer 線索；目前只作玩家申訴 / troubleshooting 分析素材，不更新履歷。
- 已完成 `app_bi game-round-record-query Step 4`，轉成保守面試 case；目前仍不更新履歷 / 自傳。
- 已完成 `app_bi game-round-record-query Step 5` 的「不更新履歷 / 自傳」判定；app_bi 查詢端未見 Nick author，iwin_gameserver writer 的 Nick commit 線索應另開後端 flow 深挖。
- 已依 2026-05-15 KB 深度檢查 `app_bi`，補齊 project-level `architecture-map.md` 與 `career-interview.md`，並把 app_bi 本地落後 `origin/main` 4 commit 的 source repo 狀態寫入 Step / evidence；正式履歷仍不更新。
- 已完成 `game_job Step 1`，建立 `projects/iwin/game_job/README.md` 與 `step1-candidate-flows.md`；目前只作 Java batch / BI projection / third-party record backup 的候選 flow 盤點，不更新履歷。
- 已完成 `third_games_api gsc-transfer-bet-settle-rollback Step 4`，轉成 GSC seamless wallet callback 的保守面試 case；目前仍不更新履歷 / 自傳。
- 已完成 `third_games_api antplay-bet-settle-rollback Step 4`，建立 Antplay 舊版 bet / settle / rollback 三段式正式面試 case；目前只作 code-backed 面試素材，不更新正式履歷 / 自傳。
- 已完成 `third_games_api antplay-bet-settle-rollback Step 5`，單條 flow claim gate 結論維持 interview-only；不新增 `third_games_api` standalone 正式履歷 / 自傳；後續同 project 第四候選 `gsc-seamless-withdraw-deposit-cancel Step 5` 也已完成。
- 已完成 `third_games_api gsc-seamless-withdraw-deposit-cancel Step 3`，建立 GSC split withdraw / deposit / rollback / cancel 主學習包；確認 split endpoint、Mongo step 1 / 2 / 3、gameserver `GSC_BET / GSC_SETTLE / GSC_REFUND / GSC_OTHER` 與 failure window。production 是否仍走 split endpoint 待 Step 4 / spec 確認；不更新正式履歷 / 自傳。
- 已完成 `third_games_api gsc-seamless-withdraw-deposit-cancel Step 4`，轉成 GSC split endpoint 正式面試 case；可講 split state transition、adapter Mongo evidence vs gameserver wallet boundary、gameserver success but Mongo fail failure window、cancel / rollback step 3 語意與 `/transfer` production 主線待確認。後續 Step 5 claim gate 已完成，仍不更新正式履歷 / 自傳。
- 已完成 `third_games_api gsc-seamless-withdraw-deposit-cancel Step 5`，claim gate 結論維持 interview-only；不新增 `third_games_api` standalone 正式履歷 / 自傳。`third_games_api` GSC split endpoint path 未掃到 Nick / `10gt12nc` direct production commit；下游 GSC direct evidence 歸屬 `iwin_gameserver`。
- 已完成 `iwin third_games_api contribution claim consolidation`；2026-05-20 rolling / scoped 收口。`third_games_api` 本 repo 只有局部測試 / merge 線索，不新增 standalone 正式履歷主成果；下游 `iwin_gameserver` Antplay / GSC / PG direct evidence 要在 `iwin_gameserver` consolidation 正確歸位。
- 已完成 `game_api coupon-redeem-credit-grant Step 5`，確認 `10gt12nc` 有 game_api / iwin_gameserver coupon path-specific commits；可作 game_api project contribution consolidation evidence，不寫主導完整 coupon / reward owner。
- 已完成 `game_job daily-game-data-summary Step 5`，確認 `10gt12nc` 有 daily summary / 時區 / 留存 / 備份相關 path-specific commits；可作 game_job project contribution consolidation evidence，不寫完整 BI pipeline owner。
- 已完成 `game_job third-party-record-mongo-backup Step 5`，確認 `10gt12nc` 有 GSC backup 分批查詢與 batch size 調整 direct commits；可作 game_job project contribution consolidation evidence，不寫完整第三方紀錄備份 owner。
- 已完成 `game_job coin-flow-batch-projection Step 4`，建立金幣流水 / 玩家行為 projection 主學習包並轉成正式面試 case；目前只作 code-backed 面試分析素材，不更新正式履歷 / 自傳。
- 已完成 `game_job coin-flow-batch-projection Step 5`，確認目前未見足夠 Nick / `10gt12nc` direct path evidence；正式履歷 / 自傳不更新，只保留為 code-backed 面試素材。
- 已完成 `game_job online-payment-data-cleaning Step 3`，建立充值 / 提現資料清洗與每日經濟資料主學習包；目前只作 code-backed 面試分析素材，不更新正式履歷 / 自傳。
- 已完成 `game_job online-payment-data-cleaning Step 4`，轉成 payment reporting projection 的正式面試 case；目前仍只作 code-backed 面試分析素材，不更新正式履歷 / 自傳。
- 已完成 `game_job online-payment-data-cleaning Step 5`，判定不更新正式履歷 / 自傳；本 flow 保留為 payment reporting projection / replay-safe 的 code-backed 面試案例。
- 已完成 `game_job partition-table-creation Step 3`，建立每日 / 每月分表建立主學習包；目前只作 code-backed 面試分析素材，不更新正式履歷 / 自傳。
- 已完成 `game_job partition-table-creation Step 4`，轉成 table rollover / schema rollout reliability 的正式面試 case；目前仍只作 code-backed 面試素材，不更新正式履歷 / 自傳。
- 已完成 `game_job partition-table-creation Step 5`，判定不更新正式履歷 / 自傳；本 flow 保留為 table rollover reliability 的 code-backed 面試案例。
- 已完成 `payment payment-provider-callback Step 5`，判定不更新正式履歷 / 自傳；2026-05-26 code-first audit 後，本 flow 改定位為 payment provider callback 狀態風險 / compensation 的保守面試素材，不包裝成完整 money correctness / wallet / reconciliation。
- 已完成 `payment withdrawal-auto-review-refund Step 5`，判定不更新正式履歷 / 自傳；本 flow 保留為提款、自動審核 / 自動出款、失敗退款的一致性與補償面試素材。
- 已完成 `payment payment-order-provider-request Step 5`，完成 provider request claim gate；已確認 Nick 在 Pay4z / NaNapay / BFPAY / NimTestPay 等 provider request / query / callback 相關 code 有 path-specific commits，2026-05-20 consolidation 重新覆核後又補入 GoldenPay direct commits；正式履歷可用「參與」口徑，不寫主導完整金流。
- 已完成 `payment manual-order-review-repair Step 3`，建立人工審核 / 補單 / 訂單修復主學習包；目前只作面試素材，不更新正式履歷 / 自傳。
- 已完成 `payment manual-order-review-repair Step 4`，轉成可面試 case；目前仍只作面試素材，不更新正式履歷 / 自傳。
- 已完成 `payment manual-order-review-repair Step 5`，判定不更新正式履歷 / 自傳；人工審核 / 補單 / 修單只保留為面試分析素材。
- 已完成 `payment payment-channel-config-selection Step 3`，建立支付列表 / 商戶 / 玩家層級 / 提現設定選擇的 runtime config 主學習包；目前只作面試素材，不更新正式履歷 / 自傳。
- 已完成 `payment payment-channel-config-selection Step 4`，轉成可面試 case；目前仍只作面試素材，不更新正式履歷 / 自傳。
- 已完成 `payment payment-channel-config-selection Step 5`，判定不更新正式履歷 / 自傳；payment Top 5 代表 flow 已收斂，但不代表全 payment project 已完成。
- 已完成 `iwin payment contribution claim consolidation`；2026-05-20 已重新覆核並補入 GoldenPay direct evidence，2026-05-26 再做 code-first claim audit。Nick 本人確認加上 `10gt12nc` commits / branches / 重要 diff，可把 payment 升級為「部分真實開發過」，但 claim 只限 provider / 商戶對接、payment / withdraw order consistency、查單與人工補償邊界；不是 payment 全量 flow 完成，也不寫完整金流 / wallet / ledger / reconciliation owner。
- 已完成 `iwin_gameserver third-party-transfer-in-out Step 5`；原 Step 5 判定暫不更新正式履歷 / 自傳，2026-05-20 project-level consolidation 已用 Nick / `10gt12nc` direct commits 升級為可併入第三方 provider 投派整合保守 claim。
- 已完成 `iwin_gameserver center-http-deposit-withdraw Step 3`，建立 center_http 上分 / 下分主學習包；目前只作 code-backed 面試素材，不更新正式履歷 / 自傳。
- 全域下一步狀態：目前沒有預設 project flow 下一步；請以 senior-owner-playbook/01-senior-owner-flow-inventory.md 與 senior-owner-playbook/06-todo.md 為準。
- 已完成 `iwin_gameserver center-http-deposit-withdraw Step 5`，追完 source repo refs、path-specific history、重要 diff 與 blame；結論維持 code-backed interview-only，不更新正式履歷 / 自傳。後續同 project `game-spin-settlement-log-reel` 與 `bet-target-set-query` 也已完成到 Step 5。
- 已完成 `iwin_gameserver game-spin-settlement-log-reel Step 3`，選 `slots-game40-sgj` 作代表 game，追一般 slot spin 到 center wallet sync 與 `log_reel` 寫入；目前只作 code-backed 面試素材，不更新正式履歷 / 自傳。
- 已完成 `iwin_gameserver game-spin-settlement-log-reel Step 4`，轉成正式面試 case；不更新正式履歷 / 自傳。
- 已完成 `iwin_gameserver game-spin-settlement-log-reel Step 5`，一般 Game40 spin 維持 interview-only；provider log reel / 投派整合 direct evidence 回填既有 project-level claim，不新增一般 slot spin 履歷 bullet。
- 已完成 `k3s-deploy gameserver-phased-rollout Step 5`，claim gate 結論維持 interview-only；可作 rollout / rollback / observability 的保守面試 case，不更新正式履歷 / 自傳。
- 已完成 `game_api partner-deposit-withdraw-bill Step 5`，判定不更新正式履歷 / 自傳；本 flow 保留為 Partner API 上分 / 下分 / 查單的 code-backed 面試案例。
- 已補上待辦事項優先規則：當 Nick 要的是「待辦、缺口、KB 維護、優先順序」時，AI 先維護 todo / KB / inventory，只列候選下一步並等待 Nick 下 Step；不得把缺口自動開工成 flow Step。
- 已完成 `game_api agent-bonus-receive-transfer Step 3`，建立代理佣金領取 / 轉帳主學習包；目前只作 code-backed 面試分析素材，不更新正式履歷 / 自傳。後續 `game_api contribution claim consolidation` 已完成，仍維持本 flow 不進正式履歷。
- 已完成 `game_api agent-bonus-receive-transfer Step 4`，轉成代理佣金領取 / 轉帳的正式面試 case；目前仍只作 code-backed 面試分析素材，不更新正式履歷 / 自傳。
- 已完成 `game_api agent-bonus-receive-transfer Step 5`，判定不更新正式履歷 / 自傳；本 flow 保留為代理佣金領取 / 轉帳的 code-backed 面試案例。
- 已完成 `iwin game_job contribution claim consolidation`；2026-05-20 已重新覆核。Nick / `10gt12nc` 在 daily summary 與 GSC backup path 有 direct commits，可把 game_job 升級為「部分真實開發過」，但不寫完整 game_job / BI pipeline / retention owner。
- 已完成 `iwin app_bi contribution claim consolidation`；已依 rolling / scoped 規則重新確認為 negative 收口，結論是不放正式履歷主成果，只作後台入口 / BI / control plane 的 code-backed 面試分析素材。
- 已完成 `iwin game_api contribution claim consolidation`；2026-05-20 已重新覆核。Nick / `10gt12nc` 在 coupon redeem / grant 與 `iwin_gameserver` bet target handler 有 direct commits，可把 `game_api` 升級為「部分真實開發過」，但正式履歷只採 coupon 保守 claim；partner / agent bonus 只作 code-backed 面試素材。
- 已完成 `iwin bi_share contribution claim consolidation`；未掃到 Nick / `10gt12nc` direct production commits，結論是不放正式履歷主成果，只作 legacy Laravel / 分享 / 佣金 / BI 報表 / GM repair 的 code-backed 面試分析素材。
- 已完成 `iwin iwin_gameserver contribution claim consolidation`；2026-05-20 rolling / scoped 收口。Nick / `10gt12nc` 在 Antplay / GSC / PG 第三方 provider 投派整合、gameserver money job、`GamePlayer` log dispatch 與 log reel path 有 direct commits，可保守放「參與第三方遊戲 provider 投派整合與 gameserver 錢包 / 投注流水串接」；center-http 上分 / 下分仍是 code-backed 面試素材，不擴張成完整 gameserver / wallet owner。
- 已完成 `iwin iwin-workspace contribution claim consolidation`；2026-06-03 已重掃 source workspace payment KB catalog 與最新 refs。可支撐 cross-repo system reconstruction、知識庫治理與 payment workspace AI-assisted 開發 / 驗證閉環；對 payment Top 5 flows 的影響是補強開發 / 驗證檢查點，不新增 standalone 正式履歷主成果，不反向包裝成子 repo 業務開發或完整金流 owner。
- 已完成 `ugsoft ugsoft-admin-api contribution claim consolidation refresh`；2026-05-27 refreshed 收口。Nick / `10gt12nc` 在 login / JWT / RBAC、商戶 / provider 白名單、超級代理、報表查詢、風控監控、RabbitMQ request log / bet record、Game API / provider IP 白名單控制面與 Quartz / report job 有大量 direct commits；三條代表 flow 已 Step 5 並回填 project-level claim。可保守補入後台 API / control plane 與非同步資料處理經驗；不寫完整 UG 平台、完整 provider gateway、完整 wallet / money flow、完整 RabbitMQ architecture owner 或完整 access-control platform owner。
- 已完成 `ugsoft ugsoft-admin-api Step 1`；2026-05-27 已建立候選 flow 盤點，包含 `connect-bet-record-mq-ingestion`、`request-log-rabbitmq-admin-consumer`、`game-api-provider-white-ip-control-plane`、`daily-hourly-report-quartz-job`、`admin-auth-token-refresh-security`、`risk-monitor-alert-rabbitmq` 等。後續 Step 2、第一條 `connect-bet-record-mq-ingestion Step 5`、第二條 `request-log-rabbitmq-admin-consumer Step 5`、第三條 `game-api-provider-white-ip-control-plane Step 5` 與 project-level contribution refresh 已完成。
- 已完成 `ugsoft ugsoft-admin-api Step 2`；2026-05-27 已選本批代表 flows：`connect-bet-record-mq-ingestion`、`request-log-rabbitmq-admin-consumer`、`game-api-provider-white-ip-control-plane`。後續第一條 `connect-bet-record-mq-ingestion Step 5`、第二條 `request-log-rabbitmq-admin-consumer Step 5` 與第三條 `game-api-provider-white-ip-control-plane Step 5` 已完成；不直接跳其他 project，也不因 Step 2 更新 `05 / 08`。
- 已完成 `ugsoft ugsoft-admin-api connect-bet-record-mq-ingestion Step 3`；2026-05-27 已建立 BetRecord MQ consumer / duplicate check / quota update supporting flow learning package。Nick / `10gt12nc` direct evidence 覆蓋 BetRecord MQ 初版、consumer 調整、mapper query 與 currency default；`arnold` 的 quota monitoring、id generation、amount normalization 只作 latest behavior / 主管 context。Step 3 不直接更新 `05 / 08`；後續 Step 4 已完成。
- 已完成 `ugsoft ugsoft-admin-api connect-bet-record-mq-ingestion Step 4`；2026-05-27 已把 BetRecord MQ consumer / duplicate check / quota eventual consistency 轉成 30 秒、90 秒、3 分鐘、STAR、Senior / Lead 追問與反問面試官素材。Step 4 不直接更新 `05 / 08`；後續 Step 5 已完成。
- 已完成 `ugsoft ugsoft-admin-api connect-bet-record-mq-ingestion Step 5`；2026-05-27 已完成 claim gate。Nick / `10gt12nc` direct evidence 足以支撐 BetRecord MQ 初版、consumer 調整、mapper 查重與 currency default；`arnold` 的 quota monitoring、id 生成、amount normalization 只作 latest behavior / 主管 context。可回填 project-level RabbitMQ / BetRecord async evidence；不直接更新 `05 / 08`。後續第二條 `request-log-rabbitmq-admin-consumer Step 5` 已完成。
- 已完成 `ugsoft ugsoft-admin-api request-log-rabbitmq-admin-consumer Step 3`；2026-05-27 已建立 RequestLog RabbitMQ 非同步入庫 learning package。Nick / `10gt12nc` direct evidence 覆蓋 RequestLog RabbitMQ 非同步化、listener / mapper / processor；`arnold` 的 request-log key / query 修正只作 latest behavior / 主管 context。Step 3 不直接更新 `05 / 08`；後續 Step 4 已完成。
- 已完成 `ugsoft ugsoft-admin-api request-log-rabbitmq-admin-consumer Step 4`；2026-05-27 已把 RequestLog RabbitMQ 非同步入庫轉成 30 秒、90 秒、3 分鐘、STAR、Senior / Lead 追問與反問面試官素材。Step 4 不直接更新 `05 / 08`；後續 Step 5 已完成。
- 已完成 `ugsoft ugsoft-admin-api request-log-rabbitmq-admin-consumer Step 5`；2026-05-27 已完成 claim gate。Nick / `10gt12nc` direct evidence 足以支撐 RequestLog RabbitMQ 非同步化、listener / mapper / processor 調整；`arnold` 的 request-log key / query 修正只作 latest behavior / 主管 context。可回填 project-level RabbitMQ / request log async supporting evidence；不直接更新 `05 / 08 / 04 / 17`，不得寫完整 RabbitMQ platform、exactly-once、DLQ / outbox、provider connector 或 money correctness owner。後續第三條代表 flow `game-api-provider-white-ip-control-plane Step 5` 已完成。
- 已完成 `ugsoft ugsoft-admin-api game-api-provider-white-ip-control-plane Step 4`；2026-05-27 已把 Game API / provider white IP 後台控制面轉成 30 秒、90 秒、3 分鐘、STAR、Senior / Lead 追問與反問面試官素材。Nick / `10gt12nc` direct evidence 覆蓋 Game API white IP 後台、DB / Redis 同步與 provider white IP CRUD 初版；`arnold` 的 provider fanout reload / global scope refactor 只作 latest behavior / 主管 context，connector reload / runtime filter 只作下游 code-backed context。Step 4 不直接更新 `05 / 08`；後續 Step 5 已完成。
- 已完成 `ugsoft ugsoft-admin-api game-api-provider-white-ip-control-plane Step 5`；2026-05-27 已完成單條 flow claim gate。可保守說參與 UGSoft 後台 Game API / provider IP 白名單控制面開發維護，處理後台 CRUD、DB / Redis 同步、權限範圍與操作紀錄，並能分析後台設定如何影響 connector runtime access-control；不可寫成主導完整 provider gateway、完整 security platform、完整 Redis fanout reload owner 或 connector runtime owner。後續 `ugsoft-admin-api contribution claim consolidation refresh` 已完成。
- 已完成 `ugsoft ugsoft-connector-api contribution claim consolidation refresh`；2026-05-27 refreshed 收口。Nick / `10gt12nc` 在 AntPlay / DerPlay provider adapter、login / balance / transfer in-out / bet-settle / callback、request / bet record MQ、job sync、transfer wallet compensation、分表與 provider reliability 有 direct commits / code evidence；三條代表 flow 已 Step 5 並回填 project-level claim。可保守補入 provider connector / gateway 經驗；不寫完整 connector architecture owner、全部 provider owner、完整 wallet / ledger / reconciliation owner 或 exactly-once / outbox owner。
- 已完成 `ugsoft ugsoft-connector-api provider-callback-bet-settle-to-mq Step 5`；2026-05-27 已完成 claim gate，可回填 project-level provider connector / callback / MQ evidence；不直接更新 `05 / 08 / 04 / 17`，不寫完整 exactly-once / outbox / reconciliation owner。
- 已完成 `ugsoft ugsoft-connector-api request-bet-record-mq-sync Step 5`；2026-05-27 已完成 claim gate，可回填 project-level job-driven bet record sync / late data / MQ evidence；不直接更新 `05 / 08 / 04 / 17`，不寫完整 reconciliation / outbox / exactly-once owner。`ugsoft-connector-api` 本批三條代表 flows 已全部 Step 5，且 `contribution claim consolidation refresh` 已完成。
- 已完成 `ugsoft ugsoft-workspace contribution claim consolidation`；2026-05-20 rolling 收口。這是 workspace / docs / harness / runbook supporting evidence，可支撐 cross-repo system reconstruction、工程規範、local / deploy harness、migration / release 風險整理；不新增 standalone 正式履歷主成果，也不反向包裝成 service runtime code。
- 已完成 `antplay antplay-slot-admin-api contribution claim consolidation refresh`；2026-05-28 refreshed 收口。Nick / `10gt12nc` 在 admin / merchant auth、商戶 / Game API 白名單、超級代理、玩家 / 投注 / request log / 報表查詢、RTP / 暗池 / 活動風控監控、RabbitMQ request log / 風控通知與 Quartz job 有大量 direct commits，可保守補入後台 API / risk ops / async data processing 經驗；不寫完整 AntPlay slot platform、game runtime、wallet / reconciliation、完整 RabbitMQ platform 或完整 security platform owner。2026-05-28 `Step 1 / Step 2`、第一條代表 flow `request-log-rabbitmq-admin-consumer Step 5` 與第二條代表 flow `game-api-whitelist-sync Step 5` 已完成並回填 project-level claim。
- 已完成 `antplay antplay-slot-admin-api request-log-rabbitmq-admin-consumer Step 3`；2026-05-28 已建立 RequestLog RabbitMQ admin consumer / audit log 入庫 flow package，確認 game-api producer、admin-api direct exchange / durable queue / routing key、listener JSON parse、`pt_day` partition key、`@UseSchema` schema route、application-level duplicate check 與 `pt_request_log` insert。Step 3 不直接更新 `05 / 08`；後續 Step 4 已完成。
- 已完成 `antplay antplay-slot-admin-api request-log-rabbitmq-admin-consumer Step 4`；2026-05-28 已轉成正式面試 case，補 30 秒 / 90 秒 / 3 分鐘講法、STAR、Senior / Lead 追問、回答框架、常見陷阱、反問面試官與不可誇大邊界。Step 4 不直接更新 `05 / 08`；後續 Step 5 是單條 flow claim gate。
- 已完成 `antplay antplay-slot-admin-api request-log-rabbitmq-admin-consumer Step 5`；2026-05-28 已完成單條 flow claim gate。結論：可作 AntPlay 後台 async audit / RabbitMQ consumer 正式面試 case，並可回填 project-level contribution consolidation supporting evidence；不直接更新 `05 / 08`，不得寫完整 RabbitMQ platform、exactly-once、outbox、DLQ / retry 或 money correctness owner。後續同 project 的 `game-api-whitelist-sync Step 5` 已完成。
- 已完成 `antplay antplay-slot-admin-api game-api-whitelist-sync Step 3`；2026-05-28 已建立 Game API 白名單控制面 / DB + Redis 同步主學習包，確認 admin-api `#682` 後台新增 / 刪除 / 查詢 / sync、DB `game_api_login_ip_white`、Redis `antplay:GameApiWhiteIp:{agentId}` 與 game-api `WhiteIpFilter` runtime allow / reject。Step 3 不直接更新 `05 / 08`；後續 Step 4 已完成。
- 已完成 `antplay antplay-slot-admin-api game-api-whitelist-sync Step 4`；2026-05-28 已轉成正式面試 case，補 30 秒 / 90 秒 / 3 分鐘講法、STAR、Senior / Lead 追問、回答框架、常見陷阱、反問面試官與不可誇大邊界。Step 4 不直接更新 `05 / 08`；後續 Step 5 已完成。
- 已完成 `antplay antplay-slot-admin-api game-api-whitelist-sync Step 5`；2026-05-28 已完成單條 flow claim gate。結論：可作 AntPlay 後台 control plane / Game API 白名單同步正式面試 case，並可回填 project-level supporting evidence；不直接更新 `05 / 08`，不得寫完整 security platform、API gateway、WAF、IAM、完整 Redis consistency framework、完整 game-api runtime owner 或 production incident owner。
- 已完成 `antplay antplay-slot-game-api contribution claim consolidation`；2026-05-21 refreshed 收口。Nick / `10gt12nc` 在 game init、bet / settle / rollback、轉帳錢包、bet record / request log / transfer transaction 分表、RabbitMQ request log、Quartz 補通知、RTP / dark pool / player control 有大量 direct commits，且五條代表 flows 均已 Step 5 並回填 project-level claim。可保守補入 game runtime / betting-settlement / transfer wallet / async log / high-traffic table governance / runtime decision 經驗；不寫完整 AntPlay slot platform、完整遊戲數學 / RTP 策略、wallet / ledger / reconciliation、完整 sharding platform 或完整 RabbitMQ / Kafka owner。
- 已完成 `antplay antplay-slot-game-api Step 1`；2026-05-21 已建立 candidate flows：`slot-bet-settle-rollback`、`transfer-wallet-money-in-out`、`request-log-rabbitmq-async`、`bet-record-sharding-schema-route`、`runtime-rtp-darkpool-player-control`。本 Step 只作 Flow Track 選題，不直接更新 05 / 08；source fetch 失敗一次後依本地 refs / 本地 working tree 保守分析。
- 已完成 `antplay antplay-slot-game-api Step 2`；2026-05-21 已比較候選 flows，第一條代表 flow 選定 `slot-bet-settle-rollback`，後續 Step 3 已完成。
- 已完成 `antplay antplay-slot-game-api slot-bet-settle-rollback Step 3`；2026-05-21 已建立新版 flow package，確認 `/game/bet` 主線、bet record state、single / transfer wallet settle、request log MQ、notify job 與 deadlock 補償風險。Step 3 不直接更新 05 / 08；後續 Step 4 已完成。
- 已完成 `antplay antplay-slot-game-api slot-bet-settle-rollback Step 4`；2026-05-21 已轉成正式面試 case，補 30 秒 / 90 秒 / 3 分鐘講法、STAR、failure scenarios、Senior / Lead 追問與 owner 改善方向。Step 4 不直接更新 05 / 08；後續 Step 5 已完成。
- 已完成 `antplay antplay-slot-game-api slot-bet-settle-rollback Step 5`；2026-05-21 已追 `a2b2af5` / `54078fe` / `31d7a46` / `d3e0002` 等重要 diff，確認本 flow 可作 `antplay-slot-game-api` project-level 履歷 claim 強化 evidence；不單獨寫完整下注結算 / wallet owner，且不宣稱 deadlock compensation 已完整落地。
- 已完成 `antplay antplay-slot-game-api transfer-wallet-money-in-out Step 3`；2026-05-21 已建立 transfer wallet 轉入 / 轉出 / 全額轉出 / 查單學習包，確認簽名、防重、transaction、lookup、DB / Redis balance 與 failure window。Step 3 先作 code-backed 面試素材，不直接更新 05 / 08；後續 Step 4 已完成。
- 已完成 `antplay antplay-slot-game-api transfer-wallet-money-in-out Step 4`；2026-05-21 已整理 30 秒 / 90 秒 / 3 分鐘講法、STAR、failure scenarios、Senior / Lead 追問與 owner 改善方向。
- 已完成 `antplay antplay-slot-game-api transfer-wallet-money-in-out Step 5`；2026-05-21 已追 `54078fe`、`718a207`、transfer table path commits、current blame 與 final behavior。結論是可回填 project-level transfer wallet / DB + Redis consistency / transaction lookup evidence；不單獨寫完整 transfer wallet owner，也不直接更新 05 / 08。
- 已完成 `antplay antplay-slot-game-api request-log-rabbitmq-async Step 3`；2026-05-21 已建立 request log RabbitMQ 非同步化學習包，確認 game-api producer、RabbitMQ exchange / queue / routing key、admin-api consumer 與落庫 SQL。Nick / `10gt12nc` 有 #774 producer / consumer direct evidence；Step 3 只作 async audit / observability 面試素材，不直接更新 05 / 08；後續 Step 4 已完成。
- 已完成 `antplay antplay-slot-game-api request-log-rabbitmq-async Step 4`；2026-05-21 已轉成正式面試 case，補 30 秒 / 90 秒 / 3 分鐘說法、STAR、failure scenarios、Senior / Lead 追問與 owner 改善方向。後續 Step 5 已完成。
- 已完成 `antplay antplay-slot-game-api request-log-rabbitmq-async Step 5`；2026-05-21 已追 #774 producer / consumer 重要 diff、current code 與 blame，確認可回填 project-level async audit / observability claim；不單獨寫完整 RabbitMQ owner、exactly-once、outbox、DLQ / retry / alert，且 routing key 最終格式修正屬他人 context evidence。
- 已完成 `antplay antplay-slot-game-api bet-record-sharding-schema-route Step 3`；2026-05-21 已建立 bet record / request log / transfer transaction 分表與 schema routing 學習包，確認 `@UseSchema` schema route、`pt_day` / `agent_id` partition key、table creator service 與 current create-table job disabled caveat。Step 3 先作 high-traffic data governance 面試素材，不直接更新 05 / 08；後續 Step 4 已完成。
- 已完成 `antplay antplay-slot-game-api bet-record-sharding-schema-route Step 4`；2026-05-21 已補 30 秒 / 90 秒 / 3 分鐘講法、STAR、Senior / Lead 追問與正式面試邊界。後續 Step 5 已完成。
- 已完成 `antplay antplay-slot-game-api bet-record-sharding-schema-route Step 5`；2026-05-21 已追 schema route、#167 bet record 分表、db_partition v2、table creator、current job disabled 與 logical-to-physical mapping evidence。結論是可回填 project-level high-traffic table governance / schema route / partition key evidence；不單獨寫完整 sharding owner，不直接更新 05 / 08。
- 已完成 `antplay antplay-slot-game-api runtime-rtp-darkpool-player-control Step 3`；2026-05-21 已追 `/game/bet` runtime path、RTP cache、dark pool total bet / win、player control、jackpot force respin 與 `SlotMathFacade` math contract。結論是可作 game API runtime decision 面試素材初版；後續 Step 4 / Step 5 已完成。
- 已完成 `antplay antplay-slot-game-api runtime-rtp-darkpool-player-control Step 4`；2026-05-21 已補 30 秒 / 90 秒 / 3 分鐘講法、STAR、Senior / Lead 追問、可用反問與正式面試邊界。後續 Step 5 已完成。
- 已完成 `antplay antplay-slot-game-api runtime-rtp-darkpool-player-control Step 5`；2026-05-21 已追 `GameFacade#bet` respin loop、dark pool `maxWin`、timeout refund branch、player control setData caller、deadlock / afterBet failure window、RTP cache / player control / jackpot current blame。結論是可回填 project-level runtime decision claim；不寫完整 RTP / math / jackpot owner，不直接更新 05 / 08。
- 已完成 `antplay antplay-slot-game-job contribution claim consolidation refresh`；2026-05-25 refreshed 收口。五條代表 flows 均已 Step 5；Nick / `10gt12nc` 在 Kafka consumer / Quartz job、代理玩家報表 projection、big-win notification、report currency / key 修正與 db partition / job config 有 direct commits，activity accumulated bet 為 Nick merge + code-backed supporting evidence，settle pool / darkpool 只作 analysis-first。可保守補入 job / event processing / report projection / notification / partition 經驗；不寫完整 Kafka event platform、reward platform、settle pool / risk / jackpot、DB sharding / schema routing、BI / report platform 或完整 AntPlay slot platform owner。
- 已完成 `antplay antplay-slot-game-job Step 1`；2026-05-22 已建立 candidate flows：`proxy-user-data-report-projection`、`activity-accumulated-bet-voucher`、`big-win-notification`、`settle-pool-monitor-darkpool-sync`、`db-partition-job-report-routing`、`kafka-job-foundation-platform-mock-rollback`。本 Step 只作 Flow Track 選題，不直接更新 05 / 08；source fetch 失敗一次後依本地 refs / 本地 working tree 保守分析。
- 已完成 `antplay antplay-slot-game-job Step 2`；2026-05-22 已比較候選 flows，第一條代表 flow 選定 `proxy-user-data-report-projection`，因 Nick / `10gt12nc` direct evidence 最集中，且最能代表 Kafka event -> DB projection -> Quartz summary / backup / delete 的 Senior Backend 題材。
- 已完成 `antplay antplay-slot-game-job proxy-user-data-report-projection Step 3`；2026-05-22 已建立 Kafka `settled_bets` -> `ag_report_player` projection -> Quartz summary / backup / delete 學習包，補 report key、currency、in-memory map、batch insert / update、summary partial failure 與 claim boundary。Step 3 不直接更新 05 / 08；後續 Step 4 / Step 5 與 project-level contribution claim consolidation refresh 已完成。
- 已完成 `antplay antplay-slot-game-job proxy-user-data-report-projection Step 4`；2026-05-22 已整理成正式面試 case，補 30 秒 / 90 秒 / 3 分鐘說法、STAR、failure scenarios、Senior / Lead 追問、常見陷阱、反問與不可誇大邊界。Step 4 不直接更新 05 / 08；後續 Step 5 已完成。
- 已完成 `antplay antplay-slot-game-job proxy-user-data-report-projection Step 5`；2026-05-25 已追 current blame、important diffs、Kafka retry / dead-letter-topic config 與 DB constraint 缺口。結論是可回填 project-level report projection / Quartz summary evidence；不單獨更新 05 / 08。後續 Rank 2 `activity-accumulated-bet-voucher Step 3` 已完成。
- 已完成 `antplay antplay-slot-game-job activity-accumulated-bet-voucher Step 3`；2026-05-25 已建立活動累計投注送 voucher / free spin 學習包，確認 Kafka `settled_bets` -> activity config -> Redis accumulate / awarded count -> `BetVoucherService` 發放路徑。此 flow 目前是 code-backed + Nick merge evidence，implementation 主要為 Gill / Arnold / Eliot context，不直接更新 05 / 08；後續 Step 4 已完成。
- 已完成 `antplay antplay-slot-game-job activity-accumulated-bet-voucher Step 4`；2026-05-25 已整理成正式 reward correctness 面試 case，補 30 秒 / 90 秒 / 3 分鐘說法、STAR、failure scenarios、Senior / Lead 追問與 owner 改善方向。Step 4 不直接更新 05 / 08；後續 Step 5 已完成。
- 已完成 `antplay antplay-slot-game-job activity-accumulated-bet-voucher Step 5`；2026-05-25 已補查 `BetVoucherService#addVoucher` 下游 idempotency / unique key、Nick merge evidence 與 current blame。結論是本 repo 沒有下游 implementation / DB unique key evidence，`refId` 每次 UUID 不足以證明 deterministic idempotency；本 flow 只作 reward correctness 面試素材與 project-level supporting evidence，不單獨更新 05 / 08。後續 Rank 3 `big-win-notification Step 3` 已完成。
- 已完成 `antplay antplay-slot-game-job big-win-notification Step 3`；2026-05-25 已建立中大獎通知 flow learning package，確認 `#303` direct evidence、Kafka `settled_bets` -> big-win rule -> `push_user` topic、玩家遮罩、game translation、producer async failure 與 privacy boundary。Step 3 不直接更新 05 / 08；後續 Step 4 已完成。
- 已完成 `antplay antplay-slot-game-job big-win-notification Step 4`；2026-05-25 已整理正式 derived notification 面試 case，補 30 秒 / 90 秒 / 3 分鐘說法、STAR、failure scenarios、Senior / Lead 追問、常見陷阱、反問與不可誇大邊界。Step 4 不直接更新 05 / 08；後續 Step 5 已完成。
- 已完成 `antplay antplay-slot-game-job big-win-notification Step 5`；2026-05-25 已補查 `_id` / bet id 去重、`BetIdPersistence` 用途與下游 `antplay-push` bridge / privacy 邊界。結論是可作 project-level big-win notification supporting evidence，但 `_id` 不是 deterministic key、`BetIdPersistence` 不是通知去重、下游未見 `fullPlayerName` filtering，不單獨更新 05 / 08；後續 Rank 4 `settle-pool-monitor-darkpool-sync Step 5` 已完成。
- 已完成 `antplay antplay-slot-game-job settle-pool-monitor-darkpool-sync Step 5`；2026-05-25 已建立 Kafka `settled_bets` -> pool grouping -> `settled_pool` increment -> Redis reset snapshot -> alert 正式面試 case 與 claim gate。結論是 code-backed / analysis-first，未找到 Nick / `10gt12nc` direct path-specific evidence，不回填正式履歷；後續 Rank 5 `db-partition-job-report-routing Step 3` 已完成。
- 已完成 `antplay antplay-slot-game-job db-partition-job-report-routing Step 3`；2026-05-25 已建立 DB partition / report schema routing 學習包，確認 `@UseSchema` / `SchemaRouteAspect` / `SchemaContextHolder`、`pt_bet_record` / `pt_request_log` partition query、`ag_report_player` report path repair，以及 `b754dae db_partition v2`、`6866866 fix ag_report_player` direct evidence。此 flow 可作 project-level 分表 / report path 維護 supporting evidence，但不單獨更新 `05 / 08`；後續 Step 4 已完成。
- 已完成 `antplay antplay-slot-game-job db-partition-job-report-routing Step 4`；2026-05-25 已整理成正式面試 case，補 30 秒 / 90 秒 / 3 分鐘說法、STAR、failure scenarios、Senior / Lead 追問、常見陷阱、反問與不可誇大邊界。Step 4 不直接更新 `05 / 08`；後續 Step 5 已完成。
- 已完成 `antplay antplay-slot-game-job db-partition-job-report-routing Step 5`；2026-05-25 已補 current data source routing config、report call sites、path-specific blame 與 important commit stat。結論是 `db_partition v2`、`fix`、`fix ag_report_player` 可作分表 / report path 維護 direct evidence；current `@UseSchema` framework 是多人後續，G3 data source mapping 未確認，不單獨更新 `05 / 08`。後續 `antplay-slot-game-job contribution claim consolidation refresh` 已完成。
- 已完成 `antplay math-core contribution claim consolidation`；2026-05-20 rolling 收口。Nick / `10gt12nc` 在 SlotMath contract、debugBet VO、currency / fixedMultiBet、RTP type、jackpot / symbol 有 direct commits，可保守補入 slot math core / contract / debug tooling 經驗；不寫完整 slot math framework 或完整 RTP 策略 owner。
- 已完成 `antplay *-math contribution claim consolidation`；2026-05-21 refreshed / grouped 收口。71 個 `*-math` repo 中 49 個有 Nick / `10gt12nc` direct commits，強 evidence 是 `sph-math`、`spn-math`、`sfm-math`、`setl-math`、`sdt-math`、`slc-math`；五條代表 flow 已全部 Step 5 並回填 project-level claim。可保守補入 slot math core / 多個 slot math module 維護與驗證經驗；不寫主導全部 math module、完整遊戲數學模型、完整 RTP 策略、完整 simulator / certification owner、完整 jackpot pool 或單一遊戲 feature owner。
- 已完成 `antplay math-workspace contribution claim consolidation`；2026-06-03 已重掃 source workspace KB、relations audit 與最新 refs。Nick 有 workspace KB / docs commits，可支撐 cross-math code reading、slot math validation workflow 與 AI-assisted 開發 / 驗證閉環；對 `star-math` 的影響是補強驗證鏈與面試追問，不作 standalone 正式履歷主成果或完整 math runtime / simulator / certification owner。
- 已完成 `antplay platform-mock contribution claim consolidation`；2026-05-20 rolling 收口。Nick / `10gt12nc` 有局部 bet / money_inout failure injection commits，只作 provider rollback / compensation testing supporting evidence，不作正式主成果。
- 已完成 `antplay buffer-id contribution claim consolidation`；2026-05-20 rolling 收口。未掃到 Nick / `10gt12nc` direct commits，只作 ID generator learning-only。
- 已完成 `rolling resume package`；2026-05-27 已依目前所有 Contribution Claim Consolidation 與最新 Step 5 收口 refresh `05-resume-master-zh.md`、`08-application-autobiography-zh.md`、`04-interview-casebook.md` 與 `17-salary-negotiation.md`。已吸收 `antplay-slot-game-api` 五條代表 flows Step 5 與 project-level consolidation refresh、`third_games_api` 本批 Step 5、`k3s-deploy gameserver-phased-rollout Step 5` 的 interview-only 邊界、`antplay-slot-game-job` 五條代表 flows Step 5 / contribution consolidation refresh，以及 `ugsoft-admin-api` / `ugsoft-connector-api` 2026-05-27 contribution refresh。這是可先投遞的草稿，不是 final；後續 flow 深掃、Step 5 或新 consolidation 必須回填修正。
- 已完成 `04 / 面試 case 對齊檢查`；2026-05-25 已把 `08` 的 104 主打 bullet 對應到 `04` 的 8-10 條可切換 case，並標出證據層級、3 分鐘主軸與不可誇大邊界。下一步不再是補履歷或平均掃 repo，而是把三條主力 case 練成 90 秒 / 3 分鐘口說版本。
- 已完成 `三條主力 case 90 秒 / 3 分鐘口說打磨` 草稿；2026-05-25 已在 `04-interview-casebook.md` 補 payment provider、wallet / bet-settle、Kafka / report projection 三條主力 case 的 90 秒版本、3 分鐘版本、常見追問與「不需要全會」判斷。這只是稿件完成；面試口說練習目前先暫停，等 Nick 明確要求才開始互動練習。
- 已補 2026 / 2027 台灣轉職月份策略；市場節奏與 Nick 個人準備節奏要分開。外部市場 Tier 1 是 `2-5 月` 最大招聘波，Tier 2 是 `9-10 月` 第二波，Tier 3 是全年其他月份仍有職缺但非高峰；`7 月 / 8 月 / 11-1 月` 出現在計畫裡，是整理、複習、補洞，不代表它們是求職黃金月。對 Nick 的定稿節奏是：2026/07 整理履歷、自我介紹與三個專案故事；2026/08 做 Production Flow、技術複習與模擬面試；2026/09-10 作第一波正式市場驗證，目標是面試、拿回饋、爭取 offer，不是暖身；2026/11-2027/01 整理回饋與補洞；2027/02-05 作第二波擴大投遞，吃最大招聘季；之後每季面試 3-5 間持續 market check。
- 已補面試準備 70 / 20 / 10 邊界：70% 放 production case / system design / claim boundary，20% Java / SQL / transaction / Redis / MQ 基本判斷遇到再補，10% LeetCode / coding test 只作投遞前保險。不得把 AI 時代 coding 焦慮變成刷題主線。
- 已補面試題 / 複習包規則：面試題必須從三條主力 production case 長出來，不建立泛用 Java / SQL / LeetCode 300 題題庫。第一輪只圍繞 payment provider、wallet / bet-settle、Kafka / report projection 產 90 秒版、3 分鐘版、追問題庫、回答要點、誇大陷阱與 case-specific 基本功。

## 下一步

### 0. 待辦事項優先規則

Nick 若先問「缺啥、待辦、優先順序、KB 要不要補」，AI 必須先維護本檔與相關 index，不直接開工 flow Step。

`contribution claim consolidation` 可以先做 rolling / scoped 版，用來支援近期履歷與面試材料；不必等 Step 2 定義的本批代表 flows 全部完成 Step 5。執行時除了掃 code，也要重讀該 project 已完成 flow KB 與目前可讀的 project 文件。未完成 flow 必須標為待深掃 / 待回填，不能宣稱 final 或全 project 已完整深掃；final consolidation 才等本批代表 flows 全部 Step 5 後再校正。

2026-05-27 更新：`antplay *-math fixed-multi-bet-currency-math-core-compatibility Step 5` 已完成，`rtp-reel-strip-simulation-validation Step 5` 已完成，`buy-free-scatter-rtp3-result-contract Step 5` 已完成，`jackpot-symbol-hit-and-prize-scaling Step 5` 已完成，`special-wild-feature-state-transform Step 5` 已完成，且 `*-math contribution claim consolidation` refresh 已完成。`antplay-slot-game-api slot-bet-settle-rollback Step 5`、`transfer-wallet-money-in-out Step 5`、`request-log-rabbitmq-async Step 5`、`bet-record-sharding-schema-route Step 5`、`runtime-rtp-darkpool-player-control Step 5`、`antplay-slot-game-api contribution claim consolidation` refresh、`antplay-slot-game-job` 五條代表 flows Step 5 / contribution consolidation refresh、`ugsoft-admin-api` / `ugsoft-connector-api` contribution refresh 與 `rolling resume package` 也已完成。`iwin iwin_gameserver center-http-deposit-withdraw Step 5`、`game-spin-settlement-log-reel Step 5`、`bet-target-set-query Step 5`、`third_games_api gsc-transfer-bet-settle-rollback Step 5`、`third_games_api oneapi-wallet-bet-result Step 5`、`third_games_api antplay-bet-settle-rollback Step 5`、`third_games_api gsc-seamless-withdraw-deposit-cancel Step 5` 與 `k3s-deploy gameserver-phased-rollout Step 5` 已完成；2026-05-27 rolling resume package、104 投遞欄位檢查、`04 / 面試 case 對齊檢查` 與三條主力 case 口說稿已回填，`08 / 17` 已可作通用 Senior Java Backend / Platform Backend 投遞版。Nick 暫時沒有特定 JD 時，不要一直要求貼 JD；面試口說練習先暫停，等 Nick 明確要求才開始；有 JD 時才客製。

來源 repo 若是內網 GitLab 或 remote 不可達，fetch 失敗一次後不要反覆重試；改用本地 refs / 本地工作樹保守分析，並標示未確認最新遠端。不要把內網 URL、IP 或敏感 remote 細節寫進 vault。

### 0.1 Senior 對標收斂終點

這份 todo 不是無限 backlog。對標 Senior Java Backend / Platform Backend 的收斂終點是：3-5 個可放履歷的 project-level claim、8-10 條可講 3 分鐘且能抗追問的 production flow、乾淨的 claim boundary，以及 `05 / 08 / 04 / 17` 對齊。

方向校正：這個收斂終點不是「Nick 一個人把整個公司大系統全量掃完」。完整大系統本來是團隊工作；個人準備要做到的是：代表 flows 能講清楚、project claims 保守可信、跨系統邊界能畫出主要協作與風險。domain-level 大地圖是可選架構補強，不是目前投遞前必做。

目前回答「還剩多少」時，優先用這個分類：

必做收口：

1. 已完成 `iwin k3s-deploy gameserver-phased-rollout Step 5`
2. 已完成 2026-05-27 `rolling resume package`，最新 claim / case 已對齊 `05 / 08 / 04 / 17`
3. 已完成 2026-05-25 `104 投遞欄位檢查`，`08` 可作通用 104 投遞稿
4. 已完成 2026-05-25 `04 / 面試 case 對齊檢查`，104 主打 bullet 已對應到 8-10 條可切換 case 與 claim boundary

資深補強，可選：

1. `iwin system map v1`：已完成 / 2026-05-28。已補 `projects/iwin/architecture-map.md`、`projects/iwin/integration-map.md` 與 `projects/iwin/career-interview.md`，把 game_api、game_job、payment、third_games_api、iwin_gameserver、app_bi、bi_share、k3s-deploy 的協作關係與 claim boundary 收成 domain-level 大地圖。
2. `AntPlay system map v1`、`UGSoft system map v1`：已完成 / 2026-05-28。已補 `projects/antplay/architecture-map.md` / `integration-map.md` / `career-interview.md` 與 `projects/ugsoft/architecture-map.md` / `integration-map.md` / `career-interview.md`。AntPlay / UGSoft 後台 control plane、game runtime、job、math contract、connector gateway 的架構視角已收斂；不是新的必做 flow。
3. 依目標 JD 補 1 條 payment / provider / MQ 類缺口 flow；沒有 JD 時先不新增。

暫不建議做：

- 官網、前端、workspace、mock、低價值 legacy repo 平均整理。
- 71 個 `*-math` repo 全量逐一深掃；目前只保留代表 flow 與 grouped claim。
- 已完成 contribution consolidation 且沒有新 evidence 的 project 反覆重做。
- 為了追求「全量完整」而把所有 domain-level map、所有 repo、所有 flow 都排成必做。這會把團隊級知識管理誤當成個人求職前置條件。

完成必做收口，且視需要補 2 個非 iwin project 代表 flow 後，就可以停止大規模整理，改成投遞、面試練習與依職缺補洞。

目前深掃 `nick-vault` 後，應放入待辦的缺口：

0. `Completeness Audit 規則補強`：2026-05-26 發現 `ugsoft` 已有 contribution consolidation，但當時沒有任何 Step 1 / Step 2 / `flows/`，先前「全掃 / 都完整」判斷混淆了 Career Track 與 Flow Track。`ugsoft-connector-api Step 1 / Step 2` 已補上，本批三條代表 flow 已全部 Step 5，且 2026-05-27 已完成 contribution refresh；`ugsoft-admin-api Step 1 / Step 2` 也已完成，第一條 `connect-bet-record-mq-ingestion Step 5`、第二條 `request-log-rabbitmq-admin-consumer Step 5` 與第三條 `game-api-provider-white-ip-control-plane Step 5` 均已完成。之後凡是回答「全掃、都完整、還缺什麼、flow 都完整嗎」，必須逐 project 分列 Flow Track / Career Track / Domain Map。Career Track 完成只能表示履歷 claim 可用；不能表示 flow 完整。
0.1 `四 domain flow completeness audit`：已建立 `projects/source-repo-flow-audit.md`，盤點 `/Users/nick/Git/antplay`（排除 `*-math`）、`/Users/nick/Git/iwin`、`/Users/nick/Git/ugsoft`、`/Users/nick/Git/DevOps` project folders。結論：不要全 repo 開工；只把缺口記入待辦。`ugsoft-connector-api Step 1 / Step 2` 已完成，本批三條代表 flow `transfer-wallet-in-out-query Step 5`、`provider-callback-bet-settle-to-mq Step 5`、`request-bet-record-mq-sync Step 5` 均已完成，且 contribution refresh 已完成；`ugsoft-admin-api Step 1 / Step 2` 已完成，第一條 `connect-bet-record-mq-ingestion Step 5`、第二條 `request-log-rabbitmq-admin-consumer Step 5` 與第三條 `game-api-provider-white-ip-control-plane Step 5` 已完成；`antplay-slot-admin-api Step 1 / Step 2`、`request-log-rabbitmq-admin-consumer Step 5`、`game-api-whitelist-sync Step 5` 與 contribution refresh 也已完成。非 iwin 後台 control plane / risk ops 廣度已收斂，不再列投遞前必做。`payment-thirdparty-simulator` 經 iwin re-audit 後降為 payment 測試 supporting evidence，不列 project Flow Track 主待辦；`openobserve`、`kafka`、`antplay-api-deploy` 經 DevOps re-audit 後只作 learning-only / supporting，不列近期 Flow Track；官網、前端、workspace、bot、notify、tool web、mock 與無 Nick direct commits repo 暫不建議。
0.2 `AntPlay re-audit`：2026-05-26 已重新掃 `/Users/nick/Git/antplay` 各 repo 的 remote refs、Nick / `10gt12nc` commits、主要 module 與既有 KB。2026-05-28 `antplay-slot-admin-api Step 1 / Step 2`、第一條代表 flow `request-log-rabbitmq-admin-consumer Step 5`、第二條代表 flow `game-api-whitelist-sync Step 5` 與 project-level contribution refresh 已完成。結論：AntPlay 沒有新的必做缺口；`antplay-push`、`platform-mock`、`math-core` 不新增主待辦，只作 supporting / 已收斂素材。
0.3 `iwin re-audit`：2026-05-26 已重新掃 `/Users/nick/Git/iwin` 各 repo 的 remote refs、Nick / `10gt12nc` commits、主要 module / path history、`payment-thirdparty-simulator` code path 與既有 `projects/iwin` KB。結論：iwin 沒有新的「真正值得補」的 project Flow Track 必做缺口；`payment`、`game_api`、`game_job`、`third_games_api`、`iwin_gameserver`、`app_bi` 已有足夠代表 flows / consolidation。`payment-thirdparty-simulator` 只作 payment provider contract / callback 測試 supporting evidence，不升級主線、不放主履歷。2026-05-28 已完成 `iwin system map v1`，補齊 domain-level architecture / integration / career-interview map；這是架構視角補強，不是新的必做 flow。
0.4 `UGSoft re-audit`：2026-05-26 已重新掃 `/Users/nick/Git/ugsoft` 各 repo 的 remote refs、Nick / `10gt12nc` commits、主要 module / path history 與既有 `projects/ugsoft` KB。結論：UGSoft 不需要全 repo 平均掃。`ugsoft-connector-api` 本批三條代表 flow `transfer-wallet-in-out-query Step 5`、`provider-callback-bet-settle-to-mq Step 5`、`request-bet-record-mq-sync Step 5` 均已於 2026-05-27 完成，contribution refresh 也已完成。`ugsoft-admin-api Step 1 / Step 2` 已於 2026-05-27 完成，第一條 `connect-bet-record-mq-ingestion Step 5`、第二條 `request-log-rabbitmq-admin-consumer Step 5` 與第三條 `game-api-provider-white-ip-control-plane Step 5` 已完成，contribution refresh 也已完成。`official-web-v3`、`ugsoft-admin-web`、`ugsoft-workspace` 不列主待辦；workspace 只作 supporting evidence。這些是狀態記錄，不代表本輪已授權開工。
0.5 `DevOps re-audit`：2026-05-26 已重新掃 `/Users/nick/Git/DevOps/primestar` 各 repo 的 remote refs、Nick / `10gt12nc` / `arnold` commits、manifests / docker-compose / CI / observability docs 與 path history。結論：DevOps 沒有新的 Senior Backend 主履歷 Flow Track 必做缺口。Nick 已確認 `arnold` 是主管帳號，不是 Nick direct evidence；`antplay-docker-deploys` 只能作主管 / 團隊 deployment context 或 learning / supporting，不作 Nick Flow Track、不放主履歷。`openobserve` / `kafka` 無 Nick / `arnold` commits，且 source 含敏感設定，只作 learning-only，不列待辦。

1. `rolling resume package`：已於 2026-05-27 回填 `third_games_api`、`k3s-deploy`、`antplay-slot-game-job`、`ugsoft-admin-api` 與 `ugsoft-connector-api` 最新 Step 5 / case 狀態到 `05 / 08 / 04 / 17`，並標明 `third_games_api`、`k3s-deploy` 維持 interview-only，`antplay-slot-game-job` 可保守寫 job / event processing、report projection / summary、big-win notification、activity supporting flow 與 partition / report path，UGSoft 可保守寫後台 API / control plane、provider connector、RequestLog / BetRecord MQ、白名單控制面與 Quartz / report job。
3. `game_api contribution claim consolidation`：已完成且 2026-05-20 已重新覆核；正式履歷只採 coupon 保守 claim，partner / agent bonus 只作面試素材，不因新規則重做。
4. `game_job contribution claim consolidation`：已完成且 2026-05-20 已重新覆核；保留為 project-level claim evidence，不因新規則重做。
5. `app_bi contribution claim consolidation`：已完成 rolling / scoped negative 收口；不放正式履歷主成果。
6. `bi_share contribution claim consolidation`：已完成 rolling / scoped negative 收口；不放正式履歷主成果。
7. `iwin-workspace contribution claim consolidation`：已完成 rolling / scoped 收口；只作 supporting evidence，不新增正式主成果。
8. `ugsoft-admin-api contribution claim consolidation refresh`：已完成 refreshed 收口；可保守補入履歷。Flow Track Step 1 / Step 2 已完成，第一條 `connect-bet-record-mq-ingestion Step 5`、第二條 `request-log-rabbitmq-admin-consumer Step 5` 與第三條 `game-api-provider-white-ip-control-plane Step 5` 均已完成並回填 project-level claim。不得宣稱全 project 所有候選 flow 完整、完整 UGSoft 平台 owner、完整 provider gateway / access-control / RabbitMQ architecture owner。
9. `ugsoft-connector-api contribution claim consolidation refresh`：已完成 refreshed 收口；可保守補入履歷。Flow Track Step 1 / Step 2 已完成，已選 `transfer-wallet-in-out-query`、`provider-callback-bet-settle-to-mq`、`request-bet-record-mq-sync` 為本批代表 flows；三條均已於 2026-05-27 完成 Step 5 並回填 project-level claim。不得宣稱全 project flow 完整、全部 provider owner、完整 wallet / reconciliation owner 或 complete final consolidation。
10. `ugsoft-workspace contribution claim consolidation`：已完成 rolling 收口；只作 supporting evidence，不作 Flow Track 主題，不新增正式履歷主成果。
11. `antplay-slot-admin-api contribution claim consolidation refresh`：已完成 refreshed 收口；可保守補入履歷。2026-05-28 Step 1 / Step 2、第一條代表 flow `request-log-rabbitmq-admin-consumer Step 5` 與第二條代表 flow `game-api-whitelist-sync Step 5` 已完成並回填 project-level claim；後續候選如風控通知 MQ、RTP / 暗池風控監控、玩家單點控制、auth / RBAC、super agent report 只作可選加強。
12. `antplay-slot-game-api contribution claim consolidation`：已完成 refreshed 收口，且已回填 `slot-bet-settle-rollback Step 5`、`transfer-wallet-money-in-out Step 5`、`request-log-rabbitmq-async Step 5`、`bet-record-sharding-schema-route Step 5` 與 `runtime-rtp-darkpool-player-control Step 5`。本批代表 flows 已全部 Step 5；05 / 08 已由 `rolling resume package` 回填。
13. `antplay-slot-game-job contribution claim consolidation`：已完成 refreshed 收口；可保守補入履歷。Flow Track Step 1 / Step 2 已完成，五條代表 flows 均已 Step 5 並已由 2026-05-27 `rolling resume package` 回填通用履歷 / 自傳 / 面試 / 談薪邊界。
14. `math-core / *-math contribution claim consolidation`：已完成 refreshed / grouped 收口；可保守補入履歷。`antplay *-math fixed-multi-bet-currency-math-core-compatibility Step 5` 已完成，`rtp-reel-strip-simulation-validation Step 5` 已完成，`buy-free-scatter-rtp3-result-contract Step 5` 已完成，`jackpot-symbol-hit-and-prize-scaling Step 5` 已完成，`special-wild-feature-state-transform Step 5` 已完成，且 contribution claim consolidation refresh 已完成。後續除非 Nick 指定 Level 3 final，不再平均掃 71 repo。
15. `math-workspace / platform-mock / buffer-id contribution claim consolidation`：已完成 rolling 收口；只作 supporting / learning，不作正式主成果。
16. `domain-level system map`：2026-05-28 已完成 `iwin system map v1`、`AntPlay system map v1` 與 `UGSoft system map v1`，新增三個 domain 的 `architecture-map.md` / `integration-map.md` / `career-interview.md`。DevOps 目前維持 learning-only / supporting，不建立正式 system map，避免把非 Nick direct evidence 包裝成履歷主線。
17. `0 到 1 system design template`：若 Nick 想補完整系統架構能力，應從已完成的 payment / wallet / bet-settle / MQ / batch / math flows 萃取 template，而不是重掃全部 repo。此項是可選加強，用於 Platform / Lead 候選面試，不是目前投遞前必做。建議只做 4 份：主力 3 份是 `Provider Integration template`、`Wallet / Bet-Settle template`、`MQ / Batch / Projection template`；備用差異化 1 份是 `Slot Math / RTP Validation template`。它們的價值是展示從 production flow 抽象成 system design 的能力，不是宣稱 Nick 主導過完整 0 到 1 大系統。四份 template v1 均已完成，位置是 `senior-owner-playbook/18-system-design-templates.md`；Slot Math / RTP Validation 仍是可選差異化，不是新必做。四份模板只代表 Senior / Platform 面試的 production thinking，不是可直接上線的完整 production spec；真正落地仍要依公司 infra、流量、SLA、資安、法規、team ownership 補詳細設計、POC、壓測、review、runbook。選型要從 failure mode 出發，不是從技術名詞出發。
18. `side project`：目前明確暫不做。它有加分價值，但不是投遞 Senior Backend 或談 10 萬以上薪資的必要條件；不要把雲端 demo、試玩、後台展示變成新主線。`20-game-backend-architecture-selection.md` 保留為架構選型備忘與面試問答素材；`21-zero-to-one-project-strategy-report.md` 保留為 0 到 1 可容器化 backend lab 的策略報告。除非 Nick 明確說要啟動 side project，否則 AI 不主動產 project spec、不寫 code、不部署雲端、不新增 side project backlog。若 Nick 再問「是不是該實作 / 本地 k3s / 既有 DB / 既有專案整合」，預設回答是：目前不建議實作，先把 flows 讀熟、講熟、能面試；若未來市場回饋要求 0 到 1 作品，再從 Interview MVP blueprint 開始，不直接接公司 DB 或公司 code。

### 1. 收斂後狀態

目前狀態：

```text
通用投遞包可用；沒有預設下一步；可以自由提問或彈性指定
```

可選加強：若 Nick 想降低焦慮、確認自己是否真的能面 Senior，不要再開全 repo 深掃；改走 `Interview Coaching Track`。位置是 `senior-owner-playbook/19-interview-coaching-question-bank.md`。題庫 v1.0 已定稿，接下來不要再編題目、加題或重排題庫；目前題庫是 14 個主題的診斷池，並收斂成 30 題核心題。最高 ROI 下一步是：`30 題核心答案草稿`、`30 題錄音自講`、`最強 3 個 project story 練到能講 3 分鐘`、`四條核心 flow 白板講法`、`找機會投遞 / 面試驗證`。30 題核心已補入 slow query、consumer lag、Spring transaction 失效，避免太偏 Payment / Provider。實務練習順序是：30 題核心 -> 履歷與定位 -> Production Flow -> Transaction / Consistency -> Incident / Troubleshooting -> DB / MQ / Spring 基本功 -> Provider Integration 專題 -> System Design -> 談薪 / HR -> Architecture Evolution。Provider Integration 是主戰場專題但不取代基本盤；Real Troubleshooting 併入 Incident，不獨立插隊第一輪；Architecture Evolution 是第二輪加分。AI 每輪只問 3-5 題，依回答做評估、追問、教學、畫圖、代碼演示或口說打磨；問答不記流水帳，只把能力判斷、補強主題與可回填素材整理進 KB。

原因：

- `gsc-transfer-bet-settle-rollback Step 5` 已完成，結論維持 code-backed interview-only，不更新正式履歷 / 自傳。
- `oneapi-wallet-bet-result Step 5` 已完成，結論維持 code-backed interview-only，不更新正式履歷 / 自傳。
- `antplay-bet-settle-rollback Step 5` 已完成，claim gate 結論維持 interview-only。
- `gsc-seamless-withdraw-deposit-cancel Step 5` 已完成，claim gate 結論維持 interview-only。
- `k3s-deploy gameserver-phased-rollout Step 5` 已完成，claim gate 結論維持 interview-only。
- 2026-05-27 rolling resume package、104 投遞欄位檢查與 `04 / 面試 case 對齊檢查` 已完成，`08 / 17` 已先調整為通用 Senior Java Backend / Platform Backend 投遞版。
- 目前已不是「履歷能不能寫」或「case 能不能對上」，三條主力 case 的 90 秒 / 3 分鐘草稿也已完成；下一個缺口是 Nick 能不能實際講出來並抗追問。
- 轉職月份與面試題準備規則已收進 KB。後續若 Nick 問「要不要補 Java / SQL / LeetCode」，預設回答是：只保留最小檢查表與遇到再補，不取代 70% production case 主線。
- 但練習線先暫停，side project 也先不做。Nick 暫時沒有特定 JD 時，AI 不再大規模補資料，也不要求貼 JD；除非 Nick 明確說要開始練，AI 不主動進入互動式口說問答。若 Nick 問下一步，先回答收斂狀態：目前沒有預設下一步，可以自由提問或彈性指定；`iwin system map v1` 已完成，後續架構視角只在 Nick 明確要 Level 3 架構審計、0 到 1 system design template、JD-specific system design 或明確啟動 side project 時再補。

### 2. 各 project 局部收斂狀態

目前 antplay 線已完成 `antplay-slot-admin-api`、`antplay-slot-game-api`、`antplay-slot-game-job`、`math-core`、`*-math`、`math-workspace`、`platform-mock`、`buffer-id` contribution consolidation，其中 `antplay-slot-admin-api` 已於 2026-05-28 完成 refreshed 收口。`antplay *-math fixed-multi-bet-currency-math-core-compatibility Step 5` 已完成，`rtp-reel-strip-simulation-validation Step 5` 已完成，`buy-free-scatter-rtp3-result-contract Step 5` 已完成，`jackpot-symbol-hit-and-prize-scaling Step 5` 已完成，`special-wild-feature-state-transform Step 5` 已完成，且 `*-math contribution claim consolidation` refresh 已完成；`slot-bet-settle-rollback Step 5`、`transfer-wallet-money-in-out Step 5`、`request-log-rabbitmq-async Step 5`、`bet-record-sharding-schema-route Step 5`、`runtime-rtp-darkpool-player-control Step 5`、`antplay-slot-game-api contribution claim consolidation` refresh、`antplay-slot-game-job contribution claim consolidation refresh`、2026-05-27 `rolling resume package`、`104 投遞欄位檢查`、`04 / 面試 case 對齊檢查` 與三條主力 case 口說稿已完成。通用投遞包已可用；沒有實際 JD 時，面試口說練習先暫停，等 Nick 明確要求。

以下是近期各 project 的局部狀態；不是新的預設下一步：

1. `game_api`：`contribution claim consolidation` 已完成；正式履歷只採 coupon 保守 claim，不因新規則重做。
2. `game_job`：`contribution claim consolidation` 已完成，不因新規則重做。
3. `payment`：Top 5 代表 flow 與 contribution consolidation 已收斂，2026-05-20 已重新覆核並補入 GoldenPay direct evidence，2026-05-26 已降回 provider / 商戶對接口徑；不因新規則重做。若 Nick 指定，可追加 provider-by-provider、MQ / reconciliation 邊界、game lobby 上下分等 flow，但這是可選加強。
4. `app_bi`：rolling / scoped negative contribution claim consolidation 已完成；不放正式履歷主成果。
5. `bi_share`：rolling / scoped negative contribution claim consolidation 已完成；不放正式履歷主成果。
6. `third_games_api`：rolling / scoped contribution consolidation 已完成；`gsc-transfer-bet-settle-rollback Step 5`、`oneapi-wallet-bet-result Step 5`、`antplay-bet-settle-rollback Step 5`、`gsc-seamless-withdraw-deposit-cancel Step 5` 已完成；本 project 本批代表 flow claim gate 已收斂。
7. `iwin_gameserver`：Career Track consolidation 已完成；`center-http-deposit-withdraw Step 5`、`game-spin-settlement-log-reel Step 5` 與 `bet-target-set-query Step 5` 已完成，Flow Track 已回到 `third_games_api`。
8. `k3s-deploy`：`gameserver-phased-rollout Step 5` 已完成；維持 interview-only，不更新正式履歷。
9. `iwin-workspace`：contribution consolidation 已完成；2026-06-03 已重掃 payment workspace KB catalog 與最新 refs，只作 system reconstruction 與 AI-assisted 開發 / 驗證閉環 supporting evidence，不作 flow 主題，不因新規則重做。
10. `ugsoft-admin-api`：contribution consolidation refresh 已完成；可作後台 API / control plane、Game API / provider IP 白名單控制面、RequestLog / BetRecord MQ、Quartz / report job 履歷素材。
11. `ugsoft-connector-api`：contribution consolidation refresh 已完成；可作 provider connector / transfer wallet / callback / request-bet-record MQ / job sync 履歷素材。Step 1 / Step 2 已完成；本批三條代表 flow `transfer-wallet-in-out-query`、`provider-callback-bet-settle-to-mq`、`request-bet-record-mq-sync` 均已完成 Step 5 並回填 project-level claim，目前 connector 已收斂。
12. `ugsoft-workspace`：contribution consolidation 已完成；只作 workspace / docs / harness / runbook supporting evidence，不作正式履歷主成果。
13. `antplay-slot-admin-api`：contribution consolidation refresh 已完成，Step 1 / Step 2、第一條代表 flow `request-log-rabbitmq-admin-consumer Step 5` 與第二條代表 flow `game-api-whitelist-sync Step 5` 已完成並回填 project-level claim；可作 AntPlay 後台 API / 商戶控制面 / 風控監控 / RabbitMQ 與 Quartz 非同步資料處理素材。目前已收斂，沒有預設下一步。
14. `antplay-slot-game-api`：refreshed contribution consolidation 已完成，Step 1 / Step 2 / `slot-bet-settle-rollback Step 5` / `transfer-wallet-money-in-out Step 5` / `request-log-rabbitmq-async Step 5` / `bet-record-sharding-schema-route Step 5` / `runtime-rtp-darkpool-player-control Step 5` 已完成。可作 AntPlay 遊戲 API runtime / 下注結算 / 轉帳錢包 / 分表 / RabbitMQ request log / high-traffic table governance / runtime decision 素材；05 / 08 已回填。
15. `antplay-slot-game-job`：contribution consolidation refresh 已完成，Flow Track Step 1 / Step 2 已完成，`proxy-user-data-report-projection Step 5` 已完成並回填 project-level report projection / Quartz summary evidence，`activity-accumulated-bet-voucher Step 5` 已完成且只作 reward correctness 面試素材 / supporting evidence，`big-win-notification Step 5` 已完成並回填 project-level supporting evidence，`settle-pool-monitor-darkpool-sync Step 5` 已完成且只作 analysis-first 面試素材，`db-partition-job-report-routing Step 5` 已完成且作分表 / report path supporting evidence；可作 AntPlay job / event processing、Kafka / Quartz、報表 projection / summary、activity accumulated bet supporting flow、big-win notification 與分表 / report path 維護素材。2026-05-25 rolling resume package 已回填。
16. `math-core`：contribution consolidation 已完成；可作 slot math core / contract / debug tooling 素材。Step 1 / Step 2 是可選加強，不是預設下一步。
17. `*-math`：contribution consolidation 已完成 refreshed / grouped，`fixed-multi-bet-currency-math-core-compatibility Step 5` 已完成，`rtp-reel-strip-simulation-validation Step 5` 已完成，`buy-free-scatter-rtp3-result-contract Step 5` 已完成，`jackpot-symbol-hit-and-prize-scaling Step 5` 已完成，`special-wild-feature-state-transform Step 5` 已完成；可作多個 slot math module / RTP / reel strip / debug / fixedMultiBet / buy free / jackpot / feature state transform 素材。已收斂，不平均掃 71 repo。
18. `math-workspace`：contribution consolidation 已完成；2026-06-03 已重掃 workspace KB / latest refs / relations audit，只作 cross-math KB / validation workflow 與 AI-assisted 開發 / 驗證閉環 supporting evidence。
19. `platform-mock`：contribution consolidation 已完成；只作 provider failure injection supporting evidence。
20. `buffer-id`：contribution consolidation 已完成；未見 Nick direct commits，只作 learning-only。
21. `rolling resume package`：已完成 2026-05-28 Resume / 104 final check 目前可用版；已吸收 `antplay-slot-game-job contribution claim consolidation refresh`、`ugsoft-admin-api contribution claim consolidation refresh`、`ugsoft-connector-api contribution claim consolidation refresh` 與 `antplay-slot-admin-api contribution claim consolidation refresh`，並同步到 `05 / 08 / 04 / 17 / 11`。UGSoft 可用後台 API / control plane、provider connector、RequestLog / BetRecord MQ、白名單控制面、Quartz / report job 口徑；AntPlay admin 可用後台 API / 商戶控制面 / 風控監控 / RabbitMQ 與 Quartz 非同步資料處理口徑；iwin / AntPlay / UGSoft system maps 只作架構與面試視角，不新增正式履歷 claim；不寫完整 UGSoft / AntPlay 平台、完整 provider gateway、完整 wallet / money flow、完整 RabbitMQ architecture owner 或完整 access-control platform owner。

### 3. 每條完成後自動判斷是否更新

每條 flow 完成後，AI 要自動判斷是否需要更新：

- project README
- Step 文件
- flow evidence / claim-boundary
- `01-senior-owner-flow-inventory.md`
- `04-interview-casebook.md`
- `05-resume-master-zh.md`
- `08-application-autobiography-zh.md`
- `06-todo.md`

但不要把履歷寫太滿。只有完成 flow 並補 evidence 後，才升級履歷說法。

### 4. 跨 repo 選題參考

若 Nick 問「所有 repo 排序 / 下一個 repo」，以 `01-senior-owner-flow-inventory.md` 的「跨 repo 優先排序」為準。這份排序只用來選題，不是 code evidence；真正開工前仍要做該 repo 的 Step 1 / Step 2。目前若目標是最快補 Senior Backend 主力素材，`payment`、`game_job`、`game_api`、`iwin_gameserver`、`antplay-slot-game-api`、`antplay-slot-game-job`、`ugsoft-admin-api`、`ugsoft-connector-api`、`math-core`、`*-math` 的履歷 claim 已先保守收斂，其中 `payment` 已於 2026-05-20 重新覆核並補入 GoldenPay direct evidence，`iwin_gameserver` 已把 third-party provider 投派整合 direct evidence 正確歸位；`math-core` / `*-math` 是目前差異化最高的 slot math 素材且 contribution claim refresh 已完成，`slot-bet-settle-rollback Step 5`、`transfer-wallet-money-in-out Step 5`、`request-log-rabbitmq-async Step 5`、`bet-record-sharding-schema-route Step 5`、`runtime-rtp-darkpool-player-control Step 5`、`antplay-slot-game-api contribution claim consolidation` refresh、`antplay-slot-game-job contribution claim consolidation` refresh、`ugsoft-admin-api` / `ugsoft-connector-api` contribution refresh、2026-05-27 `rolling resume package`、`104 投遞欄位檢查`、`04 / 面試 case 對齊檢查` 與三條主力 case 口說稿也已完成。`center-http-deposit-withdraw Step 5`、`game-spin-settlement-log-reel Step 5`、`bet-target-set-query Step 5`、`third_games_api gsc-transfer-bet-settle-rollback Step 5`、`third_games_api oneapi-wallet-bet-result Step 5`、`third_games_api antplay-bet-settle-rollback Step 5`、`third_games_api gsc-seamless-withdraw-deposit-cancel Step 5` 與 `k3s-deploy gameserver-phased-rollout Step 5` 已完成；面試口說練習先暫停，有實際 JD 時才客製 `08 / 17`。

## 當前收斂狀態

```text
通用投遞包可用；沒有預設下一步；可以自由提問或彈性指定
```

AI 會依共用規則自動重讀 KB、既有 project 文件與相關 code repo 最新狀態，不需要 Nick 每次重貼完整規則。

## Senior 面試對標門檻

不要再用「最低能投」當標準。2026-05-27 後，履歷 / 自傳 / 面試 / 談薪包已可先投遞，`04 / 面試 case 對齊檢查` 與三條主力 case 口說稿也已完成；接下來要把 Senior 面試準備度提高到 `中等可面 -> 穩過可抗追問 -> 完全對標 Senior / Platform`。但口說練習先暫停，等 Nick 明確要求再開始。已完成素材包含：

1. `payment-provider-callback` 或 `payment-order-provider-request`
2. `antplay-slot-game-api/slot-bet-settle-rollback` 或 `iwin_gameserver/third-party-transfer-in-out`
3. `antplay-slot-game-job/proxy-user-data-report-projection` 或 `game_job/daily-game-data-summary`

### 中等可面

- 3 條主力 case 能講 3 分鐘。
- 每條都有 evidence、claim boundary 與 5 個常見追問。
- 能說清入口、DB / Redis / MQ / provider、source of truth、failure window。
- 可以投 Senior / Platform Backend，但遇到強追問仍可能需要回來補洞。

### 穩過可抗追問

- 5 條 case 覆蓋 payment、wallet / bet-settle、MQ / projection、partition / high-traffic data、rollout / observability。
- 每條都有 90 秒、3 分鐘、STAR、failure scenarios、owner decision 與不可誇大邊界。
- 能回答「如果你重做會怎麼設計」、「先修哪裡」、「如何監控 / rollback / reconciliation」。
- 面試不靠背稿，能依 JD 重排案例順序。

### 完全對標 Senior / Platform

- 8-10 條 production flow 可切換使用，且 `05 / 08 / 04 / 17` 全部對齊。
- 每條 claim 都能分清：真實開發過、本人確認、code-backed、interview-only、不可誇大。
- 能把 project-level claim、單條 flow、薪資談判與自我介紹互相對上。
- 達到後停止大規模整理，改成投遞、面試練習、面試後補洞，不再平均掃 repo。

每條主力 case 都要有：

- `flow.md`
- `career-interview.md`
- `materials/evidence.md`
- `materials/decision-notes.md`
- `materials/interview.md`
- `materials/claim-boundary.md`

詳細檢查看：

```text
senior-owner-playbook/11-senior-interview-readiness.md
```

不同職缺定位怎麼準備，看：

```text
senior-owner-playbook/12-role-target-readiness-matrix.md
```

本地專案能力地圖與外部案例標注規則看：

```text
senior-owner-playbook/13-code-capability-map.md
projects/CONVENTIONS.md
```
