# Interview：game-spin-settlement-log-reel

狀態：Step 3 初版面試素材
證據層級：專案存在 / code-backed；不可直接寫成 Nick 真實開發過

## Q1：這條 flow 的核心問題是什麼？

核心不是「怎麼轉輪」，而是玩家 spin 後，遊戲結果、玩家錢包、投注流水三者要能互相對上。

如果只看 game module，會漏掉 center wallet；如果只看 center wallet，會漏掉玩家看到的 spin result；如果只看 `log_reel`，又可能把 async log 當成 transaction truth。

## Q2：為什麼 Step 3 選 Game40 當代表？

因為 Game40 是典型 slot flow，路徑清楚：

```text
Game40SpinJob -> Game40SpinUtil -> GamePlayer.addMoney -> GameToCenterSpinResultJob -> sendReelToLog -> LogReelJob
```

它可以代表「一般 slot spin」的主要架構。這不代表其他 26 個 game 都完全一樣，也不代表已逐一深掃全部 game。

## Q3：`GamePlayer.addMoney()` 的設計重點是什麼？

它不是只做加減錢。

它會：

- 檢查上限 / 下限。
- 更新 game runtime `totalCoins`。
- 建 `AddCenterCoin` 放進 queue。
- 將錢包變更送到 center。

`addMoneyQueue` 的價值是同一玩家的 game -> center sync 不會同時亂飛，但它不是完整 idempotency。

## Q4：center 端做了什麼？

`GameToCenterSpinResultJob` 做的是中心狀態更新：

- 驗證 `PlayerGameData.saveDataVersion`。
- 呼叫 `PlayerData.modifyAndGetCoin()` 更新中心錢包。
- 更新 RTP、win、bet、validBet、spinCurrency。
- 更新每日資料、提款打碼、活動進度。
- 回傳 center coins / version 給 game。

所以 center 才是 money source of truth。

## Q5：`log_reel` 的位置是什麼？

`log_reel` 是戰績 / 報表 / 客服查詢來源。

`sendReelToLog()` 會組 `LogReelData`，透過 `LogController` 推到 log server，再由 `LogReelJob` 或 `LogJobCrons` 寫 `log_reel_YYYYMMDD`。

它很重要，但不是 wallet transaction。缺 log 時要再查 center mutation，不應直接判定錢包錯。

## Q6：最危險的 failure window 是什麼？

我會優先看三個：

1. game runtime 扣款 / 派彩成功，但 center sync timeout。
2. center wallet 成功，但 log server 失敗。
3. center version mismatch，造成 game 端本局結果和 center 狀態不一致。

這三個都不是加 try-catch 就能解決，需要 query、audit、replay 和對帳。

## Q7：這條能放履歷嗎？

Step 3 不建議單獨放。

可以面試講 code-backed 分析，但正式履歷仍用 iwin_gameserver project-level consolidation 的保守說法：第三方 provider 投派整合與 gameserver wallet / log projection 串接。

## Q8：如果面試官問「這是不是你做的」？

保守回答：

```text
這條一般 slot spin 主流程我目前用 code-backed 分析語氣，不說成我主導。我的直接 evidence 較強在 iwin_gameserver 第三方 provider 投派整合與 log reel / wallet 串接；這條是我用來說明我能讀懂 game runtime 到 center wallet / log_reel 的一致性風險。
```

## Step 4 要補什麼

- 30 秒 / 90 秒 / 3 分鐘正式講法。
- STAR 案例。
- Senior / Lead 追問。
- failure scenarios 的面試回答。
- claim boundary。
