# iwin game_job Step 1：候選 Flow 盤點

更新時間：2026-05-15
掃描等級：Level 1 Flow 掃描
狀態：新建 Step 1
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 本次結論

`game_job` 是 iwin 的 Java batch / projection 專案，價值不在 HTTP API，而在 Quartz job 如何把遊戲 log、payment order、Mongo collection、Redis task state 與分表資料整理成 BI / 報表 / audit 可讀資料。

目前最值得延伸的候選 flow：

1. `daily-game-data-summary`：每日遊戲資料彙總，價值最高；有跨時區、重跑刪除、個人資料與 summary 資料分層、留存計算、備份清理。
2. `third-party-record-mongo-backup`：第三方遊戲紀錄 Mongo 備份與清理；有批次查詢、insert backup、delete origin、保留天數與 partial failure 風險。
3. `coin-flow-batch-projection`：金幣流水清算；有 log_reel / jackpot / taxt 多資料源、Redis checkpoint、跨日切換與 Mongo projection。
4. `online-payment-data-cleaning`：支付 / 提現資料清洗；從 payment order 分表彙整充值、提現、首充、代理充值與經濟資料。
5. `partition-table-creation`：每日 / 每月分表建立；支撐 log / BI table rollover，屬於可靠性輔助 flow。

不更新履歷。沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認前，本文件所有候選 flow 都只當 `專案存在 / code-backed` 或 `分析素材 / learning-only`。

## 自動重讀紀錄

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/CONVENTIONS.md`
- `projects/source-repo-inventory.md`

已重讀 vault：

- `projects/README.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/06-todo.md`
- `projects/iwin/app_bi/README.md`
- `projects/iwin/app_bi/step1-candidate-flows.md`
- `projects/iwin/app_bi/step2-flow-comparison.md`

已重讀 code repo：

- `/Users/nick/Git/iwin/game_job`
- 目前分支：`main`
- 遠端分支清單
- 近期主線 log
- `--all` 相關 log grep：`gsc`、`Antplay`、`PG`、`每日游戏数据`、`game-job`
- path-specific log：每日遊戲資料彙總、Antplay/GSC 備份、coin flow、online payment、create table
- Quartz / job / service / mapper / SQL template 主要入口

未重讀：

- 未 checkout 每個遠端分支。
- 未逐 commit diff。
- 未逐檔逐行掃完整 repo。
- 未掃完整下游 repo：`game_api`、`iwin_gameserver`、`third_games_api`、`payment`、`app_bi` 查詢端只沿用既有 vault 文件。
- 未確認 production 實際啟用 cron 設定。
- 未確認 Nick 個人 MR / ticket / commit。

## 舊文件狀態判斷

| 文件 | 狀態 | 判斷 |
| --- | --- | --- |
| `projects/iwin/game_job/README.md` | 本次新建 | 專案入口，僅放定位與下一步 |
| `projects/iwin/game_job/step1-candidate-flows.md` | 本次新建 | Step 1 主文件 |
| `projects/iwin/app_bi/flows/daily-game-record-summary/*` | 可沿用 / 但只涵蓋 app_bi 查詢端與 game_job producer 線索 | 若改做 `game_job` flow，應以 `game_job` code 為主重寫，不複製舊文 |
| `senior-owner-playbook/01-senior-owner-flow-inventory.md` | 需同步 | 本次新增 `game_job` Step 1 狀態 |
| `senior-owner-playbook/06-todo.md` | 需同步 | 下一步應加入 `game_job daily-game-data-summary Step 2` |

## 掃描等級判斷

本次使用 Level 1。

原因：

- Nick 只下 `iwin game_job`，尚未指定單條 flow。
- `game_job` 尚無既有 project 文件，第一步應先找 candidate flows。
- Level 1 足以判斷哪幾條 job flow 值得 Step 2 排序；單條完整資料流與 failure window 留到 Step 3 / Level 2。

本次做：

- 找出 Quartz / Job / Service / Mapper 入口。
- 盤點 Top 3-5 條 Senior / Owner 候選 flow。
- 標示已確認、推測、待確認與履歷邊界。

本次不做：

- 不建立單條 flow folder。
- 不深挖單一 flow。
- 不逐 commit diff。
- 不更新履歷 / 自傳。
- 不把 `game_job` 寫成 Nick 主導成果。

## 本次掃描範圍

已看 repo：

- `/Users/nick/Git/iwin/game_job`
- `/Users/nick/Git/nick/nick-vault`

source repo 狀態：

- `/Users/nick/Git/iwin/game_job` 工作區乾淨。
- 目前分支：`main`。

已看分支：

- 本地：`main`
- 遠端清單包含：`origin/main`、`origin/k3s`、`origin/feature/gsc_record_backup`、`origin/antplay_new_bak_job`、`origin/nick-DailyReport`、`origin/feature/setBlacklistHset`、`origin/antplay-pg-mongo`、`origin/fix-ci-deploy`、`origin/beta`、`origin/feature/RD-128`、`origin/feature/RD-71`。

已看 git log：

- 近期主線 log，包含 coupon、GSC 批次、Antplay 備份、每日遊戲資料彙總、PG / Antplay 時區修正。
- `--all` 相關 log，包含 `origin/nick-DailyReport` 的每日資料彙總 history、`origin/feature/gsc_record_backup`、`origin/antplay_new_bak_job`、`origin/antplay-pg-mongo`。
- path-specific log：
  - `GameDailyAntplayAndPGJob.java`
  - `GameDailyIwinJob.java`
  - `GameDailyServiceImpl.java`
  - `GameDailyDao.xml`
  - `LogReelDao.xml`
  - `ThirdLogAntplayNewJob.java`
  - `ThirdTransactionAntplayNewJob.java`
  - `CoinFlowJob.java`
  - `OnlinePaymentDataJob.java`
  - `CreateTableJob.java`

已看 code path：

- `config/application-quartz.yml`
- `src/main/java/com/Joblication.java`
- `src/main/java/com/quartz/QuartzService.java`
- `src/main/java/com/quartz/*JobQuartz.java`
- `src/main/java/com/common/job/BiJobBase.java`
- `src/main/java/com/job/biTask/GameDailyAntplayAndPGJob.java`
- `src/main/java/com/job/biTask/GameDailyIwinJob.java`
- `src/main/java/com/job/biTask/ThirdLogAntplayNewJob.java`
- `src/main/java/com/job/biTask/ThirdTransactionAntplayNewJob.java`
- `src/main/java/com/job/biTask/CoinFlowJob.java`
- `src/main/java/com/job/biTask/DailyRtpJob.java`
- `src/main/java/com/job/biTask/OnlinePaymentDataJob.java`
- `src/main/java/com/job/biTask/PlayerBetJob.java`
- `src/main/java/com/job/system/CreateTableJob.java`
- `src/main/java/com/service/impl/GameDailyServiceImpl.java`
- `src/main/resources/mapper/bilog/GameDailyDao.xml`
- `src/main/resources/mapper/logone/LogReelDao.xml`
- `src/main/resources/initBiTable/*.sql`
- `src/main/resources/initGameTable/*.sql`

## 專案定位

已確認：

- `game_job` 是 Spring Boot application，主要靠 Quartz 啟動定時任務。
- `QuartzService` 會依設定與 enable flag 註冊 job。
- job wrapper 多數設定 task name、task type、task overtime 後呼叫 `runTask()`。
- `BiJobBase` 提供 task date / custom date / Redis task state / 清除舊資料 / task finish 等共用批次能力。
- repo 同時操作 MySQL、Redis、MongoDB，並讀取多個分表與 collection。

推測：

- 多數資料是從 source log 或交易表轉成 BI / 報表 projection，不是最終交易真相。
- 真正遊戲局寫入、第三方 provider log 寫入、payment order source of truth 在其他 repo。

待確認：

- production 實際 enable 的 job 清單。
- 每條候選 flow 的 upstream writer 與 downstream app_bi 查詢端。
- job 重跑時是否會造成 duplicate insert、delete / insert gap、backup partial success 或 retention miscount。

## Top Candidate Flows

### 1. `daily-game-data-summary`

中文名稱：每日遊戲資料彙總
證據層級：專案存在 / code-backed；Nick 貢獻待確認
建議：下一步優先做 Step 2，再決定是否 Step 3 深挖

為什麼重要：

- 這條 flow 牽涉遊戲資料統計正確性：玩家數、spin counts、spin amount、profit loss、kill rate、新增玩家、次日 / 2 日 / 3 日 / 7 日留存。
- 有跨時區問題：PG、Antplay、Iwin 的資料日切分不同，history 裡有明確時區修正。
- 具備重跑與清除舊資料邊界：先刪當日 projection，再 insert personal data / summary / retention。
- `app_bi daily-game-record-summary` 已有查詢端分析，可串成 producer -> report query 的完整 projection flow。

已確認 evidence：

- `GameDailyAntplayAndPGJob` 負責 PG / Antplay，包含刪除當日資料、匯入 personal data、寫 summary、算 retention。
- `GameDailyIwinJob` 負責 Iwin，另包含 14 天前 personal data 備份 / 清除與 180 天前 summary 備份 / 清除。
- `LogReelDao.getPersonalData` 從每日 `log_reel_{yyyy_m_d}` 彙整 uid、spin count、spin amount、win currency。
- `GameDailyDao` 寫入 `log_game_daily_record`、`log_game_daily_record_new_players`、`log_game_daily_record_backup`。
- path-specific log 有 `#247` 每日遊戲資料彙總、`#384` PG 時區修正、`#403` Antplay 時區修正。

推測：

- `log_reel` 的 source writer 在 `iwin_gameserver` 或 game runtime。
- app_bi 報表查詢會讀 `log_game_daily_record` 類 projection。

待確認：

- production enable flag 與實際排程時間。
- `log_reel` upstream writer。
- app_bi 查詢端與欄位對應是否完全一致。
- 重跑時 `log_game_daily_record_new_players` 是否可能留下舊資料或造成新增玩家計算偏差。
- Nick 是否實際維護過 `#247`、`#384`、`#403` 或相關 MR。

履歷邊界：

- 潛力中高，但目前不可寫正式履歷。
- 可作為 batch correctness / timezone boundary / projection consistency 的面試分析素材。

### 2. `third-party-record-mongo-backup`

中文名稱：第三方遊戲紀錄 Mongo 備份與清理
證據層級：專案存在 / code-backed；Nick 貢獻待確認

為什麼重要：

- 第三方遊戲紀錄與交易紀錄通常是 audit / reconciliation / troubleshooting 的重要資料。
- flow 包含分批查詢、寫 backup collection、刪 origin collection、刪舊 backup，天然有 partial failure 與資料保留策略問題。
- 近期 commit 顯示 GSC / Antplay 批次大小與備份功能有持續調整。

已確認 evidence：

- `ThirdLogAntplayNewJob` 將 `third_log_antplay_new` 早於保留門檻的資料分批搬到 backup collection，再依 `_id` 刪原 collection。
- `ThirdTransactionAntplayNewJob` 對 `third_transaction_antplay_new` 做同型備份清理。
- 兩條 new job 在目前設定中 enable flag 為 true。
- history 包含 `antplay_new_bak_job`、`feature/gsc_record_backup`、GSC 批次大小調整。

推測：

- GSC 與舊 Antplay / Oneapi job 也屬於同一類 third-party record retention flow，但本次只讀 new Antplay 類別較完整。
- upstream 寫入者可能在 `third_games_api`、`game_api` 或 provider integration repo。

待確認：

- backup insert 成功但 delete 失敗、delete 成功但 backup 未完整時的處理策略。
- `_id` 型別與 VO id mapping 是否完全穩定。
- GSC job 的完整 code path 與 batch size history。
- production retention policy 是否為 14 天搬移、30 天清理。

履歷邊界：

- 目前只可作資料保留與 batch safety 分析素材。
- 不可說 Nick 設計 third-party audit backup。

### 3. `coin-flow-batch-projection`

中文名稱：金幣流水清算 / 遊戲行為投影
證據層級：專案存在 / code-backed；Nick 貢獻待確認

為什麼重要：

- 牽涉遊戲金幣流水、Jackpot、服務費 / 稅費、多渠道資料與每日切換。
- 有 Redis checkpoint / pull status / last result，適合分析 incremental batch、跨日補拉與 idempotency。
- 比單純報表查詢更接近 money correctness 影子資料，但仍需確認 source of truth。

已確認 evidence：

- `CoinFlowJob` 讀 `log_reel`、`log_jackpot`、`log_taxt`，產生 coin flow 與 user game behaviour。
- job 使用 Redis key 記錄 last pull max id、record num、pull over status、last result。
- `execAfter()` 會確認所有 channel 的三種資料表都拉完後才 `taskFinish()`。
- `afterTaskFinish()` 會重設 next day pull id / status 並清 Redis。

推測：

- projection 可能寫 Mongo `log_coin_flow*` 與 BI table `user_game_behaviour_{yyyy_m}`。
- 真正金幣交易 truth source 不在此 job。

待確認：

- `handleLogReelData()`、`handleLogJackpotData()`、`handleTaxtData()` 的完整 path 與 mapper。
- 跨日多拉與 max loop 的 failure window。
- Redis checkpoint 遺失或重設後是否會重複投影。
- app_bi 或 BI 查詢端如何使用這些 projection。

履歷邊界：

- 可作 Senior batch / incremental projection 設計題。
- 未 Level 2 前不可寫履歷。

### 4. `online-payment-data-cleaning`

中文名稱：充值 / 提現資料清洗與每日經濟資料
證據層級：專案存在 / code-backed；Nick 貢獻待確認

為什麼重要：

- 牽涉 payment order 分表、充值 / 提現分類、首充、代理充值與經濟資料。
- 與 money correctness 有關，但目前看起來是 BI projection，不是 payment source of truth。

已確認 evidence：

- `OnlinePaymentDataJob` 讀 `payment_order_{yyyy_m}`。
- 依 `billType` 分充值 / 提現，依 reason、tradeType、onlineType、channelType 做分類。
- 透過 `OnlineDataHandleService` 寫每日支付、線下支付、代理充值、首充、提款與 economic data。

推測：

- payment order source of truth 在 `payment` repo。
- 本 flow 可與 `payment-provider-callback` 或 `payment-order-status-repair` 串成 reporting / reconciliation 輔助面。

待確認：

- `OnlineDataHandleService` 各 handler 的寫入表與重跑策略。
- 跨月 payment order 分表與 date boundary。
- 是否有 callback / repair 後重新清洗的流程。

履歷邊界：

- 目前不直接寫金流成果。
- 可作 payment reporting projection 的輔助題。

### 5. `partition-table-creation`

中文名稱：每日 / 每月分表建立
證據層級：專案存在 / code-backed；Nick 貢獻待確認

為什麼重要：

- 分表建立是所有 log / BI job 的可靠性前置條件。
- 若表不存在，log writer、batch projection、報表查詢都可能失敗。

已確認 evidence：

- `CreateTableJob` 讀取 `initBiTable/*.sql` 與 `initGameTable/*.sql`。
- BI table 對固定 DB 建表，game table 會對所有 channel db 建表。
- SQL template 包含 `log_reel`、`log_currency`、`log_jackpot`、`payment_order`、`user_game_behaviour`、`player_bet` 等每日 / 每月表。

推測：

- 它是支撐性 flow，本身面試價值低於資料彙總與 coin flow，但可補 reliability / rollover 故事。

待確認：

- 建表 job production 啟用狀態。
- 失敗告警與補建流程。
- schema 變更如何 rollout 到已存在分表。

履歷邊界：

- 不建議單獨寫履歷。
- 可作 flow 內補充：batch 系統如何處理分表 rollover。

## 不優先候選

| Flow | 原因 |
| --- | --- |
| `player-bet-daily-projection` | 有每日玩家下注資訊，但目前 evidence 較薄，價值低於 `coin-flow-batch-projection` |
| `daily-rtp-projection` | 有 RTP / online game rtp projection，但多半依賴 `user_game_behaviour`，可作 coin flow 後續 |
| `redis-message-sync` | repo 有 Redis queue consumer / command handler，但本次未深掃；需要先確認是否為 production high-value flow |
| agent / weekly bonus 類 job | 可能有業務價值，但目前優先級低於遊戲資料、第三方紀錄、payment projection |

## 下一步建議

只推薦一件事：

```text
game_job daily-game-data-summary Step 2
```

原因：

- `daily-game-data-summary` evidence 最厚，且已有 `app_bi daily-game-record-summary` 查詢端可對照。
- Step 2 可以把 `daily-game-data-summary`、`third-party-record-mongo-backup`、`coin-flow-batch-projection` 放在同一張風險 / 價值比較表，確認第一條 Step 3 要深挖哪個。
- 不更新履歷 / 自傳；下一步產出是候選排序與深挖範圍，不是正式 claim。
