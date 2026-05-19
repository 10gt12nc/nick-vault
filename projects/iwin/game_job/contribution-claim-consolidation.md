# iwin game_job contribution claim consolidation

更新時間：2026-05-19
掃描等級：Level 2+ project-level claim consolidation
證據層級：部分真實開發過 + code-backed

## 結論

`game_job` 可以保守整理成 Nick 實際參與過的 Java batch / BI projection / Mongo retention 經驗，但只能限縮在已確認的 direct evidence：

1. `daily-game-data-summary`：可標示為真實開發過 + code-backed。Nick / `10gt12nc` 有 #247 每日遊戲資料彙總主體 commits，也有 PG / Antplay 時區窗口、job 拆分、新增玩家 / 留存、備份 / 清理相關 commits。
2. `third-party-record-mongo-backup`：可標示為局部真實開發過 + code-backed。Nick / `10gt12nc` 有 GSC Mongo backup 分批查詢與 batch size 調整 direct commits。
3. `coin-flow-batch-projection`、`online-payment-data-cleaning`、`partition-table-creation`：可作 code-backed 面試素材；目前不放正式履歷。

正式履歷可寫 `game_job` 的「參與 batch / projection / retention job 開發與維護」口徑，但不得寫成 Nick 主導完整 `game_job`、完整 BI pipeline、完整資料平台 owner、完整第三方紀錄備份 owner、完整金幣流水 / payment reporting owner、schema migration owner，或任何未驗證量化改善。

## 本次自動重讀

已重讀 KB：

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
- `projects/iwin/game_job/flows/daily-game-data-summary/materials/claim-boundary.md`
- `projects/iwin/game_job/flows/third-party-record-mongo-backup/flow.md`
- `projects/iwin/game_job/flows/third-party-record-mongo-backup/career-interview.md`
- `projects/iwin/game_job/flows/third-party-record-mongo-backup/materials/evidence.md`
- `projects/iwin/game_job/flows/third-party-record-mongo-backup/materials/claim-boundary.md`
- `projects/iwin/game_job/flows/coin-flow-batch-projection/flow.md`
- `projects/iwin/game_job/flows/coin-flow-batch-projection/career-interview.md`
- `projects/iwin/game_job/flows/coin-flow-batch-projection/materials/evidence.md`
- `projects/iwin/game_job/flows/coin-flow-batch-projection/materials/claim-boundary.md`
- `projects/iwin/game_job/flows/online-payment-data-cleaning/flow.md`
- `projects/iwin/game_job/flows/online-payment-data-cleaning/career-interview.md`
- `projects/iwin/game_job/flows/online-payment-data-cleaning/materials/evidence.md`
- `projects/iwin/game_job/flows/partition-table-creation/flow.md`
- `projects/iwin/game_job/flows/partition-table-creation/career-interview.md`
- `projects/iwin/game_job/flows/partition-table-creation/materials/evidence.md`

已重讀履歷 / 面試素材：

- `senior-owner-playbook/04-interview-casebook.md`
- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

## Source repo 狀態

`/Users/nick/Git/iwin/game_job`

- 已執行：`git fetch --all --prune`
- branch：`main`
- local HEAD：`23908f474efb5cfe5a3ce2bc780fb67a0860c4c2`
- `origin/main`：`23908f474efb5cfe5a3ce2bc780fb67a0860c4c2`
- ahead / behind：`0 / 0`
- 工作樹：乾淨
- 遠端分支：`origin/main`、`origin/k3s`、`origin/nick-DailyReport`、`origin/feature/gsc_record_backup`、`origin/antplay_new_bak_job`、`origin/antplay-pg-mongo`、`origin/feature/setBlacklistHset`、`origin/feature/RD-128`、`origin/feature/RD-71`、`origin/beta`、`origin/ci_test`、`origin/fix-ci-deploy`

本輪只 fetch remote refs，沒有 pull、merge、checkout、rebase 或修改公司 repo。

## 掃描範圍

已掃：

- repo-wide Nick / `10gt12nc` author log。
- Top 5 flow 的 `flow.md`、`career-interview.md`、`materials/evidence.md`、`materials/claim-boundary.md`。
- daily summary 重要 diff：`4174d3f`、`562f1e6`、`3a7fd8b`、`696acf7`、`d9edfa7`。
- GSC backup 重要 diff：`d11b1f4`、`bf92773`。
- 重要 commit 的 `git show --stat --summary`。
- branch contains：`4174d3f`、`d11b1f4`、`bf92773`。
- direct path author log：daily summary / GSC backup / coin flow / online payment cleaning / partition table creation 相關主 path。

未掃 / 不宣稱：

- 未 checkout 所有遠端分支做逐檔逐行比對。
- 未做 Level 3 每個 commit 完整 diff 鑑識。
- 未確認 production 實際 enable flag、cron 設定與部署 config。
- 未深掃 app_bi 最新 remote HEAD 的下游查詢端。
- 未深掃 upstream gameserver / third_games_api 的完整 source-of-truth flow。
- 未補 MR、ticket、incident、metric 或 performance before / after。

## repo-wide Nick / 10gt12nc commit 分類

### 可放履歷：真實開發過

`daily-game-data-summary`

代表 commits：

- `4174d3f`：新增每日遊戲資料彙總早期 job / service / mapper / quartz / config 主體，17 files changed，475 insertions。
- `562f1e6`：重整 `GameDailyJob`、`GameDailyServiceImpl`、`GameDailyDao.xml`，形成主要 daily summary path，9 files changed。
- `3a7fd8b`：拆分 PG / Antplay 與 Iwin job 排程，新增 `GameDailyIwinJob` 與 Quartz wrapper，15 files changed。
- `696acf7`：PG 對帳時區從 GMT+1 改成 GMT+8。
- `d9edfa7`：Antplay 遊戲數據彙總改 GMT+0，並調整排程。

可寫範圍：

- 參與每日遊戲資料彙總 batch / BI projection 開發與維護。
- 處理 `log_reel` 投注明細到 `log_game_daily_record` 的彙總。
- 參與資料日 / 時區窗口、delete + insert 重跑、新增玩家 / 留存、歷史資料備份 / 清理相關調整。

不可擴張：

- 不寫主導完整 BI pipeline。
- 不寫負責完整 game_job。
- 不寫負責上游 gameserver 到 app_bi 全鏈路。
- 不寫改善報表正確率、查帳時間或效能 X%。

### 可放履歷：局部真實開發過

`third-party-record-mongo-backup`

代表 commits：

- `d11b1f4`：GSC log / transaction backup job 改成分批查詢，新增 `query.limit(BATCH_SIZE)` 與每批 insert backup 後依 `_id` delete origin，6 files changed。
- `bf92773`：GSC log / transaction backup job batch size 從 1000 調整為 10000，2 files changed。

branch contains：

- `d11b1f4`：`origin/main`、`origin/k3s`。
- `bf92773`：`origin/main`、`origin/k3s`。

可寫範圍：

- 參與 GSC 第三方遊戲紀錄 Mongo 備份 job 的分批查詢與 batch size 調整。
- 可併入 `game_job` / batch / Mongo 大量資料處理履歷 bullet，不建議單獨放大成一整個專案成果。

不可擴張：

- 不寫主導完整第三方遊戲紀錄備份。
- 不寫 owner 完整 Antplay / GSC / Oneapi retention policy。
- 不寫已驗證 production enable。
- 不寫 storage 成本或查詢效率改善 X%。

## 可面試講：code-backed / 分析過

`coin-flow-batch-projection`

- 可講 Redis checkpoint、多來源增量 projection、custom date replay、MySQL 累加 upsert、Mongo delete + insert 與 reporting projection 的 money correctness 思維。
- 不可寫 Nick 真實開發或主導金幣流水清算。

`online-payment-data-cleaning`

- 可講 payment order reporting projection、`last_modify_time` 資料日、Redis distinct uid、Mongo insert projection、MySQL `economic_data_day_log` delete + insert、downstream daily total 與 replay-safe 設計。
- 不可寫 Nick 真實開發 payment reporting / reconciliation，也不可把它說成 provider callback 或 wallet source of truth。

`partition-table-creation`

- 可講 table rollover、SQL template、`CREATE TABLE IF NOT EXISTS`、多 channel DB partial success、schema drift 與 fail-fast。
- 不可寫 Nick 真實開發 schema migration platform 或 production rollout owner。

## 不可誇大清單

不可寫：

- Nick 主導 `game_job`。
- Nick 是完整 BI / data platform owner。
- Nick 主導完整第三方遊戲紀錄備份 / retention system。
- Nick 主導金幣流水清算、payment reporting、schema migration platform。
- Nick 負責上游 gameserver writer 到 app_bi 查詢端全鏈路。
- Nick 修復 production incident 或改善 X%，除非之後補 ticket / incident / metric。

## 正式履歷建議寫法

建議整合成一條，不要拆太多：

```text
參與 iwin Java batch / BI projection 與 Mongo retention job 開發維護，包含每日遊戲資料彙總、資料日 / 時區窗口、delete + insert 重跑、新增玩家 / 留存、歷史資料備份 / 清理，以及 GSC 第三方遊戲紀錄 Mongo 備份 job 的分批查詢與批次大小調整；不寫主導完整 BI pipeline 或資料平台 owner。
```

更短版本：

```text
參與遊戲資料彙總 batch / BI projection 與 GSC Mongo 備份分批處理維護，關注資料日、時區、重跑安全、批次大小與 retention 邊界。
```

## 面試定位

`game_job` 不適合包裝成「我主導整個資料平台」，但很適合當 Senior Backend 的 batch correctness case：

- 報表 projection 不是 source of truth，但會影響營運判讀。
- batch job 要處理資料日、時區、重跑、delete + insert 空窗與半成品。
- Mongo copy-then-delete retention job 不是 atomic transaction，要有 duplicate-safe、delete count、reconciliation 與 batch size trade-off。
- 支撐型 jobs 例如 partition table creation 可以講 reliability，不要當主要履歷成果。

## 已更新文件

- `projects/iwin/game_job/README.md`
- `projects/iwin/game_job/contribution-claim-consolidation.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/04-interview-casebook.md`
- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/06-todo.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

## 下一步建議

下一步回到 `game_api`，把已完成 Step 4 的第三順位 flow 做 Step 5，避免 `game_api` 卡在未完成代表 flows 而不能 project-level consolidation。

```text
iwin iwin_gameserver center-http-deposit-withdraw Step 4
```
