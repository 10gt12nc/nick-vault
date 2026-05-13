# Flow 學習包模板

每次只建立一條 flow。不要一次產很多。

## 檔案位置

```text
projects/{domain}/{project}/flows/{flow-name}/
  flow.md
  evidence.md
  interview.md
  claim-boundary.md
```

Step 1 只盤點候選 flow 時，不建立 flow folder。等 Nick 選定單一 flow 後，才依 `projects/CONVENTIONS.md` 建立完整資料夾。

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
- projects/{domain}/{project}/flows/{flow-name}/evidence.md
- projects/{domain}/{project}/flows/{flow-name}/interview.md
- projects/{domain}/{project}/flows/{flow-name}/claim-boundary.md

請產出：
1. 業務問題
2. 系統位置
3. 入口與 code 路徑
4. 正常流程
5. DB / Redis / MQ / 外部 API
6. 失敗情境
7. 補償 / retry / 對帳
8. Senior / Owner 設計取捨
9. Lead / Architect 追問
10. 面試 3 分鐘講法
11. 履歷保守 bullet
12. 不能誇大的邊界
13. 下一步要查的 evidence
```

## 拆檔規則

- `flow.md`：業務問題、系統位置、正常流程、資料與狀態、failure window、owner decision。
- `evidence.md`：code path、commit / branch / grep evidence、已確認、合理推論、待確認。不得貼出密碼、token、內網 IP、production URL 或客戶機密。
- `interview.md`：3 分鐘講法、Senior 追問、Lead / Architect 追問、可用問答。
- `claim-boundary.md`：履歷可寫、只能說分析 / 參與、不能說主導、待補 evidence。

## 輸出格式

### 1. 業務問題

這條 flow 解決什麼問題，錯了會造成什麼後果。

### 2. 系統位置

- 產品：
- 專案：
- 模組：
- 上游：
- 下游：

### 3. Code 路徑

只列可確認的路徑，不列猜測。

### 4. 正常流程

用 5 到 12 步描述，不要太長。

### 5. 資料與狀態

- DB：
- Redis：
- MQ / Kafka：
- 外部 API：
- log / audit：

### 6. Failure Window

至少整理：

- 重複請求
- timeout
- DB 成功但 MQ 失敗
- MQ 成功但 consumer 失敗
- callback 重送
- retry 重複副作用
- 人工補償與狀態不一致

### 7. Owner Decision

不要寫空泛建議，要寫：

- 先修什麼
- 暫時不修什麼
- 為什麼
- 風險
- 驗證方式

### 8. 面試講法

要能回答：

- 你怎麼發現問題。
- 你怎麼讀 code。
- 你怎麼判斷風險順序。
- 你怎麼設計可恢復方案。
- 你怎麼跟營運 / QA / 其他工程師協作。

### 9. 履歷保守寫法

只寫可證明或本人確認的內容。沒有 metric 不寫改善百分比。

### 10. Claim Boundary

明確列：

- 可以說
- 只能說分析 / 參與
- 不能說主導
- 待補 evidence
