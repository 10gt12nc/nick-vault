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
- 自動判斷是否需要補 `materials/decision-notes.md`，用來整理技術硬底子、技術選型比較、trade-off 與 owner decision。
- 自動確認 `flow.md` 是否有初階 / 中階可讀區：白話導讀、Code 分層對照、最小架構圖、正常流程圖與逐步說明。沒有這一層時，不能只補 Senior / Owner 風險分析就算完成。
- 小型 / 低風險改檔可以輕量自查後直接 commit；重大 / 實質改檔必須完整全掃確認後 commit；若本輪需要 push，AI 必須直接執行 `git push` 觸發 approval 視窗，不得只停在本地文字回報。

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
- 確認沒有 secret、token、internal IP、production URL、客戶資料。

### 重大 / 實質改檔

重大 / 實質改檔必須完整全掃確認，例如：

1. 自行再全掃確認一次，不等 Nick 追問。
2. 全掃確認至少包含：
   - 重讀本次改過的檔案。
   - 重讀受影響的共用規則 / prompt / README / index。
   - 檢查新規則是否和既有 KB 衝突。
   - 跑 `git diff --check`。
   - 跑 `git status --short` 確認只動 `nick-vault` 預期檔案。
   - 檢查沒有 secret、token、internal IP、production URL、客戶資料。
   - 檢查履歷 / 面試 claim 沒有誇大，且已標示 evidence 層級。
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
先讀懂功能與 code 分層
-> 再理解正常資料流
-> 再分析 failure / consistency / owner decision
-> 最後轉面試與履歷邊界
```

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

`senior-owner-playbook/01~16` 是文件編號，用來分類工具箱、規則、學習路線、面試、履歷與能力矩陣。

它不是 project flow 的 Step 1~16。

project flow 的 Step 固定只有：

```text
Step 1：找 candidate flows
Step 2：比較 candidate flows
Step 3：單條 flow 深挖
Step 4：轉面試 case
Step 5：檢查是否更新履歷 / 自傳
```

AI 不准把 playbook 檔名編號解讀成 flow 進度，也不准回答成「還有 Step 6~16」。

### 參考 workspace 邊界

正確參考路徑：

- `/Users/nick/Git/iwin/iwin-workspace`
- `/Users/nick/Git/antplay/math-workspace`

可參考：

- approval / 防呆規則。
- secrets redaction。
- docs / KB 導航。
- 不保留 records / operation-log / work-report 流水帳。
- 以 git log 保留操作歷史。

不可照搬：

- iwin 的 deploy、JumpServer、k3s、Harbor、`.work/<service>` 規則。
- math 的新遊戲開發 / GDD / RTP / JAR 模組規則。
- 任何公司 workspace 的複雜開發型 docs 結構。
- 個人路徑、內部 host、token、密碼或環境細節。

## 只保留新資料

外層只保留新的長期結構：

- `senior-owner-playbook/`：通用方法論、提示詞、學習路線、面試與履歷。
- `projects/`：之後各專案整理後的新分析。
- `archive/`：舊資料、舊中間稿、原始匯入、待分析、待刪與重複筆記，日後由 Nick 人工審查是否刪除。

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
Step 5：檢查是否更新履歷 / 自傳
```

AI 可以在 Step 3 內補 `materials/evidence.md`、`materials/claim-boundary.md`、`materials/decision-notes.md`，也可以把下游 repo、runtime consumer、GM receiver 標成待確認。若既有 flow 是舊平鋪格式，先讀同名舊檔並標註待遷移。

但 AI 不可以把這些補充項目升級成新的下一步名稱，除非 Nick 明確要求。

例外:

- 如果上一個 Step 不乾淨，先建議重整上一個 Step。
- 如果 Nick 明確說「補 evidence」、「補 decision-notes」、「下游定位」、「架構圖」，才做該補充任務。
- 如果 Nick 只問「下一步」，且目前 Step 3 已完成，預設回答 Step 4。
- 如果某條 flow 已完成 Step 5，預設回到同 project 的 candidate ranking，選下一條未完成 flow；不要直接跳其他 project。

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
- 掃描 `projects/` 已完成 flows、project-level career-interview、flow-level career-interview。
- 掃描 `archive/` 舊履歷、自傳、career、ai-notes、KB 中所有履歷素材。
- 去重、降誇大、刪除沒有 evidence 的強 claim。
- 每條履歷 bullet 標註來源層級：`真實開發過` / `專案存在` / `分析素材` / `待確認`。
- 沒有 evidence 的內容只能留在待確認或面試分析素材，不寫進正式投遞句子。

## 每次完成後的自動下一步建議

AI 每次完成 Step、flow 文件或 KB 更新後，不可以只說「完成」。必須自動補一段「下一步建議」。

下一步建議規則：

- 只推薦一件最值得做的事，不列一長串選項。
- 必須附上 Nick 可以直接複製的短 prompt，並用 fenced code block 包起來，格式固定為 ` ```text ... ``` `。
- code block 內只放下一句要貼給 AI 的 prompt，不放解釋、原因、標點裝飾或多個選項。
- 要依目前完成狀態判斷，不要套模板。
- 要說清楚為什麼現在做它。
- 要說清楚會產出哪些檔案或更新哪些既有檔案。
- 要說清楚是否會更新履歷；預設不更新履歷，除非 Nick 明確要求或 evidence 已足夠。
- 要說清楚 commit / push 狀態；小修輕量自查後 commit，重大改動全掃確認後 commit；若需要 push，直接觸發 `git push` approval 視窗。
- 如果同一條 flow 還沒完整，優先建議繼續補這條 flow，而不是換下一條。
- 如果 flow 的資料流已清楚，但 Nick 對底層技術不穩，可以在 Step 3 內補 `materials/decision-notes.md`；但若 Nick 問「下一步」且 Step 3 已完成，預設仍建議 Step 4，不要把 decision notes 變成新 Step。
- 如果 Nick 問「接下來」、「下一步」、「建議」，AI 要能根據目前 vault 狀態直接回答，不要求 Nick 重貼規則。

建議順序：

0. 若 Nick 明確要求地圖，才補最小地圖；否則不要插入新流程。
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
