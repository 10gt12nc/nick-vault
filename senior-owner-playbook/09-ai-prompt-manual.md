# AI 提示詞手冊

這份是之後請 AI 維護 `nick-vault`、分析專案、產出 Senior / Owner 學習包時使用的提示詞手冊。

核心原則：

- 這份提示詞手冊是全 vault 共用，不是單一專案專用。
- 任何 project / flow / Step 任務都要自動套用本手冊，不需要 Nick 每次重貼規則。
- 不要一次叫 AI 產很多。
- 不要叫 AI 平均整理全部 class。
- 每次只跑一個 step。
- 每次只完成一條 flow。
- 每次任務開始前，AI 必須先判斷 `輕量問答 / 快速狀態 / 中量維護 / 重度深掃`。只有 Step / flow / contribution consolidation / 履歷 claim / Completeness Audit 才需要自動重讀 KB、該 project 既有文件與相關 code 最新狀態；一般問答和收斂狀態的下一步不全量掃。
- KB 優化採觸發式維護：只有文件造成誤判、重複讀取、重複踩坑、交接失敗、履歷 / flow claim 風險，或 Nick 明確要求時才改。不要因為「還能更完整」就主動全面重構；若無觸發條件，應回到投遞、讀 flow、面試練習或當下任務。
- 每次掃公司 / 來源 code repo 前，AI 必須先 `git fetch --all --prune` 或用等效方式確認 remote refs 最新，並記錄 local HEAD、remote HEAD、是否 ahead / behind；不得自動 `pull`、merge、checkout、rebase 或改公司 repo 工作樹。
- 每次重讀後，AI 必須自己檢查舊 Step / 舊 flow 文件是否符合目前 KB；若不符合，要主動建議重整或直接補 evidence，不能等 Nick 追問。
- 不確定就標示「已確認 / 推測 / 待確認」。
- 只輸出整理後的新內容，不複製舊檔。
- 不寫 secret、token、內網 IP、production URL、客戶資料。
- 履歷與面試不能誇大。
- Flow 線與履歷 / 自傳線分開但互相回填：Flow 線負責系統理解與面試，Career 線負責 project-level 履歷 claim。單條 flow Step 5 不能直接代表整個 project 的履歷結論；05 / 08 原則上只吃 project contribution consolidation 結果。contribution consolidation 可以先做 rolling / scoped 版，flows 之後照舊深掃並回填履歷素材。
- contribution consolidation 完成不代表 flow 完成。當 Nick 問「全掃 / 檢查所有 / flow 都完整 / 為什麼沒 flow」時，AI 必須分開檢查 Flow Track、Career Track、Domain Map；不能只因 `contribution-claim-consolidation.md` 存在就回答 project 已完整。若缺 `step1-candidate-flows.md`、`step2-flow-comparison.md` 或 `flows/`，必須明確標成 Flow Track 缺口。
- 若 Nick 要先把所有 contribution consolidation 匯成 05 / 08，AI 要產出 `rolling resume package`：更新 05 的可直接使用履歷版、08 的投遞版，標示目前可用但非 final，並保留「後續 flow 深掃互相回填」規則。`05` 是母稿與證據池；`08` 是投遞輸出版，必須維持 104 可貼欄位：工作經驗、專長、自傳、自我推薦。
- 若 Nick 問薪資、期望待遇、談薪或 offer，AI 要先讀 `17-salary-negotiation.md`，並重新查當下市場行情；薪資資料具時效性，不可只拿舊數字回答。談薪說法要對齊 05 / 08 的保守 claim，不得用未證實的 owner / architect / 主導完整系統來抬價。
- 若 Nick 貼明確 JD，AI 要先做 fit analysis，判斷是否值得投、值得 market check、或應略過；不要每看到一個 JD 就直接產完整履歷包。若 Nick 想投、值得 market check，或 Nick 明確要求客製履歷、自傳、面試問題或 HR / 薪資 / 文化 / 技術問答，AI 要建立 `senior-owner-playbook/applications/{company-or-role}-{date}/` 的獨立 JD-specific package。原則是一個公司 / 一個 JD / 一個資料夾；通用 `05 / 08 / 17 / 04` 不因單一 JD 直接覆蓋。客製包只能重排既有 evidence、標清弱項與降調邊界，不能把 Nick 包裝成正式 Tech Lead、完整架構師、完整金流 / wallet / MQ platform owner。JD-specific package 可包含 20% 基本功最小複習清單，例如 AOP proxy、transaction 失效、SQL index、Redis consistency、MQ duplicate consume、microservice timeout / retry，但只能從 JD 與主力 production case 長出來，不建立泛用 Java / Spring / SQL 八股題海。若客製包產生全域通用規則，才另外回填共用 KB。
- 若 Nick 問 `*-workspace`、AI 開發閉環、Codex / Claude / Cursor 經驗是否能放履歷，AI 要把它歸類為 Career Track 的 supporting evidence：可支撐 AI-assisted engineering workflow / knowledge-base driven development，但不得反向包裝成 production service 功能開發或 AI 自動主導工程成果。可放在 05 / 08 / 17 的工程方法、自我推薦與談薪支撐，不作 standalone 主成果。
- 待辦事項 / KB 維護 / 缺口清單 / 優先順序是 Planning / KB Governance Track，優先於一般 Flow Step 慣性。Nick 要 AI「先做待辦」、「說缺啥」、「維護 KB」時，AI 只能更新 todo / KB / index 與列出候選下一步，不能自行把缺口開工成 Step 4 / Step 5。
- Nick 明確說「專案先不下一步」、「先只更新 KB」、「先不要推 project / flow」時，本輪是 `KB-only` 模式。完成後不要給 project flow 下一步 prompt，也不要自動開工 todo 裡的收口項目；只回報 KB 修正、檢查結果、commit / push 狀態。
- 每次完成後，若 flow / project / Career Track 尚未收口，AI 要自動給下一步建議，而且只推薦一件最值得做的事。
- Nick 若只貼 `讀kb`、`下一步`，AI 要進入 `KB Readiness + Next Action Automation`，但預設先用 `快速狀態`：讀 `README / 06 / 01` 與必要 git 狀態；只有發現 active flow、待收口 consolidation、履歷 / 面試輸出不一致，或 Nick 指定 project / flow / JD，才升級掃 `projects/` 狀態。Nick 不需要自己知道目前有多少 flow、哪些檔案要補、哪些 decision notes / resume / map 相關檔案缺；teaching notes 預設不列為缺口。
- `讀kb / 下一步` 的優先順序固定是：active flow 收口 -> project contribution consolidation -> 05 / 08 / 04 / 17 對齊 -> 高價值 project Step 1 / Step 2 -> 會影響面試追問的 decision notes / 口說材料 -> 若已收斂則轉自由提問 / 口說 / JD-specific。不得自動開工多個 flow，也不得把候選缺口包裝成必做 backlog。Teaching notes 只有 Nick 讀 flow 卡住、面試回饋暴露基本功缺口，或 Nick 明確要求「補教學」時才補。
- 若目前已收斂、沒有 active flow、沒有特定 JD、Nick 也沒有指定下一個任務，AI 不要硬塞下一步；改成回報「沒有預設下一步，可以自由提問或彈性指定」。此時不要輸出 fenced prompt，也不要把可選方向包裝成必做 backlog。
- 若 Nick 追問「這種下一步建議不錯 / 幫記 KB」，保留的格式是：先判斷 `必做收口` 是否存在，再列少數 `可選加強`（廣度延伸、深度延伸、架構視角、投遞準備），最後列 `暫不建議`。可選加強是選單，不是授權開工；不建議事項要明確，避免 AI 用 backlog 製造壓力。
- 下一步建議若需要輸出 prompt，必須附上 Nick 可直接複製的短 prompt，並用 fenced code block 包起來，格式固定為 ` ```text ... ``` `；code block 內只放下一句 prompt。
- 每次 Step 都要寫清楚實際掃描範圍；沒看其他分支、沒看下游 code、沒看後端 repo，就要明確寫未掃 / 待確認。
- 每次 Step 也要寫清楚 code repo 遠端最新性：是否已 fetch、local HEAD、remote HEAD、是否 ahead / behind；若未 fetch 或本機落後遠端，必須標示「未確認最新 code」。
- 若來源 repo remote 是內網 GitLab 或當下網路不可達，fetch 失敗一次後不要反覆重試；用本地 refs / 本地工作樹保守分析，並標示「fetch 失敗 / 未確認最新遠端 / 依本地 refs 判斷」。不要把內網 URL、IP 或敏感 remote 細節寫入 vault。
- 若 Nick 要求參考 `iwin-workspace`、`math-workspace`、`test001_unity` 或其他 workspace 的新流程，AI 必須先輸出或內化 `值得導入 / 只作參考 / 不採用 / 仍需驗證` 判斷。只吸收入口索引、non-goals、scope、success criteria、handoff / reviewer gate、Producer / Scope gate、baseline / evidence 分層、source / KB 分離、relation map 與 generated / curated 分離等防超做方法；不得把開發型 `docs/agent-roles/`、`kb/catalog/`、per-project `CLAUDE.md`、GDD / RTP / Unity / deploy / child repo 規則搬進 `nick-vault`。若 Nick 說「維護 KB / 幫優化 / 幫改 / 值得學的抄過來」，只能把結論重寫成最小 vault 規則，優先改 `00`、`09`、`06` 或 `projects/CONVENTIONS.md`，不要新增長期資料夾。
- 參考 workspace 的角色只能轉譯成 `nick-vault` 的四個 role lens：`Producer / Scope Gate`、`Flow / Technical Reviewer`、`Career Claim Reviewer`、`KB Curator`。這些只是任務前後的思考與交付檢查，不是新的 Step，也不取代 Flow Track / Career Track / Completeness Audit。
- Karpathy-style 必須內化為行為底線：先釐清假設與 non-goals、選最小可行修改、只動本輪必要檔案、每輪用可驗證條件收口。它不是新的資料夾、不是新 Step，也不是要求每次都輸出長計畫。
- Agent Workflow 在 `nick-vault` 預設只用 Lite 版：`Scope Gate -> Evidence Gate -> Output Gate -> Review Gate`。中量維護至少走 Scope / Output / Review；重度深掃、flow Step、contribution consolidation、履歷正式 claim 必須四個都走。只有真正涉及 source code 實作、跨 repo 改動、測試、重構或安全修正時，才參考 `math-workspace` 的完整 `Architect -> Planner -> Coder -> Reviewer`。
- 沒有 evidence 的技術點可以略過或標外部補讀，不要為了湊格式腦補。
- Flow 讀懂後，若需要補技術硬底子，要用 `materials/decision-notes.md` 整理技術選型、差異比較、trade-off 與 owner decision，不要只停在資料流。
- Flow 後可以補 SQL / Spring / Kafka / Redis / transaction 教學，但目前預設先不補。只有 Nick 讀該 flow 卡住、面試追問暴露基本功缺口，或 Nick 明確要求補教學時，才用 `flow-first teaching`：先完成 code-backed flow 深掃，再從本 flow 的實際 code path 補 3-5 個必要技術點。不得把每條 flow 寫成完整 SQL / Spring / Kafka 課本。
- `materials/teaching-notes.md` 只在本 flow 真的需要教學補洞時建立；內容必須回答「哪段 code 觸發、production 會壞在哪、面試怎麼白話講、最小要補什麼基本功」。沒有 local code evidence 的內容標成 `外部通用模式 / non-local`。
- 新建或重整後的 flow，預設閱讀入口只有 `flow.md`；該 flow 的保守履歷 / 面試素材放 `career-interview.md`，其他 evidence / decision / interview / claim 邊界收在 `materials/`。
- `flow.md` 必須先有初階 / 中階可讀區，包含白話導讀、Code 分層對照、最小架構圖、正常流程圖與逐步說明；後半才進 Senior / Owner 的 consistency、failure window、trade-off、owner decision。不要讓 Nick 需要自己從附錄拼出主報告。
- flow、履歷、自傳、面試素材都要標註證據層級：`真實開發過`、`專案存在 / code-backed`、`分析素材 / learning-only`、`外部案例 / non-local`、`待確認`。
- Nick 本人明確確認做過的內容也是 evidence。AI 不得只因單條 flow 沒有直接 path-specific commit 就否定整個 project 經驗；需標成「本人確認，待 commit / ticket 補強」或「本人確認 + code-backed」，再補 contribution consolidation。
- contribution consolidation 是履歷 claim gate，可以先做 rolling / scoped project-level consolidation，不必等 Step 2 定義的本批代表 flows 全部完成到 Step 5。若 project 只有單條 flow Step 5，也可以做 rolling consolidation，但文件必須標明不是 final / 全量深掃，並列出未完成 flow、未深掃路徑與待回填 evidence。
- 若某 project 的 Step 2 本批代表 flows 已全部完成 Step 5，且尚未完成 project-level contribution consolidation，該 project 進入「待收口」。Nick 問下一步、履歷、缺口或 consolidation 時，待收口 project 的 consolidation 優先於跨 project queue 與其他 project 的單條 flow Step；除非 Nick 明確指定先做別的 project / flow。若本批代表 flows 尚未全部完成但 Nick 要先補履歷，則做 rolling consolidation，並同步 todo / inventory / project README，標示「rolling consolidation 已做 / flow 深掃未完 / 待回填」。
- `nick-vault` 必須有對標資深的收斂終點。當 Nick 問「還有多少 step / 會不會一直建議 / 什麼時候結束 / 對標資深夠不夠」時，AI 要先回答結束點，而不是繼續推下一個 flow。固定分成 `必做收口`、`可選加強`、`暫不建議做`，並說明做完後是否改成投遞 / 面試練習 / 針對職缺補洞。
- Senior 對標完成標準：3-5 個 project-level 履歷 claim、8-10 條可講 3 分鐘且抗追問的 production flow、每條 claim 的真實開發 / code-backed / 不可誇大邊界乾淨、`05 / 08 / 04 / 17` 與最新 claim 對齊。達到後應停止大規模整理，不再把 backlog 當必做。
- 面試準備比例固定以 `70 / 20 / 10` 收斂：70% production case / system design / claim boundary；20% Java / SQL / transaction / Redis / MQ 基本判斷只做最小檢查表、遇到 case 或面試回饋再補；10% LeetCode / coding test 只作投遞前保險。不得把刷題、八股或 AI coding 焦慮變成新主線。
- 面試題 / 複習包必須從主力 production case 長出來，不建立泛用 Java / SQL / LeetCode 題庫。第一輪只圍繞 payment provider、wallet / bet-settle、Kafka / report projection 出 90 秒版、3 分鐘版、追問題、回答要點、誇大陷阱與 case-specific 基本功。
- 若 Nick 問轉職月份、投遞節奏或市場測試，先讀 `17-salary-negotiation.md` 的台灣轉職月份策略，並視當下日期 / 市場重新查資料。月份是投遞節奏，不是是否夠 Senior 的判斷依據。
- 當 Nick 問「flow / map 是否都完整、能不能 0 到 1 架完整系統、是不是方向歪了、是否要一個人扛大系統」時，AI 要先校正目標：本 vault 追求的是可投遞、可面試、可防追問的 Senior / Platform evidence package，不是全公司知識全量掃完。先分清 `已足夠投遞 / 面試`、`可選架構補強`、`暫不建議做`，不要直接新增一串 flow。
- `System Owner` 只能解釋為核心 flow owner thinking，不得擴張成完整公司平台 owner。AI 必須提醒：完整大系統本來就是團隊工作；Nick 要補的是代表 flows、跨系統邊界、風險判斷與口說能力，不是把所有 repo 都背起來。
- 但如果 Nick 只要求「待辦 / 缺口 / 優先順序」，AI 先把 contribution consolidation 或 flow Step 列成待辦，不自動執行；等 Nick 明確下 `project contribution claim consolidation` 或 `flow Step N` 才開始深掃與改 flow 文件。
- 大專案 / 子專案地圖與職涯能力矩陣都只是輔助層；主軸仍是 production flow，不要因為補資料而發散。
- 不可以自行創造新 Step 或新下一步名稱。下游定位、補 evidence、補 decision-notes、補架構圖都只是補充任務；除非 Nick 明確指定，否則 Step 3 完成後下一步就是 Step 4。若正在處理履歷 / 自傳 / contribution claim gate，可以先做 `{project} contribution claim consolidation` 的 rolling / scoped 版；未完成 flows 要標為待回填，不能宣稱 final。
- 新 project 只有 Step 1 時，下一步必須是 Step 2；沒有 `step2-flow-comparison.md` 或等價 Step 2 文件時，不得直接建議或建立某 flow Step 3，除非 Nick 明確指定跳過 Step 2。
- 多 module / monorepo / 多 service 專案，Step 1 / Step 2 必須先整理 root module、submodule、service instance、tooling / config 邊界，並比較候選 flow 會跨哪些 module。這不是 class summary，而是避免跳過架構邊界。
- 單條 flow 做到 Step 5 只代表該 flow 完成，不代表整個 project 完成；下一步要先回同 project 的 candidate ranking 選下一條未完成 flow，不要自行跨 project。
- `senior-owner-playbook/01~20` 是工具箱文件編號，不是 flow Step；flow Step 固定只有 Step 1~5。
- 若 Nick 說「你來問我問題」、「我答覆你評估」、「直接教學」、「可深度詳細教」、「代碼演示」、「結構圖」等，先讀 `19-interview-coaching-question-bank.md`，進入 Interview Coaching Track。AI 每輪只問 3-5 題，依回答評分、追問、教學或打磨口說；不要建立問答流水帳，只回填能力判斷、補強主題與可用素材。
- 「深掃」要標示深度：Level 1 Flow 掃描、Level 2 Flow 深掃、Level 3 極限深掃。Nick 明確要求極限深度時，要逐 module、逐檔、逐相關 commit diff 追原因與收斂。
- AI 要主動判斷本次該用哪個深掃等級，並給 Nick 建議；不是每次都等 Nick 指定。
- 小型 / 低風險改檔可以輕量自查後直接 commit；重大 / 實質改檔必須完整全掃確認後 commit；commit 前仍須做 Relationship Check，確認權威關聯檔是否需同步；並遵守多 session / staging area 防污染規則，確認沒有非本輪 staged 檔案。若需要 push，AI 必須直接執行 `git push` 觸發 approval 視窗，不要只用文字回覆本地已提交、等待 Nick 另外要求推送。

## 0. 開新對話時的總提示詞

```text
你正在維護 /Users/nick/Git/nick/nick-vault。

這不是程式專案，是 Nick 的 Senior / Owner 學習與履歷整理庫。

請先讀：
- AGENTS.md
- senior-owner-playbook/README.md
- senior-owner-playbook/00-operating-rules.md
- senior-owner-playbook/07-core-positioning.md
- senior-owner-playbook/03-flow-learning-package-template.md

規則：
- 只能動 nick-vault。
- 公司專案只能讀，不能改。
- 掃公司 code 前先 fetch remote refs 確認最新性；只允許更新 remote refs，不自動 pull / merge / checkout / rebase 或改公司 repo 工作樹。
- `archive/` 目前已清空，只保留 `.gitkeep`；不再當必要參考來源。
- 每次任務開始前先判斷模式：輕量問答 / 快速狀態不全量掃；Step / flow / claim / Completeness Audit 才自動重讀 KB、既有 project 文件與相關 code repo 最新狀態，不要等 Nick 說「重讀」。
- 新內容要重新整理、去重、結合、優化，不要複製舊檔。
- 不要產生 code。
- 不要寫 secret、token、內網 IP、production URL、客戶資料。
- 所有履歷說法要保守，沒有證據不要寫主導、獨立完成、改善百分比。
- 每次完成後，若任務尚未收口，請自動給下一步建議，只推薦一件最值得做的事，並說明是否會更新履歷、是否需要 commit / push。若已收斂且 Nick 未指定下一件事，請回報「沒有預設下一步，可以自由提問或彈性指定」。
- 只有真的需要 Nick 複製下一步時，才把 prompt 放成 fenced code block，格式固定為 ` ```text ... ``` `，code block 內只放一行短 prompt。收斂 / 自由提問狀態不要輸出 prompt。
- 如果本次是小型 / 低風險改檔，請輕量自查後直接 commit；如果本次是重大 / 實質改檔，請完成後自行全掃確認：重讀已改檔案、重讀受影響規則、檢查規則衝突、做 Relationship Check、跑 `git diff --check`、確認沒有 secret / 誇大 / 非預期檔案。Relationship Check 只強制權威檔；動態 / 衍生檔只在升級成正式素材或與權威檔衝突時處理。確認通過後自動 commit；commit 前仍須檢查 `git diff --cached --name-only`，不得混入其他 session / 其他 project 檔案。若本輪需要 push，直接執行 `git push` 觸發 approval 視窗，不要只用文字請 Nick approval。
- 每次分析都要在 evidence 寫明掃描範圍：主分支、近期分支、相關 code path、相關後端 / 下游 repo 是否已看；未看就明確標未看。
- 每份 flow.md 要先用白話與圖讓人看懂，再進資深分析；如果是 PHP / ThinkPHP、後台、前端或 BI 專案，必須轉成 Nick 熟悉的後端分層語言。
- 如果 Nick 說「深掃」，至少使用 Level 2；如果 Nick 說「極限深度 / 逐檔逐行 / 每個 commit diff」，使用 Level 3，並分批完成。
- 如果 Nick 沒說深度，請依任務主動建議 Level 1 / 2 / 3；若不建議 Level 3，要說明原因。

目標：
把專案 code、`projects/` 既有整理與 KB 素材，整理成 Senior Java Backend / Platform Backend / System Owner 可讀、可面試、可轉履歷的學習資料。
```

## 0.1 模式化自動重讀 Checklist

```text
開始前先判斷任務模式，不需要 Nick 另外提醒。

輕量問答：
- 只讀必要入口或已知狀態。
- 不掃 code / git log / projects 全量。

快速狀態：
- README.md
- senior-owner-playbook/06-todo.md
- senior-owner-playbook/01-senior-owner-flow-inventory.md
- 必要時看 git status。

中量維護：
- AGENTS.md
- senior-owner-playbook/00-operating-rules.md
- senior-owner-playbook/09-ai-prompt-manual.md
- 受影響權威檔
- Relationship Check
- git diff --check

以下 Checklist 只適用 project / flow / Step / claim / Completeness Audit 等重度任務：

KB:
- AGENTS.md
- senior-owner-playbook/00-operating-rules.md
- senior-owner-playbook/09-ai-prompt-manual.md
- senior-owner-playbook/03-flow-learning-package-template.md

Vault:
- projects/{domain}/{project}/README.md
- projects/{domain}/{project}/step*.md
- projects/{domain}/{project}/flows/{flow-name}/flow.md
- projects/{domain}/{project}/flows/{flow-name}/career-interview.md
- projects/{domain}/{project}/flows/{flow-name}/materials/*.md
- 若既有 flow 是舊平鋪格式，也要讀同層 `evidence.md` / `interview.md` / `claim-boundary.md` / `decision-notes.md`，並標註待遷移

Code repo:
- 是否已 fetch remote refs
- 目前 branch
- local HEAD / remote HEAD / ahead-behind 狀態
- 遠端 / 近期 branch 清單
- 近期 git log
- path-specific git log
- route / controller / service / repository / model / config / job / consumer / admin page

輸出時要寫清楚：
- 已重讀什麼
- 未重讀什麼
- 未重讀原因
- 既有 Step / flow 狀態：可沿用 / 需補 evidence / 需重整 / 應暫停往下
```

## 0.2 每次任務的自動維護 Checklist

```text
完成後請自動判斷是否需要維護：

- project README 是否要更新？
- architecture-map / integration-map 是否需要最小補齊，用來定位 flow？
- Step 文件是否要更新？
- flow evidence 是否要補掃描範圍？
- claim-boundary 是否要補不能誇大的地方？
- decision-notes 是否要補技術硬底子與決策比較？
- 舊 Step / 舊 flow 是否不符合目前 KB，需要重整後再往下？
- senior-owner-playbook 的共用規則是否有新增通用原則？
- 是否需要更新 todo / 下一步？

如果不更新，請說明原因，例如：
- 本次只是比較，不改 flow。
- 本次只補 evidence，不動履歷。
- 本次是 project 局部規則，不需要改共用 KB。
```

## 0.2.1 改檔後自查 / commit / push approval Checklist

```text
如果本次是小型 / 低風險改檔：

1. 重讀改過的檔案片段。
2. 做 Relationship Check：判斷本輪事實變更是否影響權威檔；有影響就同步，無影響 final 要說已檢查、不需更新。
3. 跑 git diff --check。
4. 跑 git status --short，確認只動預期檔案。
5. 跑 git diff --cached --name-only，確認沒有非本輪 staged 檔案。
6. 檢查沒有 secret、token、internal IP、production URL、客戶資料。
7. 自查通過後直接 commit。

如果本次是重大 / 實質改檔：

1. 重讀本次改過的檔案。
2. 重讀受影響的共用規則 / prompt / README / index。
3. 檢查規則是否互相衝突。
4. 做 Relationship Check：確認受影響權威檔已同步；動態 / 衍生檔只在升級成正式素材或與權威檔衝突時處理。
5. 跑 git diff --check。
6. 跑 git status --short，確認只動預期檔案。
7. 跑 git diff --cached --name-only，確認沒有非本輪 staged 檔案。
8. 檢查沒有 secret、token、internal IP、production URL、客戶資料。
9. 檢查履歷 / 面試 claim 沒有誇大，且 evidence 層級清楚。
10. 自查通過後自動 commit。
11. commit 後回報 commit hash。
12. 若需要 push，直接執行 `git push` 觸發 approval 視窗，讓 Nick 按 Yes / No；未通過 approval 不推。
13. 不要在 final 只回覆本地已提交、等待 Nick 另外要求推送。除非 Nick 明確說「不要 push / 只 commit」。

Relationship Check 權威檔：

- 全域規則 / prompt：`AGENTS.md`、`00-operating-rules.md`、`09-ai-prompt-manual.md`。
- 全域索引 / 下一步：`projects/source-repo-inventory.md`、`projects/README.md`、`06-todo.md`。
- domain / project 入口：`projects/{domain}/README.md`、`projects/{domain}/{project}/README.md`、必要時的 architecture / integration / career files。
- 履歷 / 面試輸出：`05`、`08`、`04`、`17`。
- project / flow evidence：`contribution-claim-consolidation.md`、`flow.md`、`career-interview.md`、`materials/evidence.md`、`materials/claim-boundary.md`。

動態 / 衍生檔處理：

- 自動產物、匯出稿、暫存稿、練習稿、排序稿、一次性檢查輸出，不要求每次即時同步。
- 只有被升級成正式入口 / 履歷 / 面試素材，或與權威檔產生誤導衝突時才整理。
- cache、log、工具輸出、外部 repo 產物預設不納入 KB，除非 Nick 明確要求。

push 前只做乾淨確認：

- `git status`
- `git diff --cached --name-only`
- `git diff --check`
- 確認 commit 前的 Relationship Check 已收口；不要等 push 後才做關聯補救。

多 session 防污染補充：

- 日常模式預設只在 `main` 開發，且同一時間只允許一個 session 具備改檔 / stage / commit 權限。
- 其他 session 若存在，只能只讀查詢、review 或討論，不得改檔、stage、commit 或切 branch。
- 只有在 Nick 明確需要並行整理不同 project / submodule，且每個 session 有獨立 worktree 時，才使用 project / submodule branch。
- 禁止多 session 共用同一 worktree 互相切 branch；同一 branch / worktree 同一時間只能有一個 session 負責改檔與 commit。
- 禁止在髒工作樹使用 `git add .`。
- 若 `git diff --cached --name-only` 出現非本輪檔案，AI 不得 commit；先回報 Nick，等待 reset / unstage / 明確指示。
- 若 Nick 說「先不動其他」，AI 不得整理其他 session 的 index、不得 reset、不得 unstage 非本輪內容。
- `main` 是共用 KB 的唯一正式來源、日常開發線與穩定整合線。例外使用 project / submodule branch 時，開工前、開始新 Step 前、準備 commit 前，要同步最新 `main` 以取得最新 KB。
- KB 更新可以短暫用 `codex/kb-rules` 或同類 branch 隔離，但自查通過後應優先合回 `main`；不要讓 KB 長期只存在某個 project branch。
- 合回 `main` 前必須確認該 project / submodule 的 Step / flow 已形成可讀閉環、README / evidence / claim boundary / 共用索引已同步、自查通過，且 staged 內容沒有混入其他 session。
```

## 0.3 舊文件狀態判斷提示詞

```text
請先檢查既有 Step / flow 文件是否符合目前 KB。

請輸出：

1. 既有文件清單
2. 每份狀態：
   - 可沿用
   - 需補 evidence
   - 需重整
   - 應暫停往下
3. 原因：
   - 是否有掃描等級
   - 是否有已掃 / 未掃 / 待確認
   - 是否有 branch / log / path-specific history
   - 是否把後台入口誇大成完整後端 flow
   - 是否有履歷 claim 風險
4. 下一步只推薦一件事。

如果上一個 Step 不乾淨，請不要直接建議下一個 Step。
請先建議重整上一個 Step 或補 evidence。

如果 project 只有 contribution-claim-consolidation.md，沒有 Step 1 / Step 2 / flows，請明確寫：
- Career Track：已完成 / rolling / scoped
- Flow Track：未建立，不可宣稱 flow 完整
- 下一步只是候選缺口；除非 Nick 明確下 Step，不要自動開工
```

## 1. Step 1：找 Flow

用途：剛開始掃一個專案時使用，不要直接深挖。

```text
請先做重複 flow 檢查：

檢查位置：
- projects/**/flows/
- senior-owner-playbook/01-senior-owner-flow-inventory.md
- senior-owner-playbook/04-interview-casebook.md

如果發現類似或同名 flow，請先回報：
- 已讀過的位置
- 完成狀態：只有 Step 1 / 已有 flow.md / 已有 materials/evidence.md 或舊格式 evidence.md / 已轉面試 case / 已更新履歷
- 是否建議重讀
- 建議原因：code 有新分支、evidence 不足、面試案例不足、履歷尚未更新，或不建議重讀

如果沒有讀過，再繼續下面的 Step 1。

請深掃這個專案，但不要平均整理所有 class。

目標是找出 Top 3-5 條最有 Senior / Owner 價值的 production flow。

請優先找：
- money movement
- payment callback
- wallet / ledger
- betting / settlement / rollback
- Kafka / MQ / async
- retry / compensation
- reconciliation
- high traffic API
- RBAC / admin operation
- deployment / observability

請輸出：

1. 專案定位
2. 核心入口
   - HTTP API
   - gRPC / RPC
   - Kafka / MQ consumer
   - scheduled job
   - callback / notify
   - admin operation
3. Top 3-5 candidate flows
   - flow 名稱
   - 中文名稱
   - 為什麼重要
   - production 風險
   - 已確認 evidence
   - 推測
   - 待確認
4. 建議第一條深挖 flow
5. 下一步要讀的 code path
6. 本次掃描範圍與未掃範圍
   - 已看分支
   - 未看分支
   - 已看 repo
   - 相關但未看 repo
   - 只作入口參考、不作履歷 claim 的部分

請只整理跟 Senior / Owner 有關的 flow，不要寫 class summary。
```

## 2. Step 2：技術點與風險比較

用途：Step 1 後，還沒決定先做哪條 flow 時使用。

```text
請根據 Step 1 的 candidate flows，做 Senior / Owner 技術點比較。

如果這是 multi-module / monorepo / 多 service 專案，請先列出：
- root module / submodule / service instance / tooling / config 邊界
- 哪些是 runtime service，哪些是 common library 或離線工具
- 每條候選 flow 橫跨哪些 module / upstream / downstream

比較維度：
- data consistency
- transaction boundary
- state transition
- idempotency
- retry / compensation
- cache consistency
- Kafka / MQ reliability
- downstream timeout / callback
- observability
- manual recovery
- interview value
- resume value

請輸出：

1. flow ranking
2. 每條 flow 的核心風險
3. 哪條最適合先做成 case study
4. 為什麼
5. 哪些只能算推測
6. 下一步要補的 evidence
7. 哪些 flow 只看到後台 / 前端 / BI 入口，尚未看到後端實作，因此不適合直接寫履歷
```

## 3. Step 3：單條 Flow 深挖

用途：選定一條 flow 後使用。

```text
請先做重複 flow 檢查：

檢查位置：
- projects/**/flows/{flow-name}/
- projects/**/flows/*{相近名稱}*
- senior-owner-playbook/04-interview-casebook.md
- senior-owner-playbook/05-resume-master-zh.md

如果已經做過，請先回報：
- 已有檔案位置
- 已完成哪些內容：flow.md / materials/evidence.md 或舊格式 evidence.md / career-interview.md / 面試案例 / 履歷 bullet
- 缺什麼
- 是否建議重讀

只有在以下情況才繼續深挖：
- 既有 evidence 不完整
- code 有新分支或新版本
- 舊分析太粗
- 尚未轉面試 case
- 尚未建立履歷邊界
- Nick 明確要求重做

請只深挖這一條 flow：{flow-name}

不要擴散到其他功能。

深掃等級：
- 預設使用 Level 2 Flow 深掃。
- 如果 Nick 明確要求極限深度，使用 Level 3 極限深掃。

請依 senior-owner-playbook/03-flow-learning-package-template.md 產出完整學習包。

必須包含：
- 閱讀定位與 evidence 層級
- 白話導讀
- 初中階 Code 分層對照
- 最小架構圖
- 正常流程圖
- 正常流程逐步說明
- business goal
- system position
- entry point
- full execution path
- DB / Redis / MQ / external API
- state transition
- failure window
- retry / compensation / reconciliation
- owner decision
- interview 3 分鐘講法
- resume conservative bullet
- claim boundary
- evidence
- flow-specific teaching notes（只有本 flow 需要補 SQL / Spring / Kafka / Redis / transaction 等教學時才建立）
- claim source label：真實開發過 / 專案存在 / 分析素材 / 外部案例 / 待確認
- related files
- 本次實際掃描範圍
- 未掃描範圍與原因
- 相關但尚未讀到的後端 / 下游 repo

每個重要判斷都要標示：
- 已確認
- 推測
- 待確認

禁止：
- 腦補
- 誇大 Nick 實際貢獻
- 寫 secret / token / internal IP / production URL
- 把 class summary 當成 flow analysis
- 把 teaching-notes 寫成泛用技術大全；只能補本 flow 直接相關的 3-5 個技術點
- 直接跳進 failure / consistency，卻沒有先寫清楚這條 flow 是什麼功能、誰用、入口、Controller / Service / Model / SQL / Redis / MQ / Log 對照

完成後請自動補：
- 目前這條 flow 完成到哪裡
- 下一步只推薦一件事；如果 Step 3 已乾淨且沒有履歷 / 自傳 / contribution claim gate 風險，預設推薦 Step 4
- 為什麼現在做它
- 會更新哪些檔案
- 是否會更新履歷；預設不更新
- 是否需要 commit / push；小型 / 低風險改檔輕量自查後 commit，重大 / 實質改檔全掃確認後 commit；若需要 push，直接觸發 `git push` approval 視窗
- 若這條 flow 目前只掃到後台 / 前端，請優先建議去讀真正後端 / 下游 code，而不是硬轉履歷
```

## 補充 A：Decision Notes 技術硬底子與決策比較

用途：已經看懂一條 flow 後，補足 Senior / Owner 需要的技術判斷力。

注意：這是 Step 3 的補充文件，不是新的 Step。除非 Nick 明確要求補 `materials/decision-notes.md` 或舊格式 `decision-notes.md`，否則不要把它當成「下一步」來打斷 Step 4。

```text
請針對這條 flow 補 decision-notes.md。

請先重讀：
- flow.md
- career-interview.md
- materials/evidence.md
- materials/interview.md
- materials/claim-boundary.md
- 若是舊格式，讀同層舊檔並標註待遷移
- 相關 code path

請不要重寫 flow。
請不要更新履歷。
請不要寫成教科書大全。

請只補和本 flow 直接相關的技術硬底子，例如：
- transaction boundary
- consistency
- idempotency
- retry / compensation
- DB index / lock
- Redis cache / projection
- Kafka / MQ reliability
- observability
- rollback / recovery
- 技術選型差異與 trade-off

請輸出：
1. 本 flow 牽涉哪些硬底子
2. 專案 code 已確認 evidence
3. 專案未確認
4. 外部通用模式，必須標明不是專案事實
5. Option A / Option B 技術比較
6. Senior / Owner 會怎麼判斷
7. 面試追問與回答

請輸出到：
projects/{domain}/{project}/flows/{flow-name}/materials/decision-notes.md
```

## 補充 A2：Teaching Notes Flow 後補教學

用途：Nick 已經看懂一條 flow，但卡在 SQL / Spring / Kafka / Redis / transaction 等基本功時使用。

注意：這是 Step 3 的補充文件，不是新的 Step。它服務本 flow 的口說與追問，不取代 `flow.md`、不取代 `decision-notes.md`、也不建立泛用題庫。

```text
請針對這條 flow 補 teaching-notes.md。

請先重讀：
- flow.md
- career-interview.md
- materials/evidence.md
- materials/claim-boundary.md
- materials/decision-notes.md（若存在）
- 相關 code path

請不要重寫 flow。
請不要更新履歷。
請不要寫成 SQL / Spring / Kafka / Redis 完整課本。

請只補本 flow 直接相關的 3-5 個技術點，例如：
- SQL index / lock / EXPLAIN / slow query
- Spring transaction / AOP proxy / rollback
- Kafka / MQ retry / duplicate consume / ordering / offset
- Redis cache / lock / consistency
- idempotency / retry / compensation

每個技術點請輸出：
1. 對應 code path / method / SQL / topic / key
2. 為什麼本 flow 需要懂它
3. production 可能壞在哪
4. Nick 面試怎麼白話講
5. 最小補強範圍
6. 證據層級：local code-backed / 外部通用模式 / 待確認

請輸出到：
projects/{domain}/{project}/flows/{flow-name}/materials/teaching-notes.md
```

## 補充 B：Architecture Map 大專案 / 子專案地圖

用途：不知道 repo 關係、子專案責任、flow 入口時使用。地圖只做到能定位 flow，不要取代 flow 深挖。

注意：這是輔助工具，不是 flow 主線。除非 Nick 明確要求地圖，不要自行插入到 Step 1-5 之間。

```text
請建立最小可用的大專案 / 子專案地圖。

請深掃：
- /Users/nick/Git/{domain 或 project}
- nick-vault 既有 projects/{domain}
- senior-owner-playbook 相關 KB

只能動 nick-vault。
公司專案只能讀，不能改。
不要寫 secret、token、內網 IP、production URL、客戶資料。

請輸出：
1. domain / project 定位
2. repo / module 清單與中文名稱
3. repo / module 類型
4. 上下游關係
5. 核心 production flows 候選
6. 已確認 / 推測 / 待確認
7. 哪些只是後台 / 前端 / deploy / workspace，不可誇大
8. 下一步只推薦一條 flow

請輸出到：
- projects/{domain}/architecture-map.md
- projects/{domain}/integration-map.md
或
- projects/{domain}/{project}/architecture-map.md
```

## 補充 C：Level 3 極限深掃提示詞

```text
請對這條 flow 做 Level 3 極限深掃。

要求：
- 逐 module 建立掃描清單。
- 逐檔逐行讀與 flow 有關的 code。
- 每個相關 commit diff 都要看。
- 每個重要 commit 要整理：
  - commit hash
  - 修改檔案
  - 修改內容
  - 從 diff 推測的 bug / 需求
  - 對 flow、資料狀態、failure window 的影響
  - 是否有 revert / follow-up / 收斂 commit
- 比對主分支與近期相關分支。
- 查 route / controller / service / repository / model / config / job / consumer / MQ / Redis / DB / external API / log / audit。
- 沒看完就標未掃，不要寫完整。
- 不知道原因就寫「從 diff 推測」，不要當成事實。

輸出：
1. 已掃 module 清單
2. 未掃 module 清單
3. commit timeline
4. diff-backed flow changes
5. failure / consistency matrix
6. 待確認問題
7. 下一批只推薦一件事
```

## 補充 D：AI 自動判斷深掃等級

```text
在開始前請先判斷本次掃描等級：

- Level 1：只找 candidate flows。
- Level 2：單條 flow 深挖，建立 flow / evidence / interview / claim-boundary。
- Level 3：要追 bug history、每個相關 commit diff、逐檔逐行、準備強面試或履歷 evidence。

請輸出：
1. 建議等級
2. 為什麼
3. 本次會掃什麼
4. 本次不掃什麼
5. 如果需要 Level 3，如何分批

如果你判斷不值得 Level 3，請直接說，不要硬湊。
```

## 4. Step 4：轉面試 Case

用途：flow 已經深挖後，把它變成可講案例。

```text
請把這條 flow 轉成 Senior Backend 面試 case study。

請輸出：

1. 30 秒摘要
2. 3 分鐘版本
3. 面試官可能追問
4. 我應該怎麼回答
5. 可以展現的 Senior 能力
6. 不能誇大的地方
7. 如果被問「這是你主導的嗎？」要怎麼保守回答
8. 對應履歷 bullet

語氣要真實，不要像背稿。

請輸出到：
- `projects/{domain}/{project}/flows/{flow-name}/career-interview.md`
- `projects/{domain}/{project}/flows/{flow-name}/materials/interview.md`

每條可用說法都要標註證據層級；沒有 Nick 本人 evidence 時，不得寫成真實開發成果。
```

## 5. Step 5：單條 flow claim gate

用途：檢查單條 flow 是否可作履歷 / 自傳素材，但不直接代表整個 project 履歷結論。

重要：

- Step 5 是 Flow Track 的最後一站，只判斷單條 flow。
- 正式 05 / 08 履歷自傳屬於 Career Track，原則上必須先有 project contribution claim consolidation；rolling consolidation 的保守結論可以先支援近期投遞，final consolidation 之後再校正。
- 單條 flow Step 5 可以輸出「可放履歷 / 可面試講 / 不可誇大」的 evidence，供 project consolidation 使用。
- 若 project 尚未 consolidation，不得只憑單條 flow Step 5 直接更新 05 / 08。

```text
請根據已完成的 flow evidence，檢查是否值得更新：
- senior-owner-playbook/05-resume-master-zh.md
- senior-owner-playbook/08-application-autobiography-zh.md

規則：
- 先判斷這是單條 flow Step 5 還是 project-level Career Track；單條 flow Step 5 只輸出 claim gate 與 consolidation 建議，不直接更新正式 05 / 08。
- 沒有 evidence 不更新。
- Nick 本人確認是 evidence，但要標清楚；本人確認可以支撐「參與 / 維護 / 開發」，不自動支撐「主導 / 全權 owner / 改善 X%」。
- 不寫主導、獨立完成、改善 X%，除非有明確證據。
- 可以寫參與、維護、分析、梳理、協助、優化、提出改善方向。
- 履歷只補高價值且能面試講清楚的內容。
- 若是最終更新 05 / 08，必須先深掃 code 主分支、近期分支、path-specific history、重要 diff，以及 `projects/` / KB 所有履歷自傳素材。`archive/` 目前已清空，不再列為必要來源。
- 若 Nick 指出某 repo 是主力開發經驗，且要求履歷收斂，可以先做 rolling project-level contribution consolidation：掃全部 Nick / `10gt12nc` commits、branches、重要 diff、已完成 flow KB、已完成 flow evidence、本人確認內容與目前可讀的 project 文件，再分成「可放履歷：真實開發過」、「可面試講：code-backed / 分析過」、「不可誇大」。若本批代表 flows 未完成，標成待回填，不阻塞履歷線。
- 若 Nick 沒明確說「我做很多」，但 AI 已整理出高價值 code-backed flow，且下一步要碰履歷 / 自傳 / claim boundary，也可以先做 rolling consolidation。這是避免兩種錯誤：把分析成果誇大成 Nick 成果，或因單條 flow 缺 direct evidence 就低估整個 repo 經驗。
- final contribution consolidation 才要求 Step 2 定義的本批代表 flows 全部完成到 Step 5；rolling consolidation 不能只靠單條 flow 宣稱全 project 已完整深掃。它是掃 Nick / `10gt12nc` 的 commits、branches、重要 diff、本人確認、既有 flow evidence 與已完成 flow KB，先界定履歷 claim；未完成 flows 必須標成待回填。
- 每條履歷 claim 都要標註證據層級：真實開發過 / 專案存在 / 分析素材 / 待確認。

請先列：
1. 可補內容
2. 不建議補內容
3. 原因
4. 建議修改段落

確認後再改檔案。
```

## 6. 新增專案資料夾時的提示詞

```text
請在 nick-vault 外層建立或更新 projects/{domain}/{project-name}/。

這個資料夾只放整理後的新分析，不複製舊檔。

請建立：
- README.md
- flows/{flow-name}/flow.md
- flows/{flow-name}/career-interview.md
- flows/{flow-name}/materials/evidence.md
- flows/{flow-name}/materials/interview.md
- flows/{flow-name}/materials/claim-boundary.md
- career-interview.md

如果 flow 尚未完成，不要寫進履歷 master。
```

## 7. 每次結束前檢查

```text
請做收尾檢查：

1. 是否只動 nick-vault？
2. 是否沒有動公司專案？
3. 是否沒有新增 code？
4. 是否沒有 secret / token / internal IP / production URL？
5. 是否有把推測與已確認分開？
6. 是否有避免履歷誇大？
7. 是否有檢查這條 flow 以前是否讀過？
8. 是否有更新 README 或 todo？
9. 是否已自行全掃確認？
10. 是否已做 Relationship Check，確認權威關聯檔已同步或不需更新？
11. 是否已依改動大小完成輕量自查或全掃確認，且 commit 前確認沒有非本輪 staged 檔案？
12. 若需要 push，是否已直接觸發 `git push` approval 視窗，而不是只停在本地文字回報？
13. 下一步只推薦一件事。
```

## 8. 自動下一步 / 自由提問格式

```text
下一步建議：{只推薦一件事}

規則：
- 不自行創造新 Step。
- 如果 Step 1 完成，下一步是 Step 2。
- 如果 Step 1 完成但 Step 2 文件不存在，下一步只能是 Step 2，不能直接跳 Step 3 / 建 flow folder。
- 如果 Step 2 完成，下一步是 Step 3。
- 如果 project 尚未做 contribution consolidation，且現在牽涉履歷 / 自傳 / claim / 真實開發經驗，下一步可以是 `{project} contribution claim consolidation` rolling / scoped 版；未完成代表 flows 要列為待回填，之後再回 Step 2 ranking 補下一條代表 flow。
- 如果 Step 3 完成且文件乾淨，且沒有履歷 / 自傳 / contribution claim gate 風險，下一步是 Step 4。
- 如果 Step 4 完成，下一步才檢查 Step 5 單條 flow claim gate；若要更新 05 / 08，先走 project contribution claim consolidation。
- evidence / 下游 / decision-notes / 架構圖只能作為補充或待確認，不能取代 Step 主線。
- 如果 Nick 問「還剩多少 / 會不會一直做 / 是否有結束點」，先給收斂版回答：最小必做剩多少、可選加強剩多少、暫不建議做哪些、達標後轉投遞或面試練習。不要把所有 backlog 加總成壓力數字。
- 如果 Nick 明確要求 `KB-only` 或「專案先不下一步」，本段格式停用；不要輸出 project flow 建議 prompt。
- 如果已達收斂狀態，且 Nick 沒指定下一個任務，本段格式停用；改說「沒有預設下一步，可以自由提問或彈性指定」。可列出 2-4 個可問方向，但不得用 fenced prompt、不得暗示必做。
- 收斂狀態下可用小儀表板格式：必做收口 / 可選加強 / 暫不建議；可選加強最多 1-3 項，並標示廣度或深度，不要列成長 backlog。

原因：
- {為什麼現在最適合做這件事}

會更新：
- {預計更新的檔案或資料夾}

不會做：
- 不更新履歷，除非 evidence 足夠且 Nick 明確要求。
- 小型 / 低風險改檔輕量自查後 commit；重大 / 實質改檔全掃確認後 commit；commit 前必須做 Relationship Check，確認權威關聯檔已同步或不需更新，並確認 staged 內容沒有混入其他 session / 其他 project；若需要 push，直接觸發 `git push` approval 視窗。

建議提示詞：
{Nick 下一句可以直接貼的短 prompt}
```

輸出時必須另外提供一個可直接複製的短 prompt code block：

```text
{Nick 下一句可以直接貼的短 prompt}
```

這個 code block 內只放 prompt，不放原因、說明或多個選項。
