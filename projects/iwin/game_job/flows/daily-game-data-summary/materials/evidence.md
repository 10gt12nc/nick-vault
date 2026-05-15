# daily-game-data-summary Evidence

更新時間：2026-05-15（KB 更新後深度檢查調整）
Step：3
掃描等級：Level 2
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## KB 更新後調整摘要

本次是依更新後 KB 對 `game_job daily-game-data-summary Step 3` 做深度檢查與調整，不是新增 Step 4。

檢查結論：

- Step 1 / Step 2 / Step 3 主線完整，沒有跳 Step。
- Step 3 主報告有白話導讀、Code 分層、最小架構圖、正常流程圖、逐步說明與 Senior / Owner 分析，可沿用。
- 原 Step 3 evidence 對 remote refs 最新性、遠端分支、近期 log、path-specific log 與既有文件狀態寫得不夠細，本次補齊。
- `app_bi` 本機仍落後 `origin/main` 4 commit；下游結論維持「local snapshot 線索」，不可宣稱已看最新 app_bi。

## 本次自動重讀

已重讀 vault KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`

已重讀 project 文件：

- `projects/iwin/game_job/README.md`
- `projects/iwin/game_job/step1-candidate-flows.md`
- `projects/iwin/game_job/step2-flow-comparison.md`
- `projects/iwin/game_job/flows/daily-game-data-summary/flow.md`
- `projects/iwin/game_job/flows/daily-game-data-summary/career-interview.md`
- `projects/iwin/game_job/flows/daily-game-data-summary/materials/evidence.md`
- `projects/iwin/game_job/flows/daily-game-data-summary/materials/decision-notes.md`
- `projects/iwin/game_job/flows/daily-game-data-summary/materials/interview.md`
- `projects/iwin/game_job/flows/daily-game-data-summary/materials/claim-boundary.md`
- `projects/iwin/app_bi/flows/daily-game-record-summary/flow.md`
- `projects/iwin/app_bi/flows/daily-game-record-summary/materials/evidence.md`

## 既有文件狀態判斷

| 文件 | 狀態 | 判斷 |
| --- | --- | --- |
| `projects/iwin/game_job/README.md` | 可沿用 / 本次同步 | 專案入口清楚；本次補上 Step 3 已深度檢查調整 |
| `projects/iwin/game_job/step1-candidate-flows.md` | 可沿用 | 已做 Level 1 candidate flow 掃描，且沒有直接跳 Step 3 |
| `projects/iwin/game_job/step2-flow-comparison.md` | 可沿用 | 已比較候選 flow 與風險，符合新 KB 的 Step 2 前置規則 |
| `flow.md` | 可沿用 / 需補 evidence | 主報告閱讀層次完整；本次補 KB 更新後檢查結論與分層轉譯 |
| `career-interview.md` | 可沿用 | 保守標示 Nick 貢獻待確認，未誇大 |
| `materials/evidence.md` | 本次重點調整 | 補 remote refs、分支 / log 範圍、既有文件狀態與未掃邊界 |
| `materials/decision-notes.md` | 可沿用 | 已整理 delete + insert、new player state、timezone、backup 等 owner decision |
| `materials/interview.md` | 可沿用 | 以分析案例呈現，未寫成 Nick 成果 |
| `materials/claim-boundary.md` | 可沿用 | 明確禁止主導 / 設計 / 修正 claim |

## Repo 狀態

本輪已重新執行 fetch：

- `/Users/nick/Git/iwin/game_job`：`git fetch --all --prune`
- `/Users/nick/Git/iwin/app_bi`：`git fetch --all --prune`
- `/Users/nick/Git/iwin/iwin_gameserver`：`git fetch --all --prune`
- `/Users/nick/Git/iwin/game_api`：`git fetch --all --prune`
- `/Users/nick/Git/iwin/third_games_api`：`git fetch --all --prune`

| Repo | Branch | Local HEAD | Remote HEAD | Ahead / Behind | 本輪用途 |
| --- | --- | --- | --- | --- | --- |
| `/Users/nick/Git/iwin/game_job` | `main` | `23908f474efb5cfe5a3ce2bc780fb67a0860c4c2` | `origin/main` 同 HEAD | 0 / 0 | 主 flow 深掃 |
| `/Users/nick/Git/iwin/app_bi` | `main` | `4a206a28ab8f5be4329602cdc510ee9ea41efb25` | `fd9881fc417e01f960d758b4b91ba1a10b507855` | behind 4 | 下游查詢 local snapshot；需補 remote diff |
| `/Users/nick/Git/iwin/iwin_gameserver` | `main` | `30a9fcb95bfda33b582deeb4e149eb06bed4afe3` | `origin/main` 同 HEAD | 0 / 0 | upstream writer 線索掃描 |
| `/Users/nick/Git/iwin/game_api` | `main` | `39bb6e38210bb79c6e68a6a6d818cb87986d39f0` | `origin/main` 同 HEAD | 0 / 0 | log_reel 查詢線索掃描 |
| `/Users/nick/Git/iwin/third_games_api` | `beta` | `4915ea5a5000d61eb36717203ea4c6afc45322fa` | `origin/beta` 同 HEAD | 0 / 0 | log_reel 查詢線索掃描 |

注意：本輪只 fetch remote refs，沒有 pull、merge、checkout 或修改公司 repo。

## `game_job` 分支與 log 掃描

已看遠端分支清單：

- `origin/main`
- `origin/k3s`
- `origin/feature/gsc_record_backup`
- `origin/antplay_new_bak_job`
- `origin/nick-DailyReport`
- `origin/feature/setBlacklistHset`
- `origin/antplay-pg-mongo`
- `origin/fix-ci-deploy`
- `origin/beta`
- `origin/feature/RD-128`
- `origin/ci_test`
- `origin/feature/RD-71`

已看近期主線 log：

- `23908f4`：Merge remote-tracking branch `origin/main`
- `93fa5c1`：兌換碼功能相關
- `676beeb`：新增配置
- `a19571b`：兌換碼功能
- `b993395`：GSC 批量執行改為 2500
- `a6268cb`：MongoDB ISODate 修正
- `d877c58`：Antplay 分批備份 Mongo、TestController API 備份 GSC
- `bf92773` / `d11b1f4`：GSC 分批查詢調整
- `d9edfa7`：Antplay 每日彙總 GMT+0 修正

已看本 flow path-specific log，重點包含：

- `d9edfa7`：Antplay timezone 修正。
- `696acf7`：PG timezone 修正。
- `3a7fd8b`：PG / Antplay 與 Iwin job 切分。
- `661ba5d`、`90a8c22`、`babfc2b`：新增玩家 / 留存調整。
- `52ee53d`：每日彙總跨時區修正。
- `aecf2b4` 起一系列 `#247`：每日遊戲資料彙總與備份功能演進。

未做：

- 未 checkout 遠端分支。
- 未逐檔逐行掃所有 module。
- 未逐一閱讀每個相關 commit 的完整 diff。
- 未對 `origin/nick-DailyReport` 做 branch 工作樹切換；僅透過 log / path-specific history 作 evidence 線索。

## 主程式掃描範圍

### `game_job`

已讀：

- `/Users/nick/Git/iwin/game_job/src/main/java/com/job/biTask/GameDailyAntplayAndPGJob.java`
- `/Users/nick/Git/iwin/game_job/src/main/java/com/job/biTask/GameDailyIwinJob.java`
- `/Users/nick/Git/iwin/game_job/src/main/java/com/quartz/GameDailyAntplayAndPGJobQuartz.java`
- `/Users/nick/Git/iwin/game_job/src/main/java/com/quartz/GameDailyIwinJobQuartz.java`
- `/Users/nick/Git/iwin/game_job/src/main/java/com/conllor/TestController.java`
- `/Users/nick/Git/iwin/game_job/src/main/java/com/service/impl/GameDailyServiceImpl.java`
- `/Users/nick/Git/iwin/game_job/src/main/java/com/pojo/vo/DailySummaryVo.java`
- `/Users/nick/Git/iwin/game_job/src/main/resources/mapper/bilog/GameDailyDao.xml`
- `/Users/nick/Git/iwin/game_job/src/main/resources/mapper/logone/LogReelDao.xml`
- `/Users/nick/Git/iwin/game_job/config/application-quartz.yml`

### `app_bi`

已讀 local snapshot：

- `/Users/nick/Git/iwin/app_bi/app/admin/controller/Payment.php`
- `/Users/nick/Git/iwin/app_bi/public/views/jjsj/mrucsjhz/index.js`

邊界：

- local `main` 落後 `origin/main` 4 commit。
- 本輪未讀 `origin/main` 上差異，所以不能把 app_bi 結論寫成最新狀態。

### Upstream writer 線索

已用 `rg` 掃：

- `/Users/nick/Git/iwin/iwin_gameserver`
- `/Users/nick/Git/iwin/game_api`
- `/Users/nick/Git/iwin/third_games_api`

找到線索：

- `iwin_gameserver/slots-center/src/main/java/com/slots/sql/data/SqlLogReelData.java` 有 `log_reel`、`spin_currency`、`win_currency` mapping 線索。
- `iwin_gameserver/slots-center/src/main/java/com/slots/center/job/http/AntplayTransferInOutJob.java`、`AntplaySettledJob.java`、`GSCTransferInOutJob.java`、`PGTransferInOutJob.java` 註解提到 `log_reel`、`serial_id = bet_id`、`spin_currency`、`win_currency`。
- `iwin_gameserver/slots-games/slots-game-common/src/main/java/com/slots/game/common/data/AddCenterCoinAntplay.java`、`AddCenterCoinPG.java`、`AddCenterCoinGSC.java`、`AddCenterCoinAP.java`、`GamePlayer.java` 有投注 / 贏分寫入線索。
- `game_api`、`third_games_api` 有 `LogReelYmdDao.xml` 查詢線索，但本輪未確認 writer。

邊界：

- 這只是 upstream 線索掃描，不是完整 writer flow 深掃。
- 未逐檔逐行確認投注交易如何落到 `log_reel`。
- 不得把 upstream 寫入責任寫成 Nick 的工作成果。

## 已確認事實

### Job 切分

- PG / Antplay 由 `GameDailyAntplayAndPGJob` 處理。
- Iwin 由 `GameDailyIwinJob` 處理。
- 兩個 Quartz wrapper 都使用 `@DisallowConcurrentExecution`。
- `TestController.gameDailyJob(date)` 可手動對指定 date 觸發兩個 job。

### 主流程

- Job 先刪除 `log_game_daily_record` 指定日期 / 平台 projection。
- 再由 `log_reel` 聚合 type=0 personal data。
- 再由 type=0 彙總 type=1 summary。
- 再計算新增玩家與留存，寫回 type=1。
- Iwin job 額外做舊資料 backup / delete。

### 時區窗口

- Antplay 查詢窗口目前為 `twoDay 21:00:00.000` 到 `oneDay 20:59:59.999`。
- PG 查詢窗口目前為 `twoDay 13:00:00.000` 到 `oneDay 12:59:59.999`。
- Iwin 走指定資料日分表，未看到同樣跨日窗口合併邏輯。

### Projection 表

- `log_game_daily_record`：
  - `type=0`：personal data。
  - `type=1`：summary / retention。
- `log_game_daily_record_new_players`：保存 platform / uid / create_time，用於新增玩家判斷。
- `log_game_daily_record_backup`：保存 Iwin job 的舊資料備份。

### 下游報表

- `Payment::DailyGameDataSummary()` 查 `log_game_daily_record`。
- 查詢條件包含 date range、`type=1`、optional platform。
- 使用 `SUM(CASE WHEN sub_type=... THEN value1 ELSE 0 END)` 轉成報表欄位。
- Excel endpoint 讀相近結果匯出。

## Path-specific commit evidence

已讀重要 commit / diff：

| Commit | 訊息 | 本 flow 意義 |
| --- | --- | --- |
| `3a7fd8b` | `fix: 台灣早上8點先匯總 PG antplay , 下午一點做 iwin` | 將舊每日彙總拆成 PG / Antplay 與 Iwin 兩個 job，代表平台資料日 / cron 時間有實際修正歷史 |
| `696acf7` | `fix(#384): PG對帳時區從GMT+1 改成GMT+8` | PG 查詢窗口曾修正，證明時區是 production correctness 風險 |
| `d9edfa7` | `fix(#403): Iwin game-job 計算antplay 遊戲數據彙總，改為GMT+0` | Antplay 查詢窗口曾修正，並調整排程秒數 |
| `aecf2b4` | `feat(#247): 新增每日游戏数据汇总 備份` | 加入 14 天 type=0、180 天 type=1 備份 / 清理策略 |
| `661ba5d` | `fix: 新增每日游戏数据汇总 新增人数` | 新增玩家計算曾調整 |
| `babfc2b` | `fix: 新增每日游戏数据汇总 新增人数、留存` | 留存與新增玩家邏輯曾調整 |
| `52ee53d` | `fix: 新增每日游戏数据汇总 跨時區` | 跨時區問題曾進入修正歷史 |

## 待確認

- Production 實際 enable flag、cron 與部署設定是否覆蓋 local config。
- `BiJobBase` 對失敗 job 是否有 retry、lock、告警與 task state 清理。
- `log_game_daily_record_new_players` 是否有 unique key，重跑舊日期是否會造成新增玩家數改變。
- `log_game_daily_record_backup` 是否有 unique key 或可接受 duplicate。
- backup insert 與 delete 是否在 transaction 內，或是否有 rows count reconciliation。
- `app_bi` origin/main 新增的 4 commit 是否改變 `DailyGameDataSummary()` 查詢邏輯。
- upstream `log_reel` writer 的完整資料日定義與第三方平台 settlement 邊界。
- Nick 是否有本人 MR / ticket / commit / production issue evidence。

## Evidence 分級

`專案存在 / code-backed`：

- `game_job` job / service / mapper / config / commit diff。
- `app_bi` local snapshot 查詢端。
- `iwin_gameserver` upstream writer 線索。

`分析素材 / learning-only`：

- 對 delete + insert、staging / publish marker、reconciliation 的 Owner 改善建議。
- 面試回答中的改善策略。

`待確認`：

- Nick 本人參與。
- Production 啟用狀態。
- 下游最新 remote 行為。
- 上游完整 writer flow。
