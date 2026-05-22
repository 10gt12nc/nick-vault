# Evidence - GSC seamless withdraw / deposit / rollback / cancel

## 本次掃描狀態

任務：`iwin third_games_api gsc-seamless-withdraw-deposit-cancel Step 5`

掃描等級：Level 2 Flow 深掃。

完成狀態：Step 5 已完成。

證據層級：

- `專案存在 / code-backed`
- `分析素材 / learning-only`

Nick / `10gt12nc` 貢獻判斷：

- `third_games_api` GSC split endpoint path 未掃到 Nick / `10gt12nc` direct production commit。
- `iwin_gameserver` GSC / PG path 有 Nick / `10gt12nc` direct commits，但歸屬 `iwin_gameserver` contribution claim，不反向包成 `third_games_api` direct contribution。

## 自動重讀紀錄

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`

已重讀 project 文件：

- `projects/iwin/third_games_api/README.md`
- `projects/iwin/third_games_api/step1-candidate-flows.md`
- `projects/iwin/third_games_api/step2-flow-comparison.md`
- `projects/iwin/third_games_api/contribution-claim-consolidation.md`
- `projects/iwin/third_games_api/flows/gsc-transfer-bet-settle-rollback/`
- `projects/iwin/third_games_api/flows/oneapi-wallet-bet-result/`
- `projects/iwin/third_games_api/flows/antplay-bet-settle-rollback/`
- `projects/iwin/third_games_api/flows/gsc-seamless-withdraw-deposit-cancel/flow.md`
- `projects/iwin/third_games_api/flows/gsc-seamless-withdraw-deposit-cancel/career-interview.md`
- `projects/iwin/third_games_api/flows/gsc-seamless-withdraw-deposit-cancel/materials/interview.md`
- `projects/iwin/third_games_api/flows/gsc-seamless-withdraw-deposit-cancel/materials/decision-notes.md`

## Step 4 更新紀錄

本 Step 4 做：

- 將 `career-interview.md` 從草稿升級為正式面試 case。
- 將 `materials/interview.md` 補成 30 秒、90 秒、3 分鐘、STAR、Senior / Lead 追問與回答。
- 補 `decision-notes.md` 的面試轉譯重點。
- 同步 README、Step 文件、inventory、todo 與 casebook。

本 Step 4 不做：

- 不更新 `05-resume-master-zh.md`。
- 不更新 `08-application-autobiography-zh.md`。
- 不把本 flow 標成 Nick 真實開發過。
- 不宣稱 split endpoint 是目前 production 主線。

## Step 5 更新紀錄

本 Step 5 做：

- 重新讀 KB、project README、Step 2、既有 flow / career / evidence / claim 文件。
- 重新 fetch `/Users/nick/Git/iwin/third_games_api` 與 `/Users/nick/Git/iwin/iwin_gameserver` remote refs。
- 重查 `GscController.java` GSC split endpoint path-specific history、GSC keyword history、Nick / `10gt12nc` author history。
- 重查下游 `iwin_gameserver` GSC command / wallet mutation path history，確認 direct evidence attribution。
- 將 claim gate 結論回填 `flow.md`、`career-interview.md`、`materials/claim-boundary.md`、README、Step 2、casebook、inventory 與 todo。

本 Step 5 不做：

- 不更新 `05-resume-master-zh.md`。
- 不更新 `08-application-autobiography-zh.md`。
- 不把本 flow 標成 Nick 真實開發過。
- 不把 `iwin_gameserver` direct commits 反向包裝成 `third_games_api` direct contribution。

## Source repo 狀態

`/Users/nick/Git/iwin/third_games_api`

- 已執行 `git fetch --all --prune`。
- local branch：`beta`
- local HEAD：`4915ea5a5000d61eb36717203ea4c6afc45322fa`
- `origin/beta`：`4915ea5a5000d61eb36717203ea4c6afc45322fa`
- ahead / behind：`0 / 0`
- worktree：clean
- 本輪只讀，沒有 pull / checkout / merge / rebase / 改檔。

Step 5 重新確認：

- 已再次執行 `git fetch --all --prune`。
- local branch：`beta`
- local HEAD：`4915ea5a5000d61eb36717203ea4c6afc45322fa`
- `origin/beta`：`4915ea5a5000d61eb36717203ea4c6afc45322fa`
- ahead / behind：`0 / 0`
- 本輪只讀，沒有 pull / checkout / merge / rebase / 改檔。

`/Users/nick/Git/iwin/iwin_gameserver`

- 已執行 `git fetch --all --prune`。
- local branch：`main`
- local HEAD：`30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- `origin/main`：`30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- ahead / behind：`0 / 0`
- worktree：clean
- 本輪只讀，沒有 pull / checkout / merge / rebase / 改檔。

Step 5 重新確認：

- 已再次執行 `git fetch --all --prune`。
- local branch：`main`
- local HEAD：`30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- `origin/main`：`30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- ahead / behind：`0 / 0`
- 本輪只讀，沒有 pull / checkout / merge / rebase / 改檔。

## 已讀 code path

`third_games_api`：

- `src/main/java/com/slots/web/controller/GscController.java`
  - `@RequestMapping("/api/seamless/withdraw")`
  - `@RequestMapping("/api/seamless/deposit")`
  - `@RequestMapping("/api/seamless/rollback")`
  - `@RequestMapping("/api/seamless/cancel")`
  - `insertMongo`
  - `hasBetId`
  - `queryBet`
  - `moneyInoutGetBalance`

`iwin_gameserver`：

- `slots-center/src/main/java/com/slots/center/service/HttpService.java`
  - `GSC_BET`
  - `GSC_SETTLE`
  - `GSC_REFUND`
  - `GSC_OTHER`
  - `GSCTransferInOut(...)`
- `slots-center/src/main/java/com/slots/sql/job/HttpGSCTransferInOut.java`
- `slots-center/src/main/java/com/slots/center/job/http/GSCTransferInOutJob.java`
- `slots-center/src/main/java/com/slots/center/data/PlayerData.java`
  - `modifyAndGetCoinGSC`
  - `buildCurrencyLogGSC`
- `slots-games/slots-game-common/src/main/java/com/slots/game/common/data/GamePlayer.java`
  - `sendReelToLogGSC`

## Path-specific history 摘要

`third_games_api` GSC path 重要 commit 線索：

- `e8edc0d feat(#new):串接GSC_PG遊戲`
- `575db56 fea(#GSC):修改回調路徑,串接/transfer API`
- `e4a2bba feat(#GSC): 增加判斷條件符合PG 驗證`
- `7fde77c feat(#GSC):新增pushbet API; 新增/transfer rollback判斷; 新增stage的login`
- `329279c feat(#GSC):game code設126, 測試後改回動態取得`
- `7f6bc3a feat(#GSC):移除TODO, gameId動態取得`
- `08340d0 feat:create the GSC MongoDB column of createdTime for backup`
- `4915ea5 fix: add log`

Step 5 判斷：

- GSC split endpoint 主體 commit author 不是 Nick / `10gt12nc`。
- `git log --all --author='Nick|10gt12nc' -- GscController.java` 未命中。
- `575db56` commit message 指出回調路徑改成 `/api/seamless/*`，也指出實測結果是投派整合，需串接 `/transfer` API；因此 split endpoint production status 需保守標示待確認。

`iwin_gameserver` GSC / PG / Antplay path 有多筆 Nick / `10gt12nc` direct evidence，例如 GSC + PG、PG bet_result、GSC / Antplay log reel、投派整合等；本輪只用來理解下游 boundary，claim 歸屬 `iwin_gameserver`。

## 已確認事實

- `/api/seamless/withdraw` 會驗 `operatorCode + requestTime + "withdraw" + key`，檢查 step 1 duplicate，成功後呼叫 `GSC_BET`。
- `/api/seamless/deposit` 會驗 `operatorCode + requestTime + "deposit" + key`，要求 step 1 存在，檢查 step 2 duplicate，成功後呼叫 `GSC_SETTLE`。
- `/api/seamless/rollback` 會驗 `operatorCode + requestTime + "rollback" + key`，要求 step 1 存在，檢查 step 3 duplicate，成功後呼叫 `GSC_REFUND`。
- `/api/seamless/cancel` 會驗 `operatorCode + requestTime + "cancel" + key`，要求 step 1 存在，檢查 step 3 duplicate，成功後呼叫 `GSC_OTHER`。
- `cancel` endpoint 中 `action == wager_status` 檢查目前被註解。
- `hasBetId(betId)` 查 `third_transaction_gsc` 的 `betId + step = "1"`。
- `hasBetId(betId, action, step)` 查 `betId + step + action`。
- adapter 成功呼叫 gameserver 後才寫 Mongo log / transaction。
- gameserver `GSCTransferInOut` 依 type 1 / 2 / 3 / 4 對應扣款 / 派彩 / 退款 / other。
- `GSCTransferInOutJob` 會呼叫 `modifyAndGetCoinGSC` 改玩家 coins，並推 GSC reel / bet log。

## 未掃 / 待確認

- 未查 provider 官方 GSC split endpoint contract。
- 未確認 production 目前仍使用 split endpoint，或已由 `/api/seamless/transfer` 取代。
- 未確認 Mongo `third_transaction_gsc` / `third_log_gsc` unique index。
- 未逐 commit diff 追完所有 GSC 歷史改動。
- 未重掃 `game_job` 對 GSC transaction / log 的備份、對帳或清理 job。
- 未確認 gameserver wallet mutation 前是否有 transaction-level duplicate guard。

## 本輪沒有做

- 沒有修改公司 source repo。
- 沒有更新 `05-resume-master-zh.md`。
- 沒有更新 `08-application-autobiography-zh.md`。
- 沒有把本 flow 標成 Nick 真實開發過。

## Step 5 Claim Gate 結論

- 可放履歷：不新增 `third_games_api` standalone 正式履歷成果。
- 可面試講：保留為 code-backed 正式面試 case，可講 split endpoint 狀態機、adapter Mongo evidence、gameserver wallet boundary、failure window、observability 與 `/transfer` 演進對照。
- 不可誇大：不說 Nick 主導 GSC split endpoint、不說 split endpoint 是現行 production 主線、不說已修復 production 錯帳或建立完整 idempotency / reconciliation。
- 後續回填：若未來補到 Nick 本人確認、MR / ticket / commit / incident evidence，再回到 project-level contribution consolidation 與 05 / 08 rolling package。
