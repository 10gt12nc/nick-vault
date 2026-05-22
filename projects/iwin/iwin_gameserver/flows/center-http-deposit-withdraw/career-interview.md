# center-http-deposit-withdraw career-interview

證據層級：專案存在 / code-backed；Nick 貢獻依三層 claim gate 判斷
狀態：Step 5 已完成；正式面試 case，履歷 claim gate 結論為 interview-only

## 面試定位

這條 case 用來證明 Nick 能把 money flow 拆成「上游訂單、gameserver runtime wallet、log / side effect、reconciliation」幾層來看。

保守口徑：

- 可說：我 code-backed 分析過 iwin gameserver center_http 上分 / 下分，能講清楚 wallet mutation、per-account queue、timeout retry 與 idempotency 風險。
- 不說：我主導 gameserver 錢包、完整上分 / 下分、完整金流 owner。

## 30 秒版本

我分析過 iwin gameserver 的 center_http 上分 / 下分 flow。這條 flow 由 `payment` 或 `game_api` 發 `DEPOSIT/WITHDRAW` 到 gameserver，gameserver 依玩家 `accountId` 排進 per-account game pool，再透過 `NewBillJob` 修改大廳錢包或銀行錢包。

我會把這條 flow 的風險切成三塊：上游訂單狀態、gameserver wallet mutation、帳變 / 活動 side effects。最大的 owner 問題不是怎麼加減錢，而是 timeout 後如果上游重送，gameserver 端沒有明顯 `billNos` duplicate guard，可能需要用 processed bill record 或 query-by-billNo 來避免重複加扣。

## 90 秒版本

這條 flow 的重點不是 HTTP handler，而是跨服務 money correctness。上游 `payment` 或 `game_api` 先建立訂單或 API 操作，再帶 `cmd=DEPOSIT/WITHDRAW`、`accountId`、`value`、`type`、`billNos` 呼叫 gameserver center_http。

gameserver 入口在 `HttpService`。`DEPOSIT` 驗 value 和 wallet type 後建立 `HttpNewBill`；`WITHDRAW` 會處理定額或餘額下分，並把 value 轉成負數。`HttpNewBill` 先查玩家資料，再把 `NewBillJob` 丟進 `CenterWorld.addGamePool(accountId, job)`，讓同一玩家的錢包修改在 gameserver 內序列化。

真正的 owner 風險有兩個。第一，per-account queue 只能處理同玩家併發順序，不能處理上游 retry 的同一筆 bill 重送。第二，wallet mutation、currency log、充值 / 提現 side effects 與 HTTP response 不在同一 transaction。若上游 timeout，不能直接把它當失敗重送；應該先 query bill / mutation audit，再決定訂單狀態或補償。

## 3 分鐘版本

這條 flow 的入口在 `HttpService`，`DEPOSIT` 會檢查 `value > 0` 與 `type=0/1`，`WITHDRAW` 會檢查 `withdrawType`、`value`，並把扣款 value 轉成負數。

實際改錢不在 HTTP handler 裡做，而是建立 `HttpNewBill`。它先查 `SqlPlayerData`，再建立 `NewBillJob` 丟進 `CenterWorld.addGamePool(accountId, job)`。這代表 gameserver 內同一玩家的改錢會被序列化處理，減少同玩家併發修改錢包的 race。

`NewBillJob` 會先檢查玩家是否存在、是否封禁、內部帳號限制。大廳錢包用 `modifyAndGetCoinFromBill()`，銀行錢包用 `modifyAndGetBankCoin()`。如果是充值，後面還會更新總充值、每日充值、首充、活動、VIP 與打碼限制；如果是提現，會重置提款打碼限制、增加提款次數。最後 `afterEvent()` 回 HTTP OK 並通知玩家。

我會提醒兩個關鍵邊界：

- payment / game_api 的 order state 不等於 gameserver wallet mutation；這不是同一個 DB transaction。
- gameserver 端把 `billNos` 寫進 log，但目前沒看到用它做 duplicate guard。若上游 timeout 後重送，必須靠上游訂單狀態或補一層 processed bill idempotency，否則有重複加扣風險。

## STAR 版本

Situation：

iwin 的上分 / 下分不是單一服務內完成。上游 `payment`、`game_api` 會先處理訂單或 API 操作，再把實際改玩家錢包的 command 打到 `iwin_gameserver` center_http。

Task：

我要把這條 flow 拆清楚，確認哪裡是訂單 source of truth、哪裡是 runtime wallet source of truth，以及 timeout / retry / side effect failure 時誰負責判定成功。

Action：

我追了 `HttpService -> HttpNewBill -> NewBillJob -> PlayerData`。確認 `DEPOSIT/WITHDRAW` 會被轉成 player-level job，丟進 `CenterWorld.addGamePool(accountId, job)`，再改大廳錢包或銀行錢包。接著我把風險分成四塊：`billNos` idempotency、per-account ordering、currency log failure、充值 / 提現 side effects partial success。

Result：

這條 flow 形成一個可面試的 Senior case：per-account queue 是 concurrency control，不是 idempotency；上游 timeout 後應先 query mutation / bill result，不應盲目重送；wallet mutation 後的 log 和活動 side effects 要能 audit、補償與對帳。

## Failure scenarios

| Scenario | 面試回答重點 |
| --- | --- |
| 上游 timeout，但 gameserver 已上分 | 這是 ambiguous success；先 query bill / mutation audit，不直接重送 |
| 同一 `billNos` 重送 | gameserver 主要路徑目前未看到 duplicate guard；應用 processed bill record 回同一結果 |
| 錢包已改，但 currency log push 失敗 | log 不是同 transaction；需要 log failure metric、replay 與對帳 |
| 上分成功，充值 side effects 失敗 | 不靠重送 `DEPOSIT` 修，應把 side effects 做成可補償 / 可重跑 |
| 下分時玩家在子遊戲中 | 退遊 callback 會拉長 HTTP window；需要 exit latency / fail rate metrics |
| 上游和 gameserver wallet type 不一致 | `type=0/1` 要由 contract、測試與 audit 保證，避免改錯錢包 |

## 可面試講的 owner 重點

1. `billNos` 應該是 idempotency key，不只是 log 欄位。
2. 上游 timeout 後不能盲目重送改錢 command，應該先查 bill / balance mutation 結果。
3. coin mutation、currency log、activity side effects 不在同一 transaction，要能對帳與補償。
4. per-account game pool 解決 gameserver 內部 ordering，不解決跨服務重試。
5. `WITHDRAW` 若玩家在子遊戲中會先嘗試 exit game，這會增加 HTTP timeout 與 ambiguous success 風險。

## Senior / Lead 追問

Q：為什麼不只靠 payment 訂單狀態防重？

A：上游防重很重要，但 gameserver 是實際 wallet mutation 點。若 response lost，上游看到的是 ambiguous success。gameserver 至少要能用 bill 查到是否已處理，否則上游只能在「重送可能重複加錢」和「不重送可能漏加錢」之間猜。

Q：per-account queue 和 idempotency 差在哪？

A：queue 解的是同一玩家同時間多個 job 的執行順序；idempotency 解的是同一個 business command 被重送多次時只能生效一次。兩者都要有，不能互相替代。

Q：如果 currency log 失敗，要不要 rollback 錢包？

A：通常不應該在這種 runtime flow 裡假裝跨 wallet 和 log writer 有同一 transaction。更務實的是把 wallet mutation audit 做成可查 source，再讓 log 有 replay / 補償，對帳時能辨識「錢包已變但 log 缺失」。

Q：這條 flow 要怎麼加觀測？

A：至少串起 `billNos`、accountId、cmd、value、type、upstream order id、gameserver mutation result、currency log status、HTTP response。metrics 要分 deposit / withdraw success rate、timeout、duplicate bill、exit game latency、log push failure、side effect failure。

Q：如果要最小改動降低風險，先做什麼？

A：先建立 processed bill / mutation audit，讓同一 `accountId + cmd + billNos` 的重送回同一結果；再補 query-by-billNo 給上游 timeout 後查狀態；最後補 reconciliation 對帳。

## 履歷口徑

Step 5 結論：不把這條 flow 單獨寫進正式履歷。

可放在面試素材：

- code-backed 分析過 gameserver center_http 上分 / 下分與錢包一致性。
- 可用來說明上游金流訂單與 runtime wallet mutation 的 transaction boundary。
- 可說明 Nick 對 provider 投派整合、coupon caller、payment order / withdraw 維護的直接 evidence 如何和本 flow 形成系統理解，但不能反向包裝成完整上分 / 下分 owner。

不可寫：

- 主導 gameserver 錢包。
- 負責完整上分 / 下分系統。
- 完整金流 owner。

## 下一步

```text
iwin iwin_gameserver game-spin-settlement-log-reel Step 5
```
