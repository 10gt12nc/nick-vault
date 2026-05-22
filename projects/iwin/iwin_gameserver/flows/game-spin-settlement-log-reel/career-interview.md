# Career Interview：game-spin-settlement-log-reel

狀態：Step 5 completed
證據層級：一般 `Game40` spin 主流程為專案存在 / code-backed；第三方 provider log reel / 投派整合為部分真實開發過 + code-backed
履歷結論：不新增一般 slot spin / settle 履歷 claim；只回填既有 iwin_gameserver project-level 第三方 provider 投派整合 claim

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

## 3 分鐘版本

```text
我會用 Game40 當代表講一條一般 slot spin 的完整路徑。玩家送 SpinRq 進 Game40SpinJob，這個 job 只做入口檢查，真正邏輯在 Game40SpinUtil。它會驗證玩家狀態和下注倍率，建立 gameSerialNo 和 gameStartTimestamp，然後依 reel config 算出本局窗口、免費輪、獎項和控制池結果。

算完結果後，第一個關鍵是 wallet sync。遊戲端不是直接改 DB，而是透過 GamePlayer.addMoney 先改 runtime totalCoins，然後把 AddCenterCoin 放到 addMoneyQueue，再送 GameToCenterSpinResultRq 到 center。center 的 GameToCenterSpinResultJob 會檢查 PlayerGameData 的 saveDataVersion，成功後才用 PlayerData.modifyAndGetCoin 更新中心錢包，並同步更新 bet、validBet、spinCurrency、RTP、活動與提款打碼資料。center 回來後，遊戲端會更新 version，也會依 center coins 校正 runtime totalCoins。

第二個關鍵是 log_reel。Game40SpinUtil 會組 ReelLogDate 和 logExt，透過 GamePlayer.sendReelToLog 推 REEL_NORMAL 到 LogController，再由 slots-game-log 的 LogReelJob / LogJobCrons 批次寫 log_reel_YYYYMMDD。這個 log 是戰績、報表和客服查詢的來源，但它不是 wallet transaction。

所以我看這條 flow 時，重點不是「spin algorithm 怎麼寫」，而是三個 source 的一致性：game result、center wallet、log_reel。它沒有跨 game / center / log 的單一 transaction，保護點是 addMoneyQueue、saveDataVersion、center response 校正；風險點是 center timeout 後重送、center 成功但 log server 失敗、以及 log 缺資料時不能直接判定錢包錯。Owner 級補強會是用 gameSerialNo / serial_id / uid 串三方 audit，針對 logServer null、version mismatch、center sync timeout 做 metrics / alert，並提供補 log 或 reconciliation 查單工具。
```

## STAR 版本

- Situation：一般 slot spin 牽涉遊戲結果、玩家錢包、投注流水，任何一段失敗都可能造成玩家看到的結果、center 金額與報表戰績不一致。
- Task：用一條代表 flow 讀懂 game runtime 到 center wallet / log server 的一致性邊界，整理成可面試的 Senior case。
- Action：選 `slots-game40-sgj` 作代表，不平均掃 27 個 game；追 `Game40SpinJob -> Game40SpinUtil -> GamePlayer.addMoney -> GameToCenterSpinResultJob -> sendReelToLog -> LogReelJob`，拆出 runtime cache、center source of truth、async log 三段。
- Result：建立了 code-backed 面試案例，可以清楚講 version guard、addMoneyQueue、log async failure、center timeout 與 reconciliation；但不把它誇大成 Nick 主導一般 slot spin / settle。

## 可講的 Senior 點

- `GamePlayer.totalCoins` 是遊戲端 runtime cache，center `PlayerData` 才是 money source of truth。
- `addMoneyQueue` 保護同一玩家 game -> center 的改錢請求順序。
- `PlayerGameData.saveDataVersion` 防止舊版本遊戲資料覆蓋 center 狀態。
- `sendReelToLog()` 是 async log path，不應被當成 wallet transaction 的一部分。
- `log_reel` 缺資料時，不能直接判斷玩家錢包錯，要查 center mutation 與 game result。

## Senior / Lead 追問答法

### 如果 center timeout，要不要直接重送？

不能只說直接重送。這條 flow 裡 `addMoneyQueue` 會對非 OK callback 重送 queue head，但 owner 視角要先問 center 是否可能已處理。比較穩的答法是用 `gameSerialNo` / `serial_id` / `uid` 查 center mutation audit，再決定重送或補償；否則有機會把 timeout 當失敗，造成重複處理風險。

### 如果 `log_reel` 沒資料，是不是錢包就錯？

不是。`log_reel` 是戰績 / 報表 source，不是 wallet transaction source。要先查 center 的 `PlayerData` mutation、game result 與 log server delivery。缺 log 的修復方向是補 log / replay / 對帳，不是直接改玩家錢。

### `saveDataVersion` 可以解決什麼，不能解決什麼？

它能防止舊遊戲資料覆蓋 center 狀態，也能暴露 game / center 資料不同步。但它不能完全取代 business idempotency；例如同一局 `serial_id` 是否已處理、log 是否已寫入、timeout 後重送語意，仍需要 audit key 與查單能力。

### 為什麼不一次掃全部 game？

因為那會變成 class summary。正確做法是先用一款典型 slot 建立主 flow：入口、結果、wallet sync、log_reel。之後如果要比較特殊 game，再選有差異的 game 另開 flow，不把 27 個 module 平均攤開。

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

## Step 5 claim gate

這條 flow 可作正式面試 case，但正式履歷要保守：

- 可面試講：一般 slot spin 如何串 game runtime、center wallet、`log_reel`，以及 failure window / reconciliation。
- 可回填 project claim：Nick / `10gt12nc` 在 Antplay / GSC / PG 類 provider log reel、bet / settle / refund projection 與 transfer-in-out 串接有 direct commits。
- 不可寫：Nick 主導一般 `Game40` spin / settle、全部 game runtime、完整 wallet / log architecture。

面試被問「這是不是你做的」時，建議說：

```text
一般 Game40 spin 主流程我用 code-backed 分析語氣，不說成我主導；但我在 iwin_gameserver 的直接 evidence 是第三方 provider 投派整合與投注流水 / log reel 串接，這條 flow 是我用來說明我能把一般 game runtime、center wallet 和 log projection 的一致性邊界講清楚。
```

## Step 5 後下一步

```text
iwin third_games_api gsc-transfer-bet-settle-rollback Step 5
```
