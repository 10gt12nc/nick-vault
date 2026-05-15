# iwin game_job Step 2：候選 Flow 技術點與風險比較

更新時間：2026-05-15
掃描等級：Level 1 Flow 掃描 / 候選 flow 比較
狀態：新建 Step 2
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 本次結論

`game_job` Step 2 的目的不是直接開始寫 flow，而是把 Step 1 找到的候選 flow 排序，確認哪一條最值得進 Step 3 深挖。

排序後結論：

1. 第一條最值得做的是 `daily-game-data-summary`，因為它 evidence 最厚，且同時具備 batch correctness、時區邊界、重跑清除、projection consistency、retention 計算與備份清理。
2. 第二順位是 `third-party-record-mongo-backup`，它有明確的分批搬移 / 刪除流程，適合分析 partial failure 與資料保留策略。
3. 第三順位是 `coin-flow-batch-projection`，Senior 價值可能更高，但 Step 1 evidence 還不足；需要 Step 3 前先補 `handleLogReelData()`、`handleLogJackpotData()`、`handleTaxtData()` 的完整 path。

不更新履歷。沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認前，本文件所有 flow 都只作 `專案存在 / code-backed` 或 `分析素材 / learning-only`。

## 自動重讀紀錄

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/CONVENTIONS.md`

已重讀 vault：

- `projects/iwin/game_job/README.md`
- `projects/iwin/game_job/step1-candidate-flows.md`
- `projects/iwin/app_bi/step2-flow-comparison.md` 作為 Step 2 文件形狀參考

已重讀 code repo：

- 沿用 Step 1 已掃的 `/Users/nick/Git/iwin/game_job` main branch、近期 log、遠端 branch 清單、path-specific log 與主要 job / mapper 入口。
- 本次沒有重新 checkout 遠端分支，沒有逐 commit diff，也沒有新增下游 repo 深掃。

## 舊文件狀態判斷

| 文件 | 狀態 | 判斷 |
| --- | --- | --- |
| `README.md` | 需同步 | Step 2 建立後，專案狀態應從 Step 1 更新為 Step 2 |
| `step1-candidate-flows.md` | 可沿用 | 掃描範圍、候選 flow 與 evidence 邊界已清楚 |
| `step2-flow-comparison.md` | 本次新建 | 補上 Step 2，比較候選 flow，不建立單條 flow folder |

## 比較前提

本文件只比較 Senior / Platform Backend / System Owner 價值。

不做：

- 不建立 `flows/{flow-name}/`。
- 不寫 `flow.md`。
- 不逐檔逐行。
- 不逐 commit diff。
- 不更新履歷 / 自傳。
- 不把 batch projection 寫成 Nick 的真實開發成果。

## 價值排序

| 排名 | Flow | 中文名稱 | Senior / Owner 價值 | 目前 evidence | 最大缺口 | 建議 |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | `daily-game-data-summary` | 每日遊戲資料彙總 | 中高 | job / service / mapper / 時區修正 / 重跑刪除 / 備份清理 evidence 最完整 | upstream `log_reel` writer 與 app_bi 查詢端需要 Step 3 對齊 | 下一步進 Step 3 |
| 2 | `third-party-record-mongo-backup` | 第三方遊戲紀錄 Mongo 備份與清理 | 中高 | Antplay new log / transaction backup code 清楚，且有 GSC / Antplay branch history | backup / delete partial failure、GSC 完整 path、retention policy 未深挖 | 排第二條 Step 3 候選 |
| 3 | `coin-flow-batch-projection` | 金幣流水清算 / 遊戲行為投影 | 高但未收斂 | Redis checkpoint、跨日 task finish、多資料源 projection 線索清楚 | 核心 handle method 未完整讀完；source of truth 未定位 | 先補 path 後再 Step 3 |
| 4 | `online-payment-data-cleaning` | 充值 / 提現資料清洗與每日經濟資料 | 中 | `payment_order_{yyyy_m}` 讀取與支付分類清楚 | payment source of truth 在 `payment` repo；目前只是 reporting projection | 暫不優先，之後可接 payment flow |
| 5 | `partition-table-creation` | 每日 / 每月分表建立 | 中低 | SQL template 與 channel DB 建表流程清楚 | 支撐性 flow，面試主題性較弱 | 作其他 flow 的可靠性補充 |

## 下一步排序

這裡不是直接做 flow，而是排「下一個最適合叫 AI 做什麼」。

1. `game_job daily-game-data-summary Step 3`
   - 原因：Step 1 / Step 2 已確認它是目前 evidence 最厚、風險點最完整的候選。
   - 產出：單條 flow 學習包，包含 `flow.md`、`career-interview.md`、`materials/evidence.md`、`materials/decision-notes.md`、`materials/interview.md`、`materials/claim-boundary.md`。
   - 是否更新履歷：否，至少等 Step 4 / Step 5 且補到 Nick 本人 evidence。
2. `game_job third-party-record-mongo-backup Step 3`
   - 原因：資料備份 / 刪除 flow 有明確 partial failure 問題，適合作第二條 batch safety case。
   - 產出：Mongo backup / retention / delete safety flow。
   - 是否更新履歷：否。
3. `game_job coin-flow-batch-projection Step 2 補掃`
   - 原因：價值可能最高，但 Step 1 尚未完整讀核心 handle method。
   - 產出：補足是否值得 Step 3 的 evidence。
   - 是否更新履歷：否。

本輪只推薦第一項。

## Flow 比較詳述

### 1. `daily-game-data-summary`

中文名稱：每日遊戲資料彙總
證據層級：專案存在 / code-backed；Nick 貢獻待確認

已確認：

- `GameDailyAntplayAndPGJob` 處理 PG / Antplay，每次先刪指定資料日的 projection，再匯入 personal data、summary data 與 retention data。
- `GameDailyIwinJob` 處理 Iwin，另有 14 天前 personal data 備份 / 清除與 180 天前 summary 備份 / 清除。
- `LogReelDao.getPersonalData` 從 `slots_logv1.log_reel_{yyyy_m_d}` 彙整 uid、spin count、spin amount、win currency。
- `GameDailyDao` 寫入 `log_game_daily_record`、`log_game_daily_record_new_players`、`log_game_daily_record_backup`。
- path-specific history 有 `#247` 每日遊戲資料彙總、`#384` PG 時區修正、`#403` Antplay 時區修正。

Senior / Owner 價值：

- batch replay / re-run safety。
- report projection consistency。
- timezone boundary。
- retention calculation。
- backup / cleanup policy。
- downstream app_bi 報表查詢一致性。

推測：

- `log_reel` 上游 writer 在 `iwin_gameserver` 或 game runtime。
- app_bi 的每日遊戲資料彙總查詢讀的是本 flow 產出的 projection。

待確認：

- production 實際 enable flag 與部署 cron。
- `log_reel` writer 與資料日 definition。
- `log_game_daily_record_new_players` 在重跑時是否需要同步清理。
- app_bi 查詢欄位與 `sub_type` 是否完全對齊。
- Nick 是否實際維護過相關 commit / ticket。

判斷：

- 適合作第一條 Step 3。
- 但 Step 3 只能標 `專案存在 / code-backed`，不得寫成 Nick 實作成果。

### 2. `third-party-record-mongo-backup`

中文名稱：第三方遊戲紀錄 Mongo 備份與清理
證據層級：專案存在 / code-backed；Nick 貢獻待確認

已確認：

- `ThirdLogAntplayNewJob` 與 `ThirdTransactionAntplayNewJob` 會將舊資料分批寫入 backup collection，再依 `_id` 刪除 origin collection。
- `config/application-quartz.yml` 中 Antplay new backup 類 job 目前 enable flag 為 true。
- history 有 Antplay backup job、GSC backup job、GSC batch size 調整線索。

Senior / Owner 價值：

- backup / delete partial failure。
- batch size / memory pressure。
- data retention policy。
- audit / troubleshooting 資料保存。
- 重跑與 duplicate backup 風險。

推測：

- GSC、Oneapi 與舊 Antplay 類 job 屬於同一類 retention flow。
- upstream writer 可能在 `third_games_api` 或 provider integration repo。

待確認：

- GSC 完整 path。
- `_id` 欄位型別與 VO mapping。
- backup collection 是否有 unique constraint 或 duplicate 防護。
- delete 成功但 backup 未完整的觀測與補償策略。

判斷：

- 適合作第二條 Step 3。
- 目前不優先於每日彙總，因為跨 repo 上下游與 production policy 缺口更大。

### 3. `coin-flow-batch-projection`

中文名稱：金幣流水清算 / 遊戲行為投影
證據層級：專案存在 / code-backed；Nick 貢獻待確認

已確認：

- `CoinFlowJob` 讀 `log_reel`、`log_jackpot`、`log_taxt`，產生 coin flow 與 user game behaviour。
- 使用 Redis key 追蹤 last pull max id、record num、pull over status、last result。
- `execAfter()` 會等所有 channel / table 拉完才 `taskFinish()`。
- `afterTaskFinish()` 會重設下一日 pull id / status 並清 Redis。

Senior / Owner 價值：

- incremental batch。
- checkpoint consistency。
- multi-source projection。
- cross-day catch-up。
- idempotency / duplicate projection。
- money correctness 影子資料。

推測：

- 它可能比每日彙總更接近交易風險，但 Step 1 只看到外層結構，未完整讀核心轉換細節。

待確認：

- `handleLogReelData()`、`handleLogJackpotData()`、`handleTaxtData()` 的完整資料流。
- Redis checkpoint 重置後是否會重複寫入。
- Mongo / BI table 寫入是否有 upsert / unique / replace 策略。
- app_bi 或報表端如何使用 projection。

判斷：

- 不是現在第一條 Step 3。
- 建議等每日彙總 Step 3 後，再補掃一次核心 handle path。

### 4. `online-payment-data-cleaning`

中文名稱：充值 / 提現資料清洗與每日經濟資料
證據層級：專案存在 / code-backed；Nick 貢獻待確認

已確認：

- `OnlinePaymentDataJob` 讀 `payment_order_{yyyy_m}`。
- 依充值 / 提現、reason、tradeType、onlineType、channelType 分類。
- 透過 `OnlineDataHandleService` 彙總每日支付、線下支付、代理充值、首充、提款與 economic data。

Senior / Owner 價值：

- money reporting projection。
- payment order 分表。
- 首充 / 提現 / 代理充值分類邊界。
- payment callback 與 reporting consistency。

最大缺口：

- `payment` repo 才是 source of truth。
- 若先做這條，很容易把 reporting projection 誤寫成 payment owner 成果。

判斷：

- 暫不優先。
- 可等 `payment-provider-callback` 或 `payment-order-provider-request` 之後，用來補 reporting / reconciliation view。

### 5. `partition-table-creation`

中文名稱：每日 / 每月分表建立
證據層級：專案存在 / code-backed；Nick 貢獻待確認

已確認：

- `CreateTableJob` 載入 `initBiTable/*.sql` 與 `initGameTable/*.sql`。
- BI table 對固定 DB 建表，game table 對所有 channel DB 建表。
- template 包含 log / payment / user behaviour / player bet 類分表。

Senior / Owner 價值：

- table rollover reliability。
- schema rollout。
- batch / writer 前置依賴。

判斷：

- 不適合單獨做第一條 flow。
- 可在其他 Step 3 的架構圖與 failure window 補充。

## 下一步建議

只推薦一件事：

```text
game_job daily-game-data-summary Step 3
```

原因：

- Step 1 / Step 2 已確認這條 flow 是目前 `game_job` 最乾淨的第一條深挖候選。
- Step 3 才會建立單條 flow folder 與 `flow.md` 主報告。
- Step 3 仍不更新履歷；只產出 code-backed 學習包與 evidence 邊界。
