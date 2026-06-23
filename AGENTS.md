# AI 整理規則

本專案是 `nick-vault`，Nick 的個人資源整理庫。這不是程式專案，不需要複雜的工程式 notes/logs/analysis 結構。

## 目標

- 目前主入口是 `senior-owner-playbook/`。
- 目標是整理 Nick 的 Senior Java Backend / Platform Backend / System Owner 學習資料、專案 flow、面試案例與履歷素材。
- `archive/` 已依 Nick 指示清空；之後不再把它當作必要參考來源。
- 新內容必須重新整理、去重、結合、優化，不要直接複製舊檔。
- 使用繁體中文作為主要說明語言。
- 保留清楚索引，讓下一個 AI 能快速知道資料在哪裡。

## 目錄規則

- `senior-owner-playbook/`：放跨專案通用的方法論、提示詞、學習路線、面試案例、唯一履歷 master 與投遞用自傳。
- `projects/`：未來放各專案整理後的新分析，例如 `projects/iwin/payment/flows/{flow}/`。
- `archive/`：目前只保留空資料夾用 `.gitkeep` 佔位；不要再新增舊資料或把它當長期資料位置，除非 Nick 明確要求。
- 不要新增 `ai-notes/`、`docs/`、`.claude/`、`.codex/`、`resources/` 作為長期資料位置。
- 密鑰與 token 只能放本機 `.env`，不得寫入 Markdown 或 commit。

## 近期排查防錯紀錄

- Token / 掃描成本優先用「模式分級」控管，不是每次都全套深掃：
  - `輕量問答`：概念討論、焦慮校正、閱讀策略、一般下一步；只讀必要入口或已知狀態，不掃 code、不跑 git history、不改檔。
  - `中量維護`：KB 規則、索引、todo、閱讀順序、小型一致性修正；讀 `AGENTS.md`、`00`、`09` 與受影響檔案，做 Relationship Check。
  - `重度深掃`：flow Step、contribution consolidation、履歷 / 自傳正式 claim、Completeness Audit、Nick 明確要求逐行 / 極限深掃；才讀 code、git log、重要 diff、path-specific history。
  - `快速狀態`：Nick 問「下一步 / 還要幹嘛 / 都好了嗎」且目前收斂時，只查 `README / 06 / 01 / git status` 或等價狀態入口，不得重新全掃全部 KB / projects。
- 模式分級不降低安全標準：涉及履歷 claim、flow evidence、公司 code、push / commit 或跨檔規則變更時，必須升級到中量或重度；但純問答不得用重度流程製造 token 浪費與焦慮 backlog。
- KB 優化採觸發式維護，不主動全面重構。只有當文件造成誤判、重複讀取、重複踩坑、交接失敗、履歷 / flow claim 風險，或 Nick 明確要求調整時才改；若只是「還能更完整 / 還能更漂亮」，先停止，回到投遞、讀 flow、面試練習或當下任務。
- 「不要維護流水帳」的意思是：不要建立或保留「今天做什麼、昨天做什麼、某次操作紀錄、records、operation-log、work-report」這類時間序列工作日誌。不是要改掉現有 `flow.md / evidence.md / interview.md / claim-boundary.md` 的分析結構。
- `flow.md` 就是單條 flow 的研究分析報告。不要另創 `research-analysis-report.md`、額外 README 或重複總覽檔，除非 Nick 明確要求。
- `flow.md` 必須先讓初階 / 中階讀者看懂這條 flow 在做什麼，再進 Senior / Owner 分析。前半必須有白話導讀、Code 分層對照、最小架構圖、正常流程圖與逐步說明；後半才寫 consistency、failure window、owner decision、interview / resume boundary。
- `materials/evidence.md`、`materials/decision-notes.md`、`materials/interview.md`、`materials/claim-boundary.md` 是附錄 / 輔助文件，不是要 Nick 自己拼成報告；舊平鋪格式的同名檔先視為待遷移舊格式。
- `materials/teaching-notes.md` 是可選附錄，目前預設先不補、也不批量補。只有 Nick 讀某條 flow 真的卡住、面試追問暴露基本功缺口、或 Nick 明確要求補該 flow 教學時才建立。教學必須從該 flow 的 code path 長出來，每條 flow 最多補 3-5 個技術點；沒有 local code evidence 的內容標成 `外部通用模式 / non-local`，不得寫成泛用課本或題庫。
- 新建或重整後的 flow 資料夾，預設只讓 Nick 直接讀 `flow.md` 與該 flow 的 `career-interview.md`；其他 evidence、decision、interview、claim 邊界要收在 `materials/`，避免主閱讀面混亂。既有 `iwin` 舊資料夾這輪先不搬，等 Nick 明確要求再遷移。
- flow、履歷、自傳與面試素材都要標註證據層級：`真實開發過`、`專案存在 / code-backed`、`分析素材 / learning-only`、`外部案例 / non-local`。沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認，不得標成真實開發過。
- Nick 本人明確說「我做過 / 我開發很多 / 這是我負責或參與的」時，屬於 `本人確認` evidence，不能被 AI 當成沒有 evidence。AI 必須把它和 commit / MR / ticket 一起納入 claim 判斷，但仍要保守標示為「本人確認，待 commit / ticket 補強」或「本人確認 + code-backed」，不得反過來把 Nick 的經驗抹掉。
- Nick 已確認 `arnold` 是主管帳號，不是 Nick 本人帳號。任何 `arnold` commits / branch / docs 只能作主管 / 團隊 context、current behavior context、learning / supporting evidence；不得當成 Nick direct evidence，也不得留下「待確認是否 Nick 帳號」的升級空間。
- Flow 線與履歷 / 自傳線必須分開但互相回填：Flow 線是 `Step 1 -> Step 2 -> 單條 flow Step 3 -> Step 4 -> Step 5`，負責系統理解、深掃規範、面試 case 與單條 flow claim gate；履歷 / 自傳線是 `project contribution claim consolidation -> 05 / 08`，負責 project-level 經驗包裝。`05-resume-master-zh.md` / `08-application-autobiography-zh.md` 原則上只吃 project-level consolidation 結果，不直接吃單條 flow Step 5 結論。單條 flow Step 5 不能代表整個 project 履歷結論。若 Nick 要先補履歷或 contribution consolidation，可以先做 rolling / scoped project consolidation，不必等待所有代表 flows 都 Step 5；但文件必須標明掃描範圍、已完成 flow、未完成 flow、可放履歷 / 可面試講 / 不可誇大的邊界。之後 flow Step 照舊進行，新的 flow evidence 要回填 consolidation、05 / 08 與 claim boundary。
- 反向也成立：`project contribution claim consolidation` 完成不代表 Flow Track 完成。AI 不得因某 project 已完成 consolidation、05 / 08 已回填或履歷 claim 已可用，就回答「flow 完整 / 全掃沒問題」。判斷完整度時必須分開列出：
  - Flow Track：是否有 `step1-candidate-flows.md`、`step2-flow-comparison.md`、`flows/{flow}/flow.md`、`career-interview.md`、`materials/evidence.md` / `claim-boundary.md`。
  - Career Track：是否有 `contribution-claim-consolidation.md`、是否已回填 `05 / 08 / 04 / 17`。
  - Domain Map：是否有 `architecture-map.md` / `integration-map.md`，以及是否只是可選架構補強。
  若 Flow Track 缺 Step 1 / Step 2 或完全沒有 `flows/`，必須明確說「履歷線已收斂，但 flow 線未建立 / 未完整」，不能用「已完成 contribution consolidation」帶過。
- 若 Nick 要「就目前所有 Contribution Claim Consolidation 先匯總 05 / 08，讓我能寫履歷」，AI 要做 `rolling resume package`：彙總目前所有 project-level consolidation，更新 `05-resume-master-zh.md` 的可直接使用履歷版與 `08-application-autobiography-zh.md` 的投遞版；明確標示這是目前可投遞草稿，不是 final consolidation。`05` 是履歷 / 自傳 / claim 母稿與證據池，可以較完整；`08` 是投遞用輸出版，必須維持 104 可貼欄位：工作經驗、專長、自傳、自我推薦。後續 flow / Step / 新 evidence 必須互相回填修正 05 / 08，不得因 flow 尚未全部完成而拒絕先產履歷稿。
- 若 Nick 暫時沒有特定職缺 JD，`08 / 17` 預設維持「通用 Senior Java Backend / Platform Backend 投遞版」，主軸是金流 / provider gateway / wallet / bet-settle / MQ / batch / legacy takeover。AI 不得每次都要求 Nick 貼 JD 才能繼續；只有 Nick 明確要投某個職缺、提供 JD、或要求客製時，才做 JD-specific 客製。沒有 JD 時，下一步應優先轉為通用版面試自我介紹、104 投遞欄位檢查、面試 case 練習或投遞準備。
- 參考 workspace 正確路徑：
  - `/Users/nick/Git/iwin/iwin-workspace`
  - `/Users/nick/Git/antplay/math-workspace`
  - `/Users/nick/Git/nick/test001_unity`
  - `/Users/nick/Git/nick/test001_RenPy`
- 參考 workspace 角色定位：
  - `nick-vault`：Senior / Owner 學習、flow evidence、履歷 / 面試 claim gate 的個人知識庫。
  - `math-workspace`：AI-assisted math 開發 / 驗證工作台；可學 Architect / Planner / Coder / Reviewer handoff、source / KB 分離、GDD / 相似包 / result validation 交叉驗證。
  - `test001_unity`：0 到 1 遊戲產品工作台；可學 Producer / Design / Engineering / Art / QA / Release 的角色分工、gate、scope cut 與「值得導入 / 只作參考 / 不採用 / 仍需驗證」分類。
  - `test001_RenPy`：個人 side project / Ren'Py 評估工作台；可學 AI-assisted product evaluation、scope cut、文件入口與 migration decision，不是公司專案 evidence。
  - `iwin-workspace`：大型既有系統復原 / 運維 / 跨 repo 關聯工作台；可學 source repo inventory、relation map、generated / curated 分離、唯讀 source cache、敏感資訊遮蔽與 cross-project reconstruction。
- `/Users/nick/Git/nick/*` 是 personal reference，只能作 AI workflow、side project strategy 或工作法參考，不得當公司專案 source、production evidence 或 Senior Backend 主履歷 claim。
- 參考其他 workspace 只能用來學防呆、索引、KB 治理、角色 gate、scope / non-goals、success criteria、evidence 分層與「不留流水帳」原則；不能直接照搬其開發型 docs / deploy / `.work` / 子 repo / GDD / RTP / Unity / k3s / JumpServer 規則到 `nick-vault`。
- `nick-vault` 可以吸收角色 lens，但只作思考與交付檢查，不新增部門式流程：`Producer / Scope Gate` 判斷是否該做與停止點；`Flow / Technical Reviewer` 檢查 code path、資料流與 failure；`Career Claim Reviewer` 檢查履歷可寫 / 可講 / 不可誇大；`KB Curator` 檢查索引、Relationship Check 與不留流水帳。
- `Karpathy-style` 在本 vault 的意思是：先想清楚再改、最小可行、只動必要範圍、每步有可驗證完成標準。它是所有中量 / 重度任務的行為底線，不是新增一套文件目錄或開發流程。
- `Agent Workflow` 只導入 Lite 版。非小事任務在心智上走四個 gate，不必產生實體 JSON 或流水帳：
  - `Scope Gate`：確認本輪要解決的問題、non-goals、是否真的值得改 KB / 掃 code / 開 flow。
  - `Evidence Gate`：確認本輪依據哪些 KB、source code、git history、Nick 本人確認；哪些仍是推測 / 待確認。
  - `Claim Gate`：確認內容能不能放履歷、能不能面試講、是不是只能當 learning-only；避免把 code-backed / 分析素材誇大成真實開發 owner。
  - `Review Gate`：交付前檢查是否最小修改、交付物類型是否清楚、未誇大履歷、未混入外部 workspace 規格、Relationship Check 是否收口。
- Lite 版補強三條：`ESCALATE` 是正確行為，不確定或上游規格矛盾時要停下來標示 / 回問；`files_in_scope` 是改檔圍牆，不能順手改 scope 外檔案；`success criteria` 要能被檢查，例如文件位置、diff check、Relationship Check、claim boundary 或可重現的讀法，而不是「感覺整理好了」。
- 只有當任務真的涉及 source code 修改、跨 repo 實作、測試策略、重構或安全修正時，才可參考 `math-workspace` 的完整 `Architect -> Planner -> Coder -> Reviewer` 模式；`nick-vault` 日常 flow / 履歷 / KB 任務預設使用 Lite 版。
- 規格不可隨意改。若只是「評估一下」，AI 只能提出建議與理由；未經 Nick 明確要求，不得改既有目錄規格、Step 主線、檔案責任或新增替代結構。
- 新 project 第一次完成 Step 1 後，下一步必須是 project-level Step 2：比較 candidate flows、技術點、風險、module / repo / service 邊界。不得在沒有 `step2-flow-comparison.md` 或等價 Step 2 文件時，直接建議或建立單條 flow Step 3，除非 Nick 明確指定跳過 Step 2。
- 多 module / multi repo / monorepo 類專案，Step 1 / Step 2 必須先整理 module / submodule / service instance / upstream-downstream 邊界。不能只挑一條看起來高價值的 flow 就跳過子模組地圖；也不能平均做 class summary。架構圖只作定位，Step 2 才決定哪條 flow 進 Step 3。
- 一條 flow 完成 Step 5 後，不代表整個 project 完成。若同 project 的 Step 1 / Step 2 還有未完成 candidate flows，下一步要回到同 project 選下一條 flow；不要自行跳到其他 project，除非 Nick 明確說要換專案。
- Domain / system map 不能插隊打斷已經進行中的單條 flow。若 Nick 已指定某 flow Step 3 / Step 4 / Step 5，或同一條 flow 已做到 Step 3 / Step 4 尚未收口，下一步要先把該 flow 做到 Step 5；大地圖只能在 active flow 收口後、Nick 明確要求總結、或 domain 已累積足夠代表 flows 時回補。
- `nick-vault` 的目標不是無限掃完所有 repo，而是對標 Senior Java Backend / Platform Backend 形成可投遞、可面試、可防追問的證據包。AI 回答「下一步 / 還剩多少 / 要不要繼續」時，必須分成 `必做收口`、`可選加強`、`暫不建議做`，不得把 backlog 包裝成永遠必做。
- Senior 對標結束點：3-5 個 project-level claim 可保守放履歷、8-10 條 production flow 能講 3 分鐘並抗追問、每條 claim 都分清真實開發 / code-backed / 不可誇大、`05 / 08 / 04 / 17` 與最新 claim 對齊。達到後應建議停止大規模整理，轉為投履歷、練面試、針對職缺補洞。
- Senior 面試準備度不要只用「最低能投」判斷，改用三段門檻：`中等可面`（3 條主力 case 能講 3 分鐘、有 evidence / claim boundary / 常見追問）、`穩過可抗追問`（5 條 case 覆蓋 payment、wallet / bet-settle、MQ / projection、partition / high-traffic data、rollout / observability，且能講 owner decision）、`完全對標 Senior / Platform`（8-10 條 production flow 可依 JD 切換，`05 / 08 / 04 / 17` 與 claim boundary 全部對齊）。回答下一步時要說目前屬於哪一段，不得再把低標當完成。
- side project 目前明確列為 `暫不做`。它有差異化價值，但不是 Senior Backend 投遞必要條件；不得把「雲端 demo / 開放面試官試玩 / 後台展示」包裝成新主線或資格證明。只有當 Nick 明確說要啟動 side project，才從 `20-game-backend-architecture-selection.md` 萃取最小 scope；平常只當面試架構問答素材與選型備忘。
- 面試準備比例固定以 `70 / 20 / 10` 收斂：70% 放 production case / system design / claim boundary；20% Java / SQL / transaction / Redis / MQ 基本判斷只做最小檢查表、遇到 case 或面試回饋再補；10% LeetCode / coding test 只作投遞前保險，不得變成新主線。
- AI 時代 coding 準備重點不是手刻所有題，而是能 review AI 產物是否能進 production：BigDecimal、transaction boundary、callback idempotency、Redis lock、SQL index、MQ retry、null / race condition、重複副作用等風險要能判斷。
- 面試題 / 複習包只能從主力 production case 長出來，不建立泛用 Java / SQL / LeetCode 300 題題庫。第一輪只圍繞 payment provider、wallet / bet-settle、Kafka / report projection 產 90 秒版、3 分鐘版、追問題庫、回答要點、誇大陷阱與 case-specific 基本功。`19-interview-coaching-question-bank.md` 可保留 14 個主題的診斷池，但實際優先練 30 題核心題；Provider Integration、Security / Auth、Real Troubleshooting、Architecture Evolution 是補強層，不得被包裝成全刷必做。
- 實際面試問答練習要回填 `senior-owner-playbook/interview-practice/`。它不是聊天逐字稿或流水帳；每輪只萃取題目、Nick 原答摘要、評分、會被追問打穿的點、修正版回答、下次追問與是否要回填 `04 / 19 / flow career-interview`。之後 Nick 說「以後都要記」時，預設就是記到這個資料夾，而不是新增分散 notes。
- 台灣轉職月份策略維護在 `17-salary-negotiation.md`：一般主旺季是 2-4 月，2026 因春節較晚與轉職季拉長可延到 4-5 月；9-10 月是第二波，11-1 月適合準備 / 卡位。月份只影響投遞節奏，不取代 case 準備與市場回饋。
- 當 Nick 問「flow 都完整嗎 / map 夠完整嗎 / 真的夠扛資深嗎 / 能不能 0 到 1 架完整系統 / 我是不是方向歪了」時，AI 要先處理收斂與焦慮，不得直接加新 backlog。回答必須區分：`履歷 / 面試證據包已足夠`、`全公司大系統不可能也不需要一人完整掌握`、`domain-level 大地圖若缺是可選架構補強，不是投遞前必做`。
- `System Owner` 在本 vault 的意思是能對「一條核心 production flow」理解結果、風險、failure window、補償、觀測與交接邊界；不是一個人負責整個公司、完整金流、完整遊戲平台、完整 DevOps 或完整架構決策。履歷、面試與 KB 都不得把「能 owner flow」擴張成「全系統 owner」。
- 完整度分三層：`flow-level 完整`（單條 flow 可讀、可講、claim boundary 清楚）、`project-level 完整`（代表 flows + contribution consolidation 可支撐履歷 / 面試）、`domain-level 完整`（跨 project architecture / integration map）。目前 Senior 求職必做的是前兩層；第三層只在要練架構視角、面 Platform / Lead 候選、或 Nick 明確要求大地圖時補。
- 目前收斂策略：先完成必做收口，再視需要只補 2 個非 iwin project 的代表 flow；不要平均掃所有 repo。已完成或低價值 repo（官網、前端、workspace、mock、全部 legacy / 全部 math repo 平均深掃）預設列入暫不建議，除非 Nick 明確指定。
- `senior-owner-playbook/01~20` 是工具箱 / 規則 / 學習路線的文件編號，不是 flow 的 Step 1~20。flow Step 固定只有 Step 1~5。
- 小型 / 低風險改檔可以輕量自查後直接 commit，例如錯字、路徑修正、單句規則修正、索引同步、明顯不改語意的小補充。
- 重大 / 實質改檔必須自行再全掃確認一次：重讀已改檔案、檢查相關規則是否互相衝突、跑 `git diff --check`，並確認沒有改到公司專案、沒有 secret、沒有未標示的推測或履歷誇大。結構大改、Step 主線調整、履歷正式 claim 更新，若 Nick 沒明確要求，必須先問。
- 每次改檔後、commit 前必須做 `Relationship Check`，不是等 push 後才補救。先判斷本輪「事實變更」是什麼，再檢查是否影響權威檔：`projects/source-repo-inventory.md`、`projects/{domain}/README.md`、`projects/{domain}/{project}/README.md`、`senior-owner-playbook/06-todo.md`、`05-resume-master-zh.md`、`08-application-autobiography-zh.md`、`04-interview-casebook.md`、`17-salary-negotiation.md`、對應 `contribution-claim-consolidation.md`、`flow.md / career-interview.md / materials/claim-boundary.md`。有影響就同步修；無影響要在 final 說已檢查、不需更新。
- `Relationship Check` 只強制檢查權威檔；動態 / 衍生檔不要求每次即時同步，例如自動產物、匯出稿、暫存稿、練習稿、排序稿、一次性檢查輸出。只有當它被升級成正式閱讀入口、履歷 / 面試素材，或與權威檔產生會誤導下一輪 AI 的衝突時，才需要整理或回填。cache、log、工具輸出、外部 repo 產物預設不納入 KB，除非 Nick 明確要求。
- 當 Nick 要求「深掃全部 / 檢查所有 / flow 都完整嗎 / KB 跟 code 比對 / 有沒有漏」時，Relationship Check 要升級為 `Completeness Audit`：逐一檢查受影響 domain / project 是否同時具備 Flow Track、Career Track、Domain Map 的狀態標示；沒有 flow 文件的 project 要列為缺口，不得回答全都完整。這個 audit 可以先只更新 KB / todo，不代表已授權立即開工補 Step。
- push 前只做乾淨確認：`git status`、`git diff --cached --name-only`、`git diff --check`，確認 commit 前的關聯檢查已收口；不要把關聯檢查延到 push 後才做。
- 日常模式預設只在 `main` 開發，且同一時間只允許一個 session 具備改檔 / stage / commit 權限；其他 session 若存在，只能只讀查詢、review 或討論，不得改檔、stage、commit 或切 branch。`nick-vault` 是個人知識庫，優先保持連貫性與乾淨 history，不追求多 session 平行吞吐。
- 例外模式只有在 Nick 明確需要並行整理不同 project / submodule，且每個 session 都有獨立 worktree 時才使用 project / submodule branch，例如 `codex/iwin-app-bi`、`codex/iwin-game-job`、`codex/iwin-iwin-gameserver`。禁止多 session 共用同一 worktree 互相切 branch；同一 branch / worktree 同一時間只能有一個 session 負責改檔與 commit。
- 若暫時仍在共用工作樹，AI commit 前必須檢查 `git status --short`、`git diff --cached --name-only` 與 `git diff --cached`；只能精準 stage 本輪檔案，禁止 `git add .`。若發現非本輪 staged 檔案，不得 commit，必須先回報並等待 Nick 處理或明確指示。
- `main` 是共用 KB 的唯一正式來源與穩定整合線；project / submodule branch 開工前必須以最新 `main` 為底，長時間工作或開始新 Step 前要先 merge / rebase `main`，確保讀到最新 KB。KB 更新可以短暫用 `codex/kb-rules` 或同類 branch 隔離，但自查通過後應優先合回 `main`，不得讓 KB 長期只存在某個 project branch。
- project / submodule branch 合回 `main` 的時機是：該批 Step / flow 已完成到可讀閉環、自查通過、沒有混入其他 project staged 內容、Nick 沒要求暫停，且已確認不會污染履歷 claim 或共用索引。半成品、待確認 evidence、正在被另一 session 改的內容，不合回 `main`。
- 改檔自查通過後，AI 要自動 commit。若本輪需要推上 GitHub，AI 必須直接執行 `git push` 並用 approval 視窗讓 Nick 按 Yes / No；不得只用文字回覆本地已提交、等待 Nick 另外要求推送。不得未經 approval 視窗或 Nick 明確指示直接推送。

## Senior / Owner 原則

- 以下規則是全 vault 共用規則，適用所有 `projects/{domain}/{project}`、所有 flow、所有 Step；不是只適用 `app_bi`。
- 不要做 class summary。
- 不要平均整理所有功能。
- 優先整理 production flow。
- 每次只做一條 flow。
- 每次 Nick 下 `project stepN`、`某 flow Step N`、`下一步`、`繼續` 時，AI 必須先判斷本輪是輕量問答、中量維護、重度深掃或快速狀態，再依模式重讀本 vault KB、既有專案文件與相關 code 最新狀態；Nick 不需要另外提醒「重讀 KB / 重讀 code」，但 AI 也不得在輕量問題上默認全量深掃。
- 若升級到 project / flow / Step 重讀，至少包含：`AGENTS.md`、`senior-owner-playbook/00-operating-rules.md`、`senior-owner-playbook/09-ai-prompt-manual.md`、`senior-owner-playbook/03-flow-learning-package-template.md`、該 project 既有 README / Step 文件 / flow 文件、相關 code repo 的 branch / log / 關鍵入口。
- 掃公司 / 來源 code repo 前，必須先確認遠端 refs 是否最新：預設執行 `git fetch --all --prune` 或等效方式更新 remote refs，然後記錄 local HEAD、`origin/{branch}` HEAD、是否 ahead / behind。公司 repo 只能讀，不得自動 `pull`、merge、checkout、rebase 或改工作樹；若本機落後遠端，要標示「本機未更新 / 待 Nick 確認」，除非 Nick 明確同意才更新工作樹。
- 若公司 / 來源 repo 的 remote 是內網 GitLab 或當下網路不可達，`git fetch` 失敗一次後不要反覆重試；改用本地 refs / 本地工作樹繼續保守分析，並在 evidence 標示「fetch 失敗 / 未確認最新遠端 / 依本地 refs 判斷」。不要把內網 URL、IP 或敏感 remote 細節寫入 vault。
- 自動重讀後，AI 必須主動檢查既有 Step / flow 文件是否是舊規則、舊格式、舊 evidence 或不符合目前 KB；如果發現不一致，要先標出「需重整 / 需補 evidence / 可沿用」，不能等 Nick 追問。
- 如果既有 Step 已存在但品質不足，下一步建議必須優先指向「重整既有 Step」或「補齊舊文件邊界」，而不是直接跳到下一個 Step。
- 每次產出或更新後，AI 也要自動維護對應共用索引 / 規則 / project README / Step 文件的連動關係；如果判斷不該更新，需簡短說明原因。
- 每條 flow 要分清楚已確認、推測、待確認。
- 核心關注：money correctness、transaction consistency、state transition、idempotency、retry / compensation、reconciliation、observability、owner decision。
- 履歷與面試不得誇大。沒有 evidence 前，不寫主導、獨立完成、改善 X%、正式架構師或全權 owner。
- 「深掃」不是只 grep 幾個關鍵字；它至少要讀入口、主要 module、主要資料流、相關 commit log、path-specific history 與重要 diff。
- 如果 Nick 明確說「極限深度」、「逐檔逐行」、「每個 commit diff 都看」，AI 要改用極限深掃：逐 module、逐檔、逐重要 commit diff 追修改原因、bug context、風險與最後收斂結果。
- 若時間或範圍不適合一次做完極限深掃，必須明確切成批次，並標出已掃、未掃、下一批，不可假裝完成。
- AI 必須主動判斷本次任務需要 Level 1 / Level 2 / Level 3 哪種掃描深度，並告訴 Nick 建議理由。
- 若 Nick 沒指定深度，AI 要依目標自動建議：找 flow 用 Level 1、單條 flow 深挖用 Level 2、要寫成強 evidence 或追 bug history 才建議 Level 3。
- 若 AI 判斷目前不值得 Level 3，要大方說明原因，例如後台只是入口、下游未定位、履歷 claim 不足、或先讀後端 repo 更有價值。
- 每次完成 Step 或 flow 更新後，若該 flow / project 尚未收口，必須自動給 Nick「下一步建議」，且只推薦一件最值得做的事。若已達收斂狀態或本輪是 KB / 履歷 / 狀態整理，則不要硬塞下一步，改回報「目前可自由提問 / 可彈性指定下一件事」。
- Nick 可以只貼 `讀kb`、`下一步` 作為省字入口。這不是取代既有 Step 指令，而是新增 `KB Readiness + Next Action Automation`：AI 要先走 `快速狀態`，讀 README / todo / flow inventory 與必要 git status，校正目前最值得做的一件事；只有發現 active flow、待收口 consolidation、05 / 08 / 04 / 17 明顯不一致，或 Nick 指定 project / flow / JD 時，才升級為完整 KB / projects / code 深掃。若 Nick 明確指定 `某 project / flow Step N`，仍照原本 Step 主線執行。Teaching notes 不列為預設下一步；只有讀 flow 卡住或 Nick 明確要求時才補。
- 下一步建議要說明：為什麼現在做它、會產出什麼、是否會更新履歷、是否需要 commit / push。
- 下一步建議必須附上 Nick 可直接複製的短 prompt，並用 fenced code block 包起來，例如 ` ```text ... ``` `；不要只寫在一般段落或句子裡。
- 若 Nick 問的是「還有多少 step / 會不會一直建議 / 何時結束 / 對標資深是否夠了」，AI 要先回答收斂狀態與終點，不要直接丟下一個 flow。回答必須說明：最小必做剩多少、可選加強有哪些、哪些暫不建議做，以及做完後是否轉為投遞 / 面試練習。
- 若 Nick 問的是一般「下一步 / 建議」，且目前已接近收斂，AI 要用三段式小儀表板回答：`必做收口` 先說是否真的有必做；`可選加強` 可列 1-3 個「廣度延伸 / 深度延伸 / 架構視角 / 投遞準備」方向，讓下一步可廣可深、能伸能縮；`暫不建議` 要明確列出低 ROI 或焦慮型任務。可選方向不能被包裝成必做 backlog，也不要輸出 fenced prompt，除非 Nick 明確指定要開工某一項。
- 若 Nick 問的是「是不是要一個人搞整個大系統 / 是否方向歪了 / 是否真的要全會」時，AI 必須先校正方向：Senior / Platform Backend 的準備重點是能掌握代表性核心 flows、講清楚跨系統邊界與風險，而不是取代一整個團隊完成全平台 knowledge ownership。不得用「再掃更多 repo」回避這個問題。
- 不可以自行創造新 Step 或新下一步名稱；下游定位、補 evidence、補 decision-notes、架構圖都只能是目前 Step 內的待確認或補充，除非 Nick 明確指定。
- 下一步判斷必須先看 Nick 當下是不是在要求「待辦事項、KB 規則、缺口清單、優先順序、下一步規劃」。若是，AI 只能先維護 todo / KB / index，把缺口列清楚並等待 Nick 指定下一個 flow Step；不得把自己列出的缺口自動當成已授權執行 Step 4 / Step 5。
- 若 Nick 明確說「專案先不下一步」、「先只更新 KB」、「先不要推 project / flow」，本輪屬於 `KB-only` 模式。AI 完成後只能回報 KB 檢查結果、改了哪些規則、是否 commit / push；不得附上 project flow 下一步 prompt，也不得把 todo 裡的收口項目當成本輪授權。
- 下一步判斷有優先級：履歷 / 自傳 / claim 風險優先於 Step 慣性。若 Nick 問「能不能放履歷 / 怎麼沒有經驗 / 不用履歷嗎 / contribution claim consolidation」或要求先整理履歷相關，AI 可以先做 `{project} contribution claim consolidation`，即使該 project 的代表 flows 尚未全部 Step 5。這個 consolidation 是 Career Track 的履歷 claim gate，不是新 flow Step；必須標成 rolling / scoped / limited（依實際範圍），並清楚列出哪些 claim 來自本人確認、commit / branch / diff、已完成 flow KB、未完成但 code-backed 的分析素材。不能把未深掃 flow 寫成已完整掌握。
- 若某 project 的 Step 2 本批代表 flows 已全部完成 Step 5，且尚未完成 project-level contribution consolidation，該 project 進入「待收口」狀態。Nick 問下一步、履歷、缺口或 consolidation 時，待收口 project 的 consolidation 優先於跨 project queue 與其他 project 的單條 flow Step；除非 Nick 明確指定要先做別的 project / flow。若代表 flows 尚未全部完成但 Nick 要先補履歷，則做 rolling consolidation，並在 project README、todo 或 inventory 標清楚「rolling consolidation 已做 / flow 深掃未完 / 後續需回填」。
- 如果 Step 3 已完成且文件乾淨，且不涉及履歷 / 自傳 / contribution claim gate，下一步預設是 Step 4，不是下游定位或其他自創任務。
- 如果只有 Step 1，下一步預設是 Step 2；沒有 Step 2 時，不得直接跳 Step 3 / 建 flow folder。
- 如果某條 flow 已完成 Step 5，下一步預設回到同 project 的 candidate flow ranking，選下一條未完成 flow；不要自動跨 project。
- 如果下一步是繼續同一條 flow，優先建議往 failure / consistency / interview / claim boundary 補齊，而不是立刻換新 flow。
- 每次 Step 都要在 `materials/evidence.md` 或舊格式對應 evidence 文件寫清楚本次實際掃描範圍：主分支、近期分支、相關 code path、相關後端 / 下游 repo 是否有看。
- 每次 Step 的掃描範圍也要寫清楚 code repo 遠端狀態：是否已 fetch、local HEAD、remote HEAD、是否 ahead / behind；若未 fetch 或本機落後遠端，必須標為未確認，不可宣稱已看最新 code。
- 如果沒有掃其他分支、沒有看到下游 code、或只看到後台 / 前端操作面，必須明確標成「未掃 / 待確認 / 只作關聯入口」，不能自行補成完整後端 flow。
- 後台、前端、BI、admin 類專案若不是 Nick 主開發，只能作為理解入口與對接 flow；履歷自傳應簡單帶過，不得硬包裝成主導後台或完整系統 owner。
- 沒有實際 evidence 的技術點可以寫「略」、「不展開」、「建議補讀外部文章 / 官方文件」，不要為了湊滿格式而腦補。
- `flow.md` 的閱讀層次固定是「先讀懂，再資深化」：先用初階 / 中階可讀方式說清楚功能、使用者、觸發情境、Controller / Service / Model / SQL / Redis / MQ / Log 對照與正常流程，再進入 Senior / Owner 的 state、consistency、idempotency、retry / compensation、observability、trade-off。
- 架構圖與流程圖是 `flow.md` 的教學入口，不是新 Step、不是額外任務、也不是要畫沒有 evidence 的大圖。圖只畫本 flow 已確認或明確標示待確認的上下游。
- `senior-owner-playbook/05-resume-master-zh.md` 與 `08-application-autobiography-zh.md` 分成 rolling 版與最終版：rolling 更新可以先吃 project contribution consolidation 的保守結論，用來支援近期投遞；最終版仍必須在足夠 flow 深掃後再全量校正。更新前必須掃 code 分支、path-specific history、重要 diff、KB 與 `projects/` 內履歷自傳素材，並逐條標註哪些是 Nick 真實做過、哪些只是專案存在或分析素材；`archive/` 目前已清空，不再列為必要來源。
- 履歷 / 自傳更新不得只根據單條 flow Step 5 直接寫入。若要更新 05 / 08，必須先有對應 project 的 contribution claim consolidation，或在本輪先完成 rolling / scoped consolidation。
- 對 Nick 明確指出「實際做很多」的主力 repo，例如 `iwin/payment`，不得只用單條 flow Step 5 的直接 path evidence 來否定整個 repo 經驗。可以先做 project-level rolling contribution consolidation：掃全部 Nick / `10gt12nc` commits、branches、重要 diff、已完成 flow KB、既有 flow evidence 與 Nick 本人確認，整理成「可放履歷：真實開發過」、「可面試講：code-backed / 分析過」、「不可誇大：不是主導完整系統 owner」三層，再更新履歷或 claim boundary。未完成的代表 flows 要標為「待深掃 / 待回填」，之後 Flow Track 照舊補 Step 3~5。
- 即使 Nick 沒先說「我做很多」，只要某 project 已經開始形成可面試 code-backed 素材，而 AI 的下一步會影響履歷 claim 或 Nick 明確追問履歷價值，也可以先做 rolling contribution consolidation；不能因 flow 尚未全部 Step 5 就完全卡住履歷線。
- project-level contribution consolidation 有兩種狀態：`rolling / scoped consolidation` 可先做，用於近期履歷與面試材料；`final consolidation` 才要求 Step 2 所定義本批代表 flows / Top candidate flows 全部完成到 Step 5 後再校正。兩者都必須重讀 README / Step 文件、已完成 flow KB、Nick / `10gt12nc` commits、branches、重要 diff、本人確認；差別在於 rolling 版必須明確列出未完成 flow 與待回填項，不能宣稱全 project 已完整深掃。

## Flow 格式

未來新建或重整後的專案分析建議放：

```text
projects/{domain}/{project}/
  README.md
  architecture-map.md
  flows/{flow-name}/
    flow.md
    career-interview.md
    materials/
      evidence.md
      decision-notes.md
      interview.md
      claim-boundary.md
  career-interview.md
```

`flow.md` 是唯一主報告；`career-interview.md` 是該 flow 對應的保守履歷 / 面試素材；`materials/` 是證據與輔助材料。若既有資料仍是舊平鋪格式，先標為舊格式可沿用 / 需遷移，不要未經 Nick 同意批量搬檔。

`flow.md` 內部建議順序：

1. 閱讀定位與 evidence 層級。開頭必須先補「Flow 類型與閱讀定位」：Flow 類型、所屬大系統、面試用途、閱讀方式、不要期待，避免把完整業務閉環、元件流程、後台控制點、報表投影、補償維運、部署流程或 math contract 全部混成同一種 flow。
2. 初階 / 中階可讀區：白話導讀、Code 分層對照、最小架構圖、正常流程圖、正常流程逐步說明。
3. Senior / Owner 深度區：資料狀態、transaction boundary、consistency、idempotency、failure window、retry / compensation、observability、owner decision。
4. 面試 / 履歷邊界摘要，詳細材料放 `career-interview.md` 與 `materials/`。

## 檢查

更新後檢查：

- 是否已依任務模式完成必要重讀，並在需要時重讀 KB、既有文件與相關 code 最新狀態？
- 是否已檢查既有 Step / flow 是否過舊、缺 evidence 或不符合目前 KB？
- 是否只動 `nick-vault`？
- 是否沒有動公司專案？
- 是否沒有新增 code？
- 是否沒有 secret / token / internal IP / production URL？
- 是否有把推測與已確認分開？
- 是否有避免履歷誇大？
- 是否有更新 README 或 todo？
- 是否已做 Relationship Check，確認權威關聯檔已同步或不需更新？
- 是否已自行全掃確認已改檔案與相關規則？
- 是否已依改動大小完成輕量自查或全掃確認，並 commit？
- 是否只有一個 session 具備寫入 / commit 權限？若使用多 session，其他 session 是否保持只讀，或已使用獨立 worktree？
- 若在 project / submodule branch 工作，是否已同步最新 `main` 以取得最新 KB？
- 若需要 push，是否已直接觸發 `git push` approval 視窗，而不是只停在本地文字回報？
