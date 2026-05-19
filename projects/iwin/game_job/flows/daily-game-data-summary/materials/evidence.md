# daily-game-data-summary Evidence

更新時間：2026-05-19
Step：5
掃描等級：Level 2+ claim gate；Step 4 面試收斂後補 Nick path-specific history 與重要 diff
證據層級：真實開發過 + code-backed

## Step 5 更新摘要

2026-05-19 Step 5 重新 fetch `game_job`、`app_bi` 與 `iwin_gameserver` remote refs，並掃 daily summary path-specific history。結論從「缺 Nick evidence，暫不放履歷」改為：

- `真實開發過`：Nick / `10gt12nc` 參與 `game_job` 每日遊戲資料彙總 batch / BI projection 開發與維護。
- `真實開發過`：Nick / `10gt12nc` 參與 PG / Antplay 資料日 / 時區窗口修正、job 拆分、新增玩家 / 留存與備份 / 清理相關調整。
- 可更新正式履歷 / 自傳，但只能用「參與 / 開發 / 維護」口徑，不寫主導完整 BI pipeline / game_job owner。

## Step 4 更新摘要

本次 Step 4 將既有 Step 3 flow 分析轉成 Senior Backend 面試 case study，主要更新：

- `career-interview.md`
- `materials/interview.md`
- `README.md`
- `flow.md` 的 Step 4 狀態與下一步建議

本次沒有新增 production claim，沒有更新正式履歷 / 自傳，沒有修改公司 repo。

Step 4 當時尚未補到 Nick path-specific evidence，因此不更新正式履歷 / 自傳；此限制已由 2026-05-19 Step 5 claim gate 重新覆核並更新。

## KB 更新後 Step 3 邊界保留

Step 4 開始前已重新檢查 Step 3：

- Step 1 / Step 2 / Step 3 主線完整，沒有跳 Step。
- Step 3 主報告有白話導讀、Code 分層、最小架構圖、正常流程圖、逐步說明與 Senior / Owner 分析，可沿用。
- `app_bi` 本機仍落後 `origin/main` 4 commit；下游結論維持「local snapshot 線索」，不可宣稱已看最新 app_bi。
- 上游 writer 仍只做線索掃描，未逐檔逐行確認投注交易如何落到 `log_reel`。

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
| `projects/iwin/game_job/README.md` | 可沿用 / 本次同步 | 專案入口清楚；本次補上 Step 5 完成狀態 |
| `projects/iwin/game_job/step1-candidate-flows.md` | 可沿用 | 已做 Level 1 candidate flow 掃描，且沒有直接跳 Step 3 |
| `projects/iwin/game_job/step2-flow-comparison.md` | 可沿用 | 已比較候選 flow 與風險，符合 Step 2 前置規則 |
| `flow.md` | 可沿用 / 本次同步 | 主報告閱讀層次完整；已補 Step 5 claim gate 狀態 |
| `career-interview.md` | 本次重整 | 轉成 Step 5 可用履歷 / 面試素材，補可說與不可誇大邊界 |
| `materials/evidence.md` | 本次同步 | 補 Step 5 掃描、Nick path-specific history 與重要 diff |
| `materials/decision-notes.md` | 可沿用 | 已整理 delete + insert、new player state、timezone、backup 等 owner decision |
| `materials/interview.md` | 本次重整 | 補完整 drill、白板口述與面試紅線 |
| `materials/claim-boundary.md` | 可沿用 | 明確禁止主導 / 設計 / 修正 claim |

## Repo 狀態

本輪已重新執行 fetch：

- `/Users/nick/Git/iwin/game_job`：`git fetch --all --prune`
- `/Users/nick/Git/iwin/app_bi`：`git fetch --all --prune`
- `/Users/nick/Git/iwin/iwin_gameserver`：`git fetch --all --prune`

| Repo | Branch | Local HEAD | Remote HEAD | Ahead / Behind | 本輪用途 |
| --- | --- | --- | --- | --- | --- |
| `/Users/nick/Git/iwin/game_job` | `main` | `23908f474efb5cfe5a3ce2bc780fb67a0860c4c2` | `origin/main` 同 HEAD | 0 / 0 | 主 flow 深掃；`origin/k3s` HEAD `b5da7dbf55301965b408cc92ad2021632f4a8db4` |
| `/Users/nick/Git/iwin/app_bi` | `main` | `4a206a28ab8f5be4329602cdc510ee9ea41efb25` | `fd9881fc417e01f960d758b4b91ba1a10b507855` | behind 4 | 下游查詢 local snapshot；需補 remote diff |
| `/Users/nick/Git/iwin/iwin_gameserver` | `main` | `30a9fcb95bfda33b582deeb4e149eb06bed4afe3` | `origin/main` 同 HEAD | 0 / 0 | upstream writer 線索掃描；`origin/k3s` HEAD `2519d64ccc11f9fade69cdefe23f225e571640c7` |
| `/Users/nick/Git/iwin/game_api` | `main` | `39bb6e38210bb79c6e68a6a6d818cb87986d39f0` | `origin/main` 同 HEAD | 0 / 0 | log_reel 查詢線索掃描 |
| `/Users/nick/Git/iwin/third_games_api` | `beta` | `4915ea5a5000d61eb36717203ea4c6afc45322fa` | `origin/beta` 同 HEAD | 0 / 0 | log_reel 查詢線索掃描 |

注意：本輪只 fetch remote refs，沒有 pull、merge、checkout 或修改公司 repo。

## `game_job` 分支與 log 掃描

已看遠端分支清單：`origin/main`、`origin/k3s`、`origin/feature/gsc_record_backup`、`origin/antplay_new_bak_job`、`origin/nick-DailyReport`、`origin/feature/setBlacklistHset`、`origin/antplay-pg-mongo`、`origin/fix-ci-deploy`、`origin/beta`、`origin/feature/RD-128`、`origin/ci_test`、`origin/feature/RD-71`。

已看本 flow path-specific log，重點包含，且下列 daily summary 相關 commits 的 author / committer 為 `10gt12nc <60815760+10gt12nc@users.noreply.github.com>`：

- `4174d3f`：新增每日遊戲資料彙總早期 job / service / mapper / quartz / config 主體。
- `562f1e6`：重整 `GameDailyJob`、`GameDailyServiceImpl`、`GameDailyDao.xml` 等每日彙總主 path。
- `31a3dd7`：修正 select + insert path。
- `aecf2b4`：每日遊戲資料彙總備份 / 清理。
- `babfc2b`、`661ba5d`：新增玩家 / 留存調整。
- `3a7fd8b`：拆分 PG / Antplay 與 Iwin job 排程。
- `696acf7`：PG 時區修正。
- `d9edfa7`：Antplay timezone 修正。
- `52ee53d` 起一系列 `#247`：每日遊戲資料彙總與跨時區功能演進。

未做：未 checkout 遠端分支、未逐檔逐行掃所有 module、未逐一閱讀每個相關 commit 的完整 diff。

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
| `4174d3f` | `feat(#247): 新增每日游戏数据汇总 修` | 新增每日遊戲資料彙總早期 job / service / mapper / quartz / config 主體，約 475 行 |
| `562f1e6` | `feat(#247): 新增每日游戏数据汇总` | 重整 `GameDailyJob`、service、DAO / mapper 與手動入口，形成主要 daily summary path |
| `31a3dd7` | `feat(#247): 新增每日游戏数据汇总 fix select + insert` | 調整 select + insert 重跑 / 寫入 path |
| `3a7fd8b` | `fix: 台灣早上8點先匯總 PG antplay , 下午一點做 iwin` | 將舊每日彙總拆成 PG / Antplay 與 Iwin 兩個 job，代表平台資料日 / cron 時間有實際修正歷史 |
| `696acf7` | `fix(#384): PG對帳時區從GMT+1 改成GMT+8` | PG 查詢窗口曾修正，證明時區是 production correctness 風險 |
| `d9edfa7` | `fix(#403): Iwin game-job 計算antplay 遊戲數據彙總，改為GMT+0` | Antplay 查詢窗口曾修正，並調整排程秒數 |
| `aecf2b4` | `feat(#247): 新增每日游戏数据汇总 備份` | 加入 14 天 type=0、180 天 type=1 備份 / 清理策略 |
| `661ba5d` | `fix: 新增每日游戏数据汇总 新增人数` | 新增玩家計算曾調整 |
| `babfc2b` | `fix: 新增每日游戏数据汇总 新增人数、留存` | 留存與新增玩家邏輯曾調整 |
| `52ee53d` | `fix: 新增每日游戏数据汇总 跨時區` | 跨時區問題曾進入修正歷史 |

### Step 5 claim 判斷

可升級為 `真實開發過`：

- Nick 參與每日遊戲資料彙總 batch / BI projection 開發與維護。
- Nick 參與 `game_job` daily summary job / service / DAO / mapper / config / quartz path。
- Nick 參與 PG / Antplay / Iwin job 拆分、資料日 / 時區窗口、新增玩家 / 留存、備份 / 清理相關調整。

仍只能 code-backed / 不可誇大：

- 未確認 Nick 主導完整 BI / game data pipeline。
- 未確認 production incident / ticket context。
- `app_bi` 本機仍落後 `origin/main` 4 commit，下游最新查詢不能宣稱已完整掃。
- upstream `log_reel` writer 只做線索掃描，不可寫成 Nick 負責完整上游投注落表。

## 待確認

- Production 實際 enable flag、cron 與部署設定是否覆蓋 local config。
- `BiJobBase` 對失敗 job 是否有 retry、lock、告警與 task state 清理。
- `log_game_daily_record_new_players` 是否有 unique key，重跑舊日期是否會造成新增玩家數改變。
- `log_game_daily_record_backup` 是否有 unique key 或可接受 duplicate。
- backup insert 與 delete 是否在 transaction 內，或是否有 rows count reconciliation。
- `app_bi` origin/main 新增的 4 commit 是否改變 `DailyGameDataSummary()` 查詢邏輯。
- upstream `log_reel` writer 的完整資料日定義與第三方平台 settlement 邊界。
- production issue / ticket / MR review context。
- 是否有可公開且不洩漏內部資訊的量化結果。

## Evidence 分級

`專案存在 / code-backed`：

- `game_job` job / service / mapper / config / commit diff。
- `app_bi` local snapshot 查詢端。
- `iwin_gameserver` upstream writer 線索。

`分析素材 / learning-only`：

- 對 delete + insert、staging / publish marker、reconciliation 的 Owner 改善建議。
- 面試回答中的改善策略。

`待確認`：

- Production 啟用狀態。
- 下游最新 remote 行為。
- 上游完整 writer flow。

## Step 5 結論

- 更新正式履歷 / 自傳：是，保守更新。
- 可寫：「參與 BI 批次報表資料流維護，協助處理遊戲每日彙總 projection 的資料日邊界、重跑一致性、新增玩家 / 留存與下游查詢正確性。」
- 不可寫：主導完整 BI pipeline、完整 game_job owner、負責上游 gameserver 到 app_bi 全鏈路、修復 production incident、改善 X%。
