# Interview：center-http-deposit-withdraw

## Q1：這條 flow 的核心問題是什麼？

核心不是「HTTP API 怎麼寫」，而是 payment / game_api 已經決定要上分或下分後，gameserver 怎麼安全修改玩家 runtime wallet。

真正的風險在於：

- 上游 order state 和 gameserver wallet mutation 不在同一 transaction。
- HTTP timeout 後不知道 gameserver 是否已改錢。
- 如果沒有 `billNos` idempotency guard，重送可能造成重複加扣。
- 錢包變更後還有 currency log、充值活動、VIP、打碼限制等 side effects。

## Q2：為什麼 gameserver 要用 per-account game pool？

因為同一玩家的錢包是 runtime state。如果兩個改錢請求同時進來，直接併發改 `PlayerData` 會很危險。

`CenterWorld.addGamePool(accountId, job)` 的價值是把同一玩家的 job 序列化，降低同玩家同時改錢 race。

但它不是 idempotency。它不能阻止同一個 `billNos` 被上游重送兩次，只能保證兩次按順序執行。

## Q3：`DEPOSIT` 和 `WITHDRAW` 最大差異是什麼？

`DEPOSIT` 是正數上分，會先驗 `value > 0`，再依 `type` 加到大廳或銀行。

`WITHDRAW` 會把 value 轉成負數，且多一個 `withdrawType`：

- `withdrawType=1`：定額下分。
- `withdrawType=2`：餘額下分，從玩家目前大廳 / 銀行餘額抓 value。

下分若玩家正在子遊戲，會先嘗試退出遊戲，成功後才扣款。

## Q4：如果上游 timeout，怎麼避免重複加錢？

我不會讓上游直接重送改錢 command。

建議做法：

1. 上游所有 request 都帶穩定 `billNos`。
2. gameserver 在 mutation 前用 `billNos + accountId + cmd` 檢查 processed record。
3. 如果已處理過，直接回第一次結果。
4. 如果 timeout，先 query bill mutation result，再決定上游訂單狀態。
5. 對帳 job 定期比對上游 order、gameserver mutation audit、currency log。

## Q5：coin mutation 和 currency log 失敗怎麼辦？

目前看到 `PlayerData.buildCurrencyLog()` catch exception 後只記 error，不會回滾已改的 coins / bankCoin。

所以 owner 上不能把 currency log 當成改錢 transaction 的一部分。需要：

- wallet mutation audit。
- log push failure metric / alert。
- replay 或補 log 機制。
- 對帳時能辨識「錢包已變但 log 缺失」。

## Q6：充值 side effects 為什麼危險？

因為 `recharge()` 在錢包已上分後才更新一堆 side effects，例如總充值、每日充值、首充、VIP、活動、打碼限制。

如果 side effect 中途失敗，玩家錢可能是對的，但風控 / 活動狀態可能不對。這時不能靠重送 `DEPOSIT` 解決，因為重送可能再加一次錢。

更好的方式是把 side effects 做成可重跑、可標記進度的補償流程。

## Q7：這條 flow 能放履歷嗎？

目前不建議單獨放。

可以在面試中當 code-backed 分析案例，講：

- center_http 上分 / 下分流程。
- per-account queue。
- 上游 payment / game_api 與 gameserver wallet 的 transaction boundary。
- timeout / retry / idempotency 風險。
- activity side effects 與 log consistency。

但不能寫 Nick 主導 gameserver wallet 或完整金流 owner，除非後續補本人確認、MR / ticket / production issue 或 direct path commit evidence。
