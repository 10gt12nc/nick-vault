# coupon-redeem-credit-grant evidence

更新時間：2026-05-19
Step：5
掃描等級：Level 2+ claim gate；Step 4 面試收斂後補 Nick path-specific history 與重要 diff
證據層級：真實開發過 + code-backed

## Step 5 更新摘要

2026-05-19 Step 5 重新 fetch `game_api` 與 `iwin_gameserver` remote refs，並掃 coupon path-specific history。結論從「缺 Nick evidence，暫不放履歷」改為：

- `真實開發過`：Nick / `10gt12nc` 參與 `game_api` coupon redeem flow 開發。
- `真實開發過`：Nick / `10gt12nc` 參與 `iwin_gameserver` coupon bet target handler 對接。
- 可作正式履歷 / 自傳候選 evidence；若採用只能用「參與 / 開發」口徑，不寫主導完整 coupon / reward system，且需先經 `game_api contribution claim consolidation`。

## Step 4 更新摘要

2026-05-15 Step 4 已將 Step 3 的 code-backed flow 收斂成面試案例，主要更新：

- `career-interview.md`：重寫為 Step 4 面試案例，補 30 秒、2 分鐘、5 分鐘版本、STAR 版本、反問面試官與 Step 5 待補。
- `materials/interview.md`：補 Step 4 前檢查、一句話版本、深講版、Senior 追問、Lead / Architect 追問與 Step 4 結論。
- `materials/claim-boundary.md`：補 Step 4 面試可說 / 不可說邊界。
- `README.md`、`step2-flow-comparison.md`、`flow.md`：下一步從 Step 4 更新為 Step 5。
- `senior-owner-playbook/04-interview-casebook.md`、`01-senior-owner-flow-inventory.md`：同步新增 / 更新此 flow 的面試案例索引。

Step 4 當時尚未補到 Nick path-specific evidence，因此不更新正式履歷 master，也不新增 `真實開發過` claim；此限制已由 2026-05-19 Step 5 claim gate 重新覆核並更新。

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
| `README.md` | 已同步 | Step 5 完成後已更新 flow 狀態與下一步 |
| `step1-candidate-flows.md` | 可沿用 | 候選 flow 盤點仍成立 |
| `step2-flow-comparison.md` | 已同步 | 已更新本 flow 狀態為 Step 5，下一步轉 `game_api contribution claim consolidation` |
| `flows/coupon-redeem-credit-grant/` | Step 5 已完成 | 使用新 flow package 結構；面試案例、evidence 與 claim boundary 已補齊 |

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
| `career-interview.md` | 已同步 | Step 5 後改為可保守作為 Nick 真實開發過的面試素材 |
| `materials/evidence.md` | 本輪補強 | 補上 KB 更新後覆核與最新 remote refs 狀態 |
| `materials/decision-notes.md` | 可沿用 | 已涵蓋 DB unique、idempotency key、state machine、Redis lock、reconciliation |
| `materials/interview.md` | 可沿用 | 已有面試開場、架構講法、failure window 與追問 |
| `materials/claim-boundary.md` | 已同步 | 已清楚標示可寫入正式履歷、可面試講與不可誇大的邊界 |

深度等級覆核：

- 本 flow 目前仍判定為 Level 2 Flow 深掃足夠。
- 不建議直接升 Level 3，因為 Step 5 已足以支撐保守正式履歷 claim；但尚未確認 production 實際部署 `origin/k3s` lock，也沒有 production incident / ticket evidence，可避免把履歷推到「防雙領 owner」或「完整 coupon owner」。
- 若未來要把這條 flow 轉成更強 production incident / owner claim，再建議 Level 3，重點應放在 coupon path-specific commit diff、下游 `billNo` 去重語意、production branch / deploy evidence 與 Nick 本人 ticket / MR / incident 證據。

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
| `origin/k3s` HEAD | `1dff58f5a4fdc5b95ec2a236d013e3b5b359cc61` |
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
| `origin/k3s` HEAD | `2519d64ccc11f9fade69cdefe23f225e571640c7` |
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

`game_api` path-specific log 顯示 coupon 相關 commit，且 author / committer 皆為 `10gt12nc <60815760+10gt12nc@users.noreply.github.com>`：

- `8683e32 feat(#): 兑换码功能`：新增 `CouponRedeemController`、`CouponRedeemServiceImpl`、`CouponRecordDao`、`CouponSettingDao`、mapper、DTO / entity、GM command name、LogUser 查詢等 coupon flow 主體。
- `c2dabf7 feat(#): 兑换码功能 ##`：調整 GM command 與 service 細節。
- `752f435 feat(#): 兑换码功能`：調整 `CouponRecord`、service 與 mapper 欄位。
- `39bb6e3 feat(#): 兑换码功能 CouponReason`：新增 `CouponReason` 並調整 service。
- 另有 `e52492e`、`3921fc0`、`35cc004`、`9843b15`、`89b698f`、`3d283d2`、`e9d63b1`、`d7d9ed3` 等 coupon 相關 commits。

重要 diff 摘要：

- `8683e32` 新增 coupon redeem API / service / DAO / mapper / entity，約 614 行，形成 `GET /coupons/redeem` 到 coupon setting / record / GM command 的主體。
- `39bb6e3` 新增 `CouponReason`，讓 coupon GM 上分 reason 不只是魔法數字。
- `752f435` 補 coupon record / LogUser / mapper 欄位，支撐後續 record 與玩家條件判斷。

`origin/k3s` 重要 diff：

- commit `7e4861e security: 移除 profile YML 明文密碼 + coupon 分散式鎖防雙倍兌換`
- 本文件只採 coupon service diff；不搬運 profile / config 內容。
- coupon service 新增 `coupon:lock:{uid}:{couponCode}`，用 `redisService.setIfAbsent(..., 10_000L)` 搶 lock，finally delete lock。

`iwin_gameserver` path-specific log 顯示 coupon 下游，且 author / committer 皆為 `10gt12nc <60815760+10gt12nc@users.noreply.github.com>`：

- `30a9fcb feat(#): 兑换码功能 betCnt`
- `6c99dd3 feat(#): 兑换码功能 #`

重要 diff 摘要：

- `6c99dd3` 新增 / 擴充 `SpinBetTargetConst` 與 `HttpService` coupon bet target command handler。
- `30a9fcb` 調整 `HttpService` coupon bet count / target count 處理。

### Step 5 claim 判斷

可升級為 `真實開發過`：

- Nick 參與玩家優惠券兌換上分 / 打碼要求 flow 開發。
- Nick 參與 `game_api` coupon API、service orchestration、DAO / mapper / entity 與 GM command 對接。
- Nick 參與 `iwin_gameserver` coupon bet target handler 對接 / 調整。

仍只能 code-backed / 不可誇大：

- `origin/k3s` Redis lock 防雙倍兌換 commit `7e4861e` 的 author 不是 `10gt12nc`，且有 Co-Authored-By Claude；不可寫成 Nick 設計 Redis lock 或修復 production 雙領事故。
- 未確認 production 實際跑 `origin/k3s` lock。
- 未確認下游 `billNo` 去重、完整 reconciliation、人工補償 dashboard 或 production incident。

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

## Step 5 結論

- 更新正式履歷 / 自傳：是，保守更新。
- 可寫：「參與玩家優惠券兌換上分 / 打碼要求 flow 開發，串接 `game_api` API、coupon setting / record、GM command 與 `iwin_gameserver` 下游打碼 handler。」
- 不可寫：主導完整 coupon / reward system、設計 Redis lock、修復 production 雙領 bug、完整 wallet / reconciliation owner、改善 X%。
