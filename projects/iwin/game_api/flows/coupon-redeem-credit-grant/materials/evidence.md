# coupon-redeem-credit-grant evidence

更新時間：2026-05-15
Step：3
掃描等級：Level 2 Flow 深掃
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 自動重讀紀錄

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/CONVENTIONS.md`

已重讀 vault：

- `projects/iwin/game_api/README.md`
- `projects/iwin/game_api/step1-candidate-flows.md`
- `projects/iwin/game_api/step2-flow-comparison.md`

既有文件狀態：

| 文件 | 狀態 | 判斷 |
| --- | --- | --- |
| `README.md` | 需同步 | Step 3 完成後需更新 flow 入口 |
| `step1-candidate-flows.md` | 可沿用 | 候選 flow 盤點仍成立 |
| `step2-flow-comparison.md` | 可沿用 | 已正確指向本 flow Step 3 |
| `flows/coupon-redeem-credit-grant/` | 本輪新建 | 使用新 flow package 結構 |

## Code Repo 最新狀態

### 2026-05-15 KB 更新後覆核

Nick 提醒 KB 已更新後，已依最新規則重新覆核：

- 已重新讀 `AGENTS.md`、`senior-owner-playbook/00-operating-rules.md`、`09-ai-prompt-manual.md`、`03-flow-learning-package-template.md`。
- 已重新確認多 session / staging area 規則；本次文件調整只限 `game_api` 三個檔案，不整理其他 project 的未提交變更。
- 已重新對 `game_api` 與 `iwin_gameserver` 執行 `git fetch --all --prune`，只更新 remote refs，未 pull / merge / checkout / rebase，未改公司 repo。
- 重新 fetch 後，兩個 source repo 的 local `main` 仍與 `origin/main` 同步，Step 3 code evidence 沒有因遠端更新而過期。

既有 Step 3 文件狀態判斷：

| 文件 | 狀態 | 判斷 |
| --- | --- | --- |
| `flow.md` | 可沿用 | 已有白話導讀、Code 分層、最小架構圖、正常流程圖、逐步說明與 Senior / Owner 分析 |
| `career-interview.md` | 可沿用 | 保守標示只能作面試學習素材，不寫 Nick 真實開發 |
| `materials/evidence.md` | 本輪補強 | 補上 KB 更新後覆核與最新 remote refs 狀態 |
| `materials/decision-notes.md` | 可沿用 | 已涵蓋 DB unique、idempotency key、state machine、Redis lock、reconciliation |
| `materials/interview.md` | 可沿用 | 已有面試開場、架構講法、failure window 與追問 |
| `materials/claim-boundary.md` | 可沿用 | 已清楚標示可說 / 不可說與需要補的 Nick evidence |

深度等級覆核：

- 本 flow 目前仍判定為 Level 2 Flow 深掃足夠。
- 不建議直接升 Level 3，因為尚未取得 Nick 本人 MR / ticket / production issue evidence，也未確認 production 實際部署 `origin/k3s` lock；直接逐檔逐行追完整 commit diff，對正式履歷 claim 的增益有限。
- 若下一步要把這條 flow 轉成更強 evidence 或正式履歷 claim，再建議 Level 3，重點應放在 coupon path-specific commit diff、下游 `billNo` 去重語意、production branch / deploy evidence 與 Nick 本人參與證據。

### `/Users/nick/Git/iwin/game_api`

已執行：

- `git fetch --all --prune`，並於 KB 更新後再覆核一次。
- 只更新 remote refs；未 pull、未 merge、未 checkout、未 rebase、未改公司 repo。

| 項目 | 值 |
| --- | --- |
| local branch | `main` |
| local HEAD | `39bb6e38210bb79c6e68a6a6d818cb87986d39f0` |
| `origin/main` HEAD | `39bb6e38210bb79c6e68a6a6d818cb87986d39f0` |
| local vs `origin/main` | ahead 0 / behind 0 |
| `origin/k3s` HEAD | `c43bba5a27493d28e4e8a00db27c08f98ca4fff2` |
| worktree | clean |

### `/Users/nick/Git/iwin/iwin_gameserver`

已執行：

- `git fetch --all --prune`，並於 KB 更新後再覆核一次。
- 只更新 remote refs；未 pull、未 merge、未 checkout、未 rebase、未改公司 repo。

| 項目 | 值 |
| --- | --- |
| local branch | `main` |
| local HEAD | `30a9fcb95bfda33b582deeb4e149eb06bed4afe3` |
| `origin/main` HEAD | `30a9fcb95bfda33b582deeb4e149eb06bed4afe3` |
| local vs `origin/main` | ahead 0 / behind 0 |
| worktree | clean |

## 本次實際掃描範圍

已掃 `game_api`：

- `src/main/java/com/slots/web/controller/CouponRedeemController.java`
- `src/main/java/com/slots/web/service/tbwebmanager/impl/CouponRedeemServiceImpl.java`
- `src/main/java/com/slots/web/service/tbwebmanager/impl/CouponRecordServiceImpl.java`
- `src/main/java/com/slots/web/service/tbwebmanager/impl/CouponSettingServiceImpl.java`
- `src/main/java/com/slots/web/data/dao/tbwebmanager/CouponRecordDao.java`
- `src/main/java/com/slots/web/data/dao/tbwebmanager/CouponSettingDao.java`
- `src/main/resources/mapper/tbwebmanager/CouponRecordDao.xml`
- `src/main/resources/mapper/tbwebmanager/CouponSettingDao.xml`
- `src/main/java/com/slots/web/data/dao/bilog/LogUserDao.java`
- `src/main/resources/mapper/bilog/LogUserDao.xml`
- `src/main/java/com/slots/web/pojo/entity/tbwebmanager/CouponRecord.java`
- `src/main/java/com/slots/web/pojo/entity/tbwebmanager/CouponSetting.java`
- `src/main/java/com/slots/web/common/gmintfc/service/GmIntfcComponent.java`
- `src/main/java/com/slots/web/common/gmintfc/gmcommand/GMCommandNames.java`
- `src/main/java/com/slots/web/service/impl/RedisServiceImpl.java`
- `src/main/java/com/slots/web/common/enums/RedisKeyEnums.java`
- `src/main/java/com/slots/web/common/constant/CouponReason.java`

已掃 `iwin_gameserver`：

- `slots-center/src/main/java/com/slots/center/service/HttpService.java`
- `slots-center/src/main/java/com/slots/sql/job/HttpNewBill.java`
- `slots-center/src/main/java/com/slots/center/job/http/NewBillJob.java`
- `slots-center/src/main/java/com/slots/center/data/PlayerData.java`
- `slots-center/src/main/java/com/slots/center/config/SpinBetTargetConst.java` 以 search 定位 `COUPON = 124`

已掃 workspace schema：

- `/Users/nick/Git/iwin/iwin-workspace/docs/iwin-dump-sql/46/tbwebmanager.sql`

未掃：

- 未掃 production DB。
- 未掃 runtime config、secret 或 production URL。
- 未掃客服補單 / 對帳後台。
- 未逐 commit diff 全掃所有 coupon 相關歷史；只看 path-specific log 與 `origin/k3s` 重點 diff。
- 未確認 Nick 本人 MR / ticket / production issue。

## 已確認 Evidence

### API 入口

- `CouponRedeemController#redeem`
- 路由：`GET /coupons/redeem`
- query：`couponCode`
- request：用 `GetIP.getIpAddr(httpReq)` 取得 IP
- 呼叫：`couponRedeemService.redeemCoupon(reqDto, httpReq)`

### Token 驗證

- `CouponRedeemServiceImpl#checkToken`
- 從 `Authorization` header 讀 token。
- 用 `AccountDecodeUtils.aesDecode(accessToken, appkey)` 解出 `openId`。
- Redis key pattern：`loginToken:{openId}`。
- Redis payload 裡的 `Token` 必須等於 access token。
- Redis payload 裡的 `UId` 是後續 uid。

### Coupon setting

- `CouponSettingDao.xml#getCouponSettingByCouponCode`
- SQL 條件：`coupon_code = ? and status = 1`
- `CouponSetting` 包含 `couponCode`、`userType`、`money`、`rebateMoney`、`usedCount`、`userList`、`validTime`、`status`。
- `isValid` 要求 setting 不為 null、`validTime` 不為 null、且尚未過期。

### Coupon record

- `CouponRecordDao.xml#getCouponRecordByCouponCodeAndUid`
- 查 `coupon_code + log_user_id` 判斷同 uid 同 coupon 是否已使用。
- `CouponRecordDao.xml#getCouponRecordByCouponCodeAndIP`
- 查 `coupon_code + ip` 判斷同 IP 同 coupon 使用數，service 限制 `>= 3` 就拒絕。
- `insertCouponRecord` 寫 `log_user_id`、`coupon_code`、`ip`、`money`、`cps`、`channel`、`created_at`。

### DB schema

從 workspace dump：

- `coupon_setting` 有 `UNIQUE KEY uk_coupon_setting_coupon_code (coupon_code)`。
- `coupon_record` 有：
  - `KEY idx_coupon_record_coupon_code (coupon_code)`
  - `KEY idx_coupon_record_coupon_code_user (coupon_code, log_user_id)`
  - `KEY idx_coupon_record_coupon_code_ip (coupon_code, ip)`
- `coupon_record(coupon_code, log_user_id)` 是普通 index，不是 unique key。

### Player eligibility

- `LogUserDao#getLogUserByUid`
- 讀 `account_id`、`uid`、`user_layer`、`center_id`、`first_recharge_time`、`first_recharge_amount`、`last_recharge_time`、`recharge_count`、`cps`、`channel`。
- `userType` 判斷：
  - `0`：全體用戶。
  - `1`：充值用戶。
  - `2`：當天充值用戶。
  - `3`：三天內充值用戶。
  - `4`：指定用戶，靠逗號分隔 `userList` 比對 uid。

### GM command

- `GmIntfcComponent#send` 會把 `cmd` 放入 content，依 `centerId` 從 `ZkService.centerMap` 找 center address，再 HTTP POST。
- HTTP code 200 且 response `Err == 0` 才回傳 success；否則 throw exception。
- `GMCommandNames.GM_CMD_DEPOSIT = "DEPOSIT"`。
- `GMCommandNames.SET_BET_TARGET_COUPON = "SET_BET_TARGET_COUPON"`。

### Downstream deposit

- `iwin_gameserver` `HttpService` case `"DEPOSIT"` 呼叫 `onDeposit(...)`。
- `onDeposit` 檢查 value > 0、type in 0/1。
- 建立 `HttpNewBill`，帶入 `accountId`、`userLayer`、`value`、`type`、`billNo`、`reason`、`isRecharge`、`isFirstRecharge`。
- `HttpNewBill#runImpl` 查 player data 後建立 `NewBillJob`，加入 game pool。
- `NewBillJob#modifyCoin` 會對 `type=0` 大廳餘額呼叫 `player.modifyAndGetCoinFromBill(...)`，並 push game coin log。

### Downstream bet target

- `HttpService` case `"SET_BET_TARGET_COUPON"` 呼叫 `setPlayerBetTargetCoupon(ctx, accountId, targetCnt)`。
- `setPlayerBetTargetCoupon` 找 `PlayerData`，呼叫 `pl.addWithDrawSpinNeeds(betTarget, SpinBetTargetConst.COUPON, 1, betTarget)`。
- `PlayerData#addWithDrawSpinNeeds` 讀 / 建 `SpinNeedData`，呼叫 `data.addBetTarget(...)`，再寫回 common ext `spinNeeds`。

### Branch history

`game_api` path-specific log 顯示 coupon 相關 commit：

- `39bb6e3 feat(#): 兑换码功能 CouponReason`
- `e52492e feat(#): 兑换码功能 urlWhites`
- `3921fc0 feat(#): 兑换码功能 method 'GET'`
- `35cc004 fix: 修正iP 提示语言`
- `752f435 feat(#): 兑换码功能`
- 多個 earlier coupon feature commits

`origin/k3s` 重要 diff：

- commit `7e4861e security: 移除 profile YML 明文密碼 + coupon 分散式鎖防雙倍兌換`
- 本文件只採 coupon service diff；不搬運 profile / config 內容。
- coupon service 新增 `coupon:lock:{uid}:{couponCode}`，用 `redisService.setIfAbsent(..., 10_000L)` 搶 lock，finally delete lock。

`iwin_gameserver` path-specific log 顯示 coupon 下游：

- `30a9fcb feat(#): 兑换码功能 betCnt`
- `6c99dd3 feat(#): 兑换码功能 #`

## 推測 / 待確認

推測：

- `game_api` 是 orchestration layer，不是 coupon 上分最終帳本。
- `iwin_gameserver` / `PlayerData` 更接近玩家錢包與打碼狀態 source of truth。
- `coupon_record` 是本地兌換 audit / idempotency evidence，但目前 DB 層沒有 unique guard。

待確認：

- 下游 `billNo` 是否有去重。如果沒有，重試 `DEPOSIT` 可能重複加錢。
- `NewBillJob` 修改玩家金額後的 durable persistence 時點與失敗語意。
- `SET_BET_TARGET_COUPON` 對離線玩家或 `findPlayer(accountId)` 為 null 時是否會 NPE 或回錯；本輪未深入測試。
- production 是否已部署 `origin/k3s` lock。
- Redis lock TTL 單位是否被團隊約定為秒；目前 `EX` 指向秒。
- 是否存在非 code 的補單 / reconciliation procedure。

## 本輪不更新正式履歷原因

- 沒有 Nick 本人 evidence。
- 雖然 flow code-backed，但目前只能作為學習與面試分析素材。
- Step 3 主要產出是 flow understanding，不是 final resume claim。
