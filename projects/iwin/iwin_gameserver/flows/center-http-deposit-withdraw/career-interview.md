# center-http-deposit-withdraw career-interview

證據層級：專案存在 / code-backed；Nick 貢獻依三層 claim gate 判斷
狀態：Step 3 後初版，Step 4 需再整理成正式面試 case

## 30 秒版本

我分析過 iwin gameserver 的 center_http 上分 / 下分 flow。這條 flow 由 `payment` 或 `game_api` 發 `DEPOSIT/WITHDRAW` 到 gameserver，gameserver 依玩家 `accountId` 排進 per-account game pool，再透過 `NewBillJob` 修改大廳錢包或銀行錢包。

我會把這條 flow 的風險切成三塊：上游訂單狀態、gameserver wallet mutation、帳變 / 活動 side effects。最大的 owner 問題不是怎麼加減錢，而是 timeout 後如果上游重送，gameserver 端沒有明顯 `billNos` duplicate guard，可能需要用 processed bill record 或 query-by-billNo 來避免重複加扣。

## 3 分鐘版本

這條 flow 的入口在 `HttpService`，`DEPOSIT` 會檢查 `value > 0` 與 `type=0/1`，`WITHDRAW` 會檢查 `withdrawType`、`value`，並把扣款 value 轉成負數。

實際改錢不在 HTTP handler 裡做，而是建立 `HttpNewBill`。它先查 `SqlPlayerData`，再建立 `NewBillJob` 丟進 `CenterWorld.addGamePool(accountId, job)`。這代表 gameserver 內同一玩家的改錢會被序列化處理，減少同玩家併發修改錢包的 race。

`NewBillJob` 會先檢查玩家是否存在、是否封禁、內部帳號限制。大廳錢包用 `modifyAndGetCoinFromBill()`，銀行錢包用 `modifyAndGetBankCoin()`。如果是充值，後面還會更新總充值、每日充值、首充、活動、VIP 與打碼限制；如果是提現，會重置提款打碼限制、增加提款次數。最後 `afterEvent()` 回 HTTP OK 並通知玩家。

我會提醒兩個關鍵邊界：

- payment / game_api 的 order state 不等於 gameserver wallet mutation；這不是同一個 DB transaction。
- gameserver 端把 `billNos` 寫進 log，但目前沒看到用它做 duplicate guard。若上游 timeout 後重送，必須靠上游訂單狀態或補一層 processed bill idempotency，否則有重複加扣風險。

## 可面試講的 owner 重點

1. `billNos` 應該是 idempotency key，不只是 log 欄位。
2. 上游 timeout 後不能盲目重送改錢 command，應該先查 bill / balance mutation 結果。
3. coin mutation、currency log、activity side effects 不在同一 transaction，要能對帳與補償。
4. per-account game pool 解決 gameserver 內部 ordering，不解決跨服務重試。
5. `WITHDRAW` 若玩家在子遊戲中會先嘗試 exit game，這會增加 HTTP timeout 與 ambiguous success 風險。

## 履歷口徑

目前不建議把這條 flow 單獨寫進正式履歷。

可放在面試素材：

- code-backed 分析過 gameserver center_http 上分 / 下分與錢包一致性。
- 可用來說明上游金流訂單與 runtime wallet mutation 的 transaction boundary。

不可寫：

- 主導 gameserver 錢包。
- 負責完整上分 / 下分系統。
- 完整金流 owner。

## 下一步

```text
iwin iwin_gameserver contribution claim consolidation
```
