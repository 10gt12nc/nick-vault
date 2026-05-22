# Evidence：OneAPI / PG bet_result

任務：`iwin third_games_api oneapi-wallet-bet-result Step 5`
日期：2026-05-22
掃描等級：Level 2 Flow 深掃

Step 5 補充：本輪使用 Step 3 / Step 4 已確認的 code-backed evidence 做 flow-level claim gate，並重新確認 source repo remote refs、工作樹狀態、OneAPI path Nick / `10gt12nc` author log、下游 PGTransferInOut Nick / `10gt12nc` author log。公司 repo 只讀；未 pull、未 checkout、未 merge、未 rebase、未修改。

## Source Repo 狀態

### `/Users/nick/Git/iwin/third_games_api`

- 已執行：`git fetch --all --prune`
- local branch：`beta`
- local HEAD：`4915ea5a5000d61eb36717203ea4c6afc45322fa`
- remote HEAD：`origin/beta = 4915ea5a5000d61eb36717203ea4c6afc45322fa`
- ahead / behind：`0 / 0`
- working tree：乾淨
- 本輪只讀；未 pull、未 checkout、未 merge、未 rebase、未改 source repo。

### `/Users/nick/Git/iwin/iwin_gameserver`

- 已執行：`git fetch --all --prune`
- local branch：`main`
- local HEAD：`30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- remote HEAD：`origin/main = 30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- ahead / behind：`0 / 0`
- working tree：乾淨
- 本輪只讀；未 pull、未 checkout、未 merge、未 rebase、未改 source repo。

## 已重讀 Vault

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/iwin/third_games_api/README.md`
- `projects/iwin/third_games_api/step1-candidate-flows.md`
- `projects/iwin/third_games_api/step2-flow-comparison.md`
- `projects/iwin/third_games_api/contribution-claim-consolidation.md`
- `projects/iwin/third_games_api/flows/gsc-transfer-bet-settle-rollback/*`
- `projects/iwin/third_games_api/flows/oneapi-wallet-bet-result/flow.md`
- `projects/iwin/third_games_api/flows/oneapi-wallet-bet-result/career-interview.md`
- `projects/iwin/third_games_api/flows/oneapi-wallet-bet-result/materials/interview.md`
- `projects/iwin/third_games_api/flows/oneapi-wallet-bet-result/materials/claim-boundary.md`
- `projects/iwin/third_games_api/flows/oneapi-wallet-bet-result/materials/decision-notes.md`
- `projects/iwin/README.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/04-interview-casebook.md`
- `senior-owner-playbook/06-todo.md`

## 已看 Code Path

`third_games_api`：

- `src/main/java/com/slots/web/controller/OneApiController.java`
  - `settle`
  - `insertMongoLog`
  - `insertMongoTransaction`
  - `hasBetId`
  - `hasTransactionId`
  - `queryBalance`
- `src/main/java/com/slots/web/pojo/vo/oneapi/SettleVo.java`
- `src/main/java/com/slots/web/pojo/vo/oneapi/ThirdGameTransactionVO.java`
- `src/main/java/com/slots/web/common/constant/OneapiRedis.java`
- `src/main/java/com/slots/web/common/filter/WhiteListFilter.java`

`iwin_gameserver`：

- `slots-center/src/main/java/com/slots/center/service/HttpService.java`
- `slots-center/src/main/java/com/slots/sql/job/HttpPGTransferInOut.java`
- `slots-center/src/main/java/com/slots/center/job/http/PGTransferInOutJob.java`
- `slots-games/slots-game-common/src/main/java/com/slots/game/common/data/AddCenterCoinPG.java`
- `slots-center/src/main/java/com/slots/center/data/PlayerData.java`

## Git History Evidence

`third_games_api` path history：

- `540e76c feat(#oneapi): 處理sign問題`
- `298b93f feat(#oneapi): 串接oneapi PG 遊戲`
- `5e1e7c3 feat(#oneapi): 修改投注與派獎金額邏輯, 移除/rollback API`
- `00e2c2b feat(#效能): Oneapi, Antplay log優化, 效能優化`
- `9415c98 feat: 新增mongoDB欄位`

`third_games_api` Nick / `10gt12nc` author check：

- Step 5 重新針對 `OneApiController.java`、`oneapi` VO、`OneapiRedis.java` 查 Nick / `10gt12nc` author log，未看到 direct commit。
- 依既有 contribution consolidation，`third_games_api` 本 repo direct evidence 仍主要是 `AntplayController.java` 局部測試 / branch merge 線索。

`iwin_gameserver` downstream PG history：

- `a5fbcb3` / `b58eb58 feat(#PG): GSC+ PG`
- `2fcbde5 feat(#PG): log`
- `a889980 feat(#PG): bet_result 投派整合`
- `571b6fa feat(#PG): 退款`
- `73b2524 Revert "feat(#PG): 退款"`

判斷：

- OneAPI adapter 本身：`專案存在 / code-backed`，但未見 Nick direct evidence。
- PGTransferInOut downstream：Nick / `10gt12nc` direct evidence，歸屬 `iwin_gameserver`。

## Step 5 Claim Evidence

已確認：

- `third_games_api` OneAPI adapter path 沒有 Nick / `10gt12nc` direct commit evidence。
- OneAPI adapter path 有多筆 OneAPI / PG feature / sign / log / Redis 設定 commit，可支撐 `專案存在 / code-backed`。
- `iwin_gameserver` 下游 PG / `PGTRANSFERINOUT` / bet_result 投派整合 path 有 Nick / `10gt12nc` direct commits，可支撐 `iwin_gameserver` project claim。

Step 5 判斷：

- 本 flow 可作面試 case。
- 本 flow 不新增 `third_games_api` standalone 履歷成果。
- 本 flow 不直接更新 `05-resume-master-zh.md` / `08-application-autobiography-zh.md`。
- 若後續 Nick 補本人 MR / ticket / production issue / 本人確認，再重新評估是否升級 claim。

## Code-Backed Facts

### Provider contract / signature

- `OneApiController#settle` 接 `POST /wallet/bet_result`。
- 以 `LinkedHashMap` 固定欄位順序重建 JSON validation body。
- 用 `oneApiSecretKey` 做 HMAC-SHA256，比對 request header `X-Signature`。
- 驗簽失敗回 `SC_INVALID_SIGNATURE`。

### Basic checks

- 幣別只接受 `BRL`。
- `hasUsername(username)` 查 `third_log_oneapi`。
- `resultType = END` 且 `hasBetId(betId)` false 時回 `SC_TRANSACTION_NOT_EXISTS`。

### Duplicate check

- `hasTransactionId(transactionId)` 查 `bi_log.third_transaction_oneapi`，條件是 `transactionId + step = 1`。
- duplicate 命中後用 `queryBalance(betId, "bi_log", "third_transaction_oneapi")` 回既有 balance。
- source 掃描未看到 Mongo unique index / createIndex / ensureIndex。

### Money conversion and command

- `betAmount`、`winAmount`、`jackpotAmount`、`effectiveTurnover` 乘 `100000`。
- `WIN / BET_WIN / BET_LOSE`：`addMoney = win + jackpot - bet`。
- `LOSE`：`addMoney = 0`。
- command `PGTRANSFERINOUT` 送到 gameserver，包含 `accountId`、`addMoney`、`BetCoin`、`validBetCoin`、`spinCurrency`、`transactionId`、`gameId`、`betId`、`reason`、`createTime`。

### Gameserver boundary

- `HttpService#deal` dispatch `PGTRANSFERINOUT`。
- `HttpService#PGTransferInOut` 解析 command 並建立 `HttpPGTransferInOut`。
- `HttpPGTransferInOut` 查 `SqlPlayerData`，建立 `PGTransferInOutJob` 放入 game pool。
- `PGTransferInOutJob` 檢查玩家、封禁與餘額，透過 `modifyAndGetCoinPG` 修改錢包，之後通知事件、送戰績、送打碼 log。

### Mongo writes

- gameserver 成功後，`third_games_api` 寫：
  - `third_log_oneapi`
  - `third_transaction_oneapi`
- `third_transaction_oneapi` 會記錄 balance，供 duplicate response 使用。

## 已確認 / 推測 / 待確認

已確認：

- `transactionId` 是 adapter duplicate check 的主要 key。
- `betId` 用於 END 前置檢查與 query balance。
- gameserver 是 wallet mutation boundary。
- Mongo transaction 寫入在 gameserver 成功之後。
- `iwin_gameserver` 有 Nick / `10gt12nc` PG bet_result / PGTransferInOut direct commits。

推測：

- `third_transaction_oneapi` 更像 adapter audit / duplicate projection，不是 wallet ledger。
- `game_job` OneAPI backup / cleanup job 只保留資料，不代表交易補償。

待確認：

- OneAPI 官方 contract 對 `transactionId` / `betId` / `roundId` 的唯一範圍。
- `resultType` 全部合法值與狀態轉移。
- Mongo collection 是否有 production index / unique constraint。
- gameserver wallet mutation 前是否在其他層做 transaction duplicate guard。
- provider retry timeout 的 SLA 與重送規則。
- `game_job` 是否有 OneAPI reconciliation，而不只是 backup / cleanup。

## 不搬運 / 不寫入 Vault 的內容

- 不搬運 source repo 內的測試 URL、環境值、IP、secret、token。
- 不寫 provider secret / API key。
- 不把舊 workspace 敏感環境文件內容複製進 vault。

## 下一步

```text
iwin third_games_api gsc-seamless-withdraw-deposit-cancel Step 4
```
