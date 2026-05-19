# third-party-record-mongo-backup interview

## Q1：這條 flow 在做什麼？

它是第三方遊戲紀錄的 Mongo retention job。`third_games_api` 會把 Antplay / GSC 的 provider log 和 transaction 寫進 `bi_log` 的 active collection，`game_job` 再定期把 14 天以前的資料分批搬到 `_job_bak` collection，最後清掉 backup 裡超過 30 天的資料。

## Q2：你會怎麼看它的風險？

我會先看 copy 和 delete 是否是 atomic。這裡是先查 batch、insert backup、再用 `_id` delete origin，所以不是單一 transaction。它的安全性取決於重跑是否 idempotent，以及 delete count 是否和 copied count 對得上。

## Q3：如果 insert backup 成功但 delete origin 失敗呢？

active 和 backup 會同時有同一批資料。這比資料丟失安全，但下次重跑可能再次 insert backup。所以 backup collection 最好用原 `_id` 或 source id 做唯一性，並用 duplicate-safe write；同時把 delete count 不一致標成 failure 或告警。

## Q4：如果 delete count 小於 batch size 呢？

目前 code 只 log delete count。Owner 角度我會把它視為資料狀態不一致，至少要告警，最好記錄 batch run id、copied count、deleted count、remaining count。否則事後只能從 log 推，很難確認哪批沒刪乾淨。

## Q5：為什麼 retention policy 不是小事？

因為這些資料可能用於客服查詢、provider dispute、audit 或排查交易問題。active 14 天、backup 30 天如果只是 code hard-code，可能和業務需要不一致。尤其清 backup 是 hard delete，刪掉後就不能補查。

## Q6：`@DisallowConcurrentExecution` 夠嗎？

它只能保守說明同一 Quartz scheduler 上同 job 不會重疊。若 production 有多個 instance、Quartz cluster 設定不明、或手動 endpoint 也能跑同 job，就還需要 distributed lock 或同 collection run lock。

## Q7：這條可以放履歷嗎？

Step 3 階段不建議直接放正式履歷。可以當面試分析素材。因為 code-backed 已足夠說明我能分析 retention job 的風險，但要寫成真實開發成果，需要 Step 5 再核對 `10gt12nc` 的 GSC 分批查詢 commit、Nick 本人確認與可能的 ticket / MR。
