# withdrawal-auto-review-refund career-interview

## Evidence 層級

- Flow：玩家提款、自動審核 / 自動出款與失敗退款。
- 證據層級：`專案存在 / code-backed`。
- Nick 個人貢獻：`待確認`。
- 目前用途：Senior Backend / Platform Backend / System Owner 面試素材，不進正式履歷 master。

## 3 分鐘講法

我會把這條 flow 拆成四段講：提款建單、扣分、自動出款、失敗退款。

玩家送提款後，payment 不會直接打 provider。它先查玩家資料、支付綁定、黑名單、提款設定、待審單，再呼叫 game lobby / center 查打碼目標和玩家狀態。通過後會產生訂單號，先呼叫 game lobby / center `WITHDRAW` 扣玩家金額，扣分成功才 insert `payment_order`，狀態是 `WAIT`。

如果金額 / 時間符合自動審核商戶設定，訂單會標 `is_auto_review=1`。排程 job 每分鐘掃最近待審訂單，再確認玩家層級、自動出款開關、提款金額、今日充值、今日打碼比例。通過後訂單改 `PROCESSING`，選商戶並呼叫 provider `unified*WithdrawOrder`。

provider 下單成功不代表終態完成，通常還要等 callback。provider 同步失敗或 callback 回失敗時，payment 會走共用 `asynUpdateOrderStatus` / `updateUserInfo`，把提現訂單轉失敗並呼叫 game lobby / center `DEPOSIT` 退款。這裡的重點是跨系統副作用不是一個 DB transaction，所以要看終態 guard、MQ retry、防重複退款、人工審核 fallback 和 reconciliation。

## 可用履歷 bullet

目前不建議放正式履歷。若 Nick 後續確認參與 evidence，可保守改寫成：

- 參與 / 分析 iwin 金流提款鏈路，梳理玩家提款建單、扣分、自動審核、自動出款、provider callback 失敗退款與 MQ retry 的一致性風險。
- 梳理 payment 與 game lobby / center 的跨系統資金異動邊界，聚焦 `payment_order` 狀態、玩家錢包副作用、provider 終態與退款補償。

## 面試可說

- 「我會先確認玩家按提款後，扣錢和建單哪個先發生，因為這會決定 failure window。」
- 「自動審核不只是 `is_auto_review` flag，還有商戶金額 / 時間、玩家層級、玩家自動出款開關、今日充值與打碼比例。」
- 「provider 代付成功通常只是 accepted，不等於提款成功，真正終態要看 callback 或查單。」
- 「退款必須看終態 guard 與 MQ retry，否則 provider 重送失敗 callback 可能造成重複退款。」
- 「我會把 `WAIT`、`PROCESSING`、`SUCCESS`、`ERROR` 當成 order lifecycle 來看，而不是散落在 controller 的 if else。」

## 不能誇大

- 不能說 Nick 主導 payment 自動出款。
- 不能說 Nick 設計提款退款架構。
- 不能說 Nick 修過重複退款 bug。
- 不能說下游 `billNo` idempotency 已完整確認。
- 不能說這條 flow 已可直接放履歷 master。

## 追問準備

| 問題 | 回答方向 |
| --- | --- |
| 扣分成功但建單失敗怎麼辦？ | 目前 code 顯示先 `gmDownScore` 再 insert，這是重大 failure window；要補 evidence 看是否有下游 billNo 查詢、補償 job 或人工修復入口。 |
| provider accepted 但沒有 callback 怎麼辦？ | 訂單會停 `PROCESSING`；本輪未確認 query order / reconciliation job，Step 4 要補。 |
| 怎麼避免重複退款？ | notify consumer 先檢查訂單只接受 `WAIT` / `PROCESSING`；退款異常 catch 內會回 `MqResult.SUCCESS` 防 MQ retry 再次退款。但下游錢包是否用 billNo 去重仍待確認。 |
| 自動審核條件有哪些？ | 商戶金額 / 時間、玩家 `is_autowithdraw`、玩家層級開關、提款金額上下限、今日充值、今日打碼比例。 |
| 為什麼這條 flow 有 owner 價值？ | 它橫跨玩家錢包、payment order、provider payout、MQ callback、退款補償與人工 fallback，任何斷點都可能是資金正確性問題。 |

## 下一步

```text
iwin payment withdrawal-auto-review-refund Step 4
```
