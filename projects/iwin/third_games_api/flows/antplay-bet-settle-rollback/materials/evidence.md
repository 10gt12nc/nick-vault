# Evidence - Antplay bet / settle / rollback

## 本次掃描狀態

任務：`iwin third_games_api antplay-bet-settle-rollback Step 3`

掃描等級：Level 2 Flow 深掃。

完成狀態：Step 3 已完成。

證據層級：

- `專案存在 / code-backed`
- `分析素材 / learning-only`

Nick / `10gt12nc` 貢獻判斷：

- `third_games_api` Antplay path 只掃到局部測試 commit，不足以標成此 repo 真實開發過。
- `iwin_gameserver` Antplay path 有 Nick / `10gt12nc` direct commits，但歸屬 `iwin_gameserver` contribution claim，不反向包成 `third_games_api` direct contribution。

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

## Source repo 狀態

`/Users/nick/Git/iwin/third_games_api`

- 已執行 `git fetch --all --prune`。
- local branch：`beta`
- local HEAD：`4915ea5a5000d61eb36717203ea4c6afc45322fa`
- `origin/beta`：`4915ea5a5000d61eb36717203ea4c6afc45322fa`
- ahead / behind：`0 / 0`
- worktree：clean
- 本輪只讀，沒有 pull / checkout / merge / rebase / 改檔。

`/Users/nick/Git/iwin/iwin_gameserver`

- 已執行 `git fetch --all --prune`。
- local branch：`main`
- local HEAD：`30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- `origin/main`：`30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- ahead / behind：`0 / 0`
- worktree：clean
- 本輪只讀，沒有 pull / checkout / merge / rebase / 改檔。

## 已讀 code path

`third_games_api`：

- `src/main/java/com/slots/web/controller/AntplayController.java`
  - `@PostMapping("/bet")`
  - `@PostMapping("/settle")`
  - `@PostMapping("/rollback")`
  - `@PostMapping("/bet_settle-new")`，只作對照，不併入本 flow。
  - `insertMongo`
  - `hasBetId`
  - `queryBet`
  - `queryBetTime`
  - `moneyInoutGetBalance`

`iwin_gameserver`：

- `slots-center/src/main/java/com/slots/center/service/HttpService.java`
  - `ANTPLAY_BET`
  - `ANTPLAY_SETTLE`
  - `ANTPLAY_REFUND`
  - `antplaySettled(...)`
- `slots-center/src/main/java/com/slots/sql/job/HttpAntplaySettled.java`
- `slots-center/src/main/java/com/slots/center/job/http/AntplaySettledJob.java`
- `slots-center/src/main/java/com/slots/center/data/PlayerData.java`
  - `modifyAndGetCoinAntplay`
  - `buildCurrencyLogAntplay`
- `slots-games/slots-game-common/src/main/java/com/slots/game/common/data/GamePlayer.java`
  - `sendReelToLogAntplay`

## Path-specific history 摘要

`third_games_api` Antplay path 重要 commit 線索：

- `304a285 feat(#Antplay): iwin串接Antplay API`
- `4e46ae4 feat(#Antplay): 新增gameserver入參參數; 從MongoDB取得bet`
- `e5a3ffd feat(#Antplay): 修改請求入參content-type格式`
- `0122e8d feat(#Antplay):移除postmapping的consumes設定，以接收form資料`
- `e3b2e82 feat(#Antplay):結算/回滾從MongoDB拿betTime`
- `9415c98 feat: 新增mongoDB欄位`
- `00e2c2b feat(#效能): Oneapi, Antplay log優化, 效能優化`
- `95dce61 feat(#效能): redis或DB資料取出存內存`
- `6ce8fca feat(new):接antplay三方`
- `7d14dff feat(#372):Antplay停止对接商户API，改对接slot-game-api`
- `fe2ad16 feat(#372):修正vo內容;移除多餘log;保留串接三方Antplay的code`
- `51c98a1 feat:新增antplay mongoDB 的 createdTime欄位，作備份時間點的判斷`
- `89738a9 feat: add the currency as login request param`
- `a26e661 feat: add currency in sign for login request`
- `4915ea5 fix: add log`

Nick / `10gt12nc` 在 `third_games_api` Antplay path 掃到：

- `63e88f2 測試用`
- `ec9d812 測試用`

判斷：這兩筆只能當測試 / diagnostic 線索，不足以寫成 production owner 或正式履歷主成果。

下游 `iwin_gameserver` Antplay path 有多筆 Nick / `10gt12nc` direct evidence，例如 #144 Antplay gameserver 對接、refund / settle 修正、request log query、data fix、log reel 優化等；本輪只用來理解下游 boundary，claim 歸屬 `iwin_gameserver`。

## 已確認事實

- 舊三段式 endpoint 存在：`/antplay/bet`、`/antplay/settle`、`/antplay/rollback`。
- `bet` 驗簽字串包含 `merchantId + account + game + betId + bet + timestamp + key`。
- `settle` 驗簽字串包含 win fields 與 `status`。
- `rollback` 驗簽字串不含金額。
- `bet` 會先查 balance，再呼叫 gameserver `ANTPLAY_BET`。
- `settle` / `rollback` 會先查 Mongo step 1，再讀原始 bet。
- `settle` 呼叫 gameserver `ANTPLAY_SETTLE`。
- `rollback` 呼叫 gameserver `ANTPLAY_REFUND`。
- adapter 成功後才寫 Mongo log / transaction。
- gameserver `antplaySettled` 會依 type 1 / 2 / 3 計算扣款、派彩、退款，進 `AntplaySettledJob`。
- `modifyAndGetCoinAntplay` 會改玩家 coins 並寫 currency log。
- `sendReelToLogAntplay` 會依 bet / settle / refund 寫戰績。

## 未掃 / 待確認

- 未查 provider 官方 Antplay contract。
- 未確認 production 目前仍使用舊三段式 endpoint，或已轉新 `/bet_settle-new` / slot-game-api 對接。
- 未確認 Mongo `third_transaction_antplay` / `third_log_antplay` unique index。
- 未逐 commit diff 追完所有 Antplay 歷史改動。
- 未重掃 `game_job` 對 Antplay transaction / log 的備份、對帳或清理 job。
- 未確認 gameserver wallet mutation 前是否有 transaction-level duplicate guard。

## 本輪沒有做

- 沒有修改公司 source repo。
- 沒有更新 `05-resume-master-zh.md`。
- 沒有更新 `08-application-autobiography-zh.md`。
- 沒有把本 flow 標成 Nick 真實開發過。
