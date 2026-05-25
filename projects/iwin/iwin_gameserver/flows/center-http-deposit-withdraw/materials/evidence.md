# Evidence：center-http-deposit-withdraw

## 本次掃描範圍

任務：`iwin iwin_gameserver center-http-deposit-withdraw Step 3`
日期：2026-05-19
掃描等級：Level 2 單條 flow 深掃

## KB / vault 已重讀

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/iwin/iwin_gameserver/README.md`
- `projects/iwin/iwin_gameserver/step1-candidate-flows.md`
- `projects/iwin/iwin_gameserver/step2-flow-comparison.md`
- `projects/iwin/iwin_gameserver/architecture-map.md`
- `projects/iwin/iwin_gameserver/flows/third-party-transfer-in-out/**`

## Source repo 狀態

### `/Users/nick/Git/iwin/iwin_gameserver`

- 已執行 `git fetch --all --prune`
- local branch：`main`
- local HEAD：`30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- remote HEAD：`origin/main` = `30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- ahead / behind：`0 / 0`
- working tree：乾淨
- 本次只讀，未改公司 repo

### `/Users/nick/Git/iwin/payment`

- 已執行 `git fetch --all --prune`
- local branch：`k3s`
- local HEAD：`033ff3911eddf6200be6d2f1e5f3355329a8bb0a`
- remote HEAD：`origin/k3s` = `90ac8d56945a2baabd54e7b38c64ea67ad021f91`
- ahead / behind：`0 / 1`
- working tree：有既有未追蹤 `.DS_Store`
- 本次只讀，未 pull、未切分支、未改公司 repo
- 最新性邊界：本機 `payment` 工作樹落後遠端 1 commit，本次上游分析只代表 local HEAD 加 remote refs 狀態，不宣稱看完最新 working tree。

### `/Users/nick/Git/iwin/game_api`

- 已執行 `git fetch --all --prune`
- local branch：`main`
- local HEAD：`39bb6e38210bb79c6e68a6a6d818cb87986d39f0`
- remote HEAD：`origin/main` = `39bb6e38210bb79c6e68a6a6d818cb87986d39f0`
- ahead / behind：`0 / 0`
- working tree：乾淨
- 本次只讀，未改公司 repo

## 已讀主要 code path

### iwin_gameserver

- `slots-center/src/main/java/com/slots/center/service/HttpService.java`
- `slots-center/src/main/java/com/slots/sql/job/HttpNewBill.java`
- `slots-center/src/main/java/com/slots/center/job/http/NewBillJob.java`
- `slots-center/src/main/java/com/slots/center/job/http/BaseHttpPlayerGameJob.java`
- `slots-center/src/main/java/com/slots/center/data/PlayerData.java`

### payment

- `payment/src/main/java/cn/com/payment/service/impl/PayTypeServiceImpl.java`
- `payment/src/main/java/cn/com/payment/service/impl/withdraw/BaseServiceImpl.java`
- `base/module/src/main/java/cn/com/constant/Constant.java`

### game_api

- `src/main/java/com/slots/web/common/gmintfc/service/GmIntfcComponent.java`
- `src/main/java/com/slots/web/common/gmintfc/gmcommand/GMCommandNames.java`
- `src/main/java/com/slots/web/common/zookeeper/ZkService.java`
- `src/main/java/com/slots/web/service/partner/impl/PartnerServiceImpl.java`
- `src/main/java/com/slots/web/service/tbwebmanager/impl/CouponRedeemServiceImpl.java`

## Code evidence 摘要

### HTTP 入口

- `HttpService.onDeposit()`：
  - 驗 `value > 0`
  - 驗 `type` 只能是 0 / 1
  - 建立 `HttpNewBill`
  - 設定 `accountId`、`userLayer`、`value`、`type`、`billNo`、`reason`、`isRecharge`、`isFirstRecharge`
  - 呼叫 `job.runImpl()`

- `HttpService.onWithdraw()`：
  - 驗 `type` 只能是 0 / 1
  - 驗 `withdrawType` 只能是 1 / 2
  - `withdrawType=1` 時 `value > 0`
  - `withdrawType=2` 時從 `CenterWorld.findPlayer(accountId)` 取目前大廳 / 銀行餘額
  - 建立 `HttpNewBill`
  - 設定 `value=-value`
  - 呼叫 `job.runImpl()`

### SQL job wrapper

- `HttpNewBill.runImpl()`：
  - `DbproxyController.queryPlayerData(accountId, SqlPlayerData.class, accountId, callback)`
  - 查到 player 後建立 `NewBillJob`
  - 設定 bill / account / userLayer / value / type / reason / flags
  - `CenterWorld.getInstance().addGamePool(accountId, job)`
  - 查不到 player 時回 `PLAYER_NOT_FOUND`

### 實際錢包變更

- `NewBillJob.runHttpTask()`：
  - player null 回 `PLAYER_NOT_FOUND`
  - disabled 回 `PLAYER_DISABLED`
  - 內部帳號限制部分 reason / type
  - type 1 銀行：若在遊戲中先 `tryExitGame()`，否則 `modifyCoin()` 後 `afterEvent()`
  - type 0 大廳：
    - `value < 0 && isRecharge`：下分 / 提現，可能先退遊，再扣款、`withdraw()`、`afterEvent()`
    - `value > 0 && isRecharge`：充值，上分後 `recharge()`、`afterEvent()`
    - 其他：純資金變動，上分 / 下分後 `afterEvent()`

- `NewBillJob.modifyCoin()`：
  - 大廳加款：`player.modifyAndGetCoinFromBill(0, value, 0, reason, billNo, isRecharge)`
  - 大廳扣款：先檢查餘額，再 `player.modifyAndGetCoinFromBill(-value, 0, 0, reason, billNo, isRecharge)`
  - 銀行：`player.modifyAndGetBankCoin(value, reason, false, billNo, isRecharge)`
  - 成功後 `LogController.getInstance().pushLogOfGameCoin(...)`

### PlayerData

- `modifyAndGetCoinFromBill()` 只是包 `modifyAndGetCoin(...)`。
- `modifyAndGetCoin(...)`：
  - 檢查封禁、負參數、餘額不足。
  - 更新 global server coin。
  - 直接修改 `coins`。
  - 呼叫 `buildCurrencyLog(...)`。
- `modifyAndGetBankCoin()`：
  - 直接修改 `bankCoin`，必要時修改 `coins`。
  - 呼叫 `buildCurrencyLog(...)`。
- `buildCurrencyLog()`：
  - 使用 `GameLogUtils.convertCurrency(...)` 帶 `reason`、`billNo`、`isRecharge`。
  - `LogController.pushLog(...)` 例外只記 error，不回滾錢包變更。

## Path-specific history

### iwin_gameserver

`HttpService.java`、`NewBillJob.java`、`BaseHttpPlayerGameJob.java`、`PlayerData.java` 的 path-specific log 看到：

- `30a9fcb` / `6c99dd3`：`10gt12nc` coupon 相關修改。
- 多筆 `10gt12nc` 第三方遊戲整合、PG / GSC / Antplay 相關修改。
- 未直接確認 `10gt12nc` 是本 flow 原始作者或主要 owner。

判斷：

- 本 flow 目前屬於 `專案存在 / code-backed`。
- `game_api coupon` 與 `iwin_gameserver coupon` 有 Nick direct path evidence，但不自動等於 Nick 主導 center_http deposit / withdraw 全流。

### payment

`PayTypeServiceImpl.java`、`BaseServiceImpl.java` path-specific log 看到：

- `10gt12nc` 於 2026-05-14 有 payment / withdraw insert copied order id 清理相關 commit。
- 這可支援 payment project-level contribution consolidation 的局部 evidence，但本次不把它擴張成 Nick 主導完整 gameserver 上下分。

### game_api

`PartnerServiceImpl.java`、`CouponRedeemServiceImpl.java`、`GmIntfcComponent.java` path-specific log 看到：

- `10gt12nc` 有 coupon 相關 commits。
- coupon redeem 呼叫 `DEPOSIT` 是 Nick 已整理過的 stronger evidence，但 partner deposit / withdraw 不因此自動升級。

## 已確認

- `DEPOSIT` / `WITHDRAW` 最終都會走 `HttpNewBill -> NewBillJob -> PlayerData`。
- gameserver 會用 per-account game pool 序列化同玩家 job。
- `billNos` 有傳到 currency log。
- 本次沒看到 gameserver 層明確的 `billNos` duplicate guard。
- coin mutation、currency log、recharge / withdraw side effects、HTTP response 不是單一 DB transaction。

## 待確認

- `billNos` 在 log / DB 是否有 unique index 或其他防重。
- 上游 `payment` 最新 `origin/k3s` 多出的 1 commit 是否影響本 flow。
- 上游 `payment` / `game_api` 是否在所有入口都保證同一 bill 不會重送改錢。
- `PlayerData` cache 何時 flush 到 DB，以及 flush 失敗如何補償。
- `resetWithDrawLimit()` delete `spinNeeds` 是否會清除其他打碼目標，或 `SpinNeedData.resetBetTarget()` 本來就設計成全清。

## 本次不做

- 不更新正式履歷 / 自傳。
- 不宣稱 Nick 主導 gameserver wallet。
- 不修改公司 repo。
- 不 pull / merge / checkout 公司 repo。

## Step 4 掃描範圍

- 全域下一步狀態：目前沒有預設 project flow 下一步；請以 senior-owner-playbook/01-senior-owner-flow-inventory.md 與 senior-owner-playbook/06-todo.md 為準。
日期：2026-05-21
掃描等級：Level 2 文件復核 + Step 4 面試 case 整理；未做 Level 3 逐 commit diff

### KB / vault 已重讀

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/iwin/iwin_gameserver/README.md`
- `projects/iwin/iwin_gameserver/step2-flow-comparison.md`
- `projects/iwin/iwin_gameserver/career-interview.md`
- `projects/iwin/iwin_gameserver/contribution-claim-consolidation.md`
- `projects/iwin/iwin_gameserver/flows/center-http-deposit-withdraw/flow.md`
- `projects/iwin/iwin_gameserver/flows/center-http-deposit-withdraw/career-interview.md`
- `projects/iwin/iwin_gameserver/flows/center-http-deposit-withdraw/materials/interview.md`
- `projects/iwin/iwin_gameserver/flows/center-http-deposit-withdraw/materials/claim-boundary.md`
- `projects/iwin/iwin_gameserver/flows/center-http-deposit-withdraw/materials/decision-notes.md`

### Source repo 狀態：`/Users/nick/Git/iwin/iwin_gameserver`

- 已執行 `git fetch --all --prune`
- local branch：`main`
- local HEAD：`30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- remote HEAD：`origin/main` = `30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- ahead / behind：`0 / 0`
- working tree：乾淨
- 本次只讀，未改公司 repo

### Step 4 復核 code path

- `slots-center/src/main/java/com/slots/center/service/HttpService.java`
  - `cmd=DEPOSIT` / `cmd=WITHDRAW` dispatch。
  - `onDeposit()` 參數驗證與建立 `HttpNewBill`。
  - `onWithdraw()` 處理 `withdrawType=1/2` 與 value 轉負。
- `slots-center/src/main/java/com/slots/sql/job/HttpNewBill.java`
  - 查 `SqlPlayerData` 後建立 `NewBillJob`。
  - `CenterWorld.getInstance().addGamePool(accountId, job)`。
- `slots-center/src/main/java/com/slots/center/job/http/NewBillJob.java`
  - player / disabled / internal account checks。
  - type 0 大廳、type 1 銀行錢包修改。
  - 在遊戲中先 `tryExitGame()`。
  - `modifyCoin()` 後跑 `recharge()` / `withdraw()` / `afterEvent()`。
- `slots-center/src/main/java/com/slots/center/data/PlayerData.java`
  - `modifyAndGetCoinFromBill()`。
  - `modifyAndGetCoin()`。
  - `modifyAndGetBankCoin()`。
  - `buildCurrencyLog()`。

### Step 4 path-specific history 復核

`HttpService.java`、`HttpNewBill.java`、`NewBillJob.java`、`PlayerData.java` 的 `git log --all --` 仍顯示 Nick / `10gt12nc` 在 coupon、Antplay、GSC、PG、第三方投派整合相關 path 有 direct commits；本輪未看到新的 direct evidence 可把 `center-http-deposit-withdraw` 升級為 Nick 真實開發過。

### Step 4 結論

- 已把本 flow 轉成正式面試 case。
- 仍不更新 `05-resume-master-zh.md` / `08-application-autobiography-zh.md`。
- claim 維持 `專案存在 / code-backed`。
- 後續 Step 5 已完成 claim gate，結論維持 interview-only，不新增正式履歷 claim。

## Step 5 掃描範圍

任務：`iwin iwin_gameserver center-http-deposit-withdraw Step 5`
日期：2026-05-21
掃描等級：Level 2+ claim gate；重讀 code path、blame、path-specific history、Nick / `10gt12nc` commits 與代表 diff；未做 Level 3 全 branch 逐 commit diff

### KB / vault 已重讀

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/iwin/iwin_gameserver/README.md`
- `projects/iwin/iwin_gameserver/contribution-claim-consolidation.md`
- `projects/iwin/iwin_gameserver/flows/center-http-deposit-withdraw/flow.md`
- `projects/iwin/iwin_gameserver/flows/center-http-deposit-withdraw/career-interview.md`
- `projects/iwin/iwin_gameserver/flows/center-http-deposit-withdraw/materials/evidence.md`
- `projects/iwin/iwin_gameserver/flows/center-http-deposit-withdraw/materials/claim-boundary.md`

### Source repo 狀態

#### `/Users/nick/Git/iwin/iwin_gameserver`

- 已執行 `git fetch --all --prune`
- local branch：`main`
- local HEAD：`30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- remote HEAD：`origin/main` = `30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- ahead / behind：`0 / 0`
- working tree：乾淨
- 本次只讀，未改公司 repo
- remote refs 已確認：`origin/main`、`origin/k3s`、`origin/Nick-GSC_PG`、`origin/AntPlay-transferInOut`、`origin/Nick-review`、`origin/beta`、`origin/feature/RD-162`、`origin/feature/RD-188`、`origin/nick-LocalDevTest`、`origin/rate-script`

#### `/Users/nick/Git/iwin/payment`

- 已執行 `git fetch --all --prune`
- local branch：`k3s`
- local HEAD：`92dfec3c5f097f2927100ec1a892d7b1680d63c5`
- remote HEAD：`origin/k3s` = `92dfec3c5f097f2927100ec1a892d7b1680d63c5`
- ahead / behind：`0 / 0`
- working tree：只有既有未追蹤 `.DS_Store`
- 本次只讀，未改公司 repo

#### `/Users/nick/Git/iwin/game_api`

- 已執行 `git fetch --all --prune`
- local branch：`main`
- local HEAD：`39bb6e38210bb79c6e68a6a6d818cb87986d39f0`
- remote HEAD：`origin/main` = `39bb6e38210bb79c6e68a6a6d818cb87986d39f0`
- ahead / behind：`0 / 0`
- working tree：乾淨
- 本次只讀，未改公司 repo

### Step 5 code / history evidence

#### iwin_gameserver 主路徑 blame

- `HttpService` `DEPOSIT/WITHDRAW` dispatch：目前 blame 仍為初始 commit author。
- `HttpService.onDeposit()`：
  - 方法簽名行有 `10gt12nc` blame，代表 2024-08-27 Antplay 整合期間觸碰過此區域。
  - 方法內主要參數驗證、建立 `HttpNewBill`、set bill / reason / flags、`runImpl()` 仍為初始 commit author。
- `HttpService.onWithdraw()`：目前主體 blame 仍為初始 commit author。
- `HttpNewBill.runImpl()`：目前主體 blame 仍為初始 commit author。
- `NewBillJob.runHttpTask()` / `modifyCoin()`：目前主體 blame 仍為初始 commit author。
- `PlayerData.modifyAndGetCoinFromBill()` / `modifyAndGetCoin()` / `modifyAndGetBankCoin()`：目前主體 blame 仍為初始 commit author。

#### iwin_gameserver Nick / `10gt12nc` direct evidence

- `bdd7bb5` / `94b1605`：Antplay 對接 commits，新增 Antplay provider money hook / log / HTTP command context，並觸碰 `HttpService.onDeposit()` 周邊與 `PlayerData` provider wallet mutation hook。
- `4843791` / `bb6bb9e`、GSC / PG 系列 commits：支援第三方 provider 投派整合與 gameserver wallet / log projection claim。
- `6c99dd3` / `30a9fcb`：coupon / bet target supporting evidence，支援 `game_api coupon-redeem-credit-grant` 下游 gameserver context。

判斷：

- 這些 direct evidence 足以支撐 project-level 第三方 provider 投派整合 claim。
- 這些 direct evidence 不足以把 `center-http-deposit-withdraw` 升級成 Nick 真實開發完整上分 / 下分 flow。
- `onDeposit()` 方法簽名被 `10gt12nc` 觸碰，是局部 / supporting evidence；不能寫成「Nick 開發 center_http deposit」。

#### payment 上游 evidence

- `BaseServiceImpl.upperDeposit()` 仍是 `payment` 呼叫 gameserver `DEPOSIT` 的核心上游。
- `BaseServiceImpl` 也有 `WITHDRAW` caller path。
- `10gt12nc` 在 `payment` 的 path-specific log 命中 `clear copied order id before withdraw insert`、`clear copied order id before payment insert` 與 provider/order 維護類 commits。

判斷：

- 可支援 Nick 的 `payment` project-level 經驗。
- 可支援本 flow 面試時理解上游 order / gameserver wallet boundary。
- 不足以把 `iwin_gameserver center-http-deposit-withdraw` 標成 Nick 真實開發過。

#### game_api 上游 evidence

- `CouponRedeemServiceImpl` 會呼叫 `GM_CMD_DEPOSIT`，`PartnerServiceImpl` 有 partner `DEPOSIT/WITHDRAW` caller。
- `10gt12nc` 有 coupon 相關 direct commits。

判斷：

- 可支援 `game_api coupon-redeem-credit-grant` 的正式 evidence。
- partner deposit / withdraw 與本 flow 只能作 code-backed 面試素材，不升級為 Nick 真實開發完整上下分。

### Step 5 claim gate 結論

- 本 flow 完成 Step 5。
- 證據層級維持 `專案存在 / code-backed`。
- 可面試講：是。
- 可放正式履歷：否，不新增單獨 bullet。
- 05 / 08：本輪不更新。
- project-level `iwin_gameserver` 履歷 claim 保持既有第三方 provider 投派整合保守口徑，不擴張成完整 center_http 上分 / 下分 owner。
