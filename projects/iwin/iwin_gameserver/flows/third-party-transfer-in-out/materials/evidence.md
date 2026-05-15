# third-party-transfer-in-out Evidence

更新時間：2026-05-15
掃描等級：Level 2 單條 flow 深掃；Step 4 面試收斂
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## Step 4 更新摘要

2026-05-15 Step 4 已將 Step 3 的 code-backed flow 收斂成面試案例，主要更新：

- `career-interview.md`：補 30 秒、2 分鐘、5 分鐘版本、STAR 版本、反問面試官與 Step 5 待補。
- `materials/interview.md`：補深講版、常見追問、Senior 能力點與 Lead / Architect 延伸追問。
- `materials/claim-boundary.md`：補 Step 4 可說 / 不可說面試邊界。
- `README.md`、`step2-flow-comparison.md`、`flow.md`：下一步從 Step 4 更新為 Step 5。

Step 4 不更新正式履歷 master，不新增 `真實開發過` claim。

## 本輪重讀 KB

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/CONVENTIONS.md`

## 本輪重讀 vault

- `projects/iwin/iwin_gameserver/README.md`
- `projects/iwin/iwin_gameserver/architecture-map.md`
- `projects/iwin/iwin_gameserver/step1-candidate-flows.md`
- `projects/iwin/iwin_gameserver/step2-flow-comparison.md`
- `projects/iwin/iwin_gameserver/flows/third-party-transfer-in-out/flow.md`
- `projects/iwin/iwin_gameserver/flows/third-party-transfer-in-out/career-interview.md`
- `projects/iwin/iwin_gameserver/flows/third-party-transfer-in-out/materials/evidence.md`
- `projects/iwin/iwin_gameserver/flows/third-party-transfer-in-out/materials/interview.md`
- `projects/iwin/iwin_gameserver/flows/third-party-transfer-in-out/materials/claim-boundary.md`
- `projects/iwin/iwin_gameserver/flows/third-party-transfer-in-out/materials/decision-notes.md`
- `projects/iwin/third_games_api/README.md`
- `projects/iwin/third_games_api/step1-candidate-flows.md`

## Source repo 狀態

### iwin_gameserver

- 路徑：`/Users/nick/Git/iwin/iwin_gameserver`
- 已執行：`git fetch --all --prune`；Step 4 開始前再次執行確認
- local branch：`main`
- local HEAD：`30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- remote HEAD：`origin/main` = `30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- ahead / behind：`0 / 0`
- working tree：本輪觀察為乾淨
- 補充：fetch 後看到新增遠端分支 / tags；本輪未 checkout 或 merge
- Step 4 path-specific log 核對：`HttpService.java`、`PGTransferInOutJob.java`、`AntplayTransferInOutJob.java`、`GSCTransferInOutJob.java` 的近期相關 commit 包含 `4843791 fix(#373): antplay 支援投派整合 first`、`deee1b8 feat(#GSC): GSC`、`116e8ec feat(#GSC): GSC` 等；本輪只用來核對 Step 3 evidence，未做 Level 3 逐 commit diff。

### third_games_api

- 路徑：`/Users/nick/Git/iwin/third_games_api`
- 已執行：`git fetch --all --prune`；Step 4 開始前再次執行確認
- local branch：`beta`
- local HEAD：`4915ea5a5000d61eb36717203ea4c6afc45322fa`
- remote HEAD：`origin/beta` = `4915ea5a5000d61eb36717203ea4c6afc45322fa`
- ahead / behind：`0 / 0`
- working tree：本輪觀察為乾淨
- 掃描範圍：只做 upstream adapter 最小關聯掃描，不做完整 Step 3 主體

## iwin_gameserver 已掃 code path

### Command routing

- `slots-center/src/main/java/com/slots/center/service/HttpService.java`
  - `deal()` 有 `ANTPLAY_BET`、`ANTPLAY_SETTLE`、`ANTPLAY_REFUND`、`ANTPLAYTRANSFERINOUT`、`PGTRANSFERINOUT`、`GSC_BET`、`GSC_SETTLE`、`GSC_REFUND`、`GSC_OTHER`。
  - `PGTransferInOut` 讀 `accountId`、`addMoney`、`BetCoin`、`validBetCoin`、`spinCurrency`、`transactionId`、`gameId`、`betId`、`reason`、`createTime`。
  - `antplayTransferInOut` 讀 `accountId`、`addMoney`、`betCoin`、`validBetCoin`、`spinCurrency`、`transactionId`、`gameId`、`betId`、`reason`、`createTime`。
  - `GSCTransferInOut` 依 type 映射 bet / settle / refund / other，並設定 reason、`addMoney`、`betCoin`、`validBetCoin`、`spinCurrency`。

### Player lookup / job enqueue

- `HttpAntplayTransferInOut.java`
- `HttpPGTransferInOut.java`
- `HttpGSCTransferInOut.java`

已確認：

- wrapper 先 `DbproxyController.queryPlayerData(accountId, SqlPlayerData.class, accountId, callback)`。
- 找到玩家後建立實際 `*TransferInOutJob`，設定欄位後呼叫 `CenterWorld.getInstance().addGamePool(accountId, job)`。
- 找不到玩家時回 `PLAYER_NOT_FOUND`。

### Game pool ordering

- `slots-center/src/main/java/com/slots/center/CenterWorld.java`

已確認：

- `addGamePool(openId, job)` 使用 `gamePool.addTask(job, openId.hashCode())`。

待確認：

- 本輪未展開 game pool worker 實作，因此只能推測同 `openId.hashCode()` 是 per-account ordering 的核心，不把它寫成已完全證實。

### Money mutation jobs

- `slots-center/src/main/java/com/slots/center/job/http/AntplayTransferInOutJob.java`
- `slots-center/src/main/java/com/slots/center/job/http/PGTransferInOutJob.java`
- `slots-center/src/main/java/com/slots/center/job/http/GSCTransferInOutJob.java`

已確認：

- `runHttpTask()` 檢查玩家不存在、封禁。
- AP / PG 有 `coins < betCoin` 的前置餘額檢查；GSC 仍會進 `modifyCoin()`，由內部檢查負餘額。
- `sendMoneyChange2Center()` 順序是 `modifyCoin()` -> HTTP OK -> `afterEvent()` -> `sendReelToLog()` -> `sendBetLog()`。
- GSC type 4 只做錢包與通知，不送戰績與打碼；GSC type 2 才送打碼。

### Wallet state

- `slots-center/src/main/java/com/slots/center/data/PlayerData.java`

已確認：

- `modifyAndGetCoinPG`、`modifyAndGetCoinAP`、`modifyAndGetCoinGSC` 依 `addCoin - costCoin` 變更 `coins`。
- 會呼叫 `CenterWorld.addGlobalServerCoin(offsetCoin)`。
- 會建立 currency log 並推送 `LogController.pushLog(... CURRENCY ...)`。
- 會更新玩家 win、bet、valid bet、withdraw spin count、daily task、toplist / activity 類 side effect。

未確認：

- 未看到以 `transactionId` / `betId` 先查重再跳過 wallet mutation 的邏輯。
- 未完整追玩家 cache 寫回 dbproxy 的落庫時機。

### Reel / bet logs

- `slots-games/slots-game-common/src/main/java/com/slots/game/common/data/GamePlayer.java`
- `slots-game-log/src/main/java/com/slots/game/manager/ClientActionManager.java`
- `slots-game-log/src/main/java/com/slots/game/job/player/LogReelAntplayBetJob.java`
- `slots-game-log/src/main/java/com/slots/game/job/player/LogReelAntplaySettleJob.java`
- `slots-game-log/src/main/java/com/slots/game/job/player/LogReelGSCBetJob.java`
- `slots-game-log/src/main/java/com/slots/game/job/player/LogReelGSCSettleJob.java`
- `slots-center/src/main/java/com/slots/center/data/SpinNeedData.java`

已確認：

- `GamePlayer.sendReelToLogPG/AP/GSC` 組 `LogReelData`，欄位包含 `curr_currency`、`spin_currency`、`win_currency`、`last_user_currency`、`serial_id`。
- `ClientActionManager` 將 `REEL_NORMAL`、`REEL_NORMAL_ANTPLAY_BET/SETTLE/REFUND`、`REEL_NORMAL_GSC_BET/SETTLE` 導到不同 log job。
- GSC / 舊 Antplay bet job 用 batch insert，settle / refund job 用 batch update。
- `SpinNeedData.addBetTotalPG/AP/GSC` 會送 `PLAYER_BET_RECORD` 並更新 `spinNeeds`。

## third_games_api 最小關聯 evidence

已確認：

- `OneApiController` 會組 `PGTRANSFERINOUT`，欄位包含 `accountId`、`addMoney`、`BetCoin`、`validBetCoin`、`spinCurrency`、`transactionId`、`gameId`、`betId`、`reason`、`createTime`。
- `AntplayController` 會組 `ANTPLAYTRANSFERINOUT`，欄位包含 `accountId`、`addMoney`、`betCoin`、`validBetCoin`、`spinCurrency`、`transactionId`、`gameId`、`betId`、`reason`、`createTime`。
- `GscController` 會組 `GSC_BET`、`GSC_SETTLE`、`GSC_REFUND`，欄位包含 `account`、`gameId`、`betId`、`timestamp`、`sign` 與 bet / addMoney 類金額欄位。

注意：

- `CashServiceImpl.transferInOut` 有疑似測試 / legacy path，包含硬寫測試 account / URL 的跡象；本輪不將其納入主 flow，也不在主報告寫入任何實際 URL / 帳號。
- 本輪未追完整 provider spec、signature validation、adapter response mapping、timeout retry。

## 未掃 / 待確認

- 未 checkout `iwin_gameserver` 遠端分支。
- 未逐 commit diff 或 path-specific history；本輪不是 Level 3。
- 未掃全部 27 個 active game modules。
- 未掃 production logs、DB schema、線上 config。
- 未確認 `log_currency.bill_no`、`log_reel.serial_id` 是否有 unique index。
- 未確認 Nick 本人 MR / ticket / commit / production issue。
- 未確認 provider callback 是否保證同一 `transactionId` / `betId` 重送語意。

## Step 3 結論

`third-party-transfer-in-out` 可作為 Senior / Owner code-backed flow 素材，但目前只能標示為 `專案存在 / code-backed` 與 `分析素材 / learning-only`。它最有價值的討論點是 wallet mutation 與 log / bet record 不在同一 transaction、OK response 早於 downstream log 完成、以及 idempotency guard 未確認。
