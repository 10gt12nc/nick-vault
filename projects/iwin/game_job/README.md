# iwin game_job

本資料夾整理 `/Users/nick/Git/iwin/game_job` 的專案知識。

`game_job` 是 Java / Spring Boot / Quartz 的批次任務專案，主要負責 BI / 報表投影、遊戲資料日彙總、第三方遊戲紀錄備份、分表建立、支付與玩家行為資料清洗，以及部分 Redis queue / task state 輔助能力。

它比 `app_bi` 更接近 production projection / batch correctness，但目前仍未確認 Nick 本人 MR / ticket / commit / production issue；所有內容先標為 `專案存在 / code-backed` 或 `分析素材 / learning-only`，不可直接寫成 Nick 主導成果。

## 讀檔順序

1. [step1-candidate-flows.md](step1-candidate-flows.md)：Step 1 候選 flow 盤點。
2. [step2-flow-comparison.md](step2-flow-comparison.md)：Step 2 候選 flow 價值與風險比較。
3. 未來若選定單條 flow，再讀 `flows/{flow-name}/flow.md`。
4. 單條 flow 的保守面試 / 履歷素材放 `flows/{flow-name}/career-interview.md`。
5. 證據、技術決策、面試稿與 claim 邊界放 `flows/{flow-name}/materials/`。

## 目前狀態

| 文件 / flow | 狀態 | 說明 |
| --- | --- | --- |
| `step1-candidate-flows.md` | Step 1 | Level 1 掃描，已找出 Top candidate flows |
| `step2-flow-comparison.md` | Step 2 | 已比較候選 flow 價值 / 風險；尚未建立單條 flow folder |

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

- Nick 實際參與過哪些 `game_job` 功能。
- 哪些 flow 有 Nick 本人 MR / ticket / commit / production issue evidence。
- 第三方投注紀錄、GSC / Antplay / PG upstream writer 與 app_bi 查詢端的完整鏈路。
- production 實際啟用哪些 cron job 與部署設定。

## 履歷邊界

目前只能說：

- 可用來分析 iwin 批次任務、BI projection、分表、報表資料清洗與第三方遊戲紀錄備份流程。
- 可作為面試時說明「我如何檢查 batch correctness / projection consistency / retry replay boundary」的分析素材。

目前不能說：

- Nick 主導 `game_job`。
- Nick 負責完整 BI / game data pipeline。
- Nick 是第三方遊戲紀錄備份或每日資料彙總的 owner。
- 單靠 Step 1 把 batch / projection 題目寫進正式履歷。

## 下一步建議

只推薦一件事：

```text
game_job daily-game-data-summary Step 3
```

原因：

- `game_job` Step 1 / Step 2 已完成。
- `daily-game-data-summary` 已在 Step 2 被選為第一條最值得深挖的 flow。
- 下一步才建立單條 flow folder；仍不更新正式履歷 / 自傳。
