# Evidence - app_bi daily-game-record-summary

更新時間：2026-05-15
完成狀態：Step 5 已完成
文件角色：`materials/evidence.md` 證據附錄
掃描等級：Level 2 Flow 深掃
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 自動重讀紀錄

## 2026-05-15 KB 補查

依最新 KB 補確認 source repo 遠端狀態：

- `/Users/nick/Git/iwin/app_bi` 已 fetch；本地 `main=4a206a2`，`origin/main=fd9881f`，本地落後 4 commit；未 pull、未 checkout。
- `/Users/nick/Git/iwin/game_job` 已 fetch；本地 `main=23908f4`，`origin/main=23908f4`，同步。
- 本輪未改公司 repo。
- 因此本 evidence 可作已讀素材；若要升級 claim 或重做報表 flow，需先確認是否更新本地 app_bi 或改讀遠端 diff。

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`

已重讀 vault：

- `projects/iwin/app_bi/README.md`
- `projects/iwin/app_bi/step1-candidate-flows.md`
- `projects/iwin/app_bi/step2-flow-comparison.md`
- 既有 `flows/point-control-admin-operation/*`
- 既有 `flows/admin-config-redis-sync/*`

已讀 source repo：

- `/Users/nick/Git/iwin/app_bi`
- `/Users/nick/Git/iwin/game_job`
- `/Users/nick/Git/iwin/iwin-workspace` 的 SQL dump 只作 schema 參考

## Step 4 補掃紀錄

本次 Step 4 為面試 case 轉換，不改正式履歷 / 自傳。

已補重讀：

- 本 flow 的 `flow.md`
- 本 flow 的 `career-interview.md`
- 本 flow 的 `materials/evidence.md`
- 本 flow 的 `materials/decision-notes.md`
- 本 flow 的 `materials/interview.md`
- 本 flow 的 `materials/claim-boundary.md`
- `projects/iwin/app_bi/README.md`
- `projects/iwin/app_bi/step1-candidate-flows.md`
- `projects/iwin/app_bi/step2-flow-comparison.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/04-interview-casebook.md`
- `senior-owner-playbook/06-todo.md`

已補確認 code 狀態：

- `/Users/nick/Git/iwin/app_bi` 目前分支：`main`
- `/Users/nick/Git/iwin/app_bi` 工作區：乾淨
- `/Users/nick/Git/iwin/app_bi` 近期 log 與 `Payment.php`、`public/views/jjsj/mrucsjhz/**` path-specific log
- `/Users/nick/Git/iwin/game_job` 目前分支：`main`
- `/Users/nick/Git/iwin/game_job` 工作區：乾淨
- `/Users/nick/Git/iwin/game_job` 近期 log 與 daily summary job / mapper path-specific log

Step 4 邊界：

- 未 checkout 每個遠端分支逐一比對。
- 未逐 commit diff。
- 未補到 Nick 本人 MR / ticket / production issue。
- 因此本 flow 只能轉成保守面試 case，不更新正式履歷 / 自傳。

## Step 5 補掃紀錄

本次 Step 5 為履歷 / 自傳更新判定，不新增正式 claim。

已補重讀：

- 本 flow 的 `flow.md`
- 本 flow 的 `career-interview.md`
- 本 flow 的 `materials/evidence.md`
- 本 flow 的 `materials/interview.md`
- 本 flow 的 `materials/claim-boundary.md`
- `projects/iwin/app_bi/README.md`
- `projects/iwin/app_bi/step1-candidate-flows.md`
- `projects/iwin/app_bi/step2-flow-comparison.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/06-todo.md`

已補確認 code 狀態：

- `/Users/nick/Git/iwin/app_bi` 目前分支：`main`
- `/Users/nick/Git/iwin/app_bi` 工作區：乾淨
- `/Users/nick/Git/iwin/app_bi` path-specific log：`Payment.php`、`public/views/jjsj/mrucsjhz/**`
- `/Users/nick/Git/iwin/game_job` path-specific log：daily summary jobs / service / mapper

Step 5 判定：

- 不更新 `senior-owner-playbook/05-resume-master-zh.md`。
- 不更新 `senior-owner-playbook/08-application-autobiography-zh.md`。
- 本 flow 只保留為 `分析素材 / learning-only` 與保守面試 case。

原因：

- 未補到 Nick 本人 MR / ticket / commit / production issue / 本人確認。
- 未確認 Nick 實際處理補跑、時區、金額、留存、查詢或 producer job。
- 未看到量化改善或正式 owner evidence。

## Branch / log

`app_bi`：

- 目前分支：`main`
- 工作區：乾淨
- 已看遠端分支清單；未 checkout 每個遠端分支逐一比對。
- path-specific log 已看：
  - `app/admin/controller/Payment.php`
  - `public/views/jjsj/mrucsjhz/index.html`
  - `public/views/jjsj/mrucsjhz/index.js`
  - `public/views/jjsj/mrucsjhz/index.tpl.html`

`game_job`：

- 目前分支：`main`
- 工作區：乾淨
- 已看近期 log 與 path-specific log。
- 已看 daily summary 相關 job、service、mapper、quartz config。

## 已確認 app_bi 查詢端

檔案：

- `/Users/nick/Git/iwin/app_bi/app/admin/controller/Payment.php`
- `/Users/nick/Git/iwin/app_bi/public/views/jjsj/mrucsjhz/index.html`
- `/Users/nick/Git/iwin/app_bi/public/views/jjsj/mrucsjhz/index.js`

已確認：

- 前端 table 呼叫 `/admin.php/Payment/DailyGameDataSummary`。
- Excel 下載呼叫 `/admin.php/Payment/DailyGameDataSummaryLogExcel`。
- 查詢條件包含日期區間與平台。
- Controller 查 `log_game_daily_record`。
- Controller 限定 `type = 1`。
- Controller 以 `date, platform` group。
- Controller 用 `SUM(CASE WHEN sub_type = ... THEN value1 ELSE 0 END)` pivot 欄位。
- 金額欄位用 `Base::getCurrencyUnit()` 與 `Base::divideUnit()` 做換算。
- UI 有平台資料生成時間與時區提示。

重要 app_bi commits：

- `48dbcd7 feat(#daily_game_record_summary): 每日游戏数据汇总`
- `ed74d87 feat(#daily_game_record_summary): sql 修正`
- `7cbc770 feat(#daily_game_record_summary): 加上平台選擇 金額 /100000`
- `3df1efd feat(#daily_game_record_summary): 加欄位 2日留存`
- `8cfe682 feat(#daily_game_record_summary): 修正log_game_daily_record sql`
- `de9c761 feat(#daily_summary_note): 每日游戏数据汇总加上info`
- `c22966f feat(#feature/game_summary_note): 每日游戏数据汇总 加上提示生成資料時間`
- `311bd4b feat(#feature/game_summary_note): 每日游戏数据汇总 加上提示生成資料時間v2`
- `73258e1 feat(#feature/modify_info):每日游戏数据汇总修改info`

## 已確認 game_job producer

檔案：

- `/Users/nick/Git/iwin/game_job/src/main/java/com/job/biTask/GameDailyAntplayAndPGJob.java`
- `/Users/nick/Git/iwin/game_job/src/main/java/com/job/biTask/GameDailyIwinJob.java`
- `/Users/nick/Git/iwin/game_job/src/main/java/com/service/GameDailyService.java`
- `/Users/nick/Git/iwin/game_job/src/main/java/com/service/impl/GameDailyServiceImpl.java`
- `/Users/nick/Git/iwin/game_job/src/main/resources/mapper/bilog/GameDailyDao.xml`
- `/Users/nick/Git/iwin/game_job/src/main/resources/mapper/logone/LogReelDao.xml`
- `/Users/nick/Git/iwin/game_job/src/main/resources/application-quartz.yml`

已確認流程：

- `GameDailyAntplayAndPGJob` 負責 Antplay / PG。
- `GameDailyIwinJob` 負責 Iwin。
- job 開始會刪除當日平台資料。
- job 從 `slots_logv1.log_reel_{date}` 查原始遊戲戰績。
- `LogReelDao.xml` 依平台 game id 範圍與時間窗抓資料。
- `insertPersonalData()` 寫 `log_game_daily_record type = 0`。
- `getSummaryData()` 從 type=0 聚合出 type=1 summary。
- `insertSummaryData()` 寫 `log_game_daily_record type = 1`。
- `getUidByPlatform()` 讀近 7 日 UID 計算留存。
- `application-quartz.yml` 有 `gameDailyAntplayAndPGJob` 與 `gameDailyIwinJob` cron / enable flag。
- Quartz wrapper 使用 `@DisallowConcurrentExecution`。

重要 game_job commits：

- `927ce7c feat(#247): 新增每日游戏数据汇总`
- `4dbd08b feat(#247): 新增每日游戏数据汇总 fix 不同DB庫`
- `20290f9 feat(#247): 新增每日游戏数据汇总 fix 欄位長度不夠`
- `c59ba9d feat(#247): 新增每日游戏数据汇总 fix 計算`
- `aecf2b4 feat(#247): 新增每日游戏数据汇总 備份`
- `52ee53d fix: 新增每日游戏数据汇总 跨時區`
- `babfc2b fix: 新增每日游戏数据汇总 新增人数、留存`
- `696acf7 fix(#384): PG對帳時區從GMT+1 改成GMT+8`
- `d9edfa7 fix(#403): Iwin game-job 計算antplay 遊戲數據彙總，改為GMT+0`

## Schema evidence

參考：

- `/Users/nick/Git/iwin/iwin-workspace/docs/iwin-dump-sql/46/bi_log.sql`

已確認表：

- `log_game_daily_record`
- `log_game_daily_record_backup`
- `log_game_daily_record_new_players`

已確認欄位意義：

- `date`：日期
- `platform`：Iwin / Antplay / PG
- `value1`：個體時是玩家；彙總時依 `sub_type`
- `value2`：個體投注數
- `value3`：個體投注金額
- `value4`：個體 win currency
- `type`：0 個體；1 彙總
- `sub_type`：summary / retention 類型

## 已確認 / 推測 / 待確認

### 已確認

- app_bi 查詢端與 game_job producer 都存在。
- 報表查的是 `log_game_daily_record type=1`。
- producer 會先寫 type=0，再聚合 type=1。
- Antplay / PG / Iwin 有不同 job 與時間窗。
- app_bi 曾修正 group、SUM pivot、平台選擇、金額換算、2 日留存、提示生成時間與時區說明。
- game_job history 有跨時區、欄位長度、不同 DB、計算、新增人數 / 留存、備份等修正。

### 推測

- 這條 flow 的 production 風險主要是報表 projection 不一致，而不是即時交易錯誤。
- `application-quartz.yml` 的 enable flag 可能依環境覆蓋。
- 若補跑沒有完整審計，營運可能難以判斷報表數字是否已重算完成。

### 待確認

- Nick 是否實際參與此 flow。
- 正式環境 job enable 與部署設定。
- 補跑 API 是否有權限與審計。
- 是否有 job 失敗告警、row count 檢查、source log vs summary 對帳。
- 是否有交易系統以外的 downstream 使用這份報表。

## 未掃描範圍

- 未 checkout 每個遠端分支逐一比對。
- 未逐檔逐行掃完整 `app_bi`。
- 未逐 commit diff 掃完整 `game_job` #247 所有早期探索 commit。
- 未掃 `game_api`、`iwin_gameserver`、`payment`、`third_games_api`。
- 未確認 Nick 本人 MR / ticket / commit / production issue。

## Secret / 安全檢查

本文件只記錄：

- repo path
- class / method
- table / column 名稱
- commit hash 與 commit message

未寫入：

- token
- password
- private key
- internal IP
- production URL
- 客戶資料
- 實際連線資訊
