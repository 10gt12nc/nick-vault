# decision-notes

## Step 5 判斷

`withdrawal-auto-review-refund` 值得作為 payment 第二條 flow，原因是它比 provider callback 更靠近玩家提款入口，能把扣分、建單、自動審核、provider 代付、callback 與退款補償串成完整資金閉環。

Step 5 判定：不更新正式履歷 / 自傳。本檔只保留 owner decision 與改善方向，作為面試分析素材。

## Owner Decision

| Decision | 目前 code 形狀 | 風險 | Step 5 結論 |
| --- | --- | --- | --- |
| 先扣分再建單 | `gmDownScore` 成功後才 insert order | 扣分成功但建單失敗會少一筆 payment order | 可作面試 failure window；不能宣稱已修 |
| 自動審核先標 flag，再由 job 掃 | 建單時設 `is_auto_review`，job 每分鐘處理 | job switch 關閉或 job fail 會讓訂單停 `WAIT` | 可講 backlog monitor / alarm 建議 |
| 出款前改 `PROCESSING` | job / router 都先改狀態再送 provider 或 MQ | MQ / provider call fail 可能卡單 | 可講 failed enqueue / provider timeout handling |
| provider 失敗走退款 | `asynUpdateOrderStatus(ERROR)` 後 `upperDeposit` | 重複 callback / MQ retry 可能重複退款 | 可講終態 guard 與待確認的下游 billNo 去重 |
| 自動條件不符轉人工 | job catch 或條件不符改回 `WAIT` + `is_auto_review=0` | 人工審核 backlog / remark 不完整 | 可講 repair boundary，不能說完整 reconciliation 已確認 |

## Step 4 重點

Step 4 已補齊面試 case，不需要改履歷，重點結論是：

- failure window。
- consistency。
- idempotency。
- retry / compensation。
- reconciliation。
- observability / auditability。

## Owner 改善方向

| 改善方向 | 為什麼 | 證據狀態 |
| --- | --- | --- |
| pending event / outbox | 扣分成功但建單失敗是最危險斷點 | 目前 code-backed 看到先扣分後 insert |
| failed MQ produce alarm | `ProducerUtil` catch 後只 log | 已確認 code path |
| provider query / reconciliation | provider accepted no callback 不能直接退款 | 多 provider 有查單入口，但自動 job 未確認 |
| game lobby `billNo` 去重 | 避免重複 `DEPOSIT` / `WITHDRAW` | 已確認傳入與 log，未確認 unique guard |
| aging dashboard | `WAIT` / `PROCESSING` 卡單需要可見 | 未確認 |

## 下一步

- 歷史下一步已完成：iwin_gameserver center-http-deposit-withdraw 已完成到 Step 5；目前沒有預設下一步，請以 senior-owner-playbook/01-senior-owner-flow-inventory.md 與 senior-owner-playbook/06-todo.md 為準。
