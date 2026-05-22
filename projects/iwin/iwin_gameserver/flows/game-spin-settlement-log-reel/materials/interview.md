# Interview：game-spin-settlement-log-reel

狀態：Step 4 正式面試 Q&A
證據層級：專案存在 / code-backed；不可直接寫成 Nick 真實開發過

## 30 秒講法

```text
這條 flow 是玩家 spin 後，遊戲服怎麼完成扣款、派彩、同步 center 錢包和寫投注流水。我用 Game40 當代表，主線是 Game40SpinJob -> Game40SpinUtil -> GamePlayer.addMoney -> GameToCenterSpinResultJob，另外透過 sendReelToLog 到 LogReelJob 寫 log_reel。重點是 game / center / log 沒有單一 transaction，所以要看 addMoneyQueue、saveDataVersion、async log failure 和對帳。
```

## 90 秒講法

```text
我會把它拆三段。第一段是 game runtime：Game40SpinJob 收 SpinRq 後進 Game40SpinUtil，驗證下注、產生 gameSerialNo、算 reel result。第二段是 wallet sync：GamePlayer.addMoney 先更新遊戲端 totalCoins，再把 AddCenterCoin 排進 queue，送 GameToCenterSpinResultRq 到 center；center 的 GameToCenterSpinResultJob 會檢查 saveDataVersion，成功後更新 PlayerData 錢包、bet、validBet、spinCurrency、RTP 和活動。第三段是 log：遊戲端組 ReelLogData，透過 LogController 推到 log server，由 LogReelJob / LogJobCrons 批次寫 log_reel。這條的 owner 風險是 center 成功但 log 缺、center timeout 後重送，以及 version guard 不等於完整 idempotency。
```

## 3 分鐘講法

```text
這條 flow 我會用 Game40 當代表樣本講一般 slot spin。玩家送 SpinRq 進 Game40SpinJob，入口檢查後進 Game40SpinUtil。它會驗證玩家狀態和下注倍率，產生 gameSerialNo 和 gameStartTimestamp，接著依 reel config 算出窗口、免費輪、獎項和控制池結果。

算完後第一個重點是 wallet sync。GamePlayer.addMoney 先改遊戲端 runtime totalCoins，再把 AddCenterCoin 放進 addMoneyQueue，送 GameToCenterSpinResultRq 到 center。center 的 GameToCenterSpinResultJob 會檢查 PlayerGameData.saveDataVersion，成功後透過 PlayerData.modifyAndGetCoin 更新中心錢包，並更新 bet、validBet、spinCurrency、RTP、活動和提款打碼資料。center response 回來後，game 端會更新 version，也會依 center coins 校正 runtime totalCoins。

第二個重點是 log_reel。Game40SpinUtil 會組 ReelLogDate 和 logExt，GamePlayer.sendReelToLog 推 REEL_NORMAL 到 LogController，再由 slots-game-log 的 LogReelJob / LogJobCrons 批次寫 log_reel_YYYYMMDD。這個 log 是戰績、報表、客服查詢來源，但不是 wallet transaction。

所以這條 flow 的 Senior 重點是 source of truth 和 failure window。center wallet 才是 money truth，game totalCoins 是 runtime cache，log_reel 是 report truth。沒有跨三者的單一 transaction，既有保護是 addMoneyQueue、saveDataVersion 和 center response 校正；風險是 center timeout 後是否安全重送、center 成功但 log server 失敗、log 缺資料時如何補 log 和對帳。我會建議用 gameSerialNo / serial_id / uid 串 game result、center mutation 和 log_reel，並對 version mismatch、logServer null、center sync timeout 做 metrics / alert。
```

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

## Q9：這條 flow 的 STAR 怎麼講？

Situation：遊戲 spin 不只是算結果，還會改玩家錢包並寫戰績；任一段失敗都會造成查單、報表或錢包不一致。

Task：把一般 slot spin 拆成可面試的 Senior flow，確認 game runtime、center wallet、log server 的邊界。

Action：我沒有平均掃所有 game，而是選 Game40 當代表樣本，追 `Game40SpinJob -> Game40SpinUtil -> GamePlayer.addMoney -> GameToCenterSpinResultJob -> LogReelJob`，整理出 runtime cache、money source of truth、report source 與 failure window。

Result：產出一條 code-backed 面試 case，可以講 addMoneyQueue、saveDataVersion、async log、timeout / retry / reconciliation，但不誇大成我主導一般 slot spin 開發。

## Q10：center timeout 後怎麼處理？

不能只說重送。

目前 code 看到 `addMoneyQueue` 會在非 OK callback 時重送 queue head，但 owner 角度要先判斷 center 是否已經處理。建議用 `gameSerialNo` / `serial_id` / `uid` 查 center mutation audit；若 center 已處理，應回填結果或清 queue，不應再做一次錢包 mutation。

## Q11：log server 掛了怎麼辦？

`LogController` 找不到 log server 時目前主要是記 error；`log_reel` 寫入和 center wallet update 不同 transaction。

面試回答可以說：

- 短期要有 error metric / alert。
- 中期要有 durable log queue 或 redis / local fallback。
- 長期要能用 game result + center mutation 補 `log_reel`，讓客服查單和報表可修復。

## Q12：這條 flow 的 owner 改善方案？

我會補三件事：

1. 三方 audit key：`uid + gameSerialNo / serial_id + gameId`。
2. 查單 view：game result、center mutation、log_reel 三欄狀態。
3. 重試 / 補償策略：center sync timeout 先 query，log missing 走 replay，不用重送 spin。

## Q13：Step 4 結論是什麼？

這條已可作正式面試 case，但仍不更新正式履歷 / 自傳。下一步 Step 5 才做單條 flow claim gate，檢查是否維持 interview-only，或能否回填 project-level claim。
