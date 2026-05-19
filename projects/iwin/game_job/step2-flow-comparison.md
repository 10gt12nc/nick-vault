# iwin game_job Step 2：候選 Flow 技術點與風險比較

更新時間：2026-05-19
掃描等級：Level 1 Flow 掃描 / 候選 flow 比較
狀態：已建立；Top 5 flow 已完成 Step 5
證據層級：`daily-game-data-summary` 為真實開發過 + code-backed；`third-party-record-mongo-backup` 為局部真實開發過 + code-backed；其他候選 flow 依三層 claim gate 判斷

## 本次結論

`game_job` Step 2 的目的不是直接開始寫 flow，而是把 Step 1 找到的候選 flow 排序，確認哪一條最值得進 Step 3 深挖。

排序後結論：

1. 第一條最值得做的是 `daily-game-data-summary`，因為它 evidence 最厚，且同時具備 batch correctness、時區邊界、重跑清除、projection consistency、retention 計算與備份清理。
2. 第二順位 `third-party-record-mongo-backup` 已完成 Step 5，可保守寫局部 GSC Mongo backup 分批查詢與 batch size 調整。
3. `coin-flow-batch-projection` 已完成 Step 5，Senior 價值已收斂到 Redis checkpoint、多來源增量 projection、custom date replay、MySQL 累加 upsert 與 Mongo delete+insert 的 consistency 題；正式履歷 / 自傳不更新。
4. `online-payment-data-cleaning` 已完成 Step 5，可作 payment reporting projection 的 code-backed 面試 case；正式履歷 / 自傳不更新。
5. `partition-table-creation` 已完成 Step 5，可作 table rollover / schema rollout / batch 前置依賴的 code-backed 面試素材；正式履歷 / 自傳不更新。

本 Step 2 本身不更新履歷；後續 `daily-game-data-summary` 與 `third-party-record-mongo-backup` 已完成 Step 5 claim gate，可作 game_job project contribution consolidation evidence。其他候選 flow 仍需各自完成 claim gate。

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
| `README.md` | 已同步 | 專案狀態已同步到 `daily-game-data-summary` Step 5 下一步 |
| `step1-candidate-flows.md` | 可沿用 | 掃描範圍、候選 flow 與 evidence 邊界已清楚 |
| `step2-flow-comparison.md` | 可沿用 / 已回補現況 | 補上 Step 2，比較候選 flow；目前第一條 flow 已完成 Step 5 |

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
| 1 | `daily-game-data-summary` | 每日遊戲資料彙總 | 中高 | job / service / mapper / 時區修正 / 重跑刪除 / 備份清理 evidence 最完整，且有 `10gt12nc` path-specific commits | upstream `log_reel` writer 與 app_bi 查詢端已在 Step 3 標示邊界 | 已完成 Step 5 |
| 2 | `third-party-record-mongo-backup` | 第三方遊戲紀錄 Mongo 備份與清理 | 中高 | Antplay new / GSC log 與 transaction backup code 已 Step 3 深挖，Step 4 已轉面試 case，Step 5 已確認 `10gt12nc` GSC 分批查詢 / batch size 調整局部 claim | production enable、backup unique/idempotency、app_bi / 後台查詢端未確認 | 已完成 Step 5 |
| 3 | `coin-flow-batch-projection` | 金幣流水清算 / 遊戲行為投影 | 高 | Step 5 已完成；Step 3 / 4 已深挖 `CoinFlowJob`、source mapper、Redis checkpoint、MySQL user behaviour、Mongo coin flow projection 與正式面試 case | production enable、upstream writer、BI 查詢端、Nick direct contribution 未確認 | 已完成 Step 5，不更新履歷 |
| 4 | `online-payment-data-cleaning` | 充值 / 提現資料清洗與每日經濟資料 | 中 | Step 5 已完成；`payment_order_{yyyy_m}`、Mongo `online_*`、MySQL `economic_data_day_log`、`payment_amount*` 與 downstream `daily_economic_data_total` 邊界已清楚，已轉正式面試 case | payment source of truth 在 `payment` repo；目前只是 reporting projection，Nick direct contribution 未確認 | 已完成 Step 5，不更新履歷 |
| 5 | `partition-table-creation` | 每日 / 每月分表建立 | 中低 | Step 5 已完成；SQL template、channel DB 建表、`CREATE TABLE IF NOT EXISTS`、partial success、schema drift 與檔名錯誤風險已整理成正式面試 case | 支撐性 flow，履歷主題性較弱；Nick direct contribution 未確認 | 已完成 Step 5，不更新履歷 |

## 下一步排序

這裡不是直接做 flow，而是排「下一個最適合叫 AI 做什麼」。

1. `iwin iwin_gameserver center-http-deposit-withdraw Step 4`
   - 原因：`game_job` contribution claim consolidation 已完成；`game_api` 第三順位 flow 已完成 Step 4，下一步做單條 flow claim gate。
   - 產出：確認代理佣金領取 / 轉帳 flow 是否有 Nick / `10gt12nc` direct evidence 或只能保留為 code-backed 面試素材。
   - 是否更新履歷：否。

本輪只推薦第一項。

## Flow 比較詳述

### 1. `daily-game-data-summary`

中文名稱：每日遊戲資料彙總
證據層級：真實開發過 + code-backed

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
- production issue / ticket / MR review context。

判斷：

- 已完成 Step 5，可保守寫成 Nick 參與 / 開發 / 維護成果。
- 不得寫成完整 BI pipeline owner。

### 2. `third-party-record-mongo-backup`

中文名稱：第三方遊戲紀錄 Mongo 備份與清理
證據層級：局部真實開發過 + code-backed

已確認：

- `ThirdLogAntplayNewJob` 與 `ThirdTransactionAntplayNewJob` 會將舊資料分批寫入 backup collection，再依 `_id` 刪除 origin collection。
- `config/application-quartz.yml` 中 Antplay new backup 類 job 目前 enable flag 為 true。
- `ThirdLogGscJob` 與 `ThirdTransactionGscJob` 也採用同型分批 backup / delete；main config 目前 disabled。
- `third_games_api` 的 Antplay / GSC controller 會寫入對應 active collections 與 `createdTime`。
- history 有 Antplay backup job、GSC backup job、GSC batch size 調整線索；`10gt12nc` 有 GSC 分批查詢 commit。

Senior / Owner 價值：

- backup / delete partial failure。
- batch size / memory pressure。
- data retention policy。
- audit / troubleshooting 資料保存。
- 重跑與 duplicate backup 風險。

推測：

- GSC、Oneapi 與舊 Antplay 類 job 屬於同一類 retention flow。
- upstream writer 可能在 `third_games_api` 或 provider integration repo。

已完成文件：

- `flows/third-party-record-mongo-backup/flow.md`
- `flows/third-party-record-mongo-backup/career-interview.md`
- `flows/third-party-record-mongo-backup/materials/evidence.md`

待確認：

- `_id` 欄位型別與 VO mapping。
- backup collection 是否有 unique constraint 或 duplicate 防護。
- delete 成功但 backup 未完整的觀測與補償策略。
- production 實際 enable flag。

判斷：

- 已完成 Step 5，可保守寫 GSC backup 分批查詢與 batch size 調整局部開發。
- 不可寫完整 third-party backup owner、Antplay / Oneapi owner 或 production enable 已驗證。

### 3. `coin-flow-batch-projection`

中文名稱：金幣流水清算 / 遊戲行為投影
證據層級：專案存在 / code-backed；Step 5 已判定不更新正式履歷 / 自傳

已確認：

- `CoinFlowJob` 讀 `log_reel`、`log_jackpot`、`log_taxt`，產生 coin flow 與 user game behaviour。
- 使用 Redis key 追蹤 last pull max id、record num、pull over status、last result。
- `execAfter()` 會等所有 channel / table 拉完才 `taskFinish()`。
- `afterTaskFinish()` 會重設下一日 pull id / status 並清 Redis。
- Step 3 已確認 `user_game_behaviour_{yyyy_m}` 使用累加 upsert，Mongo `log_coin_flow_{gameId}` 使用 remove + insert。
- Step 3 已確認 main config `coinFlowEnable=false`，production 實際啟用狀態待確認。
- Step 4 已轉成正式面試 case，包含 STAR、常見追問與 Lead / Architect 追問。
- Step 5 已完成 claim gate；path-specific history 未見足夠 Nick / `10gt12nc` direct evidence，正式履歷 / 自傳不更新。

Senior / Owner 價值：

- incremental batch。
- checkpoint consistency。
- multi-source projection。
- cross-day catch-up。
- idempotency / duplicate projection。
- money correctness 影子資料。

推測：

- 它可能比每日彙總更接近交易風險，但仍是報表 projection，不是 wallet source of truth。

待確認：

- upstream writer 與 source of truth。
- Redis checkpoint 重置後 production replay SOP。
- `user_game_behaviour_{yyyy_m}` 實際 unique key。
- app_bi 或報表端如何使用 projection。

判斷：

- 已完成 Step 5。
- 可作 code-backed 面試素材；正式履歷目前仍需 Nick 本人確認、MR、ticket、commit 或 production issue 才能升級。

### 4. `online-payment-data-cleaning`

中文名稱：充值 / 提現資料清洗與每日經濟資料
證據層級：專案存在 / code-backed；Nick 貢獻依三層 claim gate 判斷

已確認：

- `OnlinePaymentDataJob` 讀 `payment_order_{yyyy_m}`。
- 依充值 / 提現、reason、tradeType、onlineType、channelType 分類。
- 透過 `OnlineDataHandleService` 彙總每日支付、線下支付、代理充值、首充、提款與 economic data。
- Step 3 已建立主學習包，確認它輸出 Mongo `online_*`、MySQL `economic_data_day_log`、`payment_amount*`，並被 `DailyEconomicDataTotalJob` 下游使用。

Senior / Owner 價值：

- money reporting projection。
- payment order 分表。
- 首充 / 提現 / 代理充值分類邊界。
- payment callback 與 reporting consistency。

最大缺口：

- `payment` repo 才是 source of truth。
- 若先做這條，很容易把 reporting projection 誤寫成 payment owner 成果。

判斷：

- 已完成 Step 5。
- 已轉成保守面試 case，並完成 claim gate；正式履歷 / 自傳不更新。

### 5. `partition-table-creation`

中文名稱：每日 / 每月分表建立
證據層級：專案存在 / code-backed；Nick 貢獻依三層 claim gate 判斷

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
iwin iwin_gameserver center-http-deposit-withdraw Step 4
```

原因：

- `partition-table-creation` Step 5 已完成，正式履歷 / 自傳不更新。
- `game_job` Top 5 flow 都已收斂，但尚未做 project-level contribution consolidation。
