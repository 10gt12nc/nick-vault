# Evidence: partition-table-creation

## 掃描結論

掃描日期：2026-05-19

掃描等級：Level 2。

證據層級：專案存在 / code-backed；目前沒有 Nick / `10gt12nc` direct path evidence 可標成真實開發過。

## KB / vault 重讀

已重讀：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/iwin/game_job/README.md`
- `projects/iwin/game_job/step1-candidate-flows.md`
- `projects/iwin/game_job/step2-flow-comparison.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/06-todo.md`

## Source repo 狀態

Repo：`/Users/nick/Git/iwin/game_job`

已執行：

- `git fetch --all --prune`
- `git status --short --branch`
- `git rev-parse HEAD`
- `git rev-parse origin/main`
- `git rev-list --left-right --count HEAD...origin/main`

狀態：

- 分支：`main`
- local HEAD：`23908f474efb5cfe5a3ce2bc780fb67a0860c4c2`
- `origin/main` HEAD：`23908f474efb5cfe5a3ce2bc780fb67a0860c4c2`
- ahead / behind：`0 / 0`
- 工作區：乾淨

未做：

- 未 pull / checkout / merge / rebase。
- 未修改公司 repo。
- 未掃 production deployment 實際 enable 狀態。
- 未掃 writer repo 是否依賴這些分表。

## 已讀 code path

主要 path：

- `config/application-quartz.yml`
- `src/main/resources/application-quartz.yml`
- `src/main/java/com/quartz/QuartzService.java`
- `src/main/java/com/quartz/CreateTableJobQuartz.java`
- `src/main/java/com/quartz/JobNameUtil.java`
- `src/main/java/com/common/config/JobProperties.java`
- `src/main/java/com/common/config/InitTableConfig.java`
- `src/main/java/com/common/enums/BiTaskEnums.java`
- `src/main/java/com/job/system/CreateTableJob.java`
- `src/main/java/com/data/dao/bilog/ChannelDao.java`
- `src/main/resources/mapper/bilog/Channel.xml`
- `src/main/java/com/data/dao/logone/LogLoginDao.java`
- `src/main/resources/mapper/logone/LogLoginDao.xml`
- `src/main/resources/initBiTable/*.sql`
- `src/main/resources/initGameTable/*.sql`

## 已確認 evidence

- `QuartzService` 在 `createTableEnable=true` 時註冊 `CreateTableJobQuartz`。
- main config 中 `createTable: 0 30 0 * * ?`，`createTableEnable=false`。
- `origin/k3s` 中 `createTable: 0 0 2 * * ?`，`createTableEnable=false`。
- `CreateTableJobQuartz` 使用 `@DisallowConcurrentExecution`，設定 `BiTaskEnums.CREATE_TABLE`、`BITaskTypeEnums.DAILY`、`taskOverTime=5*60` 後呼叫 `runTask()`。
- `CreateTableJob` 載入 `classpath:/initBiTable/*.sql`，對固定 `bi_log` 建表。
- `CreateTableJob` 載入 `classpath:/initGameTable/*.sql`，查 active channel list 後對每個 channel DB 建表。
- `InitTableConfig` 從檔名解析 `originTableName` 與 `partitionFormat`。
- `partitionFormat=day` 時建立下一天日表；`partitionFormat=month` 時建立下一月月表。
- SQL template 使用 `${tableName}` placeholder，執行前替換為 `dbName.tableName`。
- MyBatis mapper 以 `${createTableSql}` 執行 classpath SQL template。
- SQL template 大多使用 `CREATE TABLE IF NOT EXISTS`。

## SQL template 範圍

BI templates：

- `collect_mail-month`
- `gold_daily_rank-month`
- `ip_login_rank-month`
- `partner_transid-month`
- `payment_diamond-month`
- `payment_order-month`
- `player_bet-month`
- `player_bet_demo-mouth`
- `user_behaviour-month`
- `user_game_behaviour-month`
- `vip_bet-month`

Game templates：

- `log_credit_card-month`
- `log_currency-day`
- `log_daily_recharge-month`
- `log_daletou_detail-month`
- `log_day_task_detail-month`
- `log_enter_x_game-month`
- `log_event-month`
- `log_every_recharge-month`
- `log_first_recharge-month`
- `log_gold_cow_detail-month`
- `log_jackpot-day`
- `log_login-month`
- `log_logout-month`
- `log_player_control_game-day`
- `log_quit_x_game-month`
- `log_recharge_toplist-month`
- `log_red_rain-month`
- `log_reel-day`
- `log_rp_control_game-day`
- `log_share_money-month`
- `log_spin_bet-day`
- `log_taxt-day`
- `log_validbet_toplist-month`
- `log_vip_award-month`

## Commit / history 掃描

已讀 path-specific history：

- `CreateTableJob.java`
- `CreateTableJobQuartz.java`
- `InitTableConfig.java`
- `initBiTable/`
- `initGameTable/`
- `Channel.xml`
- `LogLoginDao.xml`

觀察：

- direct path history 主要是 `c8a3fde feat(#first): first commit` 與 `42ae838 feat(k3s): 升 Java 21 + Spring Boot 3.2,改走容器化部署`。
- `42ae838` 只把 `CreateTableJobQuartz` 的 `javax.annotation.Resource` 改成 `jakarta.annotation.Resource`。
- `--author='10gt12nc|Nick|nick'` 在 direct path 沒有命中。
- 目前不可把此 flow 標為 Nick 真實開發過。

## 已確認風險

- main 與 `origin/k3s` 都是 `createTableEnable=false`；production 實際是否啟用未確認。
- `player_bet_demo-mouth.sql` 的 `mouth` 不會被 `InitTableConfig` 當成 `month`，可能建立 `player_bet_demo` 而非月表。
- `InitTableConfig` 只處理 `day` / `month`，未知格式不會 fail fast。
- `checkAndCreateTable()` catch exception 後只記 log，單表失敗不一定讓整個 task fail。
- 對多 channel DB 建表可能 partial success，缺少 expected / created / existed / failed summary。
- `CREATE TABLE IF NOT EXISTS` 不會修正已存在分表的 schema drift。
- job 只建立下一天 / 下一月；若過去某期缺表，沒有看到補建指定日期 / 月份的能力。
- `InitBiTableConfigs` / `initGameTableConfigs` 欄位沒有被賦值，實際每輪仍會重新 load templates。

## 待確認

- production 是否另有外部 scheduler 或 deployment override 啟用 create table job。
- writer / downstream job 對缺表時的錯誤處理。
- schema 變更如何套到既有分表。
- 是否有手動補表 SOP。
- 是否有 Nick 本人確認、MR、ticket、production issue 可補強此 flow。

## Step 4 更新紀錄

更新日期：2026-05-19

本次已重讀：

- 本 flow `flow.md`
- `career-interview.md`
- `materials/interview.md`
- `materials/claim-boundary.md`
- `materials/decision-notes.md`
- 本 evidence 文件
- `projects/iwin/game_job/README.md`
- `projects/iwin/game_job/step1-candidate-flows.md`
- `projects/iwin/game_job/step2-flow-comparison.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/06-todo.md`

本次更新：

- 將 `career-interview.md` 從 Step 3 初稿整理成 Step 4 正式面試 case。
- 補齊 STAR、3 分鐘講法、面試官追問與 owner decision framing。
- 同步 `claim-boundary.md`：目前仍不更新正式履歷 / 自傳。
- 同步 `decision-notes.md`：Step 4 的主軸是 table rollover reliability，不是 schema migration platform。

Source repo 狀態沿用本次 Step 4 開工前確認：

- 已 fetch：是。
- local HEAD：`23908f474efb5cfe5a3ce2bc780fb67a0860c4c2`
- `origin/main` HEAD：`23908f474efb5cfe5a3ce2bc780fb67a0860c4c2`
- ahead / behind：`0 / 0`
- 公司 repo 工作區：乾淨。

本次沒有新增 Nick direct evidence，沒有更新 `senior-owner-playbook/05-resume-master-zh.md` 或 `08-application-autobiography-zh.md`。

## Step 5 claim gate 更新紀錄

更新日期：2026-05-19

本次掃描等級：Level 2 claim gate。目的在確認履歷 / 自傳是否可更新，不是 Level 3 逐檔逐行全 repo 鑑識。

本次已重讀：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- 本 flow `flow.md`
- `career-interview.md`
- `materials/evidence.md`
- `materials/claim-boundary.md`
- `projects/iwin/game_job/README.md`
- `senior-owner-playbook/04-interview-casebook.md`
- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/06-todo.md`

Source repo 狀態：

- Repo：`/Users/nick/Git/iwin/game_job`
- 已 fetch：是，`git fetch --all --prune`
- 分支：`main`
- local HEAD：`23908f474efb5cfe5a3ce2bc780fb67a0860c4c2`
- `origin/main` HEAD：`23908f474efb5cfe5a3ce2bc780fb67a0860c4c2`
- ahead / behind：`0 / 0`
- 工作區：乾淨

Step 5 追加檢查：

- path-specific log：
  - `CreateTableJob.java`
  - `CreateTableJobQuartz.java`
  - `InitTableConfig.java`
  - `initBiTable/`
  - `initGameTable/`
  - `Channel.xml`
  - `LogLoginDao.xml`
- path-specific author search：`--author='10gt12nc|Nick|nick'`
- keyword history：`createTable`、`CreateTable`、`分表`、`建表`、`partition`、`table`
- keyword + author search：`--author='10gt12nc|Nick|nick'` 搭配上述 keyword
- `origin/k3s` 上 `c7352f2 feat(quartz): 對齊 prod 啟用 33 支 cron job`

Step 5 觀察：

- direct path history 仍只有 Arnold 的 `c8a3fde feat(#first): first commit` 與 `42ae838 feat(k3s): 升 Java 21 + Spring Boot 3.2,改走容器化部署`。
- direct path author search 沒有 Nick / `10gt12nc` 命中。
- keyword history 有 Arnold 的 `c7352f2 feat(quartz): 對齊 prod 啟用 33 支 cron job`、`0b0bace fix(quartz): 還原 JDBC 持久化`、`0a1b4be test: Phase D testcontainers MySQL integration tests + regression net`，但不是 Nick evidence。
- `c7352f2` 位於 `origin/k3s`，其 prod cron 對齊說明列出 `createTable` 維持 disabled；`src/main/resources/application-quartz.yml` 中 `createTable: 0 0 2 * * ?`、`createTableEnable: false`。
- 目前沒有本人確認、MR、ticket、production issue 或 direct commit 可把本 flow 升級為真實開發過。

Step 5 結論：

- 不更新正式履歷 / 自傳。
- 可加入 `senior-owner-playbook/04-interview-casebook.md` 作為 code-backed 面試案例。
- 本 flow 收斂為 Step 5：code-backed / 分析過，不可寫成 Nick 實際開發或主導。
