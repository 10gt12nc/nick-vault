# Career Interview：game-spin-settlement-log-reel

狀態：Step 3 completed
證據層級：專案存在 / code-backed；Nick direct contribution 待 Step 5 判斷
履歷結論：本 Step 不更新正式履歷 / 自傳

## 這條 flow 可以怎麼講

這條可以當成「我能看懂遊戲 runtime 如何和 center wallet、log server 保持一致」的面試素材。

保守說法：

```text
我用一條典型 slot spin flow 追過 iwin_gameserver 的 runtime path。玩家送出 spin 後，game module 先驗證下注、產生轉輪結果，透過 GamePlayer.addMoney 把扣款 / 派彩送到 center，同時把戰績推到 log server 寫 log_reel。這條 flow 的重點不是單一演算法，而是 game runtime、center wallet 和 async log 的一致性邊界。
```

## 30 秒版本

```text
這條 flow 是玩家 spin 後，遊戲服怎麼完成扣款、派彩、同步 center 錢包和寫投注流水。我用 Game40 當代表樣本，主線是 Game40SpinJob -> Game40SpinUtil -> GamePlayer.addMoney -> GameToCenterSpinResultJob -> PlayerData.modifyAndGetCoin，另外透過 sendReelToLog 推到 LogReelJob 寫 log_reel。重點是它沒有跨 game / center / log 的單一 transaction，所以要看 version guard、addMoneyQueue、log async 失敗與對帳。
```

## 90 秒版本

```text
我會先把它拆成三段。第一段是 game runtime：Game40SpinJob 收到 SpinRq 後進 Game40SpinUtil，做下注驗證、產生 gameSerialNo、算 reel result。第二段是 wallet sync：GamePlayer.addMoney 先更新遊戲端 totalCoins，再把 AddCenterCoin 放進 queue，送 GameToCenterSpinResultRq 到 center；center 的 GameToCenterSpinResultJob 會檢查 PlayerGameData version，成功後更新 PlayerData 錢包、bet、validBet、spinCurrency、RTP 和活動資料。第三段是 log：遊戲端組 ReelLogDate 和 logExt，透過 LogController 推到 log server，最後由 LogReelJob / LogJobCrons 批次寫 log_reel。這裡最重要的風險是 center 錢包成功但 log 缺、center timeout 後重送、以及 version guard 不等於完整 business idempotency。
```

## 可講的 Senior 點

- `GamePlayer.totalCoins` 是遊戲端 runtime cache，center `PlayerData` 才是 money source of truth。
- `addMoneyQueue` 保護同一玩家 game -> center 的改錢請求順序。
- `PlayerGameData.saveDataVersion` 防止舊版本遊戲資料覆蓋 center 狀態。
- `sendReelToLog()` 是 async log path，不應被當成 wallet transaction 的一部分。
- `log_reel` 缺資料時，不能直接判斷玩家錢包錯，要查 center mutation 與 game result。

## 不能誇大的地方

不能說：

- 我主導一般 slot spin / settle 流程。
- 我負責所有 game module 的結算邏輯。
- 我設計了完整 gameserver wallet / log architecture。
- 我修過這條 flow 的 production incident。

原因：

- 本次 Step 3 代表樣本 `Game40SpinUtil`、`GameToCenterSpinResultJob`、`LogReelJob` 主要 path blame 仍是 initial commit。
- Nick / `10gt12nc` 的 direct evidence 目前較強的是第三方 provider 投派整合、GSC / PG / Antplay log reel 與 gameserver log projection，不是一般 game spin 主流程。

## 面試被問「是不是你做的」

保守回答：

```text
這條一般 slot spin 主流程我會用 code-backed 分析語氣，不把它說成我主導。我的 gameserver 強 evidence 在第三方 provider 投派整合和 log reel / wallet 串接；這條是我用來說明我能讀懂 game runtime、center wallet sync、log_reel 和一致性風險。
```

## Step 3 後下一步

```text
iwin iwin_gameserver game-spin-settlement-log-reel Step 4
```
