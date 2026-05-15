# daily-game-data-summary Career / Interview Notes

更新時間：2026-05-15
Step：4
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 使用邊界

這份文件是 Step 4 的面試 case study。它把 Step 3 的 flow 分析轉成 Senior Backend 面試可講的版本，但仍不是正式履歷成果。

沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認前，不能說「我主導」、「我設計」、「我修正」或「我負責完整資料管線」。可以說的是：這是一個 code-backed 的 batch projection 分析案例，我會用它說明自己如何檢查報表批次的 correctness。

## 30 秒摘要

我分析過一條遊戲每日資料彙總批次：它從投注 log 日分表彙總玩家投注、贏分、新增玩家與留存，寫到 BI 查詢用 projection。這類 flow 的難點不是 SQL group by，而是資料日與時區窗口、delete + insert 重跑空窗、`new_players` 這種跨日累積狀態，以及下游報表是否可能讀到半成品。

證據層級：`專案存在 / code-backed`；「我分析過」屬 `分析素材 / learning-only`。

## 3 分鐘版本

這條 flow 分成兩組 job：PG / Antplay 一組，Iwin 一組。PG / Antplay 因為平台資料日與時區定義不同，會查當日與前一日的 `log_reel` 分表，再用 `platform:uid` 合併投注次數、投注額與贏分；Iwin 則用自己的資料日跑彙總，並額外做舊資料 backup / cleanup。

正常流程是：先刪除指定日期與平台的 `log_game_daily_record`，再從 `log_reel` 寫入 type=0 personal data，接著從 type=0 彙總 type=1 summary，最後計算新增玩家與留存。下游 `app_bi` 查 type=1，透過 `sub_type` pivot 成報表欄位。

Senior 角度我會看四個點。第一，delete + insert 雖然方便重跑，但刪除後到完成前會有空窗，下游如果沒有完成標記可能讀到不完整資料。第二，新增玩家表是跨日累積狀態，不像主 projection 可以單日刪掉重建，重跑舊日期可能改變結果。第三，PG / Antplay 的時區窗口在 history 裡有修正紀錄，代表資料日邊界是 production correctness 風險。第四，backup insert 後 delete 若沒有 transaction、unique key 或 rows count reconciliation，失敗時可能 duplicate 或遺失資料。

我不會把它包裝成交易主鏈路。這是 BI projection flow，source of truth 應回到 `log_reel` 與上游遊戲 / 第三方 settlement。

證據層級：flow 結構、job / mapper / commit history 為 `專案存在 / code-backed`；改善建議為 `分析素材 / learning-only`。

## 面試官可能追問

### Q1：這條批次的 source of truth 是什麼？

保守回答：

> 對這條 flow 來說，主要輸入是 `slots_logv1.log_reel_{yyyy_m_d}` 這類投注明細日分表；`log_game_daily_record` 是 BI projection。報表異常時我不會直接相信 projection，而會回到 source log 比對 count、投注額、贏分與資料日窗口。

### Q2：delete + insert 有什麼問題？

保守回答：

> 它讓單日補跑很簡單，但會產生刪除後重建前的空窗。如果 type=0 寫完但 type=1 summary 沒完成，下游只查 type=1 可能看到不完整資料。更穩的設計會用 staging table、publish marker 或完成狀態，讓 BI 只讀已完成資料日。

### Q3：重跑是不是 idempotent？

保守回答：

> 主 projection 對同一天、同平台大致可以透過 delete + insert 做到可重跑，但我不會直接說整條 flow idempotent。`log_game_daily_record_new_players` 與 backup 表是累積狀態，重跑舊日期時可能受既有狀態影響，需要 unique key、清理策略或 reconciliation 才能說穩。

### Q4：新增玩家為什麼比 summary 更難？

保守回答：

> Summary 是同日資料聚合，刪掉重算就能回到同一批 input。新增玩家要判斷某個 uid 是否過去已出現過，依賴跨日狀態。如果先跑過未來日期，再回頭補跑舊日期，輔助表可能已經包含該 uid，結果就不一定可重現。

### Q5：如果報表今天金額少一半，你會怎麼查？

保守回答：

> 我會先切層：job 是否成功、source `log_reel` count / sum 是否正常、type=0 是否完整、type=1 是否由同一批 type=0 推導、app_bi query 是否有 platform / date filter 或金額縮放問題。若剛好落在 PG / Antplay 時區邊界，會抽邊界時間點比對資料日歸屬。

### Q6：PG / Antplay 時區 bug 怎麼避免再發？

保守回答：

> 我會把平台資料日定義集中成 policy，對每個平台建立邊界案例：起點、終點、前一毫秒、後一毫秒。Job log 也要記錄實際查詢分表與 start / end time，讓對帳時可以快速知道批次到底查了哪個窗口。

### Q7：backup / cleanup 怎麼避免資料遺失？

保守回答：

> 我會要求 copy 前 source count、copy 後 backup count、delete 後 remaining count，且只有 count match 才 delete。若沒有 transaction，就至少要有 job_run_id、unique key 或 reconciliation report，避免重跑造成 duplicate backup 或 delete 掉未備份資料。

### Q8：這是你主導的嗎？

保守回答：

> 目前我不能說這是我主導或實作的成果。我可以說我讀過這條 flow，並把它整理成 batch projection correctness 的分析案例；如果要寫成我的專案成果，需要再補我本人 MR、ticket、commit 或 production issue evidence。

## 可以展現的 Senior 能力

| 能力 | 可講內容 | 證據層級 |
| --- | --- | --- |
| Batch correctness | 從 source log、projection、summary、下游查詢逐層驗證 | 分析素材 / learning-only |
| Timezone boundary | PG / Antplay 查詢窗口跨分表，且 history 有時區修正 | 專案存在 / code-backed |
| Re-run safety | delete + insert 可補跑，但需注意下游半成品與累積狀態 | 分析素材 / learning-only |
| State modeling | `new_players` 是跨日累積狀態，不是單日 projection | 專案存在 / code-backed |
| Reconciliation | backup + delete 需要 rows count、unique key 或完成標記 | 分析素材 / learning-only |
| Claim discipline | 不把 code-backed flow 誇大成 Nick 主導成果 | 待確認 / claim-boundary |

## 對應履歷 Bullet

目前不建議寫入正式履歷。

若未來補到 Nick 本人參與 evidence，可考慮保守候選句：

> 參與 BI 批次報表資料流維護，協助檢查遊戲每日彙總 projection 的資料日邊界、重跑一致性與下游查詢正確性。

目前證據層級：`待確認`。未補本人 evidence 前不可使用。

## 不能誇大的地方

- 不能說 Nick 主導每日遊戲資料彙總。
- 不能說 Nick 設計 game_job BI projection 架構。
- 不能說 Nick 修正 PG / Antplay 時區問題。
- 不能說 Nick 負責 app_bi 報表或 upstream gameserver 全鏈路。
- 不能說已確認 production enable flag、正式 cron 或完整上游 writer。
