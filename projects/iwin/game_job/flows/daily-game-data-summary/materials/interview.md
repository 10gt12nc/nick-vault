# daily-game-data-summary Interview Drill

更新時間：2026-05-15
Step：4
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## Case 定位

這是一個 BI batch projection case。主軸不是「我做了一個報表」，而是「我如何拆解一條報表批次的 correctness 風險」。

一句話故事：

> 原始投注 log 不能每次都讓 BI 即時掃，所以 `game_job` 每天把 `log_reel` 日分表彙總成 `log_game_daily_record` projection；難點在資料日、時區、重跑、留存狀態與下游半成品。

使用前提：

- 可作 Senior Backend 面試分析案例。
- 不可作 Nick 正式履歷成果。
- 若被問是否本人主導，要明確回答「目前沒有 evidence，不能這樣 claim」。

## 30 秒版本

我看過一條遊戲每日資料彙總批次。它把投注 log 日分表彙總成 BI 查詢 projection，包含玩家數、投注次數、投注額、輸贏、新增玩家與留存。這類 flow 的核心風險不是 SQL 寫法，而是跨平台資料日與時區窗口、delete + insert 重跑空窗、`new_players` 跨日累積狀態，以及下游報表是否可能讀到半成品。

## 3 分鐘版本

這條 flow 有 PG / Antplay 和 Iwin 兩組 job。PG / Antplay 因為平台資料日不是單純自然日，會查當日與前一日的 `log_reel` 分表，再用 `platform:uid` 合併投注次數、投注額和贏分。Iwin 則跑自己的資料日，並額外處理 14 天前 personal data、180 天前 summary data 的 backup / cleanup。

主流程是先刪除指定日期與平台的 `log_game_daily_record`，再寫入 type=0 personal data，接著從 type=0 聚合 type=1 summary，最後計算新增玩家與留存。下游 `app_bi` 查 type=1，依 `sub_type` pivot 成後台欄位。

我會從幾個角度評估它。第一，主 projection 可重跑，但 delete + insert 有空窗，如果 job 半途失敗，下游可能讀到空值或半成品。第二，新增玩家表是累積狀態，不像主表能單日刪掉重算，重跑舊日期可能不完全 idempotent。第三，PG / Antplay path history 有時區修正，代表資料日窗口是實際 correctness 風險。第四，backup + delete 如果沒有 transaction 或 rows count reconciliation，可能有 duplicate backup 或資料遺失風險。

如果要改善，我會先補 completion marker 與每步 rows count，讓 BI 只讀完成資料日；再把平台 data day policy 集中化並補邊界測試；最後針對 `new_players` 與 backup 流程補 unique key、重跑策略或 reconciliation。

## 面試官追問與回答

### 1. 這條 flow 的 source of truth 是哪裡？

回答：

> 對這條 projection 來說，`log_reel` 日分表是主要輸入；`log_game_daily_record` 是 BI projection。報表錯時要回 source log 驗證 count、sum、資料日窗口，而不是直接把 projection 當交易真相。

追問時可補：

- 上游 writer 只做線索掃描，未完整深掃。
- 第三方 settlement 與遊戲 runtime 才可能是更上游的事實來源。

### 2. 如果 job 半途失敗，下游會怎樣？

回答：

> 目前主表是先 delete 再 insert。如果刪掉後查 source 失敗，當日 projection 可能消失；如果 type=0 寫完但 type=1 失敗，下游只查 summary 時可能看到缺資料。比較穩的做法是 staging / publish marker，或至少 BI 只讀有完成狀態的資料日。

### 3. 你怎麼驗證 summary 是否正確？

回答：

> 我會做三層對帳：source `log_reel` 依同樣 time window 算 count / spin / win；type=0 personal data 確認 uid 維度聚合是否一致；type=1 summary 再確認是否完全由同一批 type=0 推導。最後看 app_bi 顯示時的金額縮放與 platform filter。

### 4. 為什麼新增玩家是風險？

回答：

> 因為新增玩家不是單日聚合，而是跨日狀態。它要判斷 uid 是否曾在該 platform 出現過。如果已經跑過未來日期，再補跑舊日期，輔助表可能已經前進，重跑結果就不一定可重現。

### 5. 你會怎麼設計重跑策略？

回答：

> 主表可以保留 date + platform delete / rebuild；但 `new_players` 要定義重跑窗口，例如只能補跑最近 N 天，或重跑某天時同步清理該平台該日之後的 new player state。Backup 則要有 unique key 或 job_run_id，避免重跑 duplicate。

### 6. 時區問題怎麼測？

回答：

> 我會把每個 platform 的 business day policy 抽出來，測起點、終點、前一毫秒、後一毫秒，並確認跨分表時前一日與當日資料都有被讀到。Job log 也要記錄實際 queryStartTime / queryEndTime 和 source table。

### 7. 如果你只能改一件事，先改什麼？

回答：

> 我會先加 completion marker 和 rows count。這能立刻降低兩個風險：下游讀半成品，以及 production 報表異常時不知道是哪一步少資料。沒有觀測，後面的 transaction 或架構調整都很難驗證。

### 8. 這可以放履歷嗎？

回答：

> 目前不建議。它是很好的分析案例，但沒有 Nick 本人 MR、ticket、commit 或 production issue evidence 前，只能當 code-backed learning case。要放履歷，至少要補到本人參與範圍與可驗證結果。

## 可以展現的 Senior 能力

### Batch correctness

你不是只看 job 有沒有跑，而是拆 source、projection、summary、consumer 四層。

可說：

> 我會先定義每一層的對帳指標，而不是只看最後報表有沒有數字。

證據層級：`分析素材 / learning-only`。

### Idempotency / replay boundary

你能區分「主 projection 可重建」和「輔助累積狀態不可直接重建」。

可說：

> 我不會因為主表有 delete + insert 就說整條 flow idempotent，因為 new player 和 backup 是不同 state model。

證據層級：`專案存在 / code-backed` + `分析素材 / learning-only`。

### Timezone correctness

你能把時區問題轉成可測試的 platform data day policy。

可說：

> 時區不是註解問題，是資料日歸屬問題；我會測窗口邊界與跨分表查詢。

證據層級：`專案存在 / code-backed`。

### Observability

你能指出缺少哪些 row count、完成狀態、查詢窗口 log。

可說：

> 報表批次最怕錯了不知道錯在哪一步，所以我會先補每步 rows count 與完成標記。

證據層級：`分析素材 / learning-only`。

## 白板口述

```text
log_reel_{day}
  -> game_job daily job
  -> log_game_daily_record type=0 personal_data
  -> log_game_daily_record type=1 summary / retention
  -> app_bi dashboard / Excel

side state:
  -> log_game_daily_record_new_players
  -> log_game_daily_record_backup
```

圖上要標：

- PG / Antplay time window。
- delete + insert gap。
- type=0 -> type=1 dependency。
- `new_players` 是 cumulative state。
- BI reads type=1 only。

## 面試紅線

不要說：

- 我修了 #384 / #403。
- 我主導這條 pipeline。
- 我設計每日遊戲資料彙總。
- 我保證 production cron / enable flag。
- 我擁有 app_bi、game_job、upstream gameserver 全鏈路。

可以說：

- 我分析過這條 code-backed flow。
- 我會用它說明 batch projection correctness 的檢查方式。
- 我看到主要風險在 time window、re-run boundary、累積狀態、backup cleanup 與下游讀取一致性。

## 保守履歷候選句

目前不使用。若未來補到 Nick 本人 evidence，可候選：

> 參與 BI 批次報表資料流維護，協助檢查遊戲每日彙總 projection 的資料日邊界、重跑一致性與下游查詢正確性。

目前證據層級：`待確認`。
