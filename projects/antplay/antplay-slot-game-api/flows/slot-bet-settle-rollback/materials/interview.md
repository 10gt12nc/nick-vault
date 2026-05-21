# Interview - slot-bet-settle-rollback

日期: 2026-05-21

## 0. Step 5 定位

本檔是 Step 5 後的面試細稿。主讀文件是同層 `career-interview.md`；本檔放更完整的追問、failure scenario、排查順序與回答邊界。

狀態:

- Step 5 已完成: 可作正式面試 case，也可作 project-level 履歷 claim 的強化 evidence。
- 履歷狀態: 不直接更新 `05` / `08`；由 project-level consolidation / rolling resume package 統一吸收。
- 證據限制: source repo fetch 先前失敗，依本地 refs / 本地 working tree 保守分析；未確認最新遠端。

## 1. 面試主軸

這條 case 的核心不是「我會寫一個 bet API」，而是「下注結算是 money correctness 問題」。

面試時要先把四件事拆清楚:

- Bet record 是一局下注在 Game API 本地的狀態主紀錄。
- Single wallet 的錢主要在 provider，Game API 需要處理 callback / settle / rollback 的可靠性。
- Transfer wallet 的錢在本地 DB / Redis，下注前扣款與開獎後派彩會放大 local consistency 風險。
- Request log MQ 與 notify job 是 audit / repair 輔助，不是完整 ledger 或 exactly-once。

## 2. Failure Scenario

### Scenario 1: transfer wallet 已扣款，但 bet record 沒有成功落地

回答重點:

- 先確認 `getBeforeBetMoney` 的 wallet debit 和 bet record save 是否同一 transaction boundary。
- Step 3 evidence 看到 transfer wallet 下注前先扣 `betAmount * 100`，但不能宣稱它與後續 bet record / result 完整 atomic。
- Owner 補強應把 wallet debit、bet record DEAL、failure compensation 放進可查 / 可重試流程。

### Scenario 2: RESULT 已寫入，但 settle provider 失敗

回答重點:

- `setStatusResult` 先把 record 推到 RESULT，再由 `settle` 呼叫 `AgentApiFacade#betSettle`。
- settle 成功才更新 notify success；失敗時 `notify = 0` 的 RESULT record 可由 notify job 補 call。
- 這是 retry / repair，不是完整 reconciliation；還要比對 provider response、wallet transaction 與 bet record。

### Scenario 3: single wallet provider timeout

回答重點:

- Timeout 不能直接等於失敗，因為 provider 可能已處理成功。
- 要用 bet id / provider transaction id 做 idempotency 與查詢。
- Game API 本地狀態、provider response log、notify status 要分層看。

### Scenario 4: CANCEL 已寫入，但 rollback call 失敗

回答重點:

- `cancel` 會先 `setStatusCancel`，再 call `betRollback`。
- rollback provider failure 需要靠 rollback notify job 依 `step = CANCEL` 與 notify 狀態補送。
- 需要確認 provider rollback 是否冪等，否則補送可能造成重複 side effect。

### Scenario 5: deadlock / lock exception 發生在 transfer wallet 已變動後

回答重點:

- Code 有計算 transfer wallet refund amount，也有 `CompensationService#refundTMOnDeadlock`。
- 但目前看到 flow catch 內的 refund / fail 標記呼叫被註解，所以不能說補償已完整落地。
- 面試可把它講成 owner-grade 風險發現：需要 repair table、狀態標記、告警與人工查詢。

### Scenario 6: request log MQ 失敗

回答重點:

- `sendRequestLogMq` catch exception 並 log，不阻斷主交易。
- 這個設計合理，因為 request log 是 audit / observability，不是交易 source of truth。
- 代價是 audit 可能遺失或延遲；owner 要補 MQ failure metric、重送或落地 outbox。

## 3. Senior 追問

### Q1: 這條 flow 的 source of truth 是什麼？

保守回答:

`pt_bet_record` 是一局 bet 的狀態主紀錄，記錄 step、detail、win、notify 狀態。wallet balance 則依 wallet type 不同：single wallet 的錢主要在 provider，transfer wallet 的錢在本地 DB / Redis wallet。不能把 request log MQ 當 source of truth，它只是 audit / observability。

### Q2: transfer wallet 和 single wallet 最大差異？

保守回答:

single wallet 主要靠 provider API 做 balance / settle / rollback；transfer wallet 則在 Game API 本地維護 wallet。transfer wallet 下注前先扣 bet amount，開獎後再加 total win，所以本地 DB / Redis 和 bet record 的一致性風險更高。

### Q3: 為什麼 `setStatusResult` 要限制 `step = 2`？

保守回答:

這代表只有已經 DEAL 的 bet record 才能被推到 RESULT，避免未成立的注單直接寫入開獎結果。這是一個 state transition guard，但它只保證本地 bet record 的狀態順序，不代表 wallet 或 provider 一定已經一致。

### Q4: RESULT 寫入後 settle 失敗怎麼辦？

保守回答:

程式會在 settle 時 call `AgentApiFacade#betSettle`，成功後更新 notify success。若失敗，`notify = 0` 且 `step = RESULT` 的 record 可由 Quartz notify job 補 call settle。不過這是補通知 / retry，不等於完整 ledger reconciliation。

### Q5: deadlock 時你看到什麼風險？

保守回答:

`GameFacade#bet` catch deadlock 時會判斷 transfer wallet 是否已加 total win，並計算應 refund 的 amount。但我看到實際 `refundTMOnDeadlock` 和 `setStatusFail` 呼叫目前被註解，所以不能說補償完整。這是一個 owner 應優先補強的 failure window。

### Q6: request log MQ 失敗會不會影響交易？

保守回答:

目前 `sendRequestLogMq` 內部 catch exception 並 log，主流程不會因 request log MQ failure 被 rollback。這合理，因為 request log 是 audit，不是交易 source of truth；但代價是 audit 可能遺失或延遲。

### Q7: 補通知 job 和 reconciliation 有什麼不同？

保守回答:

補通知 job 是針對 `notify = 0`、特定 `step` 的 retry，它能補送 settle / rollback，但不一定能證明 provider、wallet、bet record 三邊完全一致。Reconciliation 要有三邊資料比對、差異落地、重試 / 人工補償與告警。

## 4. Lead / Architect 追問

### Q1: 如果你是 owner，你會怎麼補強？

- 定義 bet record 與 wallet mutation 的 transaction boundary。
- 對 provider settle / rollback 引入 idempotency key 與明確 retry policy。
- 對 transfer wallet 扣款後的 deadlock 補償落實為可觀測、可重試的補償流程。
- 把 request log MQ 與交易狀態切清楚：audit failure 不影響交易，但要有告警與補寫策略。
- 補 reconciliation：比對 bet record RESULT / notify、provider settle response、wallet transaction。

### Q2: 為什麼不直接說 exactly-once？

因為本次 evidence 沒看到 outbox、dedupe table、consumer idempotency 或完整 ledger reconciliation。比較保守的說法是「用 bet id / notify job / provider callback 做 retry 與補通知」，不能包裝成 exactly-once。

### Q3: 這條 flow 的最小監控是什麼？

- `pt_bet_record step=RESULT AND notify=0` 積壓量。
- `notify_count` 超過限制的 records。
- provider settle / rollback error rate。
- transfer wallet negative balance log。
- request log MQ send failure。
- deadlock / lock exception count。

### Q4: 如果只能先做一件事，你會先做什麼？

保守回答:

我會先把 transfer wallet deadlock / partial mutation 的補償補成可追蹤流程。原因是這段直接影響玩家餘額，且目前看到的補償呼叫被註解，風險比 request log audit loss 更高。最低限度要有 fail / pending 狀態、refund attempt、retry count、告警與人工查詢入口。

### Q5: 這條 flow 和 `*-math` flow 怎麼連起來？

保守回答:

`*-math` 決定開獎結果與 result contract，`antplay-slot-game-api` 負責把結果落到 bet record、wallet settle / rollback 與 provider callback。兩者都會影響 payout correctness，但責任不同：math 是結果正確性，game-api 是交易狀態與金額副作用正確性。

## 5. 面試時避免踩雷

不要說:

- 「我設計完整 wallet ledger」。
- 「我們做到 exactly-once」。
- 「deadlock 已完整補償」。
- 「RabbitMQ 保證交易一致性」。

可以說:

- 「我會先把 bet record、wallet、provider callback 三個狀態拆開看。」
- 「我看到補通知 job 能補 settle / rollback，但它不是完整對帳。」
- 「這條 flow 最值得補強的是 transfer wallet 扣款後的 failure window。」

## 6. Step 5 Claim Gate 結論

- `a2b2af5` / `54078fe` / `31d7a46` 支撐 Nick / `10gt12nc` 參與本 flow 的 transfer wallet / deadlock failure-window 維護。
- `d3e0002` / `71fff7b` 支撐 Nick / `10gt12nc` 參與 request log MQ async audit 維護。
- 本 flow 可回填 `antplay-slot-game-api` project-level contribution consolidation，作履歷強化 evidence。
- 面試可講「我參與 / 維護 / 深挖過這條下注結算主線與 failure window」；不要講「主導完整下注結算平台」。
- Deadlock compensation 不能說已完整落地，因為目前本地 `develop` 看到實際 refund / fail 標記呼叫被註解。

下一步:

```text
antplay antplay-slot-game-api bet-record-sharding-schema-route Step 4
```
