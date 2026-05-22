# Evidence：bet-target-set-query

## 掃描範圍

任務：`iwin iwin_gameserver bet-target-set-query Step 3`
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
- `projects/iwin/iwin_gameserver/contribution-claim-consolidation.md`
- 既有完成 flows：`third-party-transfer-in-out`、`center-http-deposit-withdraw`、`game-spin-settlement-log-reel`

source repo：

- repo：`/Users/nick/Git/iwin/iwin_gameserver`
- 已執行：`git fetch --all --prune`
- local branch：`main`
- local HEAD：`30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- remote HEAD：`origin/main = 30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- ahead / behind：`0 / 0`
- source working tree：乾淨

未掃：

- 未 checkout 每個遠端 branch。
- 未掃 app_bi / payment / game_api 上游完整 caller。
- 未查 MR / ticket / production incident。
- 未驗證 DB schema / unique index。

## 已確認 code path

### HTTP command

- `slots-center/src/main/java/com/slots/center/service/HttpService.java`
  - `SET_BET_TARGET`
  - `SET_BET_TARGET_COUPON`
  - `QUERY_BET_TARGET`
  - `setPlayerBetTarget()`
  - `setPlayerBetTargetCoupon()`
  - `queryPlayerBetTarget()`

### Player state

- `slots-center/src/main/java/com/slots/center/data/PlayerData.java`
  - `addWithDrawSpinNeeds(long spinNeeds, int targetType, int betRate, long betCnt)`
  - `addWithDrawSpinCount(long count, int gameId)`
  - `queryWithDrawBetTarget()`
  - `resetWithDrawLimit(int betTargetType, long money)`

### Domain object

- `slots-center/src/main/java/com/slots/center/data/SpinNeedData.java`
  - `betTarget`
  - `betTotal`
  - `addBetTarget()`
  - `addBetTotal()`
  - `queryPlayerBetTarget()`
  - provider variants：`addBetTotalAntplay()`、`addBetTotalGSC()`、`addBetTotalPG()`、`addBetTotalAP()`

### Reason const

- `slots-center/src/main/java/com/slots/center/config/SpinBetTargetConst.java`
  - `BI_HANDLE`
  - `COUPON`
  - `REST_BET_TARGET`
  - recharge / activity / vip / mail reasons

### Log projection

- `slots-game-log/src/main/java/com/slots/game/job/player/LogBetRecordJob.java`
- `slots-game-log/src/main/java/com/slots/game/LogJobCrons.java`
- `slots-game-log/src/main/java/com/slots/game/sql/mapper/Mapper.java`
- `GameEnum.eGameLogType.PLAYER_BET_RECORD`
- table：`log_spin_bet_YYYYMMDD`

## Git evidence

Nick / `10gt12nc` direct commits:

- `6c99dd3 feat(#):  兑换码功能 #`
  - 新增 `SET_BET_TARGET_COUPON`
  - 新增 `SpinBetTargetConst.COUPON`
  - `setPlayerBetTargetCoupon()` 初版呼叫 `addWithDrawSpinNeeds(..., COUPON, 1, 0)`
- `30a9fcb feat(#):  兑换码功能  betCnt`
  - 修正 coupon 設定，將 `betCnt` 從 `0` 改為 `betTarget`
  - 這讓 `PLAYER_BET_RECORD` 的本次 bet / target 變更紀錄更能反映 coupon 來源量

其他 supporting history：

- `GameToCenterSpinResultJob` 在一般 spin 成功後以 `rq.getBetCoin()` 呼叫 `player.addWithDrawSpinCount()`。
- Antplay / GSC / PG provider transfer-in-out job 也會呼叫 provider-specific `addBetTotal*()` 或 `addWithDrawSpinCount()`。

## 已確認

- `SET_BET_TARGET` 使用 `SpinBetTargetConst.BI_HANDLE`。
- `SET_BET_TARGET_COUPON` 使用 `SpinBetTargetConst.COUPON`。
- `QUERY_BET_TARGET` 回傳 `target = queryWithDrawBetTarget()`。
- `SpinNeedData.addBetTarget()` 會重設或累加 `betTarget`，並送 `PLAYER_BET_RECORD`。
- `SpinNeedData.addBetTotal()` 在有 target 時累加 `betTotal`，無 target 時只送 log。
- log server 會把 `PLAYER_BET_RECORD` 寫入 `log_spin_bet_YYYYMMDD`。

## 合理推論

- `spinNeeds` 是提款限制 / 打碼目標的主要 runtime rule state。
- `log_spin_bet` 是 audit trail，不是 rule source。
- coupon / BI / activity 都共用同一套 target / total state，source type 由 `SpinBetTargetConst` 區分。

## 待確認

- `commExt.spinNeeds` 的持久化與 flush 時機。
- `SET_BET_TARGET` 是否只允許在線玩家；若離線是否會 NPE 或由上游避免。
- 上游是否有 idempotency key，避免同一 coupon / 後台操作重送造成 target 重複累加。
- app_bi / payment / game_api 對 `QUERY_BET_TARGET` 的實際使用路徑。

## 下一步

```text
iwin third_games_api gsc-transfer-bet-settle-rollback Step 5
```

## Step 4 掃描補充

任務：`iwin iwin_gameserver bet-target-set-query Step 4`
日期：2026-05-22
掃描等級：Level 2 Flow 深掃補讀 / Step 4 面試 case

source repo：

- repo：`/Users/nick/Git/iwin/iwin_gameserver`
- 已執行：`git fetch --all --prune`
- local branch：`main`
- local HEAD：`30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- remote HEAD：`origin/main = 30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- ahead / behind：`0 / 0`
- source working tree：乾淨

本輪補讀：

- `HttpService.doCommand()` 對 `SET_BET_TARGET` / `SET_BET_TARGET_COUPON` / `QUERY_BET_TARGET` 的 command dispatch。
- `HttpService.setPlayerBetTarget()` / `setPlayerBetTargetCoupon()` / `queryPlayerBetTarget()`。
- `PlayerData.addWithDrawSpinNeeds()` / `addWithDrawSpinCount()` / `queryWithDrawBetTarget()`。
- `SpinNeedData.addBetTarget()` / `addBetTotal()` / provider-specific `addBetTotal*()` / `sendBetLog()`。
- `SpinBetTargetConst` 實際路徑：`slots-center/src/main/java/com/slots/center/config/SpinBetTargetConst.java`。
- `GameToCenterSpinResultJob` 一般 spin 成功後呼叫 `addWithDrawSpinCount()` 的路徑。
- `LogJobCrons` 對 `PLAYER_BET_RECORD` 的 log writer registration。
- path-specific Nick / `10gt12nc` history：`6c99dd3`、`30a9fcb`。

Step 4 新增判斷：

- Step 3 文件可沿用，不需重整；本輪把它轉為正式面試 case。
- `SpinBetTargetConst` 先前只用 reason enum 口語描述，本輪補正實際 code path。
- 面試稿明確區分 `SpinNeedData` rule state 與 `log_spin_bet` audit source。
- 面試稿明確標出 offline player、source idempotency、commExt persistence、log failure 與 reconciliation 是後續若要升級 claim 時待補 evidence。

未掃 / 待確認：

- 未掃 app_bi / payment / game_api 完整 caller。
- 未 checkout 每個遠端 branch。
- 未確認 MR / ticket / production incident。
- 未追 `commExt` DB persistence / flush 全路徑。
- 未確認 `SET_BET_TARGET` offline fallback 是否存在於其他 helper。

## Step 5 掃描補充

任務：`iwin iwin_gameserver bet-target-set-query Step 5`
日期：2026-05-22
掃描等級：Level 2 Flow 深掃補讀 / 單條 flow claim gate

source repo 狀態：

- `/Users/nick/Git/iwin/iwin_gameserver`
  - 已執行：`git fetch --all --prune`
  - local branch：`main`
  - local HEAD：`30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
  - remote HEAD：`origin/main = 30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
  - ahead / behind：`0 / 0`
  - source working tree：乾淨
- `/Users/nick/Git/iwin/game_api`
  - 已執行：`git fetch --all --prune`
  - local branch：`main`
  - local HEAD：`39bb6e38210bb79c6e68a6a6d818cb87986d39f0`
  - remote HEAD：`origin/main = 39bb6e38210bb79c6e68a6a6d818cb87986d39f0`
  - ahead / behind：`0 / 0`
- `/Users/nick/Git/iwin/payment`
  - 已執行：`git fetch --all --prune`
  - local branch：`k3s`
  - local HEAD：`92dfec3c5f097f2927100ec1a892d7b1680d63c5`
  - remote HEAD：`origin/k3s = 92dfec3c5f097f2927100ec1a892d7b1680d63c5`
  - ahead / behind：`0 / 0`
  - source working tree：有既有 untracked `.DS_Store`，本輪未修改
- `/Users/nick/Git/iwin/app_bi`
  - 已執行：`git fetch --all --prune`
  - local branch：`main`
  - local HEAD：`4a206a28ab8f5be4329602cdc510ee9ea41efb25`
  - remote HEAD：`origin/main = fd9881fc417e01f960d758b4b91ba1a10b507855`
  - ahead / behind：`0 / 4`
  - 本機落後遠端；本輪只用本地 working tree 與 local refs 作保守上游關聯，不宣稱已看 app_bi 最新 main

Step 5 補讀 evidence：

- `iwin_gameserver` path-specific Nick / `10gt12nc` commits：
  - `6c99dd3`：新增 `SET_BET_TARGET_COUPON`、`SpinBetTargetConst.COUPON` 與 coupon handler。
  - `30a9fcb`：修正 coupon handler 的 `betCnt`，從 `0` 改為 `betTarget`。
- `game_api` coupon caller：
  - `CouponRedeemServiceImpl` 兌換 coupon 後呼叫 `GMCommandNames.SET_BET_TARGET_COUPON`。
  - `GMCommandNames` 定義 `SET_BET_TARGET_COUPON`。
  - Nick / `10gt12nc` 在 coupon path 有多筆 direct commits，可支撐 coupon flow 上游與 gameserver coupon handler 是同一條素材線。
- `payment` query caller：
  - `PayTypeServiceImpl` 提款前送 `QUERY_BET_TARGET`，若 target > 0 則回 `NOT_TOUCH_TARGET_BET`。
  - 本輪只作 code-backed caller evidence；不升級 Nick payment 打碼查詢 direct claim。
- `app_bi` query / set caller：
  - `DamaDetail.php` 有 `QUERY_BET_TARGET` 查詢與 `SET_BET_TARGET` 人工調整入口。
  - app_bi 本機 main 落後 origin/main，且未見 Nick direct path evidence；只作操作面 / 上游關聯。

Step 5 claim gate 結論：

- `SET_BET_TARGET_COUPON` 入口：`真實開發過 + code-backed`。
- 完整 `bet-target-set-query` flow：`部分真實開發過 + code-backed`，但只有 coupon 入口可作 direct claim。
- `SpinNeedData` core rule、一般 BI、payment / app_bi 查詢：`專案存在 / code-backed`，可面試講，不寫正式履歷主成果。
- 正式履歷 / 自傳：本輪不直接更新 `05` / `08`；若未來回填，必須透過 project-level consolidation。
