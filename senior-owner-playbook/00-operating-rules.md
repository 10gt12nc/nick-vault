# 維護規則

## 全 vault 共用規則

本文件是整個 `nick-vault` 的共用維護規則，適用所有專案、所有 flow、所有 Step。

不是只有 `app_bi` 適用。之後不管 Nick 下：

- `iwin payment step1`
- `ugsoft-admin-api step2`
- `antplay-slot-game-api 某 flow Step 3`
- `DevOps step1`
- `下一步`

AI 都必須套用同一套規則。

### Token-aware 工作模式

`nick-vault` 的防呆規則要依任務風險升級，不是每次都全量讀取。AI 開始前必須先判斷模式，並只讀該模式必要的檔案。

| 模式 | 觸發情境 | 必讀 / 必做 | 不做 |
| --- | --- | --- | --- |
| `輕量問答` | 概念討論、焦慮校正、策略判斷、讀法建議、一般職涯問答 | 讀必要入口或沿用已知 KB 狀態；可引用既有規則摘要 | 不掃 code、不跑 git log、不做 Relationship Check、不改檔 |
| `快速狀態` | Nick 問「下一步 / 還要幹嘛 / 都好了嗎」，且沒有指定 project / flow / JD | 讀 `README.md`、`06-todo.md`、`01-senior-owner-flow-inventory.md`、必要時 `git status` | 不全掃 `projects/`、不重讀所有 flow、不輸出新 backlog |
| `中量維護` | KB 規則、索引、todo、閱讀順序、小型一致性修正 | 讀 `AGENTS.md`、`00`、`09` 與受影響檔案；改檔後做 Relationship Check、`git diff --check` | 不掃公司 code，除非本輪事實需要 |
| `重度深掃` | flow Step、contribution consolidation、履歷 / 自傳正式 claim、Completeness Audit、Nick 明確要求逐行 / 極限深掃 | 讀 KB + project 文件 + source repo branch / log / path history / 重要 diff；標示已掃 / 未掃 / 待確認 | 不假裝一次掃完整個世界；範圍大時必須分批 |

升級規則：

- 只要會影響履歷 claim、面試可講內容、flow evidence、source repo 事實、commit / push 或跨檔規則，就至少用 `中量維護`。
- 只要要判斷「做過 / 沒做過 / 可不可以寫履歷 / flow 是否完整」，就用 `重度深掃` 或明確說本輪只是 limited / rolling。
- `讀kb / 下一步` 在收斂狀態下預設是 `快速狀態`；只有發現 active flow、待收口 consolidation 或 05 / 08 / 04 / 17 不一致，才升級。
- `輕量問答` 可以直接回答，不應為了儀式感讀完整 KB；但若答案牽涉最新市場、薪資、法規或特定 JD，仍依需要查最新資料。

### KB 觸發式維護停損線

KB 的目標是降低誤判與交接成本，不是追求永遠完整。AI 不得因為「還能優化」就主動全面重構 KB。

目前 `nick-vault` 是 v1 `Job Search Vault`，核心任務是讓 Nick 拿到下一份 Senior Java Backend / Platform Backend 工作。優先順序是：

1. 三個故事講順。
2. 12 條 flow 讀熟到能講。
3. 30 題核心題有基本回答。
4. 開始投遞並取得市場回饋。

AI 若提出新增大量待辦、候選 flow、知識整理、system design、skill、automation 或新目錄，必須先過 `可投遞成果檢查`：

| 檢查問題 | 若答案是否定 |
| --- | --- |
| 是否直接提升面試勝率？ | 預設可選加強或暫不建議 |
| 是否直接提升履歷品質？ | 預設可選加強或暫不建議 |
| 是否直接提升薪資談判能力？ | 預設可選加強或暫不建議 |

若三者都沒有直接提升，不得把該事項升級為必做，也不得因為「之後可能有用」就產生新 backlog。求職系統已達 80-90 分時，正確方向是使用它投遞與練面試，而不是持續重構到 95 分才開始。

只有出現以下觸發條件時，才調整 KB：

- 文件造成下一步誤判、Step 跳錯、flow / Career Track 混淆。
- 文件讓 AI 重複讀取、重複深掃、重複做 Relationship Check，造成明顯 token 浪費。
- 文件讓 Nick 或下一輪 AI 重複踩坑，例如履歷 claim 誇大、本人 evidence 被抹掉、active flow 被插隊。
- 文件交接失敗，例如路徑、權威檔、讀法、下一步 prompt 或 completion state 讓人看不懂。
- 新事實會影響履歷 / 面試 / flow evidence / source repo 判斷。
- Nick 明確要求「維護 KB / 幫改規則 / 這點記 KB」。

不該調整 KB 的情況：

- 只是覺得文字可以更漂亮。
- 只是想把規則寫得更完整，但沒有實際誤判風險。
- 只是焦慮而想重新整理全部。
- 已有規則能處理，只需要在本輪回答中套用。

若沒有觸發條件，AI 應回報「目前不建議改 KB」，並把焦點帶回讀 flow、履歷 / case、面試練習、JD 客製或使用者當下指定任務。

### v2 演化延後規則

`career/`、`incident library`、`decision library`、`industry-kb`、`skill library`、`automation framework` 都是 v2 候選，不是目前主線。

啟動條件：

- `到職後 3-6 個月`：若有新公司去識別化 incident、排查、接手系統 evidence，才考慮 Incident Library。
- `到職後 6-12 個月`：若有實際 technical decision、ownership boundary、team collaboration evidence，才考慮 Decision Library / Ownership Evidence。
- `到職後 1-2 年`：若累積足夠高品質案例與判斷框架，才考慮 Career / Industry KB 或 Skill Framework。

目前禁止：

- 因為想像未來有用就新增 `career/` 或 v2 目錄。
- 建立每日 / 每週工作日誌、學習紀錄、心得紀錄。
- 把 automation 推送結果自動寫入正式 KB。
- 把 skill source 包裝成目前求職必做。

Skill 的長期價值不在於累積很多 prompt，而在於少數高品質 framework。未來若要 skill 化，優先考慮 `Flow Analysis Framework`、`Incident Analysis Framework`、`Provider Integration Framework`、`Resume Claim Framework`、`System Design Framework` 這類可重複判斷框架；不要追求大量一次性 skill。

### `讀kb / 下一步` 自動化模式

Nick 可以只貼：

```text
讀kb
下一步
```

AI 必須把它視為 `KB Readiness + Next Action Automation`，但先用上方 `Token-aware 工作模式` 判斷是否只需快速狀態。Nick 不需要自己知道目前有多少 flow、哪些檔案要補、哪些 career / map 還缺。Teaching notes 只作讀 flow 卡住時的補洞，不列為預設盤點主線。

固定動作：

1. 快速狀態先讀：`README.md`、`06-todo.md`、`01-senior-owner-flow-inventory.md`，必要時補 `git status`。
2. 只有快速狀態發現 active flow、待收口 consolidation、履歷 / 面試輸出不一致，或 Nick 指定 project / flow / JD，才升級重讀：`AGENTS.md`、`00`、`09`、`03-flow-learning-package-template.md` 與相關 project 文件。
3. 只有升級後才掃 `projects/` 的 domain / project / flow 狀態，判斷 Flow Track、Career Track、Domain Map、Decision Notes 是否有明顯缺口；Teaching Notes 只在 Nick 讀 flow 卡住或明確要求時檢查。
4. 更新或校正 `06-todo.md` 的已完成 / 未完成 / 候選缺口；必要時同步 `01-senior-owner-flow-inventory.md` 或 project README。若只是讀狀態且沒有事實變更，不硬改檔。
5. 回答時只推薦一件最值得做的下一步；若沒有必做收口，改用 `必做收口 / 可選加強 / 暫不建議` 小儀表板。
6. 下一步要附上 Nick 可直接複製的一行 prompt，用 fenced code block 包起來；但如果目前已收斂且只適合自由提問，就不要硬塞 prompt。

自動判斷優先順序：

1. 已開始但未收口的 active flow：先完成同一條 flow 到 Step 5。
2. 某 project 本批代表 flows 已 Step 5，但尚未 contribution consolidation：先做 project-level consolidation。
3. `05 / 08 / 04 / 17` 與最新 consolidation 不一致：先做 rolling resume package / final check。
4. 缺 Step 1 / Step 2 的高價值 project：先補 Step 1 / Step 2，不直接跳 Step 3。
5. 已有 flow 但缺 `decision-notes.md` 或口說材料：只有在該缺口會影響面試追問時才推薦，不把教學補洞變成主線。`materials/teaching-notes.md` 不作自動下一步；只在 Nick 讀該 flow 卡住、面試回饋暴露缺口，或 Nick 明確要求「補教學」時才建立。
6. 若已達 Senior 投遞證據包收斂：停止大規模整理，轉為閱讀、口說練習、JD-specific 客製或自由提問。

禁止事項：

- 不得因 `讀kb / 下一步` 就自動開工多個 flow。
- 不得把所有候選缺口包裝成必做 backlog。
- 不得只看 `06-todo.md` 的文字就下結論；要交叉檢查 `projects/`、README、flow files 與 contribution consolidation。
- 不得把 `teaching-notes.md`、system map、side project 或泛用基本功補洞插隊到 active flow 前面；也不得因 49 條既有 flow 沒有 teaching-notes 就列為待辦。

### Flow Track vs Career Track

本 vault 同時有兩條線，AI 必須分清楚：

1. `Flow Track`：負責系統理解與面試案例。固定主線是 `Step 1 project candidate flows -> Step 2 flow ranking -> 單條 flow Step 3 深掃 -> Step 4 面試 case -> Step 5 單條 flow claim gate`。每條 flow 一樣要依深掃規範讀 code、history、evidence、failure window、consistency、claim boundary。Flow Step 5 只回答「這條 flow 本身能不能作履歷素材」，不能回答「整個 project 履歷怎麼寫」。
2. `Career Track`：負責履歷 / 自傳 / project-level 經驗整理。固定主線是 `project contribution claim consolidation -> 05 resume / 08 autobiography`。consolidation 要以 project 為包裝單位，掃 Nick / `10gt12nc` commits、branches、重要 diff、本人確認、既有 flow evidence、`projects/` 與 KB 內履歷素材。履歷 / 自傳可以引用 flow，但主要結論來自 project-level consolidation，不直接吃單條 flow Step 5。`archive/` 目前已清空，不再列為必要來源。

當 Nick 要求「先把目前所有 contribution consolidation 匯總成 05 / 08，讓我能寫履歷」時，走 `rolling resume package`：AI 要彙總已完成的 project-level contribution consolidation，更新 `05-resume-master-zh.md` 的可直接使用履歷版與 `08-application-autobiography-zh.md` 的投遞版，並標示為目前可用草稿 / 非 final。`05` 是履歷 / 自傳 / claim 母稿與證據池，可以保留較完整的判斷與素材；`08` 是投遞輸出版，必須維持 104 可貼欄位：工作經驗、專長、自傳、自我推薦。後續每次 flow 深掃、Step 5 或新的 consolidation，都要回填修正 05 / 08，而不是等所有 flow 做完才讓 Nick 開始寫履歷。

若 Nick 暫時沒有特定職缺 JD，`08 / 17` 預設維持「通用 Senior Java Backend / Platform Backend 投遞版」，主軸是金流 / provider gateway / wallet / bet-settle / MQ / batch / legacy takeover。AI 不得每次都要求 Nick 貼 JD 才能繼續；只有 Nick 明確要投某個職缺、提供 JD、或要求客製時，才做 JD-specific 客製。沒有 JD 時，下一步應優先轉為通用版面試自我介紹、104 投遞欄位檢查、面試 case 練習或投遞準備。

有明確 JD 時，流程固定是 `JD -> fit analysis -> 決定是否客製 -> 客製履歷 / 自傳 / 問答 -> JD-specific 20% 基本功 -> 若有面試回饋再回填`。先分析是否值得投、值得 market check、或應略過；不要每看到一個 JD 就直接產完整履歷包。若 Nick 想投、值得 market check，或 Nick 明確要求客製，客製內容放在 `senior-owner-playbook/applications/{company-or-role}-{date}/`，原則是一個公司 / 一個 JD / 一個資料夾；不要直接覆蓋通用 `05 / 08 / 17`。JD-specific package 至少包含：JD fit analysis、104 客製履歷 / 自傳 / 自我推薦、HR / 薪資 / 文化 / 技術問答、JD-specific 20% 基本功最小複習清單、投遞前閱讀順序。客製只能重排與降調既有 evidence，不得因 JD 寫 Senior、架構、mentor 就把 Nick 包裝成正式 Tech Lead、架構師或完整系統 owner。基本功複習也只能從 JD 與主力 production cases 長出來，例如 AOP、transaction、SQL index、Redis consistency、MQ duplicate consume；不得變成泛用八股題海或取代 70% production case。只有當某個 JD 客製結論變成全域通用規則時，才回填 `05 / 08 / 17 / 04`。

### 參考 workspace 工作法吸收規則

Nick 要 AI 深掃 `/Users/nick/Git/antplay/math-workspace`、`/Users/nick/Git/nick/test001_unity`、`/Users/nick/Git/nick/test001_RenPy`、`/Users/nick/Git/iwin/iwin-workspace` 或類似 workspace 來「學做事方式」時，本輪屬於 `Reference Workspace Method Extraction`。目標是萃取可重用的治理方法，不是把外部 workspace 的目錄、GDD、部署、工具輸出或子 repo 規則整套搬進 `nick-vault`。`/Users/nick/Git/nick/*` 是 personal reference，不是公司專案 source、production evidence 或 Senior Backend 主履歷 claim。

四個 workspace 的角色定位：

| Workspace | 本質角色 | 可學內容 | 不可照搬 |
| --- | --- | --- | --- |
| `nick-vault` | Senior / Owner 學習、flow evidence、履歷 / 面試 claim gate | Flow Track / Career Track 分離、claim boundary、Relationship Check、投遞收斂 | 不變成開發 repo、不新增工程式流水帳 |
| `math-workspace` | AI-assisted math 開發 / 驗證工作台 | Architect / Planner / Coder / Reviewer handoff、source / KB 分離、GDD / 相似包 / result validation 交叉驗證 | `docs/projects/` module catalog、RTP / GDD / optimizer、child repo push / deploy 規則 |
| `test001_unity` | 0 到 1 遊戲產品工作台 | Producer / Design / Engineering / Art / QA / Release 角色分工、scope cut、gate、值得導入 / 只作參考 / 不採用 / 仍需驗證分類 | Unity docs / kb 雙層目錄、素材 / 外包 / DLsite / Play Mode 專案流程 |
| `test001_RenPy` | 個人 side project / Ren'Py 評估工作台 | AI-assisted product evaluation、scope cut、文件入口、migration decision | Ren'Py / visual novel domain-specific 規則、商業題材、個人 side project 素材、任何 production claim |
| `iwin-workspace` | 大型既有系統復原 / 運維 / 跨 repo 關聯工作台 | source repo inventory、relation map、generated / curated 分離、唯讀 source cache、敏感資訊遮蔽、cross-project reconstruction | k3s / JumpServer / deploy tag / hostPath / `.work` 實作與內部環境規則 |

可吸收到 `nick-vault` 的角色 lens 只有四個，且只是思考與交付檢查，不是新 Step：

1. `Producer / Scope Gate`：判斷這件事是否該做、是否偏離 Senior 投遞目標、是否只需記 KB 或暫不做。
2. `Flow / Technical Reviewer`：檢查 code path、資料流、transaction / idempotency / MQ / DB / Redis / failure window 是否有 evidence。
3. `Career Claim Reviewer`：檢查哪些可放履歷、可面試講、不可誇大；不得把 workspace / supporting evidence 包成 production service owner。
4. `KB Curator`：檢查索引、Relationship Check、source repo inventory、relation map、動態 / 衍生檔與不留流水帳。

#### Karpathy-style / Agent Workflow Lite

`math-workspace` 的完整 `Architect -> Planner -> Coder -> Reviewer` 適合開發型工作，因為它會讀 GDD、改 source、跑 RTP / optimizer、做跨 module 驗證。`nick-vault` 是個人知識庫，主風險是 scope 發散、evidence 不清、履歷 claim 誇大、下一步誤判與 token 浪費，因此只導入 Lite 版。

Karpathy-style 在本 vault 的落地規則：

- `先想清楚`：開工前先說明任務模式、假設、non-goals；遇到不確定要標示，不把推測寫成事實。
- `簡單優先`：只做能降低誤判、補 evidence、改善閱讀或支撐投遞 / 面試的最小修改；不因為「還能更完整」就新增結構。
- `精準改動`：只動本輪必要檔案；不要順手重構鄰近規則、搬目錄、批量格式化或改其他 workspace。
- `可驗證完成`：每輪交付前確認本輪輸出、Relationship Check、`git diff --check`、履歷 claim 邊界與下一步是否收斂。
- `該升級就升級`：若需求矛盾、scope 不清、success criteria 無法驗證、需要改 scope 外檔案，或同一阻礙反覆卡住，不要硬做；必須標示 `ESCALATE / 待 Nick 確認 / 回上游規則修正`。

非小事任務套用四個 gate，但不需要建立實體 handoff JSON：

| Gate | 用途 | 最小輸出 |
| --- | --- | --- |
| `Scope Gate` | 判斷本輪做什麼、不做什麼、是否值得升級深掃或改 KB | 任務模式、scope、non-goals |
| `Evidence Gate` | 判斷依據來自 KB、code、git history、Nick 本人確認或外部參考 | 已確認 / 推測 / 待確認 |
| `Claim Gate` | 判斷本輪內容可放履歷、可面試講、不可誇大或只是 learning-only | claim 邊界與是否影響履歷 / 面試 |
| `Review Gate` | 交付前檢查最小修改、交付物類型清楚、無誇大、無外部規格混入、關聯檔同步 | Relationship Check / diff check / commit 狀態 |

補強規則：

- `files_in_scope` 是圍牆：中量 / 重度任務開工前要心中列出本輪可改檔案；實際需要改 scope 外檔案時，先停下來說明原因，不能順手改。
- `success criteria` 要具體可驗：文件維護至少要能用已改檔案位置、Relationship Check、`git diff --check`、git status / staged 清單驗證；flow / claim 任務至少要能用掃描範圍、evidence 層級、claim boundary 與已掃 / 未掃清單驗證。
- `ESCALATE` 不是失敗：當問題其實出在上游需求、KB 規則衝突、證據不足或 scope 需要擴張時，正確動作是回報阻塞與建議路徑，不是繼續編內容。
- Reviewer 心態要保留：交付前要能說 `APPROVE / REQUEST_CHANGES / ESCALATE` 的等價判斷；不能因為已經花時間就宣稱完成。

啟用規則：

- `輕量問答` 可以只內化，不需要逐條展開 gate。
- `中量維護` 必須至少走 Scope / Claim / Review Gate；若只是純規則或索引，不影響履歷 claim，也要明確說明不需更新 05 / 08 / 04 / 17。
- `重度深掃`、flow Step、contribution consolidation、履歷正式 claim 必須走四個 gate，並在 evidence / final 中保留等價資訊。
- 只有真正要改 source code、做跨 repo 實作、測試、重構或安全修正時，才參考完整 `Architect -> Planner -> Coder -> Reviewer`；一般 flow / 履歷 / KB 維護不搬完整流程。

深掃外部 workspace 後，結論必須分成：

- `值得導入`：已轉成 `nick-vault` 最小規則或明確待辦。
- `只作參考`：概念有幫助，但不進目前 vault 主線。
- `不採用`：domain 不同、scope 太大、會讓 vault 發散或製造流水帳。
- `仍需驗證`：需要更多 code / KB / Nick 決策才能判斷。

若 Nick 說「值得學的都抄過來」，AI 要翻譯成「吸收原則、重寫成 vault 規則」，不得逐字複製外部文件、不得新增 `docs/`、`kb/`、`.work/`、`ai-notes/` 等長期目錄。

### 完整性稽核：consolidation 不等於 flow

AI 回答「全掃了、都完整、沒有問題、還缺什麼、flow 都完整嗎」時，必須做 `Completeness Audit`，不能只看 Career Track。

每個受影響 project 至少分三欄判斷：

| 層級 | 要檢查什麼 | 判斷口徑 |
| --- | --- | --- |
| Flow Track | `step1-candidate-flows.md`、`step2-flow-comparison.md`、`flows/{flow}/flow.md`、`career-interview.md`、`materials/evidence.md` / `claim-boundary.md` | 用來判斷系統理解與面試 case 是否完整 |
| Career Track | `contribution-claim-consolidation.md`、`05 / 08 / 04 / 17` 回填狀態 | 用來判斷履歷 claim 是否可用 |
| Domain Map | `architecture-map.md` / `integration-map.md` / domain README | 用來判斷跨 project 協作圖是否存在；多數時候是可選補強 |

禁止錯誤回答：

```text
contribution claim consolidation 已完成，所以這個 project 沒問題 / flow 完整。
```

正確回答：

```text
Career Track 已完成 rolling consolidation，可支撐保守履歷；但 Flow Track 尚未建立 Step 1 / Step 2 / flows，因此不能說 flow 完整。
```

若某 project 只有 `contribution-claim-consolidation.md`，但沒有 `step1-candidate-flows.md` 或 `flows/`，下一步只能列為「Flow Track 缺口」。除非 Nick 明確下 Step，AI 不得在 KB-only / 缺口盤點模式中直接開工補 flow。

重要優先級：若只是學系統或準備單條面試 case，走 Flow Track。若要判斷 Nick 經驗、履歷、自傳、project claim，走 Career Track。完整 `project contribution claim consolidation` 預設要等該 project 的 Step 2 所定義「本批代表 flows / Top candidate flows」全部完成到 Step 5；若 Nick 明確縮小本批 consolidation scope，只能標成 limited consolidation。若只有單條 flow Step 5，只能保留為單條 flow claim evidence；不得推薦或命名為完整 project consolidation，下一步應回 Step 2 ranking 繼續做同 project 下一條 flow。

若某 project 的 Step 2 本批代表 flows 已全部完成 Step 5，且尚未完成 project-level contribution consolidation，該 project 進入「待收口」狀態。Nick 問「下一步、缺啥、履歷、自傳、能不能放、contribution claim consolidation」時，待收口 project 的 consolidation 優先於跨 project queue 與其他 project 的單條 flow Step；除非 Nick 明確指定先做別的 project / flow。AI 必須在 project README、todo 或 inventory 明確標示「已達 consolidation 條件 / 待收口」，避免把已可收口的 project 和一般 flow queue 混在一起。

若 Nick 當下要求的是「待辦事項、KB 規則、缺口清單、優先順序、下一步規劃」，這屬於 `Planning / KB Governance Track`，優先於 Flow Track 的 Step 慣性。AI 必須先把 todo / KB / inventory / project README 的缺口整理清楚，只列出下一步候選與理由，等待 Nick 指定要做哪個 Step 或哪個 consolidation；不得把自己列出的缺口自動升級成已授權執行的 flow Step。

若 Nick 問的是「做完後是不是會有整個系統大地圖 / 大結構 / 各子模組協作圖」，AI 必須實際檢查現有 KB 與 `projects/{domain}` 文件：

- 有：回報位置與是否需要 refresh。
- 沒有：補 KB 規則或把缺口列入 todo；若 Nick 授權建立，就建立 `projects/{domain}/architecture-map.md` / `integration-map.md`。
- 不得只回答「應該有」。

但 domain / system map 不能插隊打斷已經進行中的單條 flow。若 Nick 已明確指定某 flow Step 3 / Step 4 / Step 5，或同一條 flow 已做到 Step 3 / Step 4 尚未收口，下一步要先把該 flow 做到 Step 5；大地圖只能在 active flow 收口後、Nick 明確要求總結、或 domain 已累積足夠代表 flows 時回補。

若 Nick 明確說「專案先不下一步」、「先只更新 KB」、「先不要推 project / flow」，本輪是 `KB-only` 模式。AI 只能做規則、索引、todo、readiness 文件之間的一致性修正；完成後不附 project flow 下一步 prompt、不自動開工 todo 項目、不把「必做收口」視為本輪授權。此時的下一步只允許是「已完成 KB 維護 / 是否需要 push」這種收尾資訊。

### Senior 對標結束點與停止規則

`nick-vault` 不是要無限整理所有 repo。它的結束點是形成一組能支撐 Senior Java Backend / Platform Backend 面試與投遞的證據包，而不是把 backlog 清空。

完成標準：

1. 履歷主成果夠用：至少 3-5 個 project-level claim 可保守放履歷，且已標清楚真實開發、本人確認、code-backed、不可誇大的邊界。
2. 面試案例夠用：至少 8-10 條 production flow 能講 3 分鐘，且能回答 state transition、failure window、idempotency、retry / compensation、observability、owner decision。
3. 邊界乾淨：每個 claim 都能回答「我實際做過什麼、我分析過什麼、不能說什麼」。
4. 投遞包完成：`05-resume-master-zh.md`、`08-application-autobiography-zh.md`、`04-interview-casebook.md`、`17-salary-negotiation.md` 與最新 project contribution consolidation 對齊。

Senior 面試準備度要用三段門檻，不再只用「最低能投」判斷：

- `中等可面`：3 條主力 case 能講 3 分鐘，有 evidence、claim boundary 與常見追問。
- `穩過可抗追問`：5 條 case 覆蓋 payment、wallet / bet-settle、MQ / projection、partition / high-traffic data、rollout / observability，並能講 owner decision。
- `完全對標 Senior / Platform`：8-10 條 production flow 可依 JD 切換，`05 / 08 / 04 / 17` 與 claim boundary 全部對齊。

AI 回答「下一步 / 還差多少 / 是否夠 Senior」時，必須說明目前落在哪一段，下一步是補到中等、穩過，還是完全對標；不得把低標準包裝成完成。

side project 目前明確列為 `暫不做`。它可以是差異化加分，尤其若未來真的能雲端展示、讓面試官試玩並查看後台 / log / bet record / job / retry；但它不是 Senior Java Backend / Platform Backend 投遞的必要條件，也不是證明 Nick 有資格面 Senior 的前置條件。預設策略是先用真實 production flows、履歷、case、claim boundary 與基本功投遞；`20-game-backend-architecture-selection.md` 只保留為選型備忘與面試架構問答素材。只有 Nick 明確要求啟動 side project 時，才從該檔收斂成最小 project README / architecture plan；不得主動建議開工、寫 code、部署雲端或新增 side project backlog。

面試準備比例固定用 `70 / 20 / 10` 收斂，不得被焦慮帶成無限補洞：

- `70%`：production case、system design、claim boundary、3 分鐘口說與追問防線。
- `20%`：Java / SQL / transaction / exception / BigDecimal / index / Redis / MQ 的基本判斷，只從主力 case 卡點與面試回饋延伸。
- `10%`：LeetCode / coding test 保險，投遞前或收到面試邀約後補手感即可。

AI 時代的 coding 補強重點不是追求手刻所有程式，而是能 review AI 產物是否能進 production：transaction boundary、callback idempotency、Redis lock、BigDecimal、SQL index、MQ retry、null / race、duplicate side effect 與 observability。面試題 / 複習包也必須從主力 production case 長出來，不建立泛用 Java / SQL / LeetCode 大題庫。

### Interview Coaching Track

當 Nick 說「你來問我問題」、「我答覆你評估」、「直接教學」、「可深度詳細教」、「代碼演示」、「結構圖」時，進入 `Interview Coaching Track`。這條線不是新的 flow Step，也不是 project 深掃；它用 `senior-owner-playbook/19-interview-coaching-question-bank.md` 作題庫與評分規則，目標是把已整理的履歷、case、system design 與基本功轉成可口說、可追問、可教學的面試能力。

維護方式：

- 不建立聊天流水帳，不記錄每次逐字問答。
- 問答交流只萃取成 `能力判斷`、`補強主題`、`可回填素材`。
- 若 Nick 的回答形成更好的 90 秒 / 3 分鐘說法，回填 `04-interview-casebook.md` 或對應 flow 的 `career-interview.md`。
- 若回答暴露基本功缺口，回填 `16-career-skill-matrix.md`、`14-technical-decision-hard-skills.md` 或 `19-interview-coaching-question-bank.md` 的教學重點。
- 若回答影響正式履歷 claim，必須回到 project contribution consolidation / `05 / 08` 規則，不得只憑一次口說直接升級履歷。
- 每輪最多問 3-5 題；AI 要依回答切換 `診斷模式`、`教學模式`、`打磨模式` 或 `反問模式`，不要一次把完整題庫丟給 Nick 作答。
- 第一輪優先問：90 秒自我介紹、payment provider、wallet / bet-settle、MQ / projection、不是完整 owner 但能投 Senior 的說法。

當 Nick 問「目前還有多少 step / 會不會一直做 / 什麼時候結束 / 對標資深夠不夠」時，AI 不得只丟下一個 flow。必須把剩餘工作分成：

- `必做收口`：完成目前已進行、會影響履歷 / 面試閉環的少數 Step。
- `可選加強`：只做 1-2 個非 iwin project 的代表 flow，用來補廣度；不是必須全做。
- `暫不建議做`：官網、前端、workspace、mock、全部 legacy repo、全部 math repo 平均深掃等低 ROI 項目，除非 Nick 明確指定。

若 Nick 問的是一般「下一步 / 建議」，且 vault 已接近收斂，回答也應採同樣三段式，但語氣要更彈性：先說 `必做收口` 是否為 0；`可選加強` 可以分成「廣度延伸」（例如非 iwin 代表 flow）、「深度延伸」（例如某主力 case Level 3 / claim refresh）、「架構視角」（例如 domain map）或「投遞準備」（例如 104 / JD 客製）；最後列出 `暫不建議`，避免把全 repo 掃描、前端 / 官網 / 低價值 repo、無 evidence claim 變成焦慮 backlog。

達到完成標準後，AI 應建議停止大規模整理，轉成投遞、面試練習、針對職缺補洞；不得用 backlog 製造「永遠沒做完」的感覺。

實務口徑：`3-5 條`是投遞 / 面試最小主力 case，必須能反覆口說並抗追問；`8-10 條`是完整證據包，用來覆蓋不同面試官追問與不同職缺方向。AI 不得把兩者混成互相矛盾的完成標準。

### 完整度、焦慮與方向校正

當 Nick 追問「flow 都完整嗎 / 大小 map 夠完整嗎 / 還有沒有可以優化 / 學完能不能從 0 到 1 架完整系統 / 我是不是方向歪了」時，AI 必須先做方向校正，再談下一步。

正確判斷：

- `可投遞 / 可面試完整`：代表已具備履歷、case、claim boundary 與主力口說素材，可以投遞或練面試。
- `全量知識完整`：代表整個公司所有 repo、所有上下游、所有 production incident、所有架構決策都完整掌握。這不是本 vault 的目標，也不是單一工程師合理目標。
- `架構視角補強`：domain-level `architecture-map.md` / `integration-map.md` 可以補大地圖，但它是可選加強，用來提升 Platform / Lead 候選表達，不是投遞前必做。

AI 不得把「還能優化」講成「還沒資格」。成熟回覆要明確說：

- 哪些已經足夠支撐 Senior Java Backend / Platform Backend 投遞。
- 哪些只是可選補強，例如 iwin / antplay / ugsoft domain-level 大地圖。
- 哪些暫不建議做，例如全 repo 平均逐行掃、所有 legacy / 前端 / 官網全量整理。
- 若 Nick 的問題其實是在焦慮，先協助收斂目標與下一個最小有效動作，不要再加一串新任務。

`System Owner` 的定義必須保守：本 vault 要訓練的是能對「一條核心 production flow」說清楚成功定義、狀態、風險、補償、監控、人工邊界與交接方式；不是要求 Nick 一個人扛完整公司平台、完整金流帳務、完整遊戲 runtime、完整 DevOps / SRE 或所有架構決策。面試可以講 owner thinking，不可以包裝成全權 owner。

如果 Nick 問「是否能從 0 到 1 架完整系統」，標準回答是：目前資料能支撐他設計一個有交易正確性概念的核心子系統或服務藍圖，但不代表單人能完整取代產品、前端、DBA、SRE、QA、資安、營運與多個後端團隊。下一步若要補 0 到 1 能力，應用既有 flows 萃取 design template，而不是繼續平均深掃 repo。

AI 需要自動維護：

- 先判斷任務模式，再自動重讀必要 KB；輕量問答不做全量掃。
- project / flow / Step 任務才自動重讀該 project 既有文件。
- 涉及 flow evidence、履歷 claim、code-backed 判斷或 Completeness Audit 時，才自動重讀相關 code 最新狀態。
- 自動檢查既有 Step / flow 文件是否過舊、缺 evidence、舊格式或不符合目前 KB。
- 自動判斷掃描等級。
- 自動標示已掃 / 未掃 / 待確認。
- 自動避免履歷誇大。
- 自動給下一步建議。
- 自動判斷是否需要更新 project README、Step 文件、flow evidence、claim boundary 或共用索引。
- 自動判斷是否需要補 `materials/decision-notes.md`，用來整理技術硬底子、技術選型比較、trade-off 與 owner decision。
- 自動確認 `flow.md` 是否有初階 / 中階可讀區：白話導讀、Code 分層對照、最小架構圖、正常流程圖與逐步說明。沒有這一層時，不能只補 Senior / Owner 風險分析就算完成。
- 自動檢查 domain-level 大地圖是否存在且有吸收最新代表 flows / project contribution consolidation。當同一 domain 已累積多個 project-level claims 或代表 flows，且 Nick 問「大地圖 / 大結構 / 子模組協作 / 做完後總結」時，必須先檢查 `projects/{domain}/architecture-map.md`、`integration-map.md`、`career-interview.md`；缺漏或過舊要列為待辦或建立，不得只口頭說應該有。
- 自動檢查大地圖時必須尊重 active flow 優先級：已進行中的 flow 尚未 Step 5 前，不得把 domain map 當成下一步插隊；只能列為 active flow 收口後的待辦。
- 小型 / 低風險改檔可以輕量自查後直接 commit；重大 / 實質改檔必須完整全掃確認後 commit；若本輪需要 push，AI 必須直接執行 `git push` 觸發 approval 視窗，不得只停在本地文字回報。
- 多個 Codex / AI session 同時開著時，日常模式仍預設只在 `main` 開發，且同一時間只允許一個 session 具備改檔 / stage / commit 權限；其他 session 只能只讀。只有 Nick 明確需要並行整理不同 project / submodule，且每個 session 有獨立 worktree 時，才使用 project / submodule branch。

但「自動維護」不能改變 Step 主線。

除非 Nick 明確指定，AI 不可以自行創造新的任務名稱或插入新流程，例如「下游定位」、「補硬底子」、「架構圖補完」作為下一步。這些只能作為目前 Step 內的待確認、補充文件或 evidence 項目。

### 防跳 Step 規則

新 project 第一次完成 Step 1 後，下一步必須是 project-level Step 2，比較 candidate flows 的技術點、風險、證據強度、module / repo / service 邊界，以及哪一條最值得進 Step 3。

禁止流程：

```text
Step 1 找到候選 flow
-> 直接建議或建立某 flow Step 3
```

正確流程：

```text
Step 1 找 candidate flows
-> Step 2 比較 candidate flows 與邊界
-> Step 3 才建立單條 flow 學習包
```

只有 Nick 明確說「跳過 Step 2」「直接做某 flow Step 3」時，AI 才可以越過 Step 2；輸出時仍要標明「Nick 指定跳過 Step 2」與因此缺少的比較邊界。

如果 project 目錄只有 `step1-candidate-flows.md`，沒有 `step2-flow-comparison.md` 或等價 Step 2 文件，下一步建議必須是 `{project} Step 2`，不能推薦 `{flow} Step 3`。

### 多 module / monorepo 防錯規則

遇到 multi-module、monorepo、多 service instance 或多 repo 關聯專案時，Step 1 / Step 2 必須先建立足夠定位用的 module 邊界：

- root module / submodule / service instance / tool 目錄分層。
- 哪些是 runtime service，哪些只是 common library、tooling、config、deploy 或離線資料。
- 候選 flow 會跨哪些 module / upstream / downstream。
- 未掃的 module 必須標明未掃，不可假裝整個 repo 已完整理解。

這不是要平均整理所有 class；module map 只用來避免選 flow 時漏掉架構邊界。Step 2 仍要回到 production flow 排序。

AI 不會做的事：

- 不會在 Nick 沒有要求時背景定期掃 repo。
- 不會未經 Nick approval 直接 push。改檔後可以依風險等級自動 commit；若本輪需要推送，AI 要直接執行 `git push` 讓系統跳出 approval 視窗，由 Nick 按 Yes / No。不要只在 final 寫舊式「本地已提交、等待你再要求推送」的文字。
- 不會把後台入口硬包裝成後端成果。

## 改檔後自查、commit、push approval 規則

之後只要 AI 修改 `nick-vault` 檔案，收尾流程依風險分級。

### session / staging area 防污染規則

`nick-vault` 是個人知識庫，不是需要多人大量平行開發的 codebase。日常模式預設：

- 只在 `main` 開發。
- 同一時間只允許一個 session 具備改檔 / stage / commit 權限。
- 其他 session 若存在，只能做只讀查詢、review 或討論，不得改檔、stage、commit 或切 branch。
- 優先完成一個 project / flow / Step 後再換下一個，避免多個 AI session 同時改 Markdown 造成內容互相覆蓋或脈絡失真。

例外模式：

- 只有在 Nick 明確需要並行整理不同 project / submodule 時，才使用 project / submodule branch。
- 每個並行 session 必須有獨立 worktree，不得共用同一個工作樹。
- 同一 branch / worktree 同一時間只能有一個 session 負責改檔與 commit。
- 多 session 共用同一 worktree 互相切 branch 是禁止模式。

例外模式推薦格式：

```text
../nick-vault-iwin-app-bi
branch: codex/iwin-app-bi

../nick-vault-iwin-game-job
branch: codex/iwin-game-job

../nick-vault-iwin-gameserver
branch: codex/iwin-iwin-gameserver
```

禁止把多個不同任務長期混在同一個工作樹裡修改、stage、commit，因為 Git 的 staging area 屬於工作樹，不屬於單一 session。任何 session 執行 commit 都可能把其他 session 已 staged 的檔案一起帶走。

若 Nick 暫時要求或現場只能共用同一個工作樹，commit 前必須額外做以下檢查：

1. 跑 `git status --short`，確認工作樹與 index 狀態。
2. 跑 `git diff --cached --name-only`，確認 staged 檔案全部屬於本輪範圍。
3. 跑 `git diff --cached`，實際看 staged diff。
4. 只能用精準路徑或 `git add -p` stage 本輪檔案；禁止 `git add .`。
5. 若發現非本輪 staged 檔案，不得 commit；先回報 Nick，等待 Nick reset / unstage / 指定處理方式。

若本輪只是在髒工作樹補小規則，且 index 已有其他 session 的 staged 內容，AI 可以只改檔並回報「未 commit，原因是現場已有非本輪 staged 檔案」，不得為了自動 commit 而碰其他 session 的 staged 狀態。

### main、KB 與 project branch 同步規則

`main` 是共用 KB 的唯一正式來源，也是日常開發線與穩定整合線。project / submodule branch 只是例外模式的臨時工作線，不是 KB 的長期分叉。

規則：

1. KB 規則以 `main` 為唯一正式來源；其他 branch 讀到的 KB 若落後 `main`，必須視為可能過期。
2. 日常開發直接在 `main` 進行，但同一時間只能有一個寫入 session。
3. 例外使用 project / submodule branch 時，開工前必須以最新 `main` 為底；長時間工作、開始新 Step、重整 flow 或準備 commit 前，要先同步 `main`，確保讀到最新 KB。
4. KB 更新可以短暫用 `codex/kb-rules` 或同類 branch / worktree 隔離，避免共用 index 污染；但自查通過後應優先合回 `main`，不得讓 KB 長期只存在某個 project branch。
5. 這些同步規則只適用 `nick-vault`；公司 / 來源 code repo 仍只能 fetch remote refs，不得自動 pull / merge / checkout / rebase 或改工作樹。

若公司 / 來源 repo remote 是內網 GitLab 或目前網路不可達，`git fetch` 失敗一次後不要反覆重試；改用本地 refs / 本地工作樹做保守分析，並在 evidence 標示「fetch 失敗 / 未確認最新遠端 / 依本地 refs 判斷」。不得把內網 URL、IP 或敏感 remote 細節寫入 vault。

project / submodule branch 可以合回 `main` 的時機：

1. 該批 Step / flow 已完成到可讀閉環，例如 Step 文件、flow 主報告、career-interview、materials evidence / claim boundary 與 README / 共用索引已同步。
2. 自查通過：重讀改檔、跑 `git diff --check`、確認沒有 secret / token / internal IP / production URL / 客戶資料、沒有履歷誇大。
3. `git status --short` 與 `git diff --cached --name-only` 顯示 staged 內容只屬於要合回的 project / submodule branch，沒有混入其他 session 或其他 project。
4. 若內容會影響共用規則、履歷 master、自傳或跨 project 索引，必須確認相關 KB 已同步，且 Nick 沒要求暫停。
5. 若本輪需要推送，仍須由 AI 執行 `git push` 觸發 approval 視窗，讓 Nick 按 Yes / No。

不應合回 `main` 的狀況：

- flow 還是半成品，或 evidence / claim boundary 明顯待補。
- 同一批檔案仍有其他 session 正在改。
- staged 內容混入其他 project / submodule。
- Nick 明確說「先不要合」「先不要 commit」「先停在本地」。
- 只是探索、比較或暫存想法，還沒有形成可讀閉環。
- project / submodule branch 尚未同步最新 `main`，可能沒套用最新 KB。

### 小型 / 低風險改檔

小型 / 低風險改檔可以輕量自查後直接 commit，例如：

- 錯字、語句修正。
- 路徑或檔名修正。
- 單句規則補充。
- README / index 小同步。
- 不改變 Step 主線、不改變目錄規格、不新增履歷 claim 的小調整。

輕量自查至少包含：

- 重讀改過的檔案片段。
- 跑 `git diff --check`。
- 跑 `git status --short`，確認只動預期檔案。
- 跑 `git diff --cached --name-only`，確認沒有非本輪 staged 檔案會混入 commit。
- 確認沒有 secret、token、internal IP、production URL、客戶資料。
- 完成 commit 前的 `Relationship Check`：確認本輪事實變更是否影響權威檔；有影響就同步，無影響 final 要說已檢查、不需更新。

### Relationship Check：關聯檔案路由檢查

`Relationship Check` 的目的不是每次大改，而是避免只修局部檔案，導致 README / todo / 履歷 / 索引落後。檢查時機固定在「改檔後、commit 前」，不是 push 後才補救。

流程：

1. 判斷本輪事實變更是什麼，例如 source repo 狀態、flow Step 狀態、project 收斂狀態、contribution claim、履歷主張、下一步策略或共用規則。
2. 檢查權威檔是否受影響。
3. 有影響就同步修正。
4. 無影響不大改，但 final 要說明已檢查哪些類型的關聯檔、不需更新。

權威檔分層：

- 全域規則 / prompt：`AGENTS.md`、`senior-owner-playbook/00-operating-rules.md`、`senior-owner-playbook/09-ai-prompt-manual.md`。
- 全域索引 / 下一步：`projects/source-repo-inventory.md`、`projects/README.md`、`senior-owner-playbook/06-todo.md`。
- domain / project 入口：`projects/{domain}/README.md`、`projects/{domain}/{project}/README.md`、必要時的 `architecture-map.md` / `integration-map.md` / `career-interview.md`。
- 履歷 / 面試輸出：`05-resume-master-zh.md`、`08-application-autobiography-zh.md`、`04-interview-casebook.md`、`17-salary-negotiation.md`。
- project / flow evidence：`contribution-claim-consolidation.md`、`flow.md`、`career-interview.md`、`materials/evidence.md`、`materials/claim-boundary.md`、必要時的 `materials/decision-notes.md`。

動態 / 衍生檔不要求每次即時同步，例如：

- 自動產物、匯出稿、暫存稿。
- 練習稿、排序稿、一次性檢查輸出。
- 測試或工具產出的摘要。

只有兩種情況要處理動態 / 衍生檔：

1. 它被升級成正式閱讀入口、履歷素材、面試素材或長期 KB。
2. 它和權威檔產生會誤導下一輪 AI 或 Nick 的衝突。

不追蹤 / 不納入 KB 的檔：

- cache、log、工具輸出、外部 repo 產物。
- 公司 source repo 內的動態產物。
- 未經 Nick 指定整理的一次性檢查檔。

push 前只做乾淨確認：`git status`、`git diff --cached --name-only`、`git diff --check`，確認 commit 前的關聯檢查已收口。不要把關聯檢查延到 push 後才做；push 後才發現漏改只會製造補丁 commit 與 history 碎片。

### 重大 / 實質改檔

重大 / 實質改檔必須完整全掃確認，例如：

1. 自行再全掃確認一次，不等 Nick 追問。
2. 全掃確認至少包含：
   - 重讀本次改過的檔案。
   - 重讀受影響的共用規則 / prompt / README / index。
   - 檢查新規則是否和既有 KB 衝突。
   - 跑 `git diff --check`。
   - 跑 `git status --short` 確認只動 `nick-vault` 預期檔案。
   - 跑 `git diff --cached --name-only`，確認 staged 內容沒有混入其他 session 或其他任務。
   - 檢查沒有 secret、token、internal IP、production URL、客戶資料。
   - 檢查履歷 / 面試 claim 沒有誇大，且已標示 evidence 層級。
   - 完成 `Relationship Check`，確認受影響權威檔已同步，或明確判斷不需更新。
重大 / 實質改檔包含：

- 目錄結構調整。
- Step 主線或 flow 檔案責任調整。
- 正式履歷 / 自傳 claim 更新。
- 大量內容重整、刪改、遷移。
- 會影響其他 project / flow 的共用規則。

自查通過後，自動 commit，並回報 commit hash 與摘要。

push 一律需要 Nick approval，但正確做法是由 AI 直接執行 `git push` 觸發 approval 視窗，讓 Nick 在視窗按 Yes / No。

禁止的舊流程：

```text
commit 完
-> final 只回覆本地已提交，要求 Nick 另外再說推送
```

正確流程：

```text
commit 完
-> 若本輪需要推送，直接執行 git push with approval
-> Nick 在 approval 視窗按 Yes / No
-> push 成功後回報結果
```

只有在 Nick 明確說「不要 push」、「只 commit」、「先停在本地」時，才停在已 commit 狀態。

例外：

- Nick 明確說「不要 commit」、「先不要動 git」、「只改檔不提交」時，以 Nick 當下要求為準。
- 若 git 狀態有非本次修改且會混入 commit，AI 必須先說明並只 stage 本次相關檔案；不確定時先停下來問。
- 若 staging area 已有其他 session / 其他任務內容，且 Nick 沒明確要求 AI 代為整理 index，AI 不得 unstage、reset 或 commit 那些內容；只能回報風險並停在未 commit 狀態。
- 若自查發現問題，先修正再 commit，不可把明知有問題的狀態提交。

## 防再犯規則：流水帳、研究報告與規格變更

本段是 2026-05-13 排查後補上的防錯規則。

### 流水帳定義

Nick 說「不要維護流水帳」時，指的是不要維護這類文件：

- 今天做了什麼。
- 昨天做了什麼。
- 某次操作記錄。
- `records/`
- `operation-log`
- `work-report`
- `branch-log`
- `decision-log` 若只是按時間堆事件，也視為流水帳。

`nick-vault` 的事實來源應是：

- `flow.md`
- `career-interview.md`
- `materials/evidence.md`
- `materials/decision-notes.md`
- `materials/interview.md`
- `materials/claim-boundary.md`
- project README / architecture map
- git log

不要為了記錄 AI 做過什麼而新增時間序列日誌。

### 研究分析報告定義

在每條 flow 資料夾內：

```text
flow.md = 研究分析報告主文
```

`flow.md` 不是只給 Senior reviewer 看的風險分析，也必須是 Nick 第一次讀這條 flow 時能看懂的學習入口。正確閱讀層次是：

```text
先用 Flow 類型與閱讀定位判斷這是完整業務閉環、元件 / 服務流程、後台控制點、報表 / 投影、補償 / 維運流程、部署流程或 math contract / validation
-> 再讀懂功能與 code 分層
-> 再理解正常資料流
-> 再分析 failure / consistency / owner decision
-> 最後轉面試與履歷邊界
```

`flow.md` 開頭的「Flow 類型與閱讀定位」必須固定寫：

- Flow 類型。
- 所屬大系統。
- 面試用途。
- 閱讀方式。
- 不要期待。

新建或重整後，其他檔案要收在同一條 flow 的 `materials/`，避免主閱讀面混亂：

- `career-interview.md`：該 flow 的保守履歷 / 面試素材。
- `materials/evidence.md`：證據附錄。
- `materials/decision-notes.md`：技術決策附錄。
- `materials/interview.md`：面試稿附錄。
- `materials/claim-boundary.md`：履歷邊界附錄。

AI 不准因為 Nick 問「研究分析報告在哪」就另創 `research-analysis-report.md` 或額外 README 造成重複與混亂。應直接回答：主報告在 `flow.md`。

既有 `iwin` 舊平鋪格式這輪先不搬。未來重整時再把輔助檔移入 `materials/`，並保留 `flow.md` 作為唯一主報告。

### Flow 可讀性分層

每份 `flow.md` 必須分成兩層，但仍然是同一份主報告，不新增 Step：

第一層：初階 / 中階可讀區，用來先看懂。

- 這條 flow 是什麼功能。
- 誰會用、什麼情境觸發。
- 成功後系統狀態變成什麼。
- Route / Controller / Service / Model / SQL / Redis / MQ / Log 對照。
- 最小架構圖，只畫本 flow 相關上下游。
- 正常流程圖，用 5 到 12 步看懂資料怎麼走。
- 若是後台、前端、BI 或 PHP 專案，要轉譯成 Nick 熟悉的後端分層語言。

第二層：Senior / Owner 深度區，用來判斷價值與風險。

- source of truth 與 projection / cache。
- transaction boundary。
- state transition。
- consistency / idempotency。
- retry / compensation / reconciliation。
- observability / auditability。
- failure window。
- owner decision / trade-off / rollback。
- interview / resume boundary 摘要。

架構圖與流程圖是 `flow.md` 的讀懂工具，不是新的任務名稱。未確認的節點必須標 `待確認`，不可為了畫完整而腦補。

### 證據層級標籤

flow、履歷、自傳與面試素材都要標註來源層級：

| 標籤 | 意義 | 使用邊界 |
| --- | --- | --- |
| `真實開發過` | 有 Nick 本人 MR / ticket / commit / production issue / 本人確認 | 可考慮進履歷，但仍要保守 |
| `專案存在 / code-backed` | code 確認有這條功能或 flow，但 Nick 貢獻未確認 | 可當分析素材，不可寫成 Nick 成果 |
| `分析素材 / learning-only` | AI 深掃後整理出來的學習與面試理解 | 不直接寫進正式履歷 |
| `外部案例 / non-local` | 官方文件、網路案例、通用設計模式 | 只能補理解、比較與追問 |
| `待確認` | 目前證據不足 | 不進履歷 |

沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認，不得把內容標成 `真實開發過`。

本人確認規則：

- Nick 明確說「我做過」、「我開發很多」、「這是我負責 / 參與」時，這本身就是 evidence，層級是 `本人確認`。
- `本人確認` 可以支撐履歷使用「參與、維護、開發、處理」等保守動詞；若要寫「主導、負責整體、獨立完成、改善 X%」，仍需要更強 evidence，例如 MR / ticket / release / incident / metric。
- AI 不得因為 path-specific commit 沒直接命中某一條 flow，就把 Nick 對整個 repo / project 的實際開發經驗判成沒有。正確做法是標成「本人確認，待 commit / ticket 補強」或「本人確認 + code-backed」，再回頭做 contribution consolidation。
- 如果本人確認和 git history 看起來不一致，AI 要列出差異並補查 branches / aliases / merge commits / squash commits / ticket，而不是直接否定本人確認。

### Project-level contribution consolidation

當 Nick 表示某個 project / repo 是主力開發經驗，或 AI 發現正式履歷低估該 repo 時，Career Track 可以先做 project-level contribution consolidation，不必等 Flow Track 的本批代表 flows 全部 Step 5。這種先行版本必須標成 `rolling / scoped consolidation`，並清楚列出掃描範圍、已完成 flow、未完成 flow、待回填 evidence、可放履歷 / 可面試講 / 不可誇大的邊界。Flow Track 之後照舊補 Step 3~5，新的 flow evidence 要回填 consolidation、05 / 08 與 claim boundary。

這條規則的優先級高於 Step 慣性。也就是說，如果目前 project 已累積 code-backed flow，但尚未做 contribution consolidation，且出現以下任一情況，下一步可以先做 `{project} contribution claim consolidation`，即使代表 flows 尚未全部完成 Step 5；但必須標明是 rolling / scoped，不得宣稱 final 或全量深掃：

- Nick 問「不用履歷嗎」、「怎麼沒有經驗」、「能不能放履歷」、「contribution claim consolidation」或質疑履歷 claim。
- AI 準備更新 `05-resume-master-zh.md`、`08-application-autobiography-zh.md`、project-level career boundary 或正式 claim。
- AI 已完成一條高價值 code-backed flow，但該 project 尚未判斷 Nick / `10gt12nc` 的真實開發範圍。

`contribution claim consolidation` 是 Career Track 的履歷 claim gate，不是新的 flow Step，也不是亂創任務。它的目的只是防止把「分析過 code」誤寫成「Nick 真實開發過」，也防止只因單條 flow 缺 direct path evidence 就抹掉整個 project 經驗。

contribution consolidation 分兩種：

- `rolling / scoped consolidation`：可以先做，用於近期履歷、面試材料與防止低估經驗。它掃人的貢獻線：Nick / `10gt12nc` commits、branches、重要 diff、本人確認、既有 flow evidence、已完成 flow KB 與目前可讀的 project 文件；必須列出未完成 flow、未深掃路徑與待回填 evidence。
- `final consolidation`：等 Step 2 定義的本批代表 flows 全部完成到 Step 5 後再做最終校正。它可以取代 rolling 版的暫定邊界。

若只有一條 flow 完整，也可以做 rolling consolidation，但不能寫成全 project final 結論。contribution consolidation 的目的是建立 project-level 履歷 claim 邊界，不是用單條 flow 代表整個 project。

contribution consolidation 必須至少重讀：

- 該 project README、Step 1 / Step 2、architecture map / project-level career docs。
- 該 project 所有已完成 flow KB：`flow.md`、`career-interview.md`、`materials/evidence.md`、`materials/claim-boundary.md`、`materials/interview.md`、`materials/decision-notes.md`。
- 來源 code repo 的 Nick / `10gt12nc` commits、branches、path-specific history、重要 diff，以及 Nick 本人確認。

正式履歷 / 自傳更新規則：

- 05 / 08 只吃 project-level consolidation 的結論，不直接吃單條 flow Step 5；rolling consolidation 的保守結論可以先進 05 / 08，但要保留 evidence 狀態與不可誇大邊界。
- 單條 flow Step 5 可以成為 consolidation 的 evidence，但不能單獨決定整個 project 的履歷說法。
- 若某 project 尚未 consolidation，AI 不得因為某條 flow 已 Step 5 就直接更新 05 / 08；也不得因為某條 flow 缺 direct evidence 就否定整個 project 經驗。需要更新時，先做 rolling / scoped consolidation。

contribution consolidation 必須至少掃：

- 全部 Nick / `10gt12nc` commits。
- Nick 可能使用的 author / email / branch aliases。
- 遠端 branches 與近期 branch。
- path-specific history 與重要 commit diff。
- 已完成 flow evidence、career-interview、claim-boundary。
- Nick 本人確認的實際工作內容。

輸出必須分三層：

1. `可放履歷：真實開發過`：有本人確認，最好再有 commit / branch / ticket / production issue / code-backed evidence；用保守動詞寫成履歷素材。
2. `可面試講：code-backed / 分析過`：系統存在且 AI 已深掃，可用於解釋架構、failure window、owner decision，但不包裝成 Nick 的成果。
3. `不可誇大`：不得寫主導完整系統、全權 owner、完整架構師、改善百分比、全部 provider / 全部 flow owner，除非有更強 evidence。

重要：單條 flow 的 Step 5 只判斷那條 flow 能不能進履歷；不能拿來否定整個 project 的履歷價值。像 `iwin/payment` 這種主力金流 repo，若 Nick 確認「開發很多」，可以先做 rolling project-level contribution consolidation，讓履歷線不要被 flow 深掃進度卡住；未完成的代表 flows 之後照舊回 Flow Track 補 Step 3~5。像 `iwin_gameserver` 這種已累積 code-backed money flow 但尚未確認 Nick 貢獻的 repo，也可以先做 rolling consolidation，但必須標清楚哪些只是 code-backed / 分析過、哪些是待確認、哪些不能寫成正式成果。

### Flow 完成後的下一步

一條 flow 做到 Step 5，只代表這一條 flow 的固定主線完成，不代表整個 project 完成。

正確判斷順序：

1. 先看同 project 的 Step 1 / Step 2 candidate ranking。
2. 如果同 project 還有未完成且值得做的 candidate flow，下一步回到同 project 選下一條 flow 做 Step 3。
3. 如果同 project 的剩餘 candidate 都不值得做，或需要更高價值後端 evidence，才建議換 project。
4. 換 project 必須講清楚原因；若 Nick 沒要求換 project，不要自行把下一步改成其他 project。

例：

```text
app_bi point-control-admin-operation Step 5 完成
-> 回到 app_bi Step 2 ranking
-> 選 app_bi 下一條最值得做的 flow
```

不應直接跳：

```text
iwin payment Step 1
```

除非 Nick 明確說要轉去 payment，或目前同 project 沒有值得繼續的 flow。

### 規格不可任意改

參考其他 workspace 時，AI 只能先做評估，不得直接改 `nick-vault` 規格。

除非 Nick 明確說「更新規則 / 維護 KB / 幫調整規格」，否則不准改：

- Step 主線。
- `projects/` 目錄規格。
- flow 檔案責任。
- 長期資料位置。
- README / INDEX 的角色。

若 AI 判斷規格需要調整，必須先說：

```text
我建議改哪裡
為什麼
會影響哪些檔案
是否要我更新規則
```

取得 Nick 明確同意後才改。

### playbook 編號不是 flow Step

`senior-owner-playbook/01~20` 是文件編號，用來分類工具箱、規則、學習路線、面試、履歷、能力矩陣、薪資談判、system design、互動問答診斷與遊戲後端架構選型。

它不是 project flow 的 Step 編號。

project flow 的 Step 固定只有：

```text
Step 1：找 candidate flows
Step 2：比較 candidate flows
Step 3：單條 flow 深挖
Step 4：轉面試 case
Step 5：單條 flow claim gate
```

Step 5 只判斷該 flow 是否能作履歷 / 面試素材，並輸出 claim boundary。正式更新 `05` / `08` 必須走 Career Track 的 project contribution claim consolidation。AI 不准把 playbook 檔名編號解讀成 flow 進度，也不准回答成「還有 Step 6~16」。

### 參考 workspace 邊界摘要

完整規則見上方「參考 workspace 工作法吸收規則」。本段只保留實務邊界，避免下一輪 AI 把外部 workspace 當成 `nick-vault` 的結構模板。

可參考：approval / 防呆、secrets redaction、docs / KB 導航、role gate / handoff / reviewer gate、source / KB 分離、relation map、generated / curated 分離、scope、non-goals、success criteria、baseline / evidence 分層、不留流水帳、以 git log 保留操作歷史。

不可照搬：iwin 的 deploy / JumpServer / k3s / Harbor / `.work` 規則、math 的新遊戲開發 / GDD / RTP / JAR / optimizer 規則、test001_unity 的 Unity docs / kb 雙層結構 / 素材 / 外包 / DLsite / Play Mode 流程、test001_RenPy 的 Ren'Py / visual novel domain-specific 規則與個人 side project 素材、任何公司 workspace 的複雜開發型 docs 結構、個人路徑、內部 host、token、密碼或環境細節。

採用判斷的執行順序：

1. 先讀來源 workspace 的入口規則、索引、操作協議與新流程代表文件。
2. 先回答「這是開發 workspace 專用流程，還是可轉成 `nick-vault` 的 KB 治理方法」。
3. 若只是評估，先只回覆採用建議，不改規格。
4. 若 Nick 要「維護 KB / 幫優化 / 幫改」，只更新最小必要規則檔，優先落在 `00` / `09` / `06` 或 `projects/CONVENTIONS.md`；不要為了參考流程新增新資料夾。
5. 若流程會影響 flow、履歷、JD 客製或下一步判斷，再同步受影響的 project README、todo、flow evidence 或 claim boundary。
6. 若只是開發型細節、工具腳本、子 repo 管理、部署或 GDD / RTP domain 規則，一律不進 `nick-vault`，最多在 final 說明不採用原因。

2026-06-01 參考 `math-workspace` 新流程後的結論：值得吸收的是「先定義定位 / non-goals / source base / behavior reference / baseline parity / reviewer gate」這種防超做方法；不跟進它的工程型目錄與新遊戲開發流程。對 `nick-vault` 來說，對應做法是：開新 project / flow / JD 客製前先寫清楚本輪目標、不是什麼、證據來源、成功條件與不可誇大邊界；不是把履歷 KB 改成開發 workspace。

## 只保留新資料

外層只保留新的長期結構：

- `senior-owner-playbook/`：通用方法論、提示詞、學習路線、面試與履歷。
- `projects/`：之後各專案整理後的新分析。
- `archive/`：已依 Nick 指示清空，只保留 `.gitkeep` 佔位；之後不要再把舊資料放回 archive，除非 Nick 明確要求。

## 主軸與輔助層

本 vault 的主軸不能偏掉。

主軸永遠是：

```text
production flow
-> evidence
-> failure / consistency
-> owner decision
-> interview / resume
```

輔助層只能用來服務主軸：

- 大專案 / 子專案地圖：用來知道 flow 在整個產品的位置，不是拿來畫一堆抽象架構圖。
- `materials/decision-notes.md`：用來補單條 flow 需要的技術硬底子，不是通用技術大全。
- 職涯能力矩陣：用來檢查初階 / 中階 / 資深 / Lead 缺口，不是每天發散的新學習清單。

如果輔助層開始讓文件變多、讀不完、或偏離 production flow，AI 必須主動收斂，回到「下一條最值得做的 flow」。

## Step 主線不可改寫

專案 flow 的預設主線固定為：

```text
Step 1：找 candidate flows
Step 2：比較 candidate flows
Step 3：單條 flow 深挖
Step 4：轉面試 case
Step 5：單條 flow claim gate
```

Step 5 不是 project-level 履歷結論。若 Nick 問的是整個 project 經驗、履歷、自傳、真實開發貢獻，下一步可以轉 Career Track 做 rolling / scoped project contribution claim consolidation；未完成的代表 flows 要標成待深掃 / 待回填，後續 Flow Track 照舊回 Step 2 ranking 補下一條 flow。final consolidation 才等本批代表 flows 全部 Step 5 後校正。

AI 可以在 Step 3 內補 `materials/evidence.md`、`materials/claim-boundary.md`、`materials/decision-notes.md`，也可以把下游 repo、runtime consumer、GM receiver 標成待確認。若既有 flow 是舊平鋪格式，先讀同名舊檔並標註待遷移。

但 AI 不可以把這些補充項目升級成新的下一步名稱，除非 Nick 明確要求。

例外:

- 如果上一個 Step 不乾淨，先建議重整上一個 Step。
- 如果 Nick 明確說「補 evidence」、「補 decision-notes」、「下游定位」、「架構圖」，才做該補充任務。
- 如果 Nick 只問「下一步」，且目前 Step 3 已完成、沒有履歷 / 自傳 / contribution claim gate 風險，預設回答 Step 4。
- 如果某條 flow 已完成 Step 5，預設回到同 project 的 candidate ranking，選下一條未完成 flow；不要直接跳其他 project。

## 不複製舊檔

新增內容必須重寫，不能直接把舊檔搬回來。若未來 Nick 另外提供舊資料，可以讀作參考，但輸出要整理成：

- 更少檔案
- 更清楚分類
- 更保守 claim
- 更容易讀
- 更適合面試與履歷

## 每次 Step / 任務前的模式化重讀規則

Nick 不需要每次提醒「重讀 KB」或「重讀 code」。AI 必須先判斷本輪是 `輕量問答`、`快速狀態`、`中量維護` 還是 `重度深掃`，再決定讀取範圍：

- `輕量問答`：職涯概念、焦慮收斂、一般策略、讀法說明、術語解釋。只讀必要入口或已知狀態，不掃 code / git log / projects 全量。
- `快速狀態`：Nick 只問 `下一步`、`還要幹嘛`、`都好了嗎`，且沒有指定 project / flow / JD。讀 README、todo、flow inventory 與必要 git status；若發現 active flow、待收口 consolidation 或履歷素材矛盾，再升級。
- `中量維護`：KB 規則、索引、todo、閱讀順序、小型一致性修正。讀 AGENTS、00、09 與受影響檔案，做 Relationship Check 與 `git diff --check`。
- `重度深掃`：flow Step、contribution consolidation、正式履歷 claim、Completeness Audit、逐行 / 極限深掃。完整讀 KB、project 文件、source repo branch / log / path history / important diff。

只要 Nick 下達以下任一類請求，AI 必須升級到 project / flow 對應的重讀：

- `{project} step1 / step2 / step3`
- `{project} {flow} Step N`
- `繼續`
- `重做`
- `深掃`
- `補 evidence`
- `轉面試`

Step / flow / claim 任務開始前至少要重讀：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- 該 project 既有 README / Step 文件 / flow 文件
- 相關 code repo 的目前分支、近期 log、相關 path-specific log、主要入口

掃公司 / 來源 code repo 前，AI 必須先確認遠端 refs 是否最新：

- 預設執行 `git fetch --all --prune` 或等效方式更新 remote refs。
- 記錄 local HEAD、`origin/{branch}` HEAD、是否 ahead / behind。
- 公司 repo 只能讀，不得自動 `pull`、merge、checkout、rebase 或改工作樹。
- 如果本機 branch 落後遠端，只能標示「本機未更新 / 待 Nick 確認」，除非 Nick 明確同意才更新工作樹。
- 如果因權限、網路或 repo 狀態無法 fetch，必須在 evidence 標示「未 fetch / 遠端最新性未確認」，不可宣稱已看最新 code。

如果是 Step 1 / Step 2：

- 重讀既有 Step 1 / Step 2，避免產生重複或過期 ranking。
- 重讀 code repo 的 branch / log / route / controller / service / job / config。

如果是 Step 3 之後：

- 重讀該 flow 既有 `flow.md`、`career-interview.md`、`materials/evidence.md`、`materials/interview.md`、`materials/claim-boundary.md`；若是舊格式，讀同層舊檔並標註待遷移。
- 重讀相關 code path 與 path-specific git log。
- 檢查是否有下游 repo 尚未定位。

如果沒有重讀到某項，必須在輸出中標明「未重讀 / 原因」，不能假裝已讀。

每次 Step 的 evidence 或掃描範圍必須明確寫：

- 是否已 fetch 遠端 refs。
- local HEAD。
- remote HEAD。
- 是否 ahead / behind。
- 若本機不是遠端最新，哪些判斷只基於本機狀態，哪些需要重新確認。

## 舊文件自動排查與更新規則

AI 不能只照 Nick 當下問的 Step 往後跳。每次重讀既有 project / flow 後，必須自己判斷舊文件是否需要修正。

需要主動標出的狀態：

- `可沿用`：文件已符合目前 KB，掃描範圍、evidence、未確認邊界都清楚。
- `需補 evidence`：方向可用，但缺掃描範圍、branch / commit / code path、下游未掃邊界。
- `需重整`：舊 Step 是舊規則產物，推測過多、履歷 claim 過強、格式不符合目前 flow 標準。
- `應暫停往下`：上一個 Step 還不乾淨，直接做下一步會放大錯誤。

判斷規則：

- 如果 Step 3 已存在但沒有清楚分出已確認 / 推測 / 待確認，下一步要建議「Step 3 重整」，不是 Step 4。
- 如果 `materials/evidence.md` 或舊格式 `evidence.md` 沒寫本次掃描等級、已掃 / 未掃、branch / log / path-specific history、下游是否已掃，必須先補 evidence。
- 如果 `flow.md` 把後台入口寫得像完整後端 flow，但沒有下游 repo evidence，必須降級描述並補待確認。
- 如果 `materials/interview.md`、`materials/claim-boundary.md` 或舊格式同名檔有誇大風險，必須先修 claim boundary，再談履歷。
- 如果舊文件只是名字相同但內容過粗，AI 要明確說「有舊版，但需重整」，不能回答成「已經有了」就結束。

輸出規則：

- Nick 問「下一步」時，AI 要先說目前最後一個可用 Step 是什麼，以及是否需要重整。
- 若發現舊文件不合格，只推薦一件事：先重整舊文件。
- 若舊文件已合格，才推薦進下一 Step。

## 每條 flow 的完成標準

完整 flow 學習包至少要能回答：

1. 白話來說這條 flow 是什麼功能。
2. 誰會使用、什麼情境觸發、成功後狀態變成什麼。
3. 用 Nick 熟悉的後端分層語言看，Route / Controller / Service / Model / SQL / Redis / MQ / Log 分別是什麼。
4. 最小架構圖與正常流程圖是否能讓人先看懂。
5. 這條 flow 解決什麼業務問題。
6. 入口在哪裡。
7. 主要 code 路徑是什麼。
8. DB / Redis / MQ / 外部 API 有哪些。
9. 正常流程怎麼跑。
10. 失敗、重試、補償、對帳怎麼處理。
11. Senior / Owner 應該注意什麼設計取捨。
12. 面試怎麼講。
13. 履歷怎麼保守寫。
14. 哪些不能誇大。
15. 這條 flow 牽涉哪些技術硬底子與技術選型差異。
16. 每條面試 / 履歷說法的證據層級是什麼。

## Code 掃描與分支證據規則

每次 Step 都要明確記錄「這次真的讀了什麼」，不能讓文件看起來像完整深掃但其實只看了局部。

必須記錄：

- 掃描的 repo 路徑。
- 當前分支。
- 是否看主分支。
- 是否看近期分支或相關 feature branch。
- 是否看 path-specific git log。
- 是否看 controller / service / repository / model / config / route / job / consumer。
- 是否看相關後端、下游服務、GM command handler、MQ consumer 或 callback receiver。
- 哪些沒有看，原因是找不到、權限不足、範圍外，或 Nick 要求先略過。

判斷規則：

- 沒掃其他分支，就寫「其他分支未掃」，不能假裝有比對。
- 只看到後台 / 前端，就寫「目前只確認操作入口與資料寫入，不確認下游實際生效邏輯」。
- 沒看到下游 code，就寫「下游行為待確認」。
- 沒看到 MQ / Kafka，就寫「未確認 MQ / Kafka」，不要硬補 distributed flow。
- 找不到 evidence 的地方可以寫「略」或「不展開」，不要湊滿格式。
- 外部通用知識可以補，但必須標成「外部案例 / 通用模式」，不能混成專案既有事實。

## 深掃等級定義

`深掃` 可以依任務大小彈性分級，但必須誠實標示深度。AI 不可以把快速 grep 包裝成深掃。

AI 必須主動判斷掃描深度，不要全部丟給 Nick 決定。

判斷原則：

- 只是找候選 flow：建議 Level 1。
- 已選定單一 flow，要建立學習包：建議 Level 2。
- 這條 flow 要變成強面試 case、履歷 evidence、bug history、或 owner decision 依據：建議 Level 3 或分批 Level 3。
- 如果目前只看到後台入口、下游 repo 未定位、或還不知道真正後端在哪裡，先不要直接 Level 3 後台；應建議先定位後端 / 下游 code。
- 如果 Level 3 範圍太大，AI 要主動拆批次，例如先 commit timeline，再 module scan，再 failure matrix。
- AI 若判斷不值得極限深掃，要直接說原因，不要為了迎合而產生大量低價值文件。

### Level 1：Flow 掃描

適用：Step 1 / Step 2、找 candidate flows。

要做到：

- 看 README / route / controller / service / job / consumer / config。
- 用 `rg` 找核心 domain keyword。
- 看主要入口與主要資料流。
- 看 path-specific git log。
- 找出 Top candidate flows。

可以不做：

- 不逐檔逐行。
- 不逐 commit diff。
- 不完整追所有 edge case。

### Level 2：Flow 深掃

適用：Step 3 之後、單條 flow 深挖。

要做到：

- 追完整 execution path。
- 讀 controller / service / repository / model / config / external client。
- 讀 DB / Redis / MQ / external API / log / audit 相關 code。
- 看主分支與近期相關分支。
- 看與該 flow 有關的 path-specific commit log。
- 看重要 commit diff，理解修改了哪裡、可能修什麼 bug、對 flow 有什麼影響。
- 寫清楚 failure window、retry、compensation、reconciliation、observability。

可以不做：

- 不必掃無關 module。
- 不必看整個 repo 每一個 commit。
- 不必把每個 class 都摘要。

### Level 3：極限深掃

適用：Nick 明確說「極限深度」、「逐檔逐行」、「每個 commit diff 都看過」、「完整拆完」。

要做到：

- 逐 module 建立掃描清單。
- 逐檔逐行讀與該 flow 有關的 code。
- 每個相關 commit 的 diff 都要看。
- 對每個重要 commit 記錄：
  - commit hash
  - 修改檔案
  - 修改內容
  - 推測修的 bug / 需求
  - 對資料狀態與 production flow 的影響
  - 是否有後續 revert / follow-up / 收斂 commit
- 比對主分支與近期相關分支差異。
- 查是否有測試、log、config、migration、SQL、排程、consumer、callback receiver。
- 明確標出未掃原因與下一批。

限制：

- 極限深掃通常不能一次塞進一份文件。應拆成批次，例如 module 1、module 2、commit history、failure matrix。
- 沒有看完就不能寫「完整已掃」。
- 不知道 commit 背後真實需求時，只能寫「從 diff 推測」，不能當成事實。
- 若 flow 核心在其他後端 repo，必須建議轉去掃該 repo，不要在後台 repo 硬湊。

## 後台 / 前端 / BI 類 flow 規則

後台、前端、BI、admin 類專案可以用來理解入口、權限、操作表單、資料寫入、匯出、audit log 與對接方式，但不能自動等同 Nick 的後端主成果。

整理時要分清楚：

- 這只是操作入口。
- 這是後台寫入 DB / Redis / config。
- 這是呼叫後端 / 下游服務的對接點。
- 真正後端生效邏輯在哪個 repo，若未掃到就標待確認。

履歷處理：

- 如果 Nick 不是該後台主開發，履歷自傳只簡單帶過「理解 / 對接 / 分析後台操作 flow」。
- 不寫主導後台、不寫負責完整控制系統、不寫 owner。
- 若這條 flow 的後端核心在其他 repo，優先回到後端 repo 深挖，再決定是否能寫進履歷。

## 05 / 08 履歷自傳最終版規則

`05-resume-master-zh.md` 與 `08-application-autobiography-zh.md` 是正式履歷 / 自傳母稿，不是每條 flow 的暫存區。

在所有相關專案整理完成前，AI 只能做保守微調或加註證據狀態；不得把單條未驗證 flow 直接寫成正式成果。

若 Nick 要求「最後整理履歷 / 自傳」或「更新 05 / 08 最終版」，AI 必須先做最終核對：

- 掃描相關 code repo 的主分支、近期分支、path-specific log 與重要 commit diff。
- 若是 Nick 明確說實際做很多的主力 repo，可以先做 rolling / scoped project-level contribution consolidation，不得只根據單條 flow 的 Step 5 直接排除該 repo 經驗；未完成 flow 標成待深掃 / 待回填，之後再補 Flow Track。
- 全面掃 Nick / `10gt12nc` commits、branches、重要 diff 與可能的 author aliases，並和 Nick 本人確認內容交叉整理。
- 掃描 `projects/` 已完成 flows、project-level career-interview、flow-level career-interview。
- 掃描 `projects/`、`senior-owner-playbook/`、KB 中所有履歷素材；`archive/` 目前已清空，不再列為必要來源。
- 去重、降誇大、刪除沒有 evidence 的強 claim。
- 每條履歷 bullet 標註來源層級：`真實開發過` / `專案存在` / `分析素材` / `待確認`。
- 沒有 evidence 的內容只能留在待確認或面試分析素材，不寫進正式投遞句子。
- 若本人確認足夠但 commit / ticket 尚未補齊，履歷可用「參與 / 維護 / 開發」保守口徑，並在 claim boundary 標註「本人確認，待 commit / ticket 補強」。

## 每次完成後的下一步 / 自由提問規則

AI 每次完成 Step、flow 文件或 KB 更新後，不可以只說「完成」，但也不可以在已收斂時硬塞任務。

判斷方式：

- 如果目前仍在單條 flow、project Step、contribution consolidation 或 rolling resume package 的未收口狀態，要補「下一步建議」。
- 如果目前已達通用投遞包可用、面試稿已完成、沒有特定 JD、沒有 active flow、Nick 也沒有指定下一個任務，則進入「自由提問 / 彈性指定」狀態。
- 自由提問狀態下，AI 只回報目前狀態、已完成什麼、可問哪些方向，不輸出 fenced prompt，不把 `iwin system map v1`、面試練習、JD 客製或新 flow 包裝成預設下一步。
- Nick 可以自由問薪資、履歷、面試、某條 flow、架構圖、缺口、學習方向、或直接指定下一個 Step；AI 再依當下問題套用對應規則。

下一步建議規則：

- 只推薦一件最值得做的事，不列一長串選項。
- 必須附上 Nick 可以直接複製的短 prompt，並用 fenced code block 包起來，格式固定為 ` ```text ... ``` `。
- code block 內只放下一句要貼給 AI 的 prompt，不放解釋、原因、標點裝飾或多個選項。
- 要依目前完成狀態判斷，不要套模板。
- 要說清楚為什麼現在做它。
- 要說清楚會產出哪些檔案或更新哪些既有檔案。
- 要說清楚是否會更新履歷；預設不更新履歷，除非 Nick 明確要求或 evidence 已足夠。
- 要說清楚 commit / push 狀態；小修輕量自查後 commit，重大改動全掃確認後 commit；若需要 push，直接觸發 `git push` approval 視窗。
- 若 Nick 要的是待辦事項、缺口清單、KB 維護或優先順序，下一步只更新 todo / KB / index，並停在「請 Nick 下 Step」；不得直接替 Nick 開始 Flow Track 的 Step 4 / Step 5。
- 如果同一條 flow 還沒完整，優先建議繼續補這條 flow，而不是換下一條。
- 如果 project 尚未做 contribution consolidation，且下一步會影響履歷 claim 或 Nick 正在追問履歷價值，可以先建議 `{project} contribution claim consolidation` rolling / scoped 版；未完成代表 flows 要列為待回填，後續再回 Flow Track 補深度。
- 如果 flow 的資料流已清楚，但 Nick 對底層技術不穩，可以在 Step 3 內補 `materials/decision-notes.md`；但若 Nick 問「下一步」且 Step 3 已完成、沒有履歷 / 自傳 / contribution claim gate 風險，預設仍建議 Step 4，不要把 decision notes 變成新 Step。`materials/teaching-notes.md` 更低優先級，預設先不補；只有 Nick 讀該 flow 卡住、面試回饋指出基本功缺口，或 Nick 明確要求補教學時才補。
- 如果 Nick 問「接下來」、「下一步」、「建議」，AI 要能根據目前 vault 狀態直接回答，不要求 Nick 重貼規則。
- 如果目前已是收斂狀態，回答「沒有預設下一步；可以自由提問或彈性指定」，並簡短列出可選方向，但不要把清單當成必做 backlog。
- 收斂狀態下可以用小儀表板格式：`必做收口：目前沒有 / 有 X`、`可選加強：廣度、深度或架構視角各 1 個以內`、`暫不建議：明確排除低 ROI 或焦慮型任務`。目的不是讓 Nick 一次選很多，而是讓下一步可廣可深、能伸能縮。

建議順序：

0. 若 Nick 明確要求待辦事項、缺口清單、KB 維護或優先順序，先維護 todo / KB / index，列清楚缺口與建議，不自動執行 flow Step。
0.5. 若 Nick 明確要求地圖，才補最小地圖；否則不要插入新流程。
0.6. 若 project 尚未做 contribution consolidation，且目前問題牽涉履歷 / 自傳 / claim 或 Nick 對「是否有經驗」提出質疑，可以先做 `{project} contribution claim consolidation` rolling / scoped 版；未完成代表 flows 要列為待回填，後續再回 Step 2 ranking 繼續補同 project 下一條代表 flow。若本批代表 flows 已全部 Step 5，可標成 final consolidation。
1. Step 1 完成後：建議做 Step 2，比較 candidate flows。
2. Step 2 完成後：建議挑排名最高且 evidence 足夠的單一 flow 做 Step 3。
3. Step 3 完成後：建議補 failure scenarios、consistency、idempotency、retry、compensation、reconciliation。
4. Step 3 文件已乾淨後：建議 Step 4，轉面試 case。
5. Step 4 完成後：建議檢查 claim boundary，再決定是否進 Step 5。
6. 面試 case 完成後：建議檢查 claim boundary，再決定是否更新履歷。
7. 履歷 evidence 不足時：建議先補 MR / ticket / commit / 實際參與證據，不更新履歷。

防錯補充：

- 如果 Step 1 完成但 Step 2 不存在，下一步只能是 Step 2。
- 多 module / monorepo 專案的 Step 2 必須比較候選 flow 的 module / service / repo 邊界；不能只在 Step 1 選一條 flow 後直接進 Step 3。

## Senior / Lead / Architect 對標

Senior 不是背技術名詞，而是能把 production flow 講清楚：

- money correctness
- transaction consistency
- idempotency
- retry / compensation
- auditability
- observability
- deployment risk
- operation recovery
- trade-off
- ownership boundary

Lead / Architect 更進一步，要能回答：

- 系統邊界怎麼切。
- 哪裡是 source of truth。
- 哪些資料只是 projection / cache / report。
- 怎麼讓團隊可以維護。
- 怎麼降低事故半徑。
- 怎麼用漸進式方案落地，而不是空談重構。

## 安全線

不得寫入：

- token
- password
- private key
- internal IP
- production URL
- 可識別客戶或公司機密的資訊

寫履歷時不得把分析成果包裝成已上線成果。
