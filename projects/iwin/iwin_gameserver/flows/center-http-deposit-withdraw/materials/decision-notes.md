# Decision Notes：center-http-deposit-withdraw

## Decision 1：`billNos` 是 log 欄位還是 idempotency key？

目前 code-backed evidence 顯示 `billNos` 會被傳入 `PlayerData.buildCurrencyLog()`，但本次在 gameserver 主要路徑未看到「處理前先查 bill 是否已處理」的 duplicate guard。

Owner 判斷：

- 如果 `billNos` 只是 log 欄位，HTTP timeout + upstream retry 會變成重複加扣風險。
- 如果上游已保證不重送，gameserver 仍應至少能 query 這筆 bill 的處理結果，避免 ambiguous success。

建議：

- 建立 `processed_bill` 或等價 record，key 可先用 `accountId + cmd + billNos`，並校驗 `value + type + reason`。
- 重送同 key 且 payload 相同時回第一次結果。
- 重送同 key 但 payload 不同時回 conflict，不改錢。

## Decision 2：gameserver per-account queue 能解決什麼？

`CenterWorld.addGamePool(accountId, job)` 對同玩家的 wallet mutation 有價值，因為同一玩家的加扣可以在 gameserver 內排隊。

它能解決：

- 同玩家同時上分 / 下分造成的 in-memory race。
- 改錢與 player state 檢查在同一個玩家序列中執行。

它不能解決：

- 上游 timeout 後重送同一 bill。
- payment 訂單狀態已成功但 gameserver response lost。
- currency log / activity side effect 失敗後的對帳。

## Decision 3：上游訂單與 gameserver wallet 要不要同一 transaction？

不應該假裝同一 transaction。這裡跨服務、跨 runtime、跨 log writer，實務上只能做 application-level consistency。

合理設計：

1. 上游建立 pending order。
2. 呼叫 gameserver mutation。
3. gameserver 以 idempotency key 落 mutation audit。
4. 上游依 response 或 query-by-billNo 決定 order status。
5. 對帳 job 比對 order / mutation audit / currency log。

## Decision 4：充值 side effects 失敗怎麼辦？

`NewBillJob.recharge()` 在 coin mutation 後才跑大量 side effects：累積充值、每日充值、首充活動、每日首充、信用卡活動、筆存筆送、充值返利、VIP、打碼限制等。

Owner 風險：

- 錢已加，但 side effects 中途失敗。
- HTTP 可能回 server exception，但玩家錢包已變。
- 後續重送如果沒有 bill idempotency，可能重複上分。

建議：

- 把 wallet mutation result 與 side effect result 拆開記錄。
- side effects 做可重跑 / 可補償的 job，不要讓上游靠重送 `DEPOSIT` 補 side effect。

## Decision 5：下分遇到玩家在遊戲中

`NewBillJob` 看到玩家在子遊戲時，會先送 `S2S_GM_EXIT_GAME_RQ`，等待 callback 成功後再改錢。

Owner 風險：

- callback 延遲造成 HTTP timeout。
- 子遊戲拒絕退出造成上游提款失敗。
- 成功退出與扣款之間仍需 audit，避免狀態不明。

建議：

- 上游 timeout 後先 query mutation/audit，不直接重送。
- metrics 分開看：exit game latency、exit fail rate、withdraw mutation latency。

## Decision 6：不要把本 flow 誇大成完整金流 owner

這條 flow 很適合 Senior 面試，因為它能講 transaction boundary、idempotency、runtime wallet、activity side effects。

但履歷 claim 要保守：

- 本次證據可支援 code-backed 分析。
- 不能支援 Nick 主導 gameserver wallet。
- 若要升級，需要 Nick 本人確認、MR / ticket / production issue，或 path-specific commit diff 直接命中本 flow。
