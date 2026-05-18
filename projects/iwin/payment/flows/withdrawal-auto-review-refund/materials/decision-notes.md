# decision-notes

## Step 3 判斷

`withdrawal-auto-review-refund` 值得作為 payment 第二條 flow，原因是它比 provider callback 更靠近玩家提款入口，能把扣分、建單、自動審核、provider 代付、callback 與退款補償串成完整資金閉環。

## Owner Decision

| Decision | 目前 code 形狀 | 風險 | Step 4 要補 |
| --- | --- | --- | --- |
| 先扣分再建單 | `gmDownScore` 成功後才 insert order | 扣分成功但建單失敗會少一筆 payment order | 查是否有補償 / repair / 下游 billNo 查詢 |
| 自動審核先標 flag，再由 job 掃 | 建單時設 `is_auto_review`，job 每分鐘處理 | job switch 關閉或 job fail 會讓訂單停 `WAIT` | backlog monitor / alarm |
| 出款前改 `PROCESSING` | job / router 都先改狀態再送 provider 或 MQ | MQ / provider call fail 可能卡單 | failed enqueue / provider timeout handling |
| provider 失敗走退款 | `asynUpdateOrderStatus(ERROR)` 後 `upperDeposit` | 重複 callback / MQ retry 可能重複退款 | 終態 guard、下游 billNo 去重、raw callback audit |
| 自動條件不符轉人工 | job catch 或條件不符改回 `WAIT` + `is_auto_review=0` | 人工審核 backlog / remark 不完整 | app_bi repair boundary |

## Step 4 重點

Step 4 不需要改履歷，應專注補：

- failure window。
- consistency。
- idempotency。
- retry / compensation。
- reconciliation。
- observability / auditability。

## 下一步

```text
iwin payment withdrawal-auto-review-refund Step 4
```
