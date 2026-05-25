# iwin game_job

本資料夾整理 `/Users/nick/Git/iwin/game_job` 的專案知識。

`game_job` 是 Java / Spring Boot / Quartz 的批次任務專案，主要負責 BI / 報表投影、遊戲資料日彙總、第三方遊戲紀錄備份、金幣流水 / 玩家行為 projection、分表建立、支付與玩家行為資料清洗，以及部分 Redis queue / task state 輔助能力。

它比 `app_bi` 更接近 production projection / batch correctness。`contribution-claim-consolidation.md` 已完成 project-level 收口，並於 2026-05-20 重新覆核：`daily-game-data-summary` 可保守列為「真實開發過」，Nick / `10gt12nc` 在每日遊戲資料彙總、備份 / 清理、PG / Antplay 時區修正、job 拆分、新增玩家 / 留存等 path 有直接 commit evidence；`third-party-record-mongo-backup` 可保守列為「局部真實開發過」，限 GSC Mongo backup job 分批查詢與 batch size 調整。`coin-flow-batch-projection`、`online-payment-data-cleaning`、`partition-table-creation` 已完成 Step 5，目前只作 code-backed 面試素材，不更新正式履歷 / 自傳。仍不得寫成主導完整 BI pipeline、完整 game_job owner、完整第三方紀錄備份 owner、完整金幣流水 owner、完整 payment reporting owner、完整 schema migration owner 或負責上游 gameserver 到 app_bi 全鏈路。

## 讀檔順序

1. [step1-candidate-flows.md](step1-candidate-flows.md)：Step 1 候選 flow 盤點。
2. [step2-flow-comparison.md](step2-flow-comparison.md)：Step 2 候選 flow 價值與風險比較。
3. [flows/daily-game-data-summary/flow.md](flows/daily-game-data-summary/flow.md)：每日遊戲資料彙總 Step 3 主報告。
4. [flows/daily-game-data-summary/career-interview.md](flows/daily-game-data-summary/career-interview.md)：該 flow 的保守面試 / 履歷素材。
5. [flows/third-party-record-mongo-backup/flow.md](flows/third-party-record-mongo-backup/flow.md)：第三方遊戲紀錄 Mongo 備份與清理 Step 3 主報告。
6. [flows/third-party-record-mongo-backup/career-interview.md](flows/third-party-record-mongo-backup/career-interview.md)：該 flow 的保守面試素材。
7. [flows/coin-flow-batch-projection/flow.md](flows/coin-flow-batch-projection/flow.md)：金幣流水清算 / 玩家行為投影 Step 3 主報告。
8. [flows/coin-flow-batch-projection/career-interview.md](flows/coin-flow-batch-projection/career-interview.md)：該 flow 的 Step 4 正式面試 case。
9. [flows/online-payment-data-cleaning/flow.md](flows/online-payment-data-cleaning/flow.md)：充值 / 提現資料清洗與每日經濟資料 Step 3 主報告。
10. [flows/online-payment-data-cleaning/career-interview.md](flows/online-payment-data-cleaning/career-interview.md)：該 flow 的 Step 4 正式面試 case。
11. [flows/partition-table-creation/flow.md](flows/partition-table-creation/flow.md)：每日 / 每月分表建立 Step 3 主報告。
12. [flows/partition-table-creation/career-interview.md](flows/partition-table-creation/career-interview.md)：該 flow 的 Step 4 正式面試 case。
13. [contribution-claim-consolidation.md](contribution-claim-consolidation.md)：project-level 履歷 / 面試 claim 收口。
14. 證據、技術決策、面試稿與 claim 邊界放 `flows/{flow-name}/materials/`。

## 目前狀態

| 文件 / flow | 狀態 | 說明 |
| --- | --- | --- |
| `step1-candidate-flows.md` | Step 1 | Level 1 掃描，已找出 Top candidate flows |
| `step2-flow-comparison.md` | Step 2 | 已比較候選 flow 價值 / 風險；選出 `daily-game-data-summary` 作為第一條 Step 3 flow |
| `flows/daily-game-data-summary/flow.md` | Step 5 | 已完成每日遊戲資料彙總 flow 學習包與 claim gate；可作 `game_job` project contribution consolidation evidence |
| `flows/daily-game-data-summary/career-interview.md` | Step 5 | 已轉成 Senior Backend 面試 case study，並補可用 / 不可誇大履歷邊界 |
| `flows/third-party-record-mongo-backup/flow.md` | Step 5 | 已完成 Mongo backup / delete / retention flow 學習包、面試 case 與 claim gate |
| `flows/third-party-record-mongo-backup/career-interview.md` | Step 5 | 已補 30 秒 / 3 分鐘 / STAR / Lead 追問；可保守寫局部 GSC backup 分批處理經驗 |
| `flows/coin-flow-batch-projection/flow.md` | Step 5 | 已完成金幣流水 / 玩家行為 projection 主學習包、正式面試 case 與 claim gate；目前 code-backed，不更新履歷 |
| `flows/coin-flow-batch-projection/career-interview.md` | Step 5 | 已補 30 秒 / 3 分鐘 / STAR / Lead 追問；只能作 code-backed 面試素材 |
| `flows/online-payment-data-cleaning/flow.md` | Step 5 | 已完成充值 / 提現資料清洗與每日經濟資料主學習包、正式面試 case 與 claim gate；目前 code-backed，不更新履歷 |
| `flows/online-payment-data-cleaning/career-interview.md` | Step 5 | 已補 30 秒 / 3 分鐘 / STAR / Lead 追問；只能作 code-backed 面試素材 |
| `flows/partition-table-creation/flow.md` | Step 5 | 已完成每日 / 每月分表建立主學習包、正式面試 case 與 claim gate；目前 code-backed，不更新履歷 |
| `flows/partition-table-creation/career-interview.md` | Step 5 | 已補 30 秒 / 3 分鐘 / STAR / Lead 追問；只能作 code-backed 面試素材 |
| `contribution-claim-consolidation.md` | 已完成 / 2026-05-20 已重新覆核 | 已收斂 project-level 可放履歷 / 可面試講 / 不可誇大三層 |

## 專案定位

已確認：

- Java 8 / Spring Boot 2.x / Quartz / MyBatis / Redis / MongoDB / MySQL。
- 核心入口包含 `config/application-quartz.yml`、`com.quartz.*JobQuartz`、`com.job.biTask.*`、`com.job.system.*`、`com.common.job.BiJobBase`。
- `QuartzService` 依設定載入 cron job，部分 job 使用 `@DisallowConcurrentExecution` 避免同一 Quartz job 重疊執行。
- `BiJobBase` 提供 task date、custom date、Redis task state、跨日 task finish 與清除資料等共用批次框架。

推測：

- `game_job` 多數不是交易 source of truth，而是從遊戲 log、payment order、Mongo collection 或分表資料產生 BI / 報表 / audit projection。
- 高價值點集中在「重跑是否安全、時區邊界、分批備份刪除、跨日資料窗口、Redis task state、報表投影一致性」。

待確認：

- Nick 是否參與過 `game_job` 其他 flow。
- 哪些其他 flow 有 Nick 本人 MR / ticket / commit / production issue evidence。
- 第三方投注紀錄、GSC / Antplay / PG upstream writer 與 app_bi 查詢端的完整鏈路。
- production 實際啟用哪些 cron job 與部署設定。

## 履歷邊界

目前可以說：

- 可用來分析 iwin 批次任務、BI projection、分表、報表資料清洗與第三方遊戲紀錄備份流程。
- `daily-game-data-summary` 可保守寫成 Nick 參與每日遊戲資料彙總 batch / BI projection 開發與維護，包含資料日 / 時區窗口、delete + insert 重跑、summary / retention、新增玩家與歷史資料備份 / 清理。
- `third-party-record-mongo-backup` 可保守寫成 Nick 參與 GSC 第三方遊戲紀錄 Mongo 備份 job 的分批查詢與 batch size 調整。
- `coin-flow-batch-projection` 目前只能作 code-backed 面試分析素材，不能寫成真實開發過。
- `online-payment-data-cleaning` 目前只能作 payment reporting projection 的 code-backed 面試分析素材，不能寫成真實開發過。
- 其他 `game_job` flow 仍需各自完成 claim gate，不能因 daily summary / GSC backup evidence 擴大成整個 `game_job` owner。

目前不能說：

- Nick 主導 `game_job`。
- Nick 負責完整 BI / game data pipeline。
- Nick 是第三方遊戲紀錄備份 owner。
- Nick 是金幣流水清算 owner。
- Nick 設計完整 Antplay / GSC / Oneapi retention policy。
- Nick 主導完整上游 gameserver -> game_job -> app_bi 報表鏈路。
- 寫任何未驗證的量化改善，例如報表正確率、查帳時間或效能改善 X%。

## 歷史下一步狀態

原本的下一步已完成：

```text
- 歷史下一步已完成：iwin_gameserver center-http-deposit-withdraw 已完成到 Step 5；目前沒有預設下一步，請以 senior-owner-playbook/01-senior-owner-flow-inventory.md 與 senior-owner-playbook/06-todo.md 為準。
```

原因：

- `game_job` contribution claim consolidation 已完成，2026-05-20 重新覆核後結論不變。
- 歷史下一步已完成：iwin_gameserver center-http-deposit-withdraw 已完成到 Step 5；目前沒有預設下一步，請以 senior-owner-playbook/01-senior-owner-flow-inventory.md 與 senior-owner-playbook/06-todo.md 為準。
- `game_api`、`payment`、`app_bi`、`bi_share` 的 contribution consolidation 也已收斂。
- 歷史下一步已完成：iwin_gameserver center-http-deposit-withdraw 已完成到 Step 5；目前沒有預設下一步，請以 senior-owner-playbook/01-senior-owner-flow-inventory.md 與 senior-owner-playbook/06-todo.md 為準。

## 履歷 claim 分層（2026-05-18 KB 對齊）

- 可放履歷：真實開發過。Nick / `10gt12nc` 有 `daily-game-data-summary` path-specific commits，可保守寫「參與每日遊戲資料彙總 batch / BI projection 開發與維護」。
- 可放履歷：局部真實開發過。Nick / `10gt12nc` 有 GSC backup 分批查詢與 batch size 調整 commits，可保守寫「參與 GSC 第三方遊戲紀錄 Mongo 備份 job 分批處理維護」；已由 `contribution-claim-consolidation.md` 收斂。
- 可面試講：code-backed / 實作過 + 分析過。可用 daily game data summary 說明 batch projection、delete-insert 重跑、一致性、時區分表、backup / cleanup、報表正確性與資料日邊界修正；可用 third-party Mongo backup 說明 copy-then-delete、idempotency、batch size 與 retention policy；可用 coin flow 說明 Redis checkpoint、多來源增量 projection、custom date replay、MySQL 累加 upsert 與 Mongo delete+insert 的一致性邊界；可用 online payment data cleaning 說明 payment order reporting projection、last_modify_time 資料日、Redis distinct uid、Mongo insert projection、MySQL economic day log delete+insert、downstream daily total 與 replay-safe 設計；可用 partition table 說明 table rollover、schema template、partial success、schema drift 與 fail-fast。coin flow、online payment data cleaning 與 partition table 目前不寫正式履歷。
- 不可誇大：不得寫成 Nick 主導完整 game_job BI projection、完整資料平台 owner、完整第三方紀錄備份 owner、金幣流水清算 owner、schema migration owner、負責上游 gameserver 到 app_bi 全鏈路或有未驗證量化改善。
