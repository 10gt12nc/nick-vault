# AI 提示詞手冊

這份是之後請 AI 維護 `nick-vault`、分析專案、產出 Senior / Owner 學習包時使用的提示詞手冊。

核心原則：

- 不要一次叫 AI 產很多。
- 不要叫 AI 平均整理全部 class。
- 每次只跑一個 step。
- 每次只完成一條 flow。
- 不確定就標示「已確認 / 推測 / 待確認」。
- 只輸出整理後的新內容，不複製舊檔。
- 不寫 secret、token、內網 IP、production URL、客戶資料。
- 履歷與面試不能誇大。
- 每次完成後，AI 要自動給下一步建議，而且只推薦一件最值得做的事。
- 每次 Step 都要寫清楚實際掃描範圍；沒看其他分支、沒看下游 code、沒看後端 repo，就要明確寫未掃 / 待確認。
- 沒有 evidence 的技術點可以略過或標外部補讀，不要為了湊格式腦補。
- 「深掃」要標示深度：Level 1 Flow 掃描、Level 2 Flow 深掃、Level 3 極限深掃。Nick 明確要求極限深度時，要逐 module、逐檔、逐相關 commit diff 追原因與收斂。
- AI 要主動判斷本次該用哪個深掃等級，並給 Nick 建議；不是每次都等 Nick 指定。

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
- archive/ 只能當參考來源。
- 新內容要重新整理、去重、結合、優化，不要複製舊檔。
- 不要產生 code。
- 不要寫 secret、token、內網 IP、production URL、客戶資料。
- 所有履歷說法要保守，沒有證據不要寫主導、獨立完成、改善百分比。
- 每次完成後，請自動給下一步建議，只推薦一件最值得做的事，並說明是否會更新履歷、是否需要 commit / push。
- 每次分析都要在 evidence 寫明掃描範圍：主分支、近期分支、相關 code path、相關後端 / 下游 repo 是否已看；未看就明確標未看。
- 如果 Nick 說「深掃」，至少使用 Level 2；如果 Nick 說「極限深度 / 逐檔逐行 / 每個 commit diff」，使用 Level 3，並分批完成。
- 如果 Nick 沒說深度，請依任務主動建議 Level 1 / 2 / 3；若不建議 Level 3，要說明原因。

目標：
把專案 code / 舊資料 / 待刪區參考內容，整理成 Senior Java Backend / Platform Backend / System Owner 可讀、可面試、可轉履歷的學習資料。
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
- 完成狀態：只有 Step 1 / 已有 flow.md / 已有 evidence.md / 已轉面試 case / 已更新履歷
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
- 已完成哪些內容：flow.md / evidence.md / career-interview.md / 面試案例 / 履歷 bullet
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

完成後請自動補：
- 目前這條 flow 完成到哪裡
- 下一步只推薦一件事
- 為什麼現在做它
- 會更新哪些檔案
- 是否會更新履歷；預設不更新
- 是否需要 commit / push；預設不需要
- 若這條 flow 目前只掃到後台 / 前端，請優先建議去讀真正後端 / 下游 code，而不是硬轉履歷
```

## 3.1 Level 3 極限深掃提示詞

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

## 3.2 AI 自動判斷深掃等級

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
```

## 5. Step 5：履歷 / 自傳更新

用途：只有 flow 完成後，才允許更新履歷。

```text
請根據已完成的 flow evidence，檢查是否值得更新：
- senior-owner-playbook/05-resume-master-zh.md
- senior-owner-playbook/08-application-autobiography-zh.md

規則：
- 沒有 evidence 不更新。
- 不寫主導、獨立完成、改善 X%，除非有明確證據。
- 可以寫參與、維護、分析、梳理、協助、優化、提出改善方向。
- 履歷只補高價值且能面試講清楚的內容。

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
- flows/{flow-name}/evidence.md
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
9. 下一步只推薦一件事。
```

## 8. 自動下一步建議格式

```text
下一步建議：{只推薦一件事}

原因：
- {為什麼現在最適合做這件事}

會更新：
- {預計更新的檔案或資料夾}

不會做：
- 不更新履歷，除非 evidence 足夠且 Nick 明確要求。
- 不 commit / push，除非 Nick 明確要求。

建議提示詞：
{Nick 下一句可以直接貼的短 prompt}
```
