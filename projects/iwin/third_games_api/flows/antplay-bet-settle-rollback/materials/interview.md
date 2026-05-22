# Interview：Antplay bet / settle / rollback

完成狀態：Step 5 已完成，正式面試 case 與單條 flow claim gate 已收斂；下一步回同 project candidate ranking，做 `gsc-seamless-withdraw-deposit-cancel Step 3`。

## 30 秒草稿

Antplay 舊版三段式 flow 分成 bet 扣款、settle 派彩、rollback 退款。`third_games_api` 負責 MD5 驗簽、Redis game / center routing、Mongo step evidence；真正改錢在 `iwin_gameserver` 的 `ANTPLAY_BET / SETTLE / REFUND`。最大風險是 gameserver 已改錢但 adapter Mongo 沒寫成功，provider retry 可能重複扣款、派彩或退款，所以 idempotency guard 應該靠近 wallet mutation boundary。

## 90 秒草稿

Antplay 舊 flow 不是整合式投派，而是三個 endpoint。`bet` 先驗簽、查餘額、找 gameId 與 center_http，送 gameserver 扣款，成功後寫 Mongo step 1。`settle` 和 `rollback` 會先查 step 1 是否存在，再從 Mongo 讀原始 bet / betTime，分別送 gameserver 派彩或退款，成功後寫 step 2 / step 3。

這裡的 owner 風險是狀態紀錄在 adapter 層，而且寫在 gameserver 成功之後。如果 gameserver 成功但 Mongo 寫失敗，下一次 provider retry 時 adapter 會看不到成功 evidence，可能再次送 gameserver。更穩的設計是用 provider + betId + step 作冪等 key，把防重放在錢包改動邊界，或先落 pending transaction，再用狀態機推進。

## 3 分鐘講法

Antplay 這條是舊版三段式 seamless wallet flow。Provider 先打 `/antplay/bet` 通知下注，adapter 驗 MD5 sign、轉內部金額單位、用 Redis 找 game id 和 center_http，查餘額後送 gameserver `ANTPLAY_BET`。gameserver 成功後，adapter 才寫 `third_log_antplay` type 3 和 `third_transaction_antplay` step 1。

後續 `settle` 和 `rollback` 都依賴 step 1。`settle` 會帶 totalWin / normalWin / bonus / free win / status 等欄位，adapter 驗簽後查 step 1、讀原始 bet 和 betTime，再送 gameserver `ANTPLAY_SETTLE`。`rollback` 則查 step 1 和原始 bet，送 `ANTPLAY_REFUND` 把原下注退回。

真正的 money boundary 在 gameserver。`HttpService#antplaySettled` 會依 type 轉成扣款、派彩或退款，再進 `AntplaySettledJob` 呼叫 `modifyAndGetCoinAntplay` 改玩家餘額，後面還有 currency log、reel log、打碼與活動分數等 side effect。

這條 flow 的面試重點不是 API 有沒有回成功，而是 state 和 transaction boundary 是否安全。現在 adapter 的 step evidence 是 gameserver 成功後才寫，所以如果 gameserver 成功、Mongo 寫失敗或 adapter timeout，provider retry 可能查不到 evidence 而再次送 gameserver。這會造成 double deduct、double settle 或 double refund。owner 改善方向是確認 provider retry key，定義 provider + betId + action / step 的 idempotency key，並把防重放到 wallet mutation 前，或先落 durable request state，再做 audit / reconciliation。

## STAR 講法

Situation：第三方遊戲 provider 的 bet / settle / rollback 直接牽涉玩家餘額，provider retry / timeout 是常態。

Task：需要拆清楚 adapter Mongo evidence 和 gameserver wallet source of truth，找出重複扣款、重複派彩、重複退款的風險。

Action：沿 `/antplay/bet`、`/settle`、`/rollback` 追 MD5 sign、Redis mapping、Mongo step 1 / 2 / 3、gameserver `ANTPLAY_*` command、`AntplaySettledJob`、`modifyAndGetCoinAntplay` 與 log side effect。

Result：整理出核心 failure window 是 `gameserver wallet success -> adapter Mongo insert fail / timeout -> provider retry`。面試改善方向是把 idempotency guard 移到 wallet mutation boundary，或先落 durable request state，再對 provider statement、adapter Mongo、gameserver currency / reel log 做 reconciliation。

## Senior 追問清單

- 如果 `bet` 成功但 step 1 沒寫，`settle` 來了怎麼辦？
- retry 的 key 是 `sign`、`betId`、還是 provider transaction id？
- gameserver success 但 adapter timeout，provider retry 怎麼保證 deterministic response？
- `settle` / `rollback` 是否互斥？如果 settle 後 rollback 又來怎麼處理？
- Mongo log 和 transaction evidence 的責任要怎麼切？
- 新 `/bet_settle-new` 可能改善了什麼？又帶來什麼新風險？

## 常見問答

### 為什麼 sign 不一定適合當 idempotency key？

因為 sign 可能包含 timestamp。若 provider retry 時 timestamp 或簽章重算，sign 會變，不能穩定代表同一筆 money event。要先確認 provider contract，再選 provider + betId + action / step 或 provider transaction id。

### step 1 存在是否代表可以安全 settle / rollback？

只代表 adapter 曾留下 bet evidence，不代表 settle / rollback 本身沒處理過。settle / rollback 仍需要自己的 step idempotency guard，尤其是 gameserver 成功但 step 2 / 3 寫失敗時。

### rollback 和 settle 可以同時存在嗎？

要看 provider contract。從 owner 角度，rollback 和 settle 應有互斥或可補償的狀態機，不能只靠「有 bet 就可以 refund」。

### audit log 寫失敗要 rollback wallet 嗎？

通常不應直接反向改錢，因為 wallet mutation 可能已是事實。更穩的是留下 failed / pending 狀態、repair job 或 reconciliation，把 audit 補齊或產生人工處理單。

### 如果面試官問「你會怎麼改」？

先確認 provider retry / unique key contract；在 wallet mutation 前做 idempotency guard；adapter 先落 durable request state；對 gameserver response、Mongo audit、currency log / reel log 建 reconciliation；最後加 retry exhausted alert 和人工 repair 入口。

## 保守邊界

- 這是 code-backed 分析素材，不是 Nick 在 `third_games_api` 的正式履歷成果。
- 若要談 Nick 實際開發 evidence，應回到 `iwin_gameserver` project claim。
- Step 5 已確認本 flow 不新增正式履歷 / 自傳；只作 interview-only case。

## 可反問面試官

- 你們第三方遊戲 provider 的 retry contract 是否保證 transaction id 穩定？
- wallet service 自己有沒有 provider event unique key？
- adapter audit、wallet ledger、game reel log 是怎麼 reconciliation 的？
- 如果 audit log 寫失敗，你們是自動補、人工補，還是依 provider statement 對帳？

## 下一步

```text
iwin third_games_api gsc-seamless-withdraw-deposit-cancel Step 3
```
