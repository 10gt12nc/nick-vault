# AI 整理規則

本專案是 `nick-vault`，Nick 的個人資源整理庫。這不是程式專案，不需要複雜的工程式 notes/logs/analysis 結構。

## 目標

- 目前主入口是 `senior-owner-playbook/`。
- 目標是整理 Nick 的 Senior Java Backend / Platform Backend / System Owner 學習資料、專案 flow、面試案例與履歷素材。
- 把已經用過、等待 Nick 人工審查是否刪除的原始資料放在 `archive/`。
- 新內容必須重新整理、去重、結合、優化，不要直接複製舊檔。
- 使用繁體中文作為主要說明語言。
- 保留清楚索引，讓下一個 AI 能快速知道資料在哪裡。

## 目錄規則

- `senior-owner-playbook/`：放跨專案通用的方法論、提示詞、學習路線、面試案例、唯一履歷 master 與投遞用自傳。
- `projects/`：未來放各專案整理後的新分析，例如 `projects/iwin/payment/flows/{flow}/`。
- `archive/`：只放舊資料、舊中間稿、原始匯入、待分析與等待人工審查刪除的資料。
- 不要新增 `ai-notes/`、`docs/`、`.claude/`、`.codex/`、`resources/` 作為長期資料位置。
- 密鑰與 token 只能放本機 `.env`，不得寫入 Markdown 或 commit。

## 近期排查防錯紀錄

- 「不要維護流水帳」的意思是：不要建立或保留「今天做什麼、昨天做什麼、某次操作紀錄、records、operation-log、work-report」這類時間序列工作日誌。不是要改掉現有 `flow.md / evidence.md / interview.md / claim-boundary.md` 的分析結構。
- `flow.md` 就是單條 flow 的研究分析報告。不要另創 `research-analysis-report.md`、額外 README 或重複總覽檔，除非 Nick 明確要求。
- `materials/evidence.md`、`materials/decision-notes.md`、`materials/interview.md`、`materials/claim-boundary.md` 是附錄 / 輔助文件，不是要 Nick 自己拼成報告；舊平鋪格式的同名檔先視為待遷移舊格式。
- 新建或重整後的 flow 資料夾，預設只讓 Nick 直接讀 `flow.md` 與該 flow 的 `career-interview.md`；其他 evidence、decision、interview、claim 邊界要收在 `materials/`，避免主閱讀面混亂。既有 `iwin` 舊資料夾這輪先不搬，等 Nick 明確要求再遷移。
- flow、履歷、自傳與面試素材都要標註證據層級：`真實開發過`、`專案存在 / code-backed`、`分析素材 / learning-only`、`外部案例 / non-local`。沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認，不得標成真實開發過。
- 參考 workspace 正確路徑：
  - `/Users/nick/Git/iwin/iwin-workspace`
  - `/Users/nick/Git/antplay/math-workspace`
- 參考其他 workspace 只能用來學防呆、索引、KB 治理與「不留流水帳」原則；不能直接照搬其開發型 docs / deploy / `.work` / 子 repo 規則到 `nick-vault`。
- 規格不可隨意改。若只是「評估一下」，AI 只能提出建議與理由；未經 Nick 明確要求，不得改既有目錄規格、Step 主線、檔案責任或新增替代結構。
- 一條 flow 完成 Step 5 後，不代表整個 project 完成。若同 project 的 Step 1 / Step 2 還有未完成 candidate flows，下一步要回到同 project 選下一條 flow；不要自行跳到其他 project，除非 Nick 明確說要換專案。
- `senior-owner-playbook/01~16` 是工具箱 / 規則 / 學習路線的文件編號，不是 flow 的 Step 1~16。flow Step 固定只有 Step 1~5。

## Senior / Owner 原則

- 以下規則是全 vault 共用規則，適用所有 `projects/{domain}/{project}`、所有 flow、所有 Step；不是只適用 `app_bi`。
- 不要做 class summary。
- 不要平均整理所有功能。
- 優先整理 production flow。
- 每次只做一條 flow。
- 每次 Nick 下 `project stepN`、`某 flow Step N`、`下一步`、`繼續` 時，AI 必須自動重讀本 vault KB、既有專案文件與相關 code 最新狀態，不需要 Nick 另外提醒「重讀 KB / 重讀 code」。
- 自動重讀至少包含：`AGENTS.md`、`senior-owner-playbook/00-operating-rules.md`、`senior-owner-playbook/09-ai-prompt-manual.md`、`senior-owner-playbook/03-flow-learning-package-template.md`、該 project 既有 README / Step 文件 / flow 文件、相關 code repo 的 branch / log / 關鍵入口。
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
- 每次完成 Step 或 flow 更新後，必須自動給 Nick「下一步建議」，且只推薦一件最值得做的事。
- 下一步建議要說明：為什麼現在做它、會產出什麼、是否會更新履歷、是否需要 commit / push。
- 不可以自行創造新 Step 或新下一步名稱；下游定位、補 evidence、補 decision-notes、架構圖都只能是目前 Step 內的待確認或補充，除非 Nick 明確指定。
- 如果 Step 3 已完成且文件乾淨，下一步預設是 Step 4，不是下游定位或其他自創任務。
- 如果某條 flow 已完成 Step 5，下一步預設回到同 project 的 candidate flow ranking，選下一條未完成 flow；不要自動跨 project。
- 如果下一步是繼續同一條 flow，優先建議往 failure / consistency / interview / claim boundary 補齊，而不是立刻換新 flow。
- 每次 Step 都要在 `materials/evidence.md` 或舊格式對應 evidence 文件寫清楚本次實際掃描範圍：主分支、近期分支、相關 code path、相關後端 / 下游 repo 是否有看。
- 如果沒有掃其他分支、沒有看到下游 code、或只看到後台 / 前端操作面，必須明確標成「未掃 / 待確認 / 只作關聯入口」，不能自行補成完整後端 flow。
- 後台、前端、BI、admin 類專案若不是 Nick 主開發，只能作為理解入口與對接 flow；履歷自傳應簡單帶過，不得硬包裝成主導後台或完整系統 owner。
- 沒有實際 evidence 的技術點可以寫「略」、「不展開」、「建議補讀外部文章 / 官方文件」，不要為了湊滿格式而腦補。
- `senior-owner-playbook/05-resume-master-zh.md` 與 `08-application-autobiography-zh.md` 只有在專案整理到足夠完整後才做最終版。最終更新前必須深度掃描 code 分支、path-specific history、重要 commit diff、所有 KB / archive 內履歷自傳素材，並逐條標註哪些是 Nick 真實做過、哪些只是專案存在或分析素材。

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

## 檢查

更新後檢查：

- 是否已自動重讀 KB、既有文件與相關 code 最新狀態？
- 是否已檢查既有 Step / flow 是否過舊、缺 evidence 或不符合目前 KB？
- 是否只動 `nick-vault`？
- 是否沒有動公司專案？
- 是否沒有新增 code？
- 是否沒有 secret / token / internal IP / production URL？
- 是否有把推測與已確認分開？
- 是否有避免履歷誇大？
- 是否有更新 README 或 todo？
