# daily-game-data-summary Interview Drill

更新時間：2026-05-15
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 核心故事

這是一條 BI batch projection flow。它每天從遊戲投注 log 分表彙總玩家投注、贏分、新增玩家與留存，寫成後台報表查詢用資料。它的難點不是 CRUD，而是資料日、時區、重跑、累積狀態與下游讀半成品的風險。

## Senior 面試追問

### 1. 你怎麼判斷這條 flow 的正確性？

回答方向：

- 先確認 source log：`log_reel` 的資料日、平台、game_id range、time window。
- 再確認 projection：type=0 personal data 的 count / sum 是否能對回 source。
- 再確認 summary：type=1 是否完全由同一批 type=0 推導。
- 最後確認下游：app_bi 是否只讀完成資料日，金額 / rate 縮放是否一致。

### 2. Delete + insert 有什麼問題？

回答方向：

- 對重跑很方便，但有空窗。
- job 半途失敗會留下缺資料或半成品。
- 如果下游沒有完成標記，使用者可能看到錯誤報表。
- 可用 staging table、publish marker、job_run_id 改善。

### 3. 新增玩家為什麼比 summary 更難？

回答方向：

- Summary 是同日 type=0 聚合，可刪掉重算。
- 新增玩家要知道「以前是否出現過」，依賴跨日累積狀態。
- 重跑舊日期時，未來狀態可能已經改變，導致結果不一定可重現。
- 要決定重跑範圍、清理策略與 unique constraint。

### 4. PG / Antplay 時區 bug 會怎麼排？

回答方向：

- 找出平台 settlement 定義與 business day。
- 檢查 query start / end 是否跨分表。
- 抽邊界時間點的投注，看被歸到哪個資料日。
- 比對修正前後同一天 source count / amount 差異。
- 補單元測試或固定案例避免再回歸。

### 5. Backup / cleanup 怎麼避免資料遺失？

回答方向：

- backup 前先 count source。
- insert backup 後 count backup。
- count match 才 delete source。
- delete 後 count remaining。
- 用 unique key 或 job_run_id 防 duplicate。
- failure 時保留可重跑狀態與 log。

## Lead / Owner 面試追問

### 1. 如果報表今天少一半金額，你怎麼切問題？

回答方向：

1. 查 job 是否成功、是否有 timeout / exception。
2. 查該 date / platform 的 source `log_reel` count / sum。
3. 查 type=0 personal data 是否完整。
4. 查 type=1 summary 是否由 type=0 推導成功。
5. 查 app_bi query / platform filter / 金額縮放。
6. 查時區窗口是否剛好落在變更邊界。

### 2. 你會優先改哪裡？

回答方向：

- 第一優先是完成標記與 rows count，降低下游讀半成品與排查成本。
- 第二優先是把 platform time window policy 集中化並加測試。
- 第三優先是新增玩家表與 backup 表的重跑 / reconciliation 策略。

### 3. 這條 flow 可以寫履歷嗎？

回答方向：

- 若沒有本人 evidence，不應寫成成果。
- 可以在面試中作為分析案例，說明自己如何拆 batch correctness。
- 若未來有本人 evidence，要用保守描述，避免誇大成完整資料平台 owner。

## 白板圖口述

可以畫：

```text
log_reel daily tables
  -> game_job daily job
  -> log_game_daily_record type=0
  -> log_game_daily_record type=1
  -> app_bi dashboard / excel

side state:
  -> log_game_daily_record_new_players
  -> log_game_daily_record_backup
```

圖上要特別標：

- Time window。
- Delete + insert。
- New players state。
- Downstream reads type=1。

## 面試紅線

不要說：

- 我修了 #384 / #403。
- 我設計這整條 pipeline。
- 我保證 production 是這個 config。
- 我擁有 app_bi / game_job / upstream gameserver 全鏈路。

可以說：

- 我會用這條 flow 當例子，說明 batch projection 應該怎麼檢查。
- 我看到這類 flow 的風險集中在 time window、re-run boundary、累積狀態與下游讀取一致性。
