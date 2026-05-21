# Interview：center-http-deposit-withdraw

狀態：Step 5 完成；正式面試 Q&A / claim gate 已收斂
證據層級：專案存在 / code-backed；不可直接寫成 Nick 真實開發過

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

## Q8：面試官問「這是不是你做的」要怎麼答？

保守答法：

我在這條 flow 上目前用 code-backed 分析語氣，不把它說成我主導。我的強 evidence 在 `iwin_gameserver` 第三方 provider 投派整合、`payment` provider / order 維護、`game_api` coupon 與部分 `game_job`。這條 center_http 上下分 flow 我會用來說明我能讀懂 runtime wallet path、transaction boundary、idempotency 與 reconciliation 風險。

## Q9：如果要設計 `billNos` idempotency，key 怎麼選？

我會先用：

```text
accountId + cmd + billNos
```

再校驗 `value`、`type`、`reason` 與 upstream source。相同 key 且 payload 相同時回第一次結果；相同 key 但 payload 不同時回 conflict，不改錢。

如果只用 `billNos`，不同上游或不同 wallet type 的資料污染風險比較高；如果 key 太細又可能讓重送繞過防重。實務上還要看 payment / game_api 的 bill number contract。

## Q10：Step 4 的三分鐘面試主線怎麼收斂？

可以照這個順序：

1. 先講這條 flow 的業務風險：跨上游訂單與 gameserver wallet。
2. 再講 code path：`HttpService -> HttpNewBill -> NewBillJob -> PlayerData`。
3. 接著講已確認的保護：per-account game pool。
4. 然後講缺口：`billNos` 在主要路徑目前只看到進 log，未看到 duplicate guard。
5. 最後講 owner decision：processed bill、query-by-billNo、mutation audit、log replay、reconciliation。

## Q11：如果 log 和 wallet 不一致，排查順序是什麼？

先查上游 order，再查 gameserver mutation trace / `LogUtil.BALANCE`，再查 currency log / game coin log，最後查 recharge / withdraw side effects。不能只看某一張 log 表就判斷錢包 truth，因為 Step 3 已看到 `buildCurrencyLog()` 例外不會 rollback 已改的 coins / bankCoin。

## Q12：這條 flow 對 Senior Backend 有什麼價值？

價值在於它能展現三個能力：

- 看懂跨服務 source of truth，不把 payment order 和 runtime wallet 混成一個 transaction。
- 看懂 concurrency 和 idempotency 的差異。
- 能提出 owner 級補強，不只是說「加 try-catch」或「重試」。

## Q13：Step 5 後結論有改嗎？

沒有升級成正式履歷 claim。

Step 5 追了 `iwin_gameserver`、`payment`、`game_api` 的 remote refs、path-specific history、重要 diff 與 blame。結論是：Nick / `10gt12nc` 在 `iwin_gameserver` 的強 evidence 仍是第三方 provider 投派整合、gameserver money job 與 log projection；`center_http DEPOSIT/WITHDRAW` 主流程目前只支援 code-backed 分析，不支援寫成 Nick 主導或完整 gameserver wallet owner。

面試時可以講這條 flow 的設計風險與改善方案，但若被問「是不是你做的」，要回答成分析過 / 參與相關 gameserver wallet 串接經驗，不要說自己主導這條 center_http 上下分。
