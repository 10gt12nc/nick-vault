# daily-game-data-summary Decision Notes

更新時間：2026-05-15
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 1. Projection 不等於 Source of Truth

本 flow 產出的 `log_game_daily_record` 是 BI projection。它讓報表查詢變快、欄位變乾淨，但不能倒過來當投注交易真相。

Owner 判斷：

- 對帳或修復應回到 `log_reel` / upstream settlement。
- `log_game_daily_record` 可以刪除重建，但 source log 不應被批次任意修改。
- 報表異常時要先分辨是 source log 問題、projection 問題，還是 app_bi 顯示 / 縮放問題。

## 2. Delete + Insert 重跑策略

目前主 projection 採刪除指定日期 / 平台後重建。

優點：

- 實作直覺。
- 對單日補跑友善。
- 避免同日期 summary 重複累加。

風險：

- 刪除後、重建完成前，下游可能查到空資料。
- type=0 完成但 type=1 未完成時，下游 summary 不完整。
- 新增玩家表與 backup 表不是完全同一刪除重建邊界。

更穩的替代：

- staging table + 完成後 rename / publish。
- 增加 `job_run_id` 與 `is_published`。
- BI 只讀完成標記的資料日。
- 重跑完成後比對 row count、sum spin、sum win。

## 3. 新增玩家表是累積狀態

`log_game_daily_record_new_players` 的語意不是單日 projection，而是「這個 platform / uid 是否曾被視為新玩家」。

這造成一個重要差異：

- 主表重跑同一天時，可以刪掉再重建。
- 新增玩家表如果已經累積到未來日期，重跑舊日期時不一定能還原當時第一次跑的結果。

Owner 需要決定：

- 是否允許只重跑最近日期。
- 是否重跑前要清理某日期之後的 new players。
- 是否將新增玩家改成可由歷史 type=0 重新推導，而不是累積寫入。
- 是否加 unique key 防止同 platform / uid 重複。

## 4. Timezone Window 要可測試

PG / Antplay 的資料日不是自然日，且 path history 已出現 PG GMT+8、Antplay GMT+0 修正。

風險：

- 分表日期與 business date 不一致。
- 跨午夜投注可能被算到錯誤資料日。
- 第三方 settlement time zone 改變時，程式字串可能被漏改。

建議：

- 抽成 platform data day policy。
- 對每個平台建立邊界測試：起點、終點、前一毫秒、後一毫秒。
- log 實際查詢窗口與分表清單。
- 對高金額日期建立抽樣 reconciliation。

## 5. Backup + Delete 需要 Reconciliation

Iwin job 對舊資料做 backup 後 delete。這是容量治理，不是單純報表邏輯。

需要確認：

- insert backup 與 delete 是否同 transaction。
- backup 是否有 duplicate 防護。
- delete 是否只在 insert rows count 符合預期後執行。
- 重跑備份日期是否會重複寫 backup。

比較保守的策略：

- 先 copy，記錄 source count / backup count。
- count match 才 delete。
- delete 後再記錄 remaining count。
- job log 保留 date、type、copied rows、deleted rows。

## 6. Observability 應按平台 / 日期 / 步驟打點

目前有開始、結束與耗時 log，但對排查 production correctness 仍不足。

建議最小觀測欄位：

- data date。
- platform。
- source tables。
- query start / end time。
- personal rows inserted。
- summary rows inserted。
- new players inserted。
- retention rows inserted。
- backup rows copied / deleted。
- job run status。

這些資料能讓 owner 在報表異常時快速回答：「今天是 source 沒資料、projection 沒完成、還是下游查詢問題？」

## 7. 履歷決策

目前沒有 Nick 本人 evidence，所以不更新正式履歷。

若未來補到本人 evidence，也應避免寫得太大。比較安全的 claim 是「參與 / 協助維護 BI batch projection 的一致性與重跑邊界」，而不是「負責完整遊戲資料平台」。
