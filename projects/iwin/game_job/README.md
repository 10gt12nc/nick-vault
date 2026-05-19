# iwin game_job

本資料夾整理 `/Users/nick/Git/iwin/game_job` 的專案知識。

`game_job` 是 Java / Spring Boot / Quartz 的批次任務專案，主要負責 BI / 報表投影、遊戲資料日彙總、第三方遊戲紀錄備份、分表建立、支付與玩家行為資料清洗，以及部分 Redis queue / task state 輔助能力。

它比 `app_bi` 更接近 production projection / batch correctness。`daily-game-data-summary` 已完成 Step 5，Nick / `10gt12nc` 在每日遊戲資料彙總、備份 / 清理、PG / Antplay 時區修正、job 拆分、新增玩家 / 留存等 path 有直接 commit evidence，可保守列為「真實開發過」；仍不得寫成主導完整 BI pipeline、完整 game_job owner 或負責上游 gameserver 到 app_bi 全鏈路。

## 讀檔順序

1. [step1-candidate-flows.md](step1-candidate-flows.md)：Step 1 候選 flow 盤點。
2. [step2-flow-comparison.md](step2-flow-comparison.md)：Step 2 候選 flow 價值與風險比較。
3. [flows/daily-game-data-summary/flow.md](flows/daily-game-data-summary/flow.md)：每日遊戲資料彙總 Step 3 主報告。
4. [flows/daily-game-data-summary/career-interview.md](flows/daily-game-data-summary/career-interview.md)：該 flow 的保守面試 / 履歷素材。
5. [flows/third-party-record-mongo-backup/flow.md](flows/third-party-record-mongo-backup/flow.md)：第三方遊戲紀錄 Mongo 備份與清理 Step 3 主報告。
6. [flows/third-party-record-mongo-backup/career-interview.md](flows/third-party-record-mongo-backup/career-interview.md)：該 flow 的保守面試素材。
7. 證據、技術決策、面試稿與 claim 邊界放 `flows/{flow-name}/materials/`。

## 目前狀態

| 文件 / flow | 狀態 | 說明 |
| --- | --- | --- |
| `step1-candidate-flows.md` | Step 1 | Level 1 掃描，已找出 Top candidate flows |
| `step2-flow-comparison.md` | Step 2 | 已比較候選 flow 價值 / 風險；選出 `daily-game-data-summary` 作為第一條 Step 3 flow |
| `flows/daily-game-data-summary/flow.md` | Step 5 | 已完成每日遊戲資料彙總 flow 學習包與 claim gate；可保守更新履歷 / 自傳 |
| `flows/daily-game-data-summary/career-interview.md` | Step 5 | 已轉成 Senior Backend 面試 case study，並補可用 / 不可誇大履歷邊界 |
| `flows/third-party-record-mongo-backup/flow.md` | Step 3 | 已完成 Mongo backup / delete / retention flow 學習包；先作 code-backed 面試素材 |
| `flows/third-party-record-mongo-backup/career-interview.md` | Step 3 | 已補保守面試回答；不更新正式履歷，待 Step 5 claim gate |

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
- 其他 `game_job` flow 仍需各自完成 claim gate，不能因 daily summary evidence 擴大成整個 `game_job` owner。

目前不能說：

- Nick 主導 `game_job`。
- Nick 負責完整 BI / game data pipeline。
- Nick 是第三方遊戲紀錄備份 owner。
- Nick 主導完整上游 gameserver -> game_job -> app_bi 報表鏈路。
- 寫任何未驗證的量化改善，例如報表正確率、查帳時間或效能改善 X%。

## 下一步建議

只推薦一件事：

```text
iwin game_job third-party-record-mongo-backup Step 4
```

原因：

- `third-party-record-mongo-backup` Step 3 已完成，正常流程、主要 failure window、upstream writer 關係與 evidence 邊界已收斂。
- 下一步應把它轉成 Step 4 面試 case / decision notes，而不是直接升履歷。
- Step 5 才判斷 `10gt12nc` 的 GSC 分批查詢 commit 是否足以形成局部真實開發 claim。

## 履歷 claim 分層（2026-05-18 KB 對齊）

- 可放履歷：真實開發過。Nick / `10gt12nc` 有 `daily-game-data-summary` path-specific commits，可保守寫「參與每日遊戲資料彙總 batch / BI projection 開發與維護」。
- 可面試講：code-backed / 實作過 + 分析過。可用 daily game data summary 說明 batch projection、delete-insert 重跑、一致性、時區分表、backup / cleanup、報表正確性與資料日邊界修正。
- 不可誇大：不得寫成 Nick 主導完整 game_job BI projection、完整資料平台 owner、負責上游 gameserver 到 app_bi 全鏈路或有未驗證量化改善。
