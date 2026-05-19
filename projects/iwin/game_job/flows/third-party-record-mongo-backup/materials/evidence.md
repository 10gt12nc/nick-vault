# third-party-record-mongo-backup evidence

## 本次掃描範圍

任務：`iwin game_job third-party-record-mongo-backup Step 3` / Step 4 / Step 5 claim gate

掃描深度：Level 2。

Step 4 補掃：

- 重新 fetch `/Users/nick/Git/iwin/game_job` remote refs。
- 重新 fetch `/Users/nick/Git/iwin/third_games_api` remote refs。
- 補讀 `10gt12nc` 兩個 GSC commit 的 stat 與 diff：`d11b1f4`、`bf92773`。
- 補整理面試 case、decision framing、claim boundary。

Step 5 補掃：

- 再次 fetch `/Users/nick/Git/iwin/game_job` remote refs。
- 再次 fetch `/Users/nick/Git/iwin/third_games_api` remote refs。
- 確認 `d11b1f4`、`bf92773` branch contains：`main`、`origin/main`、`origin/k3s`。
- 補讀 `git show --name-status`、`git show --stat`、path-specific `10gt12nc` author log。
- 補判斷正式履歷 / 自傳可用範圍。

nick-vault：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/iwin/game_job/README.md`
- `projects/iwin/game_job/step1-candidate-flows.md`
- `projects/iwin/game_job/step2-flow-comparison.md`
- `projects/iwin/game_job/flows/daily-game-data-summary/*`

source repo：

- `/Users/nick/Git/iwin/game_job`
- `/Users/nick/Git/iwin/third_games_api`

未掃：

- app_bi / 客服查詢端是否查 backup collection。
- production k3s-deploy 實際 config。
- provider 投注 / 派彩 / rollback 完整交易語意。
- Mongo index / backup collection DDL。

## Source repo 遠端狀態

`/Users/nick/Git/iwin/game_job`

- 已執行：`git fetch --all --prune`；Step 4 / Step 5 已再次執行
- local branch：`main`
- local HEAD：`23908f474efb5cfe5a3ce2bc780fb67a0860c4c2`
- `origin/main`：`23908f474efb5cfe5a3ce2bc780fb67a0860c4c2`
- ahead / behind：`0 / 0`
- 另看 remote ref：`origin/k3s b5da7dbf55301965b408cc92ad2021632f4a8db4`
- 工作樹：乾淨，只讀

`/Users/nick/Git/iwin/third_games_api`

- 已執行：`git fetch --all --prune`；Step 4 / Step 5 已再次執行
- local branch：`beta`
- local HEAD：`4915ea5a5000d61eb36717203ea4c6afc45322fa`
- `origin/beta`：`4915ea5a5000d61eb36717203ea4c6afc45322fa`
- ahead / behind：`0 / 0`
- 另看 remote ref：`origin/k3s f1ccacc3afdbebcdd89e6fc3a6b672603c5ee19e`
- 工作樹：乾淨，只讀

## game_job 已讀 code path

Quartz / config：

- `config/application-quartz.yml`
- `src/main/java/com/quartz/QuartzService.java`
- `src/main/java/com/quartz/ThirdLogAntplayNewJobQuartz.java`
- `src/main/java/com/quartz/ThirdTransactionAntplayNewJobQuartz.java`
- `src/main/java/com/quartz/ThirdLogGscJobQuartz.java`
- `src/main/java/com/quartz/ThirdTransactionGscJobQuartz.java`

Job / framework：

- `src/main/java/com/common/job/BiJobBase.java`
- `src/main/java/com/common/database/MongoDynamicTemplate.java`
- `src/main/java/com/job/biTask/ThirdLogAntplayNewJob.java`
- `src/main/java/com/job/biTask/ThirdTransactionAntplayNewJob.java`
- `src/main/java/com/job/biTask/ThirdLogGscJob.java`
- `src/main/java/com/job/biTask/ThirdTransactionGscJob.java`

VO / enum / config：

- `src/main/java/com/pojo/vo/ThirdLogAntplayNewVO.java`
- `src/main/java/com/pojo/vo/ThirdTransactionAntplayNewVO.java`
- `src/main/java/com/pojo/vo/ThirdLogGscVO.java`
- `src/main/java/com/pojo/vo/ThirdTransactionGscVO.java`
- `src/main/java/com/common/enums/BiTaskEnums.java`
- `src/main/java/com/common/config/JobProperties.java`

Manual endpoint：

- `src/main/java/com/conllor/TestController.java`

## third_games_api 已讀 code path

- `src/main/java/com/slots/web/controller/AntplayController.java`
- `src/main/java/com/slots/web/controller/GscController.java`

已確認只做 writer 對照，未把這兩個 controller 的投注 / 派彩 / rollback 流程納入本 Step 3。

## 主要 code evidence

### Quartz 啟用

`config/application-quartz.yml`：

- `thirdLogAntplayNewJob: 0/5 * * * * ?`
- `thirdLogAntplayNewJobEnable: true`
- `thirdTransactionAntplayNewJob: 0/5 * * * * ?`
- `thirdTransactionAntplayNewJobEnable: true`
- `thirdLogGscJobEnable: false`
- `thirdTransactionGscJobEnable: false`

判斷：main config 顯示 Antplay new enabled、GSC disabled；production 實際啟用需看部署 config，不能直接宣稱 production。

### QuartzService 註冊

`QuartzService` 依 `JobProperties` enable flag 註冊：

- `ThirdLogAntplayNewJobQuartz`
- `ThirdTransactionAntplayNewJobQuartz`
- `ThirdLogGscJobQuartz`
- `ThirdTransactionGscJobQuartz`

### Quartz wrapper

wrapper 上有 `@DisallowConcurrentExecution`，並做：

- `setTaskName(BiTaskEnums.*.getCode())`
- `setTaskType(BITaskTypeEnums.DAILY)`
- `setTaskOverTime(600)`
- `runTask()`

判斷：單 Quartz job 有防同 scheduler overlap；distributed / manual overlap 待確認。

### BiJobBase

`runTask()`：

- `execBefore()`
- `checkStart()`
- Redis task status 設為 running
- `taskLogService.saveTaskLog`
- `execute()`
- exception 時 status = failed
- `end()`
- `taskLogService.updateTaskLog`
- `execAfter()`

判斷：它提供 job execution 狀態，不等於每筆 Mongo 文件完成 reconciliation。

### Backup jobs

四支 job 共同模式：

```text
targetDate = LocalDate.now().minusDays(14).atStartOfDay()
query createdTime < targetDate
limit batch size
mongo.insert(batch, backupCollection)
remove origin by _id in idList
remove backup createdTime < today - 30 days
```

Batch size：

- `ThirdLogAntplayNewJob`：1000
- `ThirdTransactionAntplayNewJob`：1000
- `ThirdLogGscJob`：10000
- `ThirdTransactionGscJob`：2500

### Upstream writer

`third_games_api`：

- `AntplayController` 寫 `third_log_antplay_new` 與 `third_transaction_antplay_new`，並放入 `createdTime`。
- `GscController` 寫 `third_log_gsc` 與 `third_transaction_gsc`，並放入 `createdTime`。

## Path-specific commit history

已讀 `git log --oneline --name-only` 與 `git show --stat --summary`。

| Commit | Date | Author | Subject | 判斷 |
| --- | --- | --- | --- | --- |
| `3ede707` | 2024-11-06 | Derek | `feat(#MongoDB): 對PG, Antplay collection資料進行備份, 備份資料只保留最新30日` | 舊 Oneapi / Antplay backup job 初版 |
| `69d20c5` | 2025-04-08 | Derek | `feat:新增antplay投注紀錄備份` | Antplay new log / transaction backup job 初版 |
| `5aa51d5` | 2025-04-10 | Derek | `feat:add cron job for gsc bet record backup in MongoDB` | GSC backup job 初版 |
| `d11b1f4` | 2025-05-05 | `10gt12nc` | `fix  gsc 改分批查詢` | Nick / `10gt12nc` 直接參與線索；GSC 改成分批查詢 |
| `bf92773` | 2025-05-05 | `10gt12nc` | `fix  gsc 改分批查詢 10000` | Nick / `10gt12nc` 直接參與線索；GSC batch size 調整 |
| `d877c58` | 2025-05-06 | Derek | `feat:antplay分批備份mongo,TestController API備份GSC` | Antplay new 改分批；GSC 加手動 API |
| `a6268cb` | 2025-05-07 | Derek | `fix: use the ISODate for mongoDB` | `createdTime` 型別 correctness 風險 |
| `b993395` | 2025-05-09 | arnold | `fix : gsc 批量执行改为2500` | GSC transaction batch size 再調整 |
| `c7352f2` | 2026-05-07 | arnold | `feat(quartz): 對齊 prod 啟用 33 支 cron job` | config 對齊線索；production 實際仍需部署確認 |

## Step 4 補讀 commit diff 摘要

`d11b1f4` by `10gt12nc`：

- `ThirdLogGscJob` 從一次 query 全部符合 `createdTime < targetDate` 的資料，改成 `while (true)` 分批查詢。
- 新增 `query.limit(BATCH_SIZE)`，初始 batch size 為 1000。
- 每批 insert backup 後，收集該批 VO 的 `id`，再用 `_id in idList` delete origin。
- `ThirdTransactionGscJob` 做同型修改。
- 這個 diff 的核心價值：降低一次查全量 / 一次 insert / 一次 remove 大集合的風險，讓 job 可分段處理。

`bf92773` by `10gt12nc`：

- `ThirdLogGscJob` batch size 從 1000 調成 10000。
- `ThirdTransactionGscJob` batch size 從 1000 調成 10000。
- 後續 `b993395` by arnold 又把 GSC transaction batch size 調成 2500。
- 判斷：batch size 是 production tuning 題，但本次沒有 metrics，不能寫量化改善。

## Step 5 claim gate

Branch contains：

- `d11b1f4`：`main`、`origin/main`、`origin/k3s`
- `bf92773`：`main`、`origin/main`、`origin/k3s`

`git show --name-status`：

- `d11b1f4` 修改：
  - `config/application-quartz.yml`
  - `src/main/java/com/job/biTask/ThirdLogGscJob.java`
  - `src/main/java/com/job/biTask/ThirdTransactionGscJob.java`
  - `src/main/java/com/pojo/vo/ThirdLogGscVO.java`
  - `src/main/java/com/pojo/vo/ThirdTransactionGscVO.java`
  - `src/main/resources/application-quartz.yml`
- `bf92773` 修改：
  - `src/main/java/com/job/biTask/ThirdLogGscJob.java`
  - `src/main/java/com/job/biTask/ThirdTransactionGscJob.java`

Path-specific `10gt12nc` author log：

- GSC backup path 直接相關只有 `d11b1f4`、`bf92773`。
- 同一 author 在 `game_job` 還有 daily summary / timezone / retention 類 commits，但那些已歸入 `daily-game-data-summary` Step 5，不混到本 flow claim。

Step 5 判斷：

- 可升級為「局部真實開發過 + code-backed」。
- 可寫「參與 GSC 第三方遊戲紀錄 Mongo 備份 job 的分批查詢與批次大小調整」。
- 不可寫「主導完整第三方遊戲紀錄備份系統」。
- 不可寫「負責完整 Antplay / GSC retention policy」。
- 不可寫任何 storage / 查詢效率量化改善。

## 已確認

- `game_job` 有第三方 Mongo retention job。
- Antplay new 與 GSC 都採用「查舊資料 -> insert backup -> delete origin -> delete old backup」。
- retention 門檻為 active 14 天、backup 30 天。
- `third_games_api` 確實寫入本 flow 處理的 active collections。
- `10gt12nc` 有 GSC 分批查詢與 batch size 調整 commit。

## 推測

- active collection 主要支援近期查詢 / troubleshooting，backup collection 支援較舊 audit 查詢。
- GSC disabled in main 可能與部署 config / k3s branch 有關。
- backup duplicate 可能取決於 insert 是否保留 `_id` 與 backup collection unique index。

## 待確認

- backup collection 是否保留原 `_id`，以及是否有 unique index。
- production 實際 enable flag 與部署 config。
- app_bi / 後台是否查 backup collection。
- 多 instance 是否會併發跑相同 job。
- delete count 小於 batch size 時是否有告警。
- retention policy 是否符合 provider dispute / audit 保留需求。

## 履歷判斷

本 Step 5 可保守更新正式履歷。

可寫：

- 「參與 GSC 第三方遊戲紀錄 Mongo 備份 job 的分批查詢與批次大小調整。」
- 建議併入 `game_job` / 批次處理 / Mongo 大量資料處理 bullet，不單獨放大。

不能寫：

- Nick 主導第三方遊戲紀錄備份。
- Nick owner Antplay / GSC retention policy。
- Nick 改善 storage 或查詢效率 X%。

## Step 5 產出

- `claim-boundary.md` 已升級為 Step 5，標示局部真實開發過。
- `career-interview.md` 已補可放履歷 / 不可誇大。
- `README.md`、Step 文件、inventory、todo 已同步。
- `05-resume-master-zh.md`、`08-application-autobiography-zh.md` 已保守同步。

## Step 4 產出

- `career-interview.md` 已轉成 Step 4 面試 case。
- `materials/interview.md` 已補 30 秒 / 3 分鐘 / STAR / Q&A。
- `materials/decision-notes.md` 已補 batch size trade-off 與 owner decision。
- `materials/claim-boundary.md` 已標示仍需 Step 5 claim gate。
