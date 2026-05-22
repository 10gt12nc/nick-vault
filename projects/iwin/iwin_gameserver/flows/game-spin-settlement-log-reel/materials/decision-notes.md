# Decision Notes：game-spin-settlement-log-reel

## Decision 1：代表 game 只選一款

`slots-games` active module 很多。Step 3 若同時掃 27 個 game，會變 class summary。

本輪選 `slots-game40-sgj`，因為它能代表一般 slot spin path：

```text
SpinJob -> SpinUtil -> GamePlayer.addMoney -> center sync -> sendReelToLog -> LogReelJob
```

這不是說 Game40 是唯一重要遊戲，而是先用一款完整樣本建立可面試主線。

## Decision 2：center wallet 是 money source of truth

`GamePlayer.totalCoins` 會先在 game runtime 變動，但 owner 視角不能把它當最終 truth。

原因：

- game runtime 需要低延遲回應玩家。
- center 端才負責 `PlayerData.modifyAndGetCoin()`、bet、validBet、spinCurrency、RTP、活動與提款打碼。
- game 端最後會依 center response 的 coins / version 校正。

面試說法：

```text
game 端先做 runtime mutation，但 money truth 要回 center 確認；否則只看遊戲服記憶體會誤判。
```

## Decision 3：version guard 不是完整 idempotency

`PlayerGameData.saveDataVersion` 可以防止舊版本遊戲資料覆蓋 center。

但它不是完整 business idempotency，因為：

- 它不一定能回答同一局 `serial_id` 是否已處理。
- 它對 timeout / callback unknown 的語意仍需要查證。
- 它不是對 `log_reel` 寫入的防重。

Owner 建議：

- 以 `gameSerialNo` / `serial_id` / `uid` 建 mutation audit。
- center mutation、game result、log_reel 三方可查。

## Decision 4：log_reel 是 report source，不是 wallet transaction

`sendReelToLog()` 是 async log path。

風險：

- center wallet 成功，但 log server node 不可用。
- log server cache 尚未 flush 就 crash。
- log table 寫入延遲造成客服 / BI 查詢短暫缺資料。

所以不能用「log_reel 有沒有」單獨判定玩家錢是否正確。

## Decision 5：Step 3 不更新履歷

本 flow 很適合面試，但不直接更新履歷。

原因：

- `Game40SpinUtil`、`GameToCenterSpinResultJob`、`LogReelJob` 主要 path blame 仍是 initial commit。
- Nick / `10gt12nc` 的直接 evidence 較強在 third-party provider integration 與 log reel 優化，不足以說主導一般 game spin / settle。
- 履歷仍以 project-level contribution consolidation 的第三方 provider 投派整合保守 claim 為準。

## Decision 6：Step 4 只轉面試，不升級 claim

Step 4 的責任是把 Step 3 code-backed flow 轉成可面試 case。

本輪新增：

- 30 秒 / 90 秒 / 3 分鐘講法。
- STAR。
- center timeout、log server failure、saveDataVersion、log_reel source of truth 的追問答法。

本輪不做：

- 不更新 05 / 08。
- 不把一般 slot spin 寫成 Nick 真實開發過。
- 不替代 Step 5 claim gate。

Step 5 才判斷此 flow 是否維持 interview-only。

## Decision 7：Step 5 拆開一般 slot spin 與 provider log reel claim

Step 5 不把整條 flow 一刀切成「有做過」或「沒做過」。

判斷：

- `Game40SpinJob` / `Game40SpinUtil` 的一般 slot spin 主流程維持 code-backed 面試素材。
- Nick / `10gt12nc` 的 direct commits 命中 third-party provider log reel / 投派整合支線，包含 Antplay / GSC / PG 類 bet / settle / refund / transfer-in-out。
- 因此只能回填既有 project-level provider integration claim，不能寫成一般 slot spin / all game runtime owner。

## 下一步

```text
iwin iwin_gameserver bet-target-set-query Step 5
```
