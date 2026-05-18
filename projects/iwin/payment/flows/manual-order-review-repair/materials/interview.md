# interview

## Step 3 可用追問

| 追問 | 回答方向 |
| --- | --- |
| 為什麼人工審核不能直接改 status？ | 因為充值成功、提現退回都會觸發 wallet side effect；只改 status 會讓 payment order 與玩家餘額不一致。 |
| payment 怎麼避免同一筆訂單被審兩次？ | `oderView` 讀單後要求狀態仍是 `WAIT`；app_bi `bill_check` 也只查 `status=1`。但這只是第一層 guard，不代表完整 exactly-once。 |
| 卡在 `PROCESSING` 怎麼辦？ | 先查 provider / 商戶後台與 wallet log，unknown 不退款、不手動成功。app_bi UI 也提示自動提現異常要先代付快查。 |
| 退回提現的風險是什麼？ | 退回會呼叫 `upperDeposit` 把錢加回玩家，若退款成功後本地訂單或統計更新失敗，就需要 repair / reconcile。 |
| 直接修復狀態應該怎麼管？ | 要有權限、原因、before-after、provider 查單結果、wallet log evidence；必要時另補 wallet / 統計 side effect。 |

## Step 4 要補

- 一版可口述的完整 case。
- failure window 的面試問答。
- 人工 repair SOP checklist。
- claim boundary：目前不更新正式履歷。

## 下一步

```text
iwin payment manual-order-review-repair Step 4
```
