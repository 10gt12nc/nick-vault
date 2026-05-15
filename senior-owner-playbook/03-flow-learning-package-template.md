# Flow 學習包模板

每次只建立一條 flow。不要一次產很多。

本模板是全 `projects/` 共用模板，適用所有 domain / project / flow，不是單一專案專用。

## 自動重讀

每次建立或更新 flow 學習包前，AI 必須自動重讀：

- KB 規則
- 該 project 既有 Step 文件
- 該 flow 既有文件
- 相關 code path 與 git log

Nick 不需要每次提醒「重讀 KB / 重讀 code」。

沒有重讀到的部分，必須在 evidence 裡標出。

重讀後也要檢查既有 flow 文件是否符合目前 KB：

- `可沿用`：格式、evidence、邊界都清楚。
- `需補 evidence`：內容方向可用，但掃描範圍、branch / log / 下游未掃邊界不足。
- `需重整`：舊規則產物，推測過多、格式不乾淨或 claim 有誇大風險。
- `應暫停往下`：上一個 Step 不乾淨，直接做下一 Step 會讓錯誤擴大。

若既有 Step 3 不合格，要先重整 Step 3，不要直接跳 Step 4。

## 自動維護

每次建立或更新 flow 後，AI 必須自動判斷是否要同步維護：

- project README
- Step 文件
- `materials/evidence.md`
- `materials/claim-boundary.md`
- flow-level `career-interview.md`
- project-level `career-interview.md`
- 共用 playbook 規則

預設不更新履歷 master；只有 evidence 足夠且 Nick 明確要求時才更新。

## 閱讀層次

每條 flow 的 `flow.md` 必須先讓 Nick 讀懂，再進 Senior / Owner 深挖。這不是新增 Step，而是 Step 3 以後 `flow.md` 的固定寫法。

```text
第一層：初階 / 中階可讀區
-> 第二層：Senior / Owner 深度區
```

第一層要回答：

- 這是什麼功能。
- 誰會用。
- 什麼情境觸發。
- 成功後系統狀態變成什麼。
- Route / Controller / Service / Model / SQL / Redis / MQ / Log 對照。
- 最小架構圖。
- 正常流程圖。
- 正常流程逐步說明。

第二層才回答：

- source of truth。
- state transition。
- transaction boundary。
- consistency / idempotency。
- retry / compensation / reconciliation。
- observability / auditability。
- failure window。
- owner decision / trade-off。
- interview / resume boundary。

## 深掃等級

Flow 學習包預設使用 Level 2 Flow 深掃。

- Level 1 Flow 掃描：找 candidate flows，不逐檔逐行。
- Level 2 Flow 深掃：單條 flow 追完整 code path、資料流、相關 commit log、重要 diff、failure window。
- Level 3 極限深掃：Nick 明確要求時才啟用，逐 module、逐檔逐行、逐相關 commit diff，追每筆改動原因、bug context、後續 revert / follow-up / 收斂。

若沒有完成 Level 3，不得說「完整逐檔逐行已掃」。

AI 需要主動判斷深度：

- 不確定 flow 值不值得做時，先 Level 1。
- 已選定 flow 時，先 Level 2。
- 需要強 evidence、bug history、履歷 claim 或 owner decision 時，再建議 Level 3。
- 如果目前只是後台入口，且真正後端 / 下游未定位，先建議去定位後端 repo，不要直接把後台做 Level 3。

## 檔案位置

```text
projects/{domain}/{project}/flows/{flow-name}/
  flow.md
  career-interview.md
  materials/
    evidence.md
    interview.md
    claim-boundary.md
    decision-notes.md
```

Step 1 只盤點候選 flow 時，不建立 flow folder。等 Nick 選定單一 flow 後，才依 `projects/CONVENTIONS.md` 建立完整資料夾。

`flow.md` 是唯一主報告與預設閱讀入口。`career-interview.md` 是該 flow 的保守履歷 / 面試素材。`materials/` 只放證據、技術決策、面試稿細節與不可誇大邊界。

既有舊平鋪格式可以先沿用，但要標註「舊格式 / 待遷移」。未經 Nick 要求，不批量搬動既有 `iwin` 資料。

## Prompt

```text
請只做一條 flow：{flow-slug}

請先讀：
- /Users/nick/Git/nick/nick-vault/projects/CONVENTIONS.md

請深掃：
- /Users/nick/Git/nick/nick-vault/archive
- 相關 code repo

只能動 nick-vault。
不要複製舊文，請重寫成新的學習包。
不要腦補。沒有 evidence 就寫「待確認」。
不要寫 token、密碼、內網 IP、production URL。

請輸出到：
- projects/{domain}/{project}/flows/{flow-name}/flow.md
- projects/{domain}/{project}/flows/{flow-name}/career-interview.md
- projects/{domain}/{project}/flows/{flow-name}/materials/evidence.md
- projects/{domain}/{project}/flows/{flow-name}/materials/interview.md
- projects/{domain}/{project}/flows/{flow-name}/materials/claim-boundary.md

請產出：
1. 閱讀定位與 evidence 層級
2. 白話導讀
3. 初中階 Code 分層對照
4. 最小架構圖
5. 正常流程圖
6. 正常流程逐步說明
7. 業務問題
8. 系統位置
9. 入口與 code 路徑
10. DB / Redis / MQ / 外部 API
11. 資料狀態與 state transition
12. 失敗情境
13. 補償 / retry / 對帳
14. Senior / Owner 設計取捨
15. Lead / Architect 追問
16. 面試 3 分鐘講法
17. 履歷保守 bullet
18. 不能誇大的邊界
19. 下一步要查的 evidence
20. 本次實際掃描範圍與未掃描範圍
21. 每條履歷 / 面試說法的證據層級：真實開發過 / 專案存在 / 分析素材 / 外部案例 / 待確認
```

## 完成後必須給下一步建議

每次完成 flow 學習包或其中一個 Step 後，AI 必須自動告訴 Nick 下一步建議。

規則：

- 只推薦一件事。
- 如果 flow 尚未完整，優先建議繼續補同一條 flow。
- 如果上一個 Step 是舊規則產物或 evidence 不乾淨，優先建議重整上一個 Step。
- Step 3 後通常建議補 failure scenarios / consistency / idempotency / retry / compensation / reconciliation。
- 面試 case 完成前，不急著更新履歷。
- 如果目前只看到後台 / 前端 / BI 操作入口，優先建議補讀真正後端 / 下游 repo。
- 下一步 prompt 必須放成 Nick 可直接複製的 fenced code block，格式固定為 ` ```text ... ``` `；code block 內只放一行短 prompt。
- 小型 / 低風險改檔輕量自查後 commit；重大 / 實質改檔全掃確認後 commit；若需要 push，直接觸發 `git push` approval 視窗，不要只停在本地文字回報。

## 拆檔規則

- `flow.md`：唯一主報告。前半寫初階 / 中階可讀區：閱讀定位、白話導讀、Code 分層對照、最小架構圖、正常流程圖與正常流程逐步說明；後半寫 Senior / Owner 深度區：業務問題、系統位置、資料與狀態、failure window、owner decision、面試 / 履歷邊界摘要。
- `career-interview.md`：該 flow 的保守履歷 / 面試素材、可說與不可說、證據層級。
- `materials/evidence.md`：code path、commit / branch / grep evidence、已確認、合理推論、待確認。不得貼出密碼、token、內網 IP、production URL 或客戶機密。
- `materials/interview.md`：3 分鐘講法、Senior 追問、Lead / Architect 追問、可用問答。
- `materials/claim-boundary.md`：履歷可寫、只能說分析 / 參與、不能說主導、待補 evidence。
- `materials/decision-notes.md`：技術硬底子、技術選型差異、trade-off、owner decision。只補與本 flow 直接相關的底層知識，不寫成教科書大全。

## 輸出格式

### 0. 閱讀定位

- Flow 中文名稱：
- Flow slug：
- 完成狀態：
- 證據層級：
- 本 flow 是業務功能 / 共用能力 / 後台入口 / 報表查詢 / deploy flow：
- 是否只確認到入口：

### 1. 白話導讀

用初階 / 中階也能懂的方式回答：

- 這是什麼功能。
- 誰會用。
- 什麼時候會觸發。
- 使用者或系統做了什麼動作。
- 成功後資料或狀態會變成什麼。
- 如果失敗，最直覺會壞在哪裡。

### 2. 初中階 Code 分層對照

若專案不是 Java，也要轉譯成 Nick 熟悉的後端分層：

```text
Route / API：
Controller：
Service / Business：
Model / DAO / Repository：
SQL / Table：
Redis：
MQ / Kafka / 下游通知：
External API：
Log / Audit：
Config：
```

找不到或沒有 evidence 的項目，寫 `未確認`、`不適用` 或 `略`，不要硬湊。

### 3. 最小架構圖

只畫本 flow 相關上下游。可以用 Mermaid；未確認節點要標 `待確認`。

### 4. 正常流程圖

用流程圖或編號流程，讓人先看懂從入口到資料落點的主路徑。

### 5. 正常流程逐步說明

用 5 到 12 步描述，不要太長。

### 6. 業務問題

這條 flow 解決什麼問題，錯了會造成什麼後果。

### 7. 系統位置

- 產品：
- 專案：
- 模組：
- 上游：
- 下游：

### 8. Code 路徑

只列可確認的路徑，不列猜測。

### 8.1 掃描範圍

每份 evidence 都要寫：

- 已看 repo：
- 已看分支：
- 已看 code path：
- 已看 git log：
- 相關但未看 repo：
- 未看分支 / 未看原因：
- 本 flow 是否只確認到後台 / 前端入口：

### 9. 資料與狀態

- DB：
- Redis：
- MQ / Kafka：
- 外部 API：
- log / audit：

### 10. Failure Window

至少整理：

- 重複請求
- timeout
- DB 成功但 MQ 失敗
- MQ 成功但 consumer 失敗
- callback 重送
- retry 重複副作用
- 人工補償與狀態不一致

### 11. Owner Decision

不要寫空泛建議，要寫：

- 先修什麼
- 暫時不修什麼
- 為什麼
- 風險
- 驗證方式

### 12. 面試講法

要能回答：

- 你怎麼發現問題。
- 你怎麼讀 code。
- 你怎麼判斷風險順序。
- 你怎麼設計可恢復方案。
- 你怎麼跟營運 / QA / 其他工程師協作。

### 13. 履歷保守寫法

只寫可證明或本人確認的內容。沒有 metric 不寫改善百分比。

### 14. Claim Boundary

明確列：

- 可以說
- 只能說分析 / 參與
- 不能說主導
- 待補 evidence
