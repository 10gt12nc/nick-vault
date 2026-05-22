# Evidence：game-spin-settlement-log-reel

## 掃描範圍

任務：`iwin iwin_gameserver game-spin-settlement-log-reel Step 3`
日期：2026-05-22
掃描等級：Level 2 Flow 深掃

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`

已重讀 vault：

- `projects/iwin/iwin_gameserver/README.md`
- `projects/iwin/iwin_gameserver/step1-candidate-flows.md`
- `projects/iwin/iwin_gameserver/step2-flow-comparison.md`
- `projects/iwin/iwin_gameserver/flows/third-party-transfer-in-out/**`
- `projects/iwin/iwin_gameserver/flows/center-http-deposit-withdraw/**`

source repo：

- repo：`/Users/nick/Git/iwin/iwin_gameserver`
- 已執行：`git fetch --all --prune`
- local branch：`main`
- local HEAD：`30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- remote HEAD：`origin/main = 30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- ahead / behind：`0 / 0`
- source working tree：乾淨

未掃：

- 未 checkout 每個遠端分支。
- 未逐一掃 27 個 active game modules。
- 未掃 app_bi 查詢端。
- 未跑測試。
- 未查 MR / ticket / production issue。

## 代表 game 選擇

本 Step 選 `slots-game40-sgj` 作代表。

理由：

- 是典型 slot spin path，有 `Game40SpinJob` 與 `Game40SpinUtil`。
- 能完整連到共同 runtime：`GamePlayer.addMoney()`、`sendMoneyChange2Center()`、`sendReelToLog()`。
- 能連到 center wallet：`GameToCenterSpinResultJob`。
- 能連到 log server：`LogReelJob`、`LogJobCrons`、`Mapper.batchInsertLogReel()`。

不選 27 個 game 一起掃的理由：

- 會變成平均整理 module，違反 KB。
- Step 2 已要求先挑代表 game，再進 Step 3。

## 已確認 code path

### Game40 entry

- `slots-games/slots-game40-sgj/src/main/java/com/slots/game/sgj/job/Game40SpinJob.java`
  - `runTask()` 取得 player 與 `Game40Pb.SpinRq`。
  - 呼叫 `Game40SpinUtil.spinJobRunTask(player, rq, packet.getSeq())`。

- `slots-games/slots-game40-sgj/src/main/java/com/slots/game/sgj/util/Game40SpinUtil.java`
  - `spinJobRunTask()` 做 validate、建立 `gameSerialNo`、`gameStartTimestamp`、控制池 loop。
  - `spin()` / `spinNormal()` / `spinStart()` 產生 reel window 與結果。
  - `rewardFromDarkPool()` 扣下注、派彩、回 client、組 log、送 `sendReelToLog()`。

### Game common wallet sync

- `slots-games/slots-game-common/src/main/java/com/slots/game/common/data/GamePlayer.java`
  - `addMoney(addMoney, BetCoin, validBetCoin, spinCurrency)`：先改遊戲端 `totalCoins`，再加入 `addMoneyQueue`。
  - `takeAddMoneyQueue2Center()`：queue head 未送出時呼叫 `sendMoneyChange2Center()`。
  - `sendMoneyChange2Center()`：送 `S2S_GAME_TO_CENTER_SPIN_RESULT` 到 center，callback 後更新 version / totalCoins 或重送。
  - `sendReelToLog()`：組 `Game_Log.LogReelData` 並透過 `LogController.pushLog()` 推 log server。

### Center wallet

- `slots-center/src/main/java/com/slots/center/factory/GameToCenterFactory.java`
  - register `S2S_GAME_TO_CENTER_SPIN_RESULT` -> `GameToCenterSpinResultJob`。

- `slots-center/src/main/java/com/slots/center/job/s2s/game/GameToCenterSpinResultJob.java`
  - 檢查 player disabled、server close、`PlayerGameData`、saveDataVersion。
  - 呼叫 `player.modifyAndGetCoin(rq.getCostCoin(), rq.getAddCoin(), rq.getGameId(), eCoinReason.GameSpin, rq.getGameStartTimestamp())`。
  - 更新 `GameRTP`、`win`、`bet`、`validBet`、`spinCurrency`、daily data、withdraw spin count、活動。
  - 回傳 `GameToCenterSpinResultRs`，含 coins / win / bet / validBet / spinCurrency / version。

### Log server

- `slots-common/src/main/java/com/slots/common/controller/LogController.java`
  - `pushLog(uid, eGameLogType, msg)` 加入 `LogPool`。
  - `TaskJob` 送 packet 到 log server node。
  - 找不到 log server 時只記 error。

- `slots-game-log/src/main/java/com/slots/game/job/player/LogReelJob.java`
  - 解析 `LogReelData`。
  - `writeToCache()` 後依日期分 table。
  - 呼叫 `Mapper.batchInsertLogReel()`。

- `slots-game-log/src/main/java/com/slots/game/LogJobCrons.java`
  - register `REEL_NORMAL` player proc。
  - 依 `gameStartTime` 寫 `log_reel_YYYYMMDD` 或 `log_reel_point_YYYYMMDD`。

- `slots-game-log/src/main/java/com/slots/game/sql/mapper/Mapper.java`
  - `batchInsertLogReel()` 寫入 `uid`、`game_id`、`serial_no`、`serial_id`、`curr_currency`、`spin_currency`、`win_currency`、`spin_bet`、`spin_rate`、`common_data`、`free_no`、`game_start_time`。

## Git / blame evidence

### Repo 最新性

- fetch 成功。
- `main` 與 `origin/main` 一致。
- source working tree 乾淨。

### Nick / 10gt12nc evidence

`git log --all --author='10gt12nc|Nick|nick' -- slots-games slots-game-log slots-center/.../SqlLogReelData.java` 顯示多筆 `10gt12nc` commit 與 log reel / third-party provider path 相關，例如：

- `49de246`：push log reel。
- `5f690ab`：戰績 log。
- `da7dc4b`：log reel 優化。
- `4843791` / `bb6bb9e`：Antplay 投派整合。
- `116e8ec`：GSC。
- `2fcbde5`：PG log。

判斷：

- 這些是 `iwin_gameserver` log reel / third-party provider integration 的強 supporting evidence。
- 不足以直接說 Nick 開發一般 `Game40SpinUtil` 主 spin flow。

### Path blame

已看 blame：

- `Game40SpinUtil.rewardFromDarkPool()` 的扣下注、派彩、log 組裝、`sendReelToLog()` 主要是 initial commit。
- `GamePlayer.addMoney()` 主要是 initial commit；其中 line 303 的 error log 有 `10gt12nc` 觸碰。
- `GamePlayer.sendReelToLog()` 一般 `REEL_NORMAL` path 主要是 initial commit。
- `GameToCenterSpinResultJob` 主要是 initial commit。
- `LogReelJob` 主要是 initial commit。

Step 3 結論：

- 本 flow 先標為 `專案存在 / code-backed`。
- 可以面試講 code path 與 owner 分析。
- 不更新履歷。

## 已確認

- `Game40SpinJob` 是代表 game spin request 入口。
- `Game40SpinUtil` 計算結果後會扣下注、派彩、送戰績。
- `GamePlayer.addMoney()` 會先更新 game runtime coins，再送 center sync。
- center 用 `GameToCenterSpinResultJob` 更新中心錢包與投注 / 有效投注 / 實際投注資料。
- `sendReelToLog()` 推 log server 寫 `log_reel`。
- center wallet update 與 log write 不在同一 transaction。

## 合理推論

- `gameSerialNo` / `serial_id` 是排查單局投注流水的重要 key。
- `saveDataVersion` 是防止舊 game data 覆蓋 center 狀態的核心 guard。
- `addMoneyQueue` 是同玩家 game -> center sync 的順序保護，不是完整 business idempotency。

## 待確認

- `serial_id` / `gameSerialNo` 是否有 DB unique 或其他 duplicate guard。
- log server node 不可用時是否有外部補 log / replay tool。
- center sync timeout 後，若 center 實際已更新但 callback 失敗，重送是否完全安全。
- app_bi 查詢端如何依 `log_reel` 顯示遊戲局紀錄。

## 下一步

```text
iwin iwin_gameserver bet-target-set-query Step 5
```

## Step 4 更新紀錄

日期：2026-05-22

本次未重新掃 source code；沿用 Step 3 已 fetch / 已確認的 source repo 狀態與 code path，將 Step 3 結論轉成正式面試 case。

已更新：

- `career-interview.md`
- `materials/interview.md`
- `flow.md` Step 4 結論

本次不更新：

- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

原因：

- Step 4 是面試 case，不是 claim gate。
- Nick direct contribution 仍待 Step 5 判斷。

## Step 5 掃描範圍

日期：2026-05-22
任務：`iwin iwin_gameserver game-spin-settlement-log-reel Step 5`
掃描等級：Level 2 + path-specific history / 重要 diff；未做全 repo Level 3 逐檔逐行。

source repo：

- repo：`/Users/nick/Git/iwin/iwin_gameserver`
- 已執行：`git fetch --all --prune`
- local branch：`main`
- local HEAD：`30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- remote HEAD：`origin/main = 30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- ahead / behind：`0 / 0`
- source working tree：乾淨

本次已掃：

- `git log --all --author='10gt12nc|Nick|nick'` over:
  - `slots-games/slots-game40-sgj`
  - `slots-games/slots-game-common/src/main/java/com/slots/game/common/data/GamePlayer.java`
  - `slots-center/src/main/java/com/slots/center/job/s2s/game/GameToCenterSpinResultJob.java`
  - `slots-game-log/src/main/java/com/slots/game/job/player/LogReelJob.java`
  - `slots-game-log/src/main/java/com/slots/game/LogJobCrons.java`
  - `slots-game-log/src/main/java/com/slots/game/sql/mapper/Mapper.java`
  - `slots-common/src/main/java/com/slots/common/controller/LogController.java`
- path-specific history for `Game40SpinJob.java`、`Game40SpinUtil.java`、`GamePlayer.java`、`GameToCenterSpinResultJob.java`、`LogReelJob.java`、`LogJobCrons.java`、`Mapper.java`。
- representative diffs:
  - `49de246`：Antplay push log reel，包含 `GamePlayer`、`LogJobCrons`、`Mapper` update path。
  - `5f690ab`：Antplay 戰績 log。
  - `da7dc4b`：Antplay log reel 優化，包含 bet / settle type、refund routing、mapper update。
  - `053e2be`：GSC settle log reel / mapper 優化。
  - `2fcbde5`：PG log wording / transfer-in-out log dispatch。
  - `4843791`：Antplay transfer-in-out / `sendReelToLogAP` / money job。
  - `9180fc9`：Java 21 / dependency WIP by `arnold`，有 touching Game40 / GamePlayer / LogReelJob，但不是 Nick direct evidence。

未掃：

- 未 checkout 每個遠端 branch。
- 未掃 MR / ticket / production incident。
- 未逐一深掃其他 26 個 game modules。
- 未驗證 DB unique index / production data。

## Step 5 path-specific evidence

### 一般 Game40 spin 主流程

`git log --all --oneline -- Game40SpinJob.java Game40SpinUtil.java` 只看到：

- `860f7b1 first commit`
- `9180fc9 feat(k3s): WIP ...`，author 為 `arnold`，屬 Java 21 / dependency migration touching，不是 Nick direct contribution。

判斷：

- `Game40SpinJob` / `Game40SpinUtil` 可作 code-backed 面試素材。
- 目前不能寫成 Nick 真實開發過一般 Game40 spin / settle 主流程。

### GamePlayer / log reel / provider projection

Nick / `10gt12nc` 的 direct commits 明確命中 provider log reel / 投派整合支線：

- `4843791` 新增 / 串 Antplay transfer-in-out、`sendReelToLogAP`、provider money job 與 log dispatch。
- `49de246` 啟用 / 調整 log reel update path，包含 `LogJobCrons` 的 `REEL_NORMAL_UPDATE` 與 `Mapper.batchUpdateLogReel()`。
- `5f690ab` 建立 Antplay 戰績 log path。
- `da7dc4b` 調整 Antplay bet / settle / refund 的 reel type、rebate / score / update routing。
- `053e2be` 調整 GSC settle log reel / mapper。
- `2fcbde5` 調整 PG transfer-in-out log dispatch / wording。

判斷：

- 這些 commits 能支撐「第三方 provider 投派整合與投注流水 / log reel projection」。
- 它們不能自動擴張成「一般 slot spin 主流程」或「全部 game runtime owner」。

## Step 5 claim gate 結論

- 本 flow 完成 Step 5。
- 一般 `Game40` spin / settle：維持 `專案存在 / code-backed`，不更新正式履歷 / 自傳。
- provider log reel / 投派整合：`部分真實開發過 + code-backed`，回填既有 project-level `iwin_gameserver` claim。
- `05` / `08` 本輪不直接更新；因為 rolling resume package 先前已吃 project-level consolidation，本輪只是補強既有 claim evidence，不新增履歷句。
