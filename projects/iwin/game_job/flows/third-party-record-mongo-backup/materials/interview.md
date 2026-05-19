# third-party-record-mongo-backup interview

完成狀態：Step 5。

用途：把 Step 3 的 code-backed flow 轉成面試可講 case，並標示 Step 5 claim gate 後可保守放履歷的範圍。

## 30 秒講法

這條 flow 是第三方遊戲 provider log / transaction 的 Mongo retention job。它每次把 14 天以前的 active collection 資料分批搬到 `_job_bak`，再刪 origin，backup 超過 30 天再清掉。面試重點不是「會寫排程」，而是 copy-then-delete 不是 atomic，所以要思考 retry 是否 idempotent、delete count 是否能對帳、以及 retention policy 是否符合 audit / dispute 需求。

## 3 分鐘講法

我會先把它定位成 audit / troubleshooting retention，不是 wallet source of truth。上游 `third_games_api` 寫 Antplay / GSC 的 Mongo log 與 transaction collection，`game_job` 的 Quartz job 會定期處理舊資料。

正常流程是：Quartz wrapper 設定 task name 與 daily task type，進 `BiJobBase.runTask()`，由 `BiJobBase` 寫 Redis task state 與 task log。實際 job 查 `createdTime < today - 14 days` 的文件，依 batch size 分批 insert 到 backup collection，再用這批文件的 `_id` 從 active collection 刪除。最後刪除 backup 中 `createdTime < today - 30 days` 的資料。

Senior 分析我會抓四件事：

第一，copy 和 delete 沒有跨 collection transaction。insert backup 成功但 delete origin 失敗時，資料會同時存在 active 和 backup。這是可接受的保守方向，但需要 idempotent retry。

第二，backup insert 的重跑安全要確認。若 backup 保留原 `_id` 且有 unique key，可以用 duplicate-safe upsert 或 duplicate handling；若沒有，重跑可能產生 duplicate backup。

第三，delete count 要能對帳。目前 code 只 log delete count，沒有看到持久化 reconciliation。若 delete count 小於 batch size，應該標失敗或告警，不該默默繼續。

第四，retention policy 不能只看 code。active 14 天、backup 30 天要對齊客服查詢、provider dispute、audit 與 storage 成本。

最後我會補一句：`10gt12nc` 的 GSC commit 有把一次查全量改成分批查詢與 batch size 調整，這是局部直接 evidence；Step 5 後可以保守寫成「參與 GSC Mongo backup 分批處理」，但不能說是完整 retention owner。

## STAR 講法

Situation：第三方遊戲 provider log / transaction 持續寫入 Mongo，資料量變大後，需要保留近期查詢，又不能讓 active collection 無限膨脹。

Task：設計或維護一個可重跑、可觀測、避免一次處理過大資料量的 retention job。

Action：我會從 code 看出它採 copy-before-delete，並檢查 batch size、`createdTime` 門檻、`_id` delete、task log、Redis status 與 upstream writer。GSC 的歷史 commit 也顯示曾把一次查詢全量改成分批查詢，再調整 batch size。

Result：這能降低單次查詢和批次操作壓力，但仍需要補 idempotent backup、delete count 對帳與 retention policy。面試中可以用它展示我看 batch correctness 與 owner decision 的方式。

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

Step 5 後可以保守放正式履歷，但只限局部：GSC backup job 的分批查詢與 batch size 調整。不能寫完整第三方紀錄備份 owner，也不能寫 Antplay / Oneapi retention policy owner。

## Q8：`10gt12nc` 的 commit 可以怎麼講？

保守講法是：「我參與過 GSC backup job 的分批查詢與 batch size 調整，從一次查全量改成分批處理，這是降低單次批次壓力與避免大集合操作的一種維護方向。」

不能講成：「我主導整個第三方遊戲紀錄備份系統」或「我設計完整 retention policy」。

## Q9：如果面試官問你會怎麼改？

我會分三層：

1. 先補 idempotency：backup collection 用 source `_id` 或 source id 做唯一性，backup write duplicate-safe。
2. 再補 reconciliation：每批記錄 fetched / copied / deleted / remaining，delete count 不等於 batch size 就 fail + alert。
3. 最後補 policy：14 / 30 天改成設定，並和客服查詢、provider dispute、audit retention 對齊。

## Q10：這條 case 最能展現什麼能力？

它能展現我不是只看 job 能不能跑，而是會看資料生命週期、partial failure、重跑安全、保留策略與觀測性。這是 Senior Backend / System Owner 看 batch job 時該有的角度。
