# Career / Industry KB Evolution Plan

狀態：草案，待 Nick 調整。

本檔先記錄兩個想法：

1. 用 Codex automation 定期推送少量高品質材料，降低 Nick 主動找資料的成本。
2. 未來如果進新公司，`nick-vault` 是否能從求職履歷庫，升級成長期職涯 / 工作復盤 / 產業 KB / skill source。

這份不是自動開工授權，也不是新的必做主線。正式建立 automation、改目錄規格、或把工作內容納入 KB 前，都要 Nick 再確認。

## 一句話結論

可以做，但要守住三條線：

1. 自動化只推少量材料，不製造資訊焦慮。
2. Vault 可以升級，但不要變成每日流水帳。
3. 新公司工作內容只能抽象化、去識別化、證據分層，不放公司機密。

## Part A：自動化推送規劃

### 目標

自動化的目標不是讓 AI 每週產大量文件，而是讓 Nick 週期性收到「少量、可讀、可行動」的材料。

合格的推送應該能回答：

- 這週我該看什麼？
- 為什麼跟工作、面試或日語有關？
- 我只花 20-40 分鐘能做什麼？
- 哪些不用現在做？

不合格的推送：

- 一次塞 20 篇文章。
- 產生長篇摘要但沒有行動建議。
- 把基本功、刷題、日語、產業文章全部變成壓力。
- 自動寫入 KB，造成未確認內容污染正式文件。

### 建議先開 1 個 automation

第一階段只建議先開一個：

```text
每週 Backend / 面試 / 日語輕量週報
```

頻率：

- 每週一次。
- 建議週一早上或週日晚間。
- 不建議一開始每天推送。

輸出長度：

- 3-5 個 backend / platform / AI coding 重點。
- 3 題面試小練習。
- 5-10 個日語工作句。
- 1 個本週建議行動。
- 1 個明確「本週不做」。

### 推送內容模組

#### 1. Backend / Platform 知識週報

主題範圍：

- Java / Spring Boot / Spring transaction
- MySQL / PostgreSQL / index / slow query
- Redis consistency / lock / cache
- MQ / Kafka duplicate consume / retry / ordering
- payment / wallet / provider callback / idempotency
- observability / incident / rollback / reconciliation
- AI-assisted coding review / Codex / Claude Code 實務

篩選規則：

- 優先挑跟 Nick 既有 production cases 能接上的內容。
- 每週最多 3-5 個重點。
- 每個重點要附「跟 Nick 哪條 flow / 面試 case 有關」。
- 如果只是流行技術名詞，先略過。

輸出格式：

```text
本週 Backend 重點
1. 主題
   - 為什麼重要
   - 對應 Nick case
   - 只要先懂哪一點
   - 不用現在深挖什麼
```

#### 2. 面試題小練習

頻率：

- 跟週報一起推即可。
- 不建議獨立變成每天刷題。

每週題型：

1. Java / Spring / transaction 1 題。
2. SQL / Redis / MQ 1 題。
3. Production case 追問 1 題。

原則：

- 題目要能接回 `19-interview-coaching-question-bank.md` 或主力 flow。
- 不新增泛用 300 題題庫。
- 答不出才回補 `interview-practice/` 或 `19`，不要每題都寫正式 KB。

範例：

```text
本週 3 題
1. 為什麼 self-invocation 會讓 @Transactional 失效？
2. MQ consumer 重複消費時，你會用哪個欄位做 idempotency key？
3. payment callback 已上分，但 provider 又重送一次，你怎麼判斷與阻擋？
```

#### 3. 日語工作推送

目標：

- 服務未來工作、遠端協作、日商 / 外商環境、日文面試或 Slack / meeting 溝通。
- 先求能用，不做完整日語課程。

每週內容：

- 5-10 個工作常用句。
- 3 個會議 / Slack / 報告情境。
- 1 小段自我介紹、進度回報或問題回報修正。

範例主題：

- 進度回報：今天完成什麼、卡在哪、明天做什麼。
- 問問題：我不確定規格，想確認 A/B。
- 風險回報：這段如果今天上線，可能有資料不一致風險。
- Code review：這裡我擔心 retry 後會重複副作用。

日語推送不需要寫進正式履歷 KB；除非 Nick 明確要建立日語面試稿或工作日語包。

### Automation 建立前確認項

正式建立前要 Nick 確認：

1. 頻率：每週一次或每週兩次。
2. 時間：週一早上、週日晚間、或其他時間。
3. 內容比例：Backend / 面試 / 日語 是否都放同一封。
4. 是否只推送在 Codex thread，不自動改檔。
5. 是否需要查網路最新文章；若需要，推送時要附來源與日期。

建議第一版：

```text
每週一早上，推送 Backend + 面試 + 日語輕量週報；只回在 Codex thread，不自動寫入 KB。
```

## Part B：Vault 升級方向

### 目前定位

目前 `nick-vault` 的主定位是：

```text
Senior Java Backend / Platform Backend 求職、履歷、自傳、production flow、面試 evidence package。
```

它的價值是：

- 讓履歷 claim 有證據邊界。
- 讓面試 case 有 flow / evidence / claim boundary。
- 讓 Nick 不必每次從零整理經歷。

### 可升級方向

未來可以升級成四層。

#### 1. 職涯證據庫

用途：

- 記錄 Nick 做過什麼類型的工作。
- 哪些能寫履歷。
- 哪些只能面試講。
- 哪些只是 learning-only。

內容型態：

- project contribution consolidation
- role / responsibility boundary
- claim boundary
- interview story
- resume bullet source

不放：

- 每日工作流水帳。
- 公司內部細節。
- 未去識別化的 issue / ticket / 客戶資料。

#### 2. 工作復盤庫

用途：

- 把重要 issue、技術決策、排查方法、事故風險整理成可重用案例。

適合記：

- 某類 callback 重送如何避免重複副作用。
- 某次 batch partial failure 如何重跑。
- 某段 legacy code 怎麼靠 git history / log / DB / MQ 重建風險。
- 某次 rollout 如何控制 blast radius。

不適合記：

- 今天做了什麼。
- 主管說了什麼。
- 公司內部系統名、客戶名、URL、IP、token。
- 可識別 production incident 的敏感細節。

建議格式：

```text
問題類型
背景抽象化
風險
排查路徑
決策
結果
可面試講法
不可誇大
敏感資訊處理
```

#### 3. 產業 KB

用途：

- 長期累積 gaming / payment / wallet / provider / platform / AI coding 的產業理解。

主題範圍：

- Payment / Provider Gateway
- Wallet / Ledger / Bet-Settle
- Game Backend / Slot / RTP / Math Contract
- MQ / Batch / Projection / Report
- Platform / Observability / Incident
- AI-assisted Engineering Workflow

原則：

- 產業 KB 要從實際 production case 或可信來源長出來。
- 外部文章必須標成 `外部案例 / non-local`。
- 不把外部案例寫成 Nick 做過。

#### 4. Skill Source

用途：

- 把可重複的工作法整理成 prompt / checklist / skill。

候選 skill：

- Git History Debugging / Risk Reconstruction
- Callback Idempotency Review
- Payment Provider Integration Review
- MQ Duplicate Consume Review
- SQL Slow Query Triage
- AI Code Review for Production Risk
- Interview Story Builder

Skill 化條件：

- 已重複使用 3 次以上。
- 有明確 input / output。
- 有安全邊界。
- 不依賴公司機密。
- 可以被下一個 AI 或 Nick 自己重用。

不建議太早 skill 化：

- 還沒穩定的個人想法。
- 只用過一次的 prompt。
- 含公司內部流程或敏感細節的方法。

## Part C：新公司資料安全線

如果 Nick 到新公司後要把經驗整理進 vault，預設只能寫抽象化版本。

### 可以寫

- 問題類型：callback duplicate、transaction boundary、MQ retry、slow query。
- 技術風險：idempotency、consistency、timeout unknown、partial failure。
- 個人方法：如何查 log、看 git history、比對 DB / MQ、設計補償。
- 面試可講的抽象案例。
- 去識別化後的決策原則。

### 不可以寫

- token / password / private key。
- internal IP / production URL。
- 客戶名、供應商名、商戶名。
- 公司內部 repo 名稱、未公開架構圖。
- 可識別的 incident 時間、金額、流量、影響範圍。
- 公司 code、SQL dump、log 原文。
- 還沒公開的產品、商業策略、薪資或人事資訊。

### 證據層級

新公司內容也要套用 evidence label：

- `真實開發過 / 本人確認`：Nick 實際參與，但仍需去識別化。
- `工作復盤 / 去識別化`：可作個人成長與面試素材，不暴露公司資料。
- `公司機密 / 不入庫`：只能本機腦內或公司系統，不進 vault。
- `外部案例 / non-local`：文章、公開文件、面試題、產業資料。

## Part D：建議目錄演化

目前先不改目錄。若未來真的升級，建議仍保持簡單。

候選演化：

```text
senior-owner-playbook/
  22-career-industry-kb-evolution-plan.md
  interview-practice/
  applications/

projects/
  {domain}/{project}/...

career/
  work-retrospectives/
  industry-kb/
  skills/
```

但 `career/` 不要現在建立。只有當 Nick 到新公司後，真的開始累積抽象化工作復盤與產業 KB，再決定是否新增。

如果新增 `career/`，它的責任應該是：

- `work-retrospectives/`：去識別化工作復盤，不是每日流水帳。
- `industry-kb/`：產業主題整理，不是文章剪貼簿。
- `skills/`：可重複 prompt / checklist / skill source，不是一次性筆記。

仍不建議新增：

- `ai-notes/`
- `docs/`
- `.claude/`
- `.codex/`
- `resources/`
- `daily-log/`
- `work-log/`
- `records/`

## Part E：明天調整時的決策問題

明天 Nick 可以只回答這幾題：

1. 自動化要不要先開？
2. 週報頻率要每週一次還是兩次？
3. Backend / 面試 / 日語要放同一份，還是分開？
4. 自動化只推送 thread，還是允許產出候選 KB 草稿？
5. Vault 升級要先停在規劃，還是先加 `career/` 目錄？
6. 新公司工作復盤要不要先設模板？
7. skill source 要先收哪一個：git history、callback idempotency、AI code review，還是 interview story？

建議答案：

```text
先不改大目錄。
先開每週一次輕量週報。
只推送在 Codex thread，不自動寫 KB。
等新公司或實際工作復盤需求出現，再新增 career/。
第一個 skill source 候選先放 Git History Debugging / Risk Reconstruction。
```

## Part F：最小下一步

明天若要正式調整，可以用這句：

```text
讀 22，幫我決定 automation 要怎麼開，以及 vault 要不要先新增 career/ 結構；先不要建立 automation，也先不要 push。
```

如果要直接建立 automation，可以改用：

```text
讀 22，幫我建立每週一早上的 Backend / 面試 / 日語輕量週報 automation，只推送在這個 thread，不自動改 KB。
```
