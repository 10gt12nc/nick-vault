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

AI 需要自動維護：

- 自動重讀 KB。
- 自動重讀該 project 既有文件。
- 自動重讀相關 code 最新狀態。
- 自動檢查既有 Step / flow 文件是否過舊、缺 evidence、舊格式或不符合目前 KB。
- 自動判斷掃描等級。
- 自動標示已掃 / 未掃 / 待確認。
- 自動避免履歷誇大。
- 自動給下一步建議。
- 自動判斷是否需要更新 project README、Step 文件、flow evidence、claim boundary 或共用索引。

AI 不會做的事：

- 不會在 Nick 沒有要求時背景定期掃 repo。
- 不會自動 commit / push，除非 Nick 明確要求或本對話已要求推送。
- 不會把後台入口硬包裝成後端成果。

## 只保留新資料

外層只保留新的長期結構：

- `senior-owner-playbook/`：通用方法論、提示詞、學習路線、面試與履歷。
- `projects/`：之後各專案整理後的新分析。
- `archive/`：舊資料、舊中間稿、原始匯入、待分析、待刪與重複筆記，日後由 Nick 人工審查是否刪除。

## 不複製舊檔

新增內容必須重寫，不能直接把舊檔搬回來。可以讀舊資料，但輸出要整理成：

- 更少檔案
- 更清楚分類
- 更保守 claim
- 更容易讀
- 更適合面試與履歷

## 每次 Step 前的自動重讀規則

Nick 不需要每次提醒「重讀 KB」或「重讀 code」。只要 Nick 下達以下任一類請求，AI 必須自動重讀：

- `{project} step1 / step2 / step3`
- `{project} {flow} Step N`
- `下一步`
- `繼續`
- `重做`
- `深掃`
- `補 evidence`
- `轉面試`

每次開始前至少要重讀：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- 該 project 既有 README / Step 文件 / flow 文件
- 相關 code repo 的目前分支、近期 log、相關 path-specific log、主要入口

如果是 Step 1 / Step 2：

- 重讀既有 Step 1 / Step 2，避免產生重複或過期 ranking。
- 重讀 code repo 的 branch / log / route / controller / service / job / config。

如果是 Step 3 之後：

- 重讀該 flow 既有 `flow.md`、`evidence.md`、`interview.md`、`claim-boundary.md`。
- 重讀相關 code path 與 path-specific git log。
- 檢查是否有下游 repo 尚未定位。

如果沒有重讀到某項，必須在輸出中標明「未重讀 / 原因」，不能假裝已讀。

## 舊文件自動排查與更新規則

AI 不能只照 Nick 當下問的 Step 往後跳。每次重讀既有 project / flow 後，必須自己判斷舊文件是否需要修正。

需要主動標出的狀態：

- `可沿用`：文件已符合目前 KB，掃描範圍、evidence、未確認邊界都清楚。
- `需補 evidence`：方向可用，但缺掃描範圍、branch / commit / code path、下游未掃邊界。
- `需重整`：舊 Step 是舊規則產物，推測過多、履歷 claim 過強、格式不符合目前 flow 標準。
- `應暫停往下`：上一個 Step 還不乾淨，直接做下一步會放大錯誤。

判斷規則：

- 如果 Step 3 已存在但沒有清楚分出已確認 / 推測 / 待確認，下一步要建議「Step 3 重整」，不是 Step 4。
- 如果 `evidence.md` 沒寫本次掃描等級、已掃 / 未掃、branch / log / path-specific history、下游是否已掃，必須先補 evidence。
- 如果 `flow.md` 把後台入口寫得像完整後端 flow，但沒有下游 repo evidence，必須降級描述並補待確認。
- 如果 `interview.md` 或 `claim-boundary.md` 有誇大風險，必須先修 claim boundary，再談履歷。
- 如果舊文件只是名字相同但內容過粗，AI 要明確說「有舊版，但需重整」，不能回答成「已經有了」就結束。

輸出規則：

- Nick 問「下一步」時，AI 要先說目前最後一個可用 Step 是什麼，以及是否需要重整。
- 若發現舊文件不合格，只推薦一件事：先重整舊文件。
- 若舊文件已合格，才推薦進下一 Step。

## 每條 flow 的完成標準

完整 flow 學習包至少要能回答：

1. 這條 flow 解決什麼業務問題。
2. 入口在哪裡。
3. 主要 code 路徑是什麼。
4. DB / Redis / MQ / 外部 API 有哪些。
5. 正常流程怎麼跑。
6. 失敗、重試、補償、對帳怎麼處理。
7. Senior / Owner 應該注意什麼設計取捨。
8. 面試怎麼講。
9. 履歷怎麼保守寫。
10. 哪些不能誇大。

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

## 每次完成後的自動下一步建議

AI 每次完成 Step、flow 文件或 KB 更新後，不可以只說「完成」。必須自動補一段「下一步建議」。

下一步建議規則：

- 只推薦一件最值得做的事，不列一長串選項。
- 要依目前完成狀態判斷，不要套模板。
- 要說清楚為什麼現在做它。
- 要說清楚會產出哪些檔案或更新哪些既有檔案。
- 要說清楚是否會更新履歷；預設不更新履歷，除非 Nick 明確要求或 evidence 已足夠。
- 要說清楚是否需要 commit / push；預設不 commit、不 push，除非 Nick 明確要求。
- 如果同一條 flow 還沒完整，優先建議繼續補這條 flow，而不是換下一條。
- 如果 Nick 問「接下來」、「下一步」、「建議」，AI 要能根據目前 vault 狀態直接回答，不要求 Nick 重貼規則。

建議順序：

1. Step 1 完成後：建議做 Step 2，比較 candidate flows。
2. Step 2 完成後：建議挑排名最高且 evidence 足夠的單一 flow 做 Step 3。
3. Step 3 完成後：建議補 failure scenarios、consistency、idempotency、retry、compensation、reconciliation。
4. failure / consistency 補完後：建議轉面試 case，讓 Nick 能用 3 分鐘講清楚。
5. 面試 case 完成後：建議檢查 claim boundary，再決定是否更新履歷。
6. 履歷 evidence 不足時：建議先補 MR / ticket / commit / 實際參與證據，不更新履歷。

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
