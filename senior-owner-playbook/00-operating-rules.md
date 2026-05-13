# 維護規則

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
