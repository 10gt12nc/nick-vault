# iwin game_api Step 1：候選 Flow 盤點

更新時間：2026-05-15
掃描等級：Level 1 Flow 掃描
狀態：已建立
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 本次結論

`game_api` 是 iwin 玩家端 / partner API 的 Java Spring Boot API 層。它比 `app_bi` 更值得做 Senior / Owner flow，因為多條 flow 直接碰到玩家登入狀態、GM command、Mongo 訂單 / 佣金紀錄、MySQL / Redis / 動態分表查詢與 money correctness。

目前最值得延伸的候選 flow：

1. `coupon-redeem-credit-grant`：優惠券兌換上分與打碼要求，近期 commit 密集，money correctness 與 failure window 明顯，建議第一條深挖。
2. `partner-deposit-withdraw-bill`：partner API 上分 / 下分 / 查單，直接涉及金額、訂單狀態、GM command 與 Mongo 分日表。
3. `agent-bonus-receive-transfer`：代理佣金領取 / 轉帳，涉及 Mongo money balance、Redis projection、GM 上分與防重複提交。
4. `game-record-dynamic-table-query`：玩家 / partner 遊戲戰績查詢，涉及按日動態表、分頁、金額倍率與 troubleshooting。
5. `login-register-token-cache`：玩家登入註冊、token、Redis cache、停服白名單、shareinstall / 第三方登入，屬於高流量入口。

不更新履歷。沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認前，所有候選 flow 都只當 `專案存在 / code-backed` 或 `分析素材 / learning-only`。

## 自動重讀紀錄

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/CONVENTIONS.md`
- `projects/source-repo-inventory.md`

已重讀 vault：

- `projects/README.md`
- `projects/iwin/app_bi/README.md`
- `projects/iwin/app_bi/step1-candidate-flows.md`
- 確認 `projects/iwin/game_api/` 原本不存在，這輪新建 README 與 Step 1。

已重讀參考文件：

- `/Users/nick/Git/iwin/iwin-workspace/docs/專案分析/game_api.md`
- `/Users/nick/Git/iwin/iwin-workspace/docs/專案分析/game_api/kb/INDEX.md` 僅定位存在，未深讀。

已重讀 code repo：

- `/Users/nick/Git/iwin/game_api`
- 目前分支：`main`
- 遠端分支清單：已看到 `origin/main`、`origin/k3s`、`origin/coupon`、`origin/fix-performance`、`origin/beta`、`origin/feature/RD-128` 等。
- 近期主線 log：已看到 coupon 兌換、黑名單效能、loginBeforeAddr、shareinstall、health_check 等近期線索。
- path-specific log：已看 coupon、partner、login、agent share 相關主要檔案 log。

已看主要 code path：

- `src/main/java/com/slots/web/controller/*Controller.java`
- `src/main/java/com/slots/web/service/impl/LoginServiceImpl.java`
- `src/main/java/com/slots/web/service/impl/RegisteredServiceImpl.java`
- `src/main/java/com/slots/web/service/tbwebmanager/impl/CouponRedeemServiceImpl.java`
- `src/main/java/com/slots/web/service/partner/impl/PartnerServiceImpl.java`
- `src/main/java/com/slots/web/service/share/impl/AgentShareServiceImpl.java`
- `src/main/java/com/slots/web/service/impl/GameLogServiceImpl.java`
- `src/main/java/com/slots/web/service/impl/InvitationRankingServiceImpl.java`
- `src/main/resources/mapper/login/*.xml`
- `src/main/resources/mapper/bilog/InvitationFreeSpinsDao.xml`
- `src/main/resources/mapper/logone/*.xml`
- `src/main/resources/mapper/tbwebmanager/*.xml`

未重讀：

- 未 checkout 每個遠端分支。
- 未逐 commit diff。
- 未掃下游 GM command handler。
- 未掃 `iwin_gameserver`、`game_job`、`payment`、`third_games_api`。
- 未確認 Nick 個人 MR / ticket / production issue。

## 舊文件狀態判斷

| 文件 | 狀態 | 判斷 |
| --- | --- | --- |
| `projects/iwin/game_api/README.md` | 本輪新建 | 專案入口，保守標註履歷邊界 |
| `projects/iwin/game_api/step1-candidate-flows.md` | 本輪新建 | Level 1 candidate flow 盤點 |
| workspace 舊分析 `docs/專案分析/game_api.md` | 可參考 / 不搬運 | 有專案概覽與 API / DB / Redis 線索，但含舊環境資訊與敏感配置，不能直接複製進 vault |

## 掃描等級判斷

本次使用 Level 1。

原因：

- Nick 只指定 `iwin game_api`，目前 project 尚未在 vault 建立。
- Step 1 目標是找 Top 3-5 candidate flows，不是深挖單一 flow。
- `game_api` 候選很多，直接 Level 2 容易選錯 flow。
- Level 3 目前不值得，因為還沒選定 flow，也還沒確認 Nick 本人 evidence。

本次做：

- 建立 `game_api` 專案入口。
- 找出 Top 5 production flow 候選。
- 標清楚已確認、推測、待確認。
- 標清楚不可更新正式履歷。

本次不做：

- 不建立單條 flow folder。
- 不逐檔逐行掃完整 repo。
- 不逐 commit diff。
- 不更新履歷 / 自傳。
- 不把 `game_api` 包裝成 Nick 主開發成果。

## 本次掃描範圍

已看 repo：

- `/Users/nick/Git/iwin/game_api`
- `/Users/nick/Git/iwin/iwin-workspace` 的舊分析文件
- `/Users/nick/Git/nick/nick-vault`

source repo 狀態：

- `/Users/nick/Git/iwin/game_api`
- 目前分支：`main`

已看分支：

- 本地：`main`
- 遠端清單包含：`origin/main`、`origin/k3s`、`origin/coupon`、`origin/fix-performance`、`origin/beta`、`origin/bugs/write_black_list`、`origin/fix-ci-deploy`、`origin/feature/RD-128`、`origin/test`。

已看 git log：

- 近期主線 log，包含 coupon 兌換、health_check 白名單、黑名單效能、loginBeforeAddr、shareinstall、Google login、邀請活動等線索。
- path-specific log：`CouponRedeemServiceImpl.java`、`CouponRedeemController.java`、`AgentShareServiceImpl.java`、`LoginServiceImpl.java`、`PartnerServiceImpl.java`。

已看 code path：

- controllers：`LoginController`、`ConfigurationController`、`PartnerController`、`CouponRedeemController`、`ActivityController`、`InvitationFreeSpinsController`、`GameController`。
- services：`LoginServiceImpl`、`RegisteredServiceImpl`、`CouponRedeemServiceImpl`、`PartnerServiceImpl`、`AgentShareServiceImpl`、`GameLogServiceImpl`、`InvitationRankingServiceImpl`。
- mapper：login、bilog、logone、tbwebmanager 相關 XML。

## 專案定位

已確認：

- `game_api` 是 Java / Spring Boot API 專案。
- 對外入口包含玩家登入、SMS、帳號操作、登入前設定、遊戲紀錄查詢、partner API、優惠券兌換、代理分潤與邀請轉盤活動。
- 讀寫介質包含 MySQL、MongoDB、Redis，以及 GM command 對接。
- 多條 flow 的風險不是單一 CRUD，而是「API 層先寫紀錄 / 狀態，再呼叫下游 GM command 或更新 Redis projection」的一致性問題。

推測：

- `game_api` 很可能是 orchestration / API gateway-like service，不是錢包最終 source of truth。
- coupon、partner 上下分、代理佣金領取需要下游 game server / GM handler 才能完整判斷 transaction boundary。
- game record query 是查詢 / troubleshooting flow，高流量但 money side effect 較少。

待確認：

- 下游 GM command handler 實作位置與回傳語意。
- 金額倍率 `moneyRatio` 與幣別單位在各 flow 是否一致。
- Mongo / Redis / MySQL 更新失敗時是否有補償、對帳或人工修復流程。
- Nick 實際開發或維護 evidence。

## Top Candidate Flows

### 1. `coupon-redeem-credit-grant`

中文名稱：優惠券兌換上分 / 打碼要求
證據層級：專案存在 / code-backed；Nick 貢獻待確認
建議：第一條深挖 flow

為什麼重要：

- 直接呼叫 GM 上分與設定打碼要求，屬於 money correctness。
- 近期 commit 幾乎集中在「兌換碼功能」，有較高機率能追到完整演進脈絡。
- 有明顯 consistency / idempotency 題：檢查兌換資格、呼叫 GM、插入兌換紀錄、更新兌換次數之間不是同一個 transaction。

production 風險：

- GM 上分成功但 `coupon_record` 插入失敗，可能造成玩家拿到錢但系統認為未兌換。
- `coupon_record` 插入成功但更新設定 count 失敗，可能造成總量控制不準。
- 同 uid / 同 IP 限制是先查後寫，併發下可能有 race condition。
- 打碼要求與上分是兩次 GM command，中間失敗會造成 bonus 與 wagering requirement 不一致。

已確認 evidence：

- `CouponRedeemController#redeem`：`GET /coupons/redeem`，讀 `couponCode` 與 request IP。
- `CouponRedeemServiceImpl#redeemCoupon`：驗 Authorization token、查 Redis `loginToken`、檢查 coupon setting、uid / IP 兌換限制、查 `log_user`、呼叫 GM 上分與打碼、插入 `coupon_record`、更新 coupon count。
- `CouponRecordService` / `CouponSettingService` 操作 `tbwebmanager` 相關表。
- `LogUserDao#getLogUserByUid` 提供玩家與充值條件資料。
- 近期 path-specific log 幾乎都是 coupon 功能 commit。

推測：

- GM command 實際落點與帳本更新在下游 repo，不在 `game_api`。
- 目前沒有看到明確分散式鎖或唯一鍵保證，需要 Step 2 / Step 3 再確認 DB schema 與 DAO 實作。

待確認：

- `coupon_record` 是否有 unique constraint。
- `coupon_setting` count 更新是否有條件式更新或超額防護。
- GM command 是否同步成功才回傳，或只是 enqueue。
- 失敗後是否有補單 / 對帳流程。
- Nick 是否實際參與 coupon 功能。

履歷邊界：

- 潛力高，但目前不可寫進正式履歷。
- 可先作為 `專案存在 / code-backed` 的 money flow 分析素材。

### 2. `partner-deposit-withdraw-bill`

中文名稱：Partner API 上分 / 下分 / 查單
證據層級：專案存在 / code-backed；Nick 貢獻待確認
建議：高價值候選，Step 2 和 coupon 比較後再決定是否深挖

為什麼重要：

- 直接對 partner 暴露 `NewPay`、`Withdraw`、`BillInfo`、`BillList` 等 API。
- 涉及驗簽、玩家定位、金額倍率轉換、Mongo 訂單紀錄、GM command 與查單。
- 是典型 Senior Backend 會問的 idempotency、reconciliation、transaction boundary 題。

production 風險：

- 先插入 Mongo 訂單 status=1，再呼叫 GM，上分 / 下分成功與訂單狀態更新之間存在 failure window。
- catch `FmsServiceException` 會把訂單改 status=4；一般 Exception 分支目前需再確認是否也會更新失敗狀態。
- `billNos` 由日期加 UUID 產生，外部 partner 重送是否能 idempotent 待確認。
- 查單按分日 collection 查詢，跨日 / 分頁 / sort 可能有邊界。

已確認 evidence：

- `PartnerController` 有 `/api/NewPay`、`/api/Withdraw`、`/api/BillInfo`、`/api/BillList`。
- `PartnerServiceImpl#newPay`：驗參、產生 `billNos`、`saveCoin`、呼叫 `GM_CMD_DEPOSIT`、`updCoin(status=0)`。
- `PartnerServiceImpl#withdraw`：驗參、`saveCoin`、呼叫 `GM_CMD_WITHDRAW`、`updCoin(status=0)`。
- `PartnerServiceImpl#saveCoin`：寫 Mongo `bi_log.coin_change_log_{date}`，status 初始為 1。
- `PartnerServiceImpl#queryBillInfos`：按日期 collection 查詢訂單。

推測：

- 這條 flow 的真正 money source of truth 仍在 GM / game server。
- `coin_change_log` 可能是 partner 查單與補償依據，不一定是錢包帳本。

待確認：

- 驗簽與 replay 防護是否包含 timestamp tolerance / nonce。
- GM command 失敗、timeout、重試時的狀態語意。
- partner 重送同一筆 business order 的 idempotency 設計。
- 下游 wallet / game server 是否有對應 billNos 去重。

履歷邊界：

- 高價值，但目前不可寫成 Nick 成果。
- 若後續補到 Nick evidence，可能比 coupon 更接近 Platform Backend / partner integration 案例。

### 3. `agent-bonus-receive-transfer`

中文名稱：代理佣金領取 / 轉帳
證據層級：專案存在 / code-backed；Nick 貢獻待確認
建議：適合做 consistency / projection 案例，但需確認是否與 Nick 經歷相關

為什麼重要：

- 佣金可領金額、轉帳、領取後上分，直接涉及 money-like balance。
- 同時操作 Mongo `agent_money`、`agent_transfer_Money`、Redis `GameAgent:{rid}` projection、GM 上分。
- 有防重複提交與收益計算任務狀態檢查，適合分析 owner decision。

production 風險：

- `transferReceive` 更新轉出 / 轉入 Mongo 後再寫 Redis，中間失敗可能造成 projection mismatch。
- `bonusReceive` 呼叫 GM 上分後才清 Mongo money 與寫領取紀錄，中間失敗可能造成重領或帳面不一致。
- `checkBonusReceiveLook` 只看到 20 秒節流語意，是否足以防併發待確認。
- `@Transactional` 對 MongoDynamicTemplate / 多 DB / Redis / GM command 是否有效待確認。

已確認 evidence：

- `PartnerController` 有 `getDayFenx_transfer`、`getDayFenx_enter`、`getDayFenx_zhuanzhang`、`getFenx_money` 等代理分潤 API。
- `AgentShareServiceImpl#transferReceive`：檢查金額、檢查下線關係、更新轉出 / 轉入 Mongo、更新 Redis、插入轉帳紀錄。
- `AgentShareServiceImpl#bonusReceive`：查代理、查可領金額、呼叫 GM 上分、清 Mongo money、更新 Redis total_money、插入領取紀錄。
- `GameAgentDao.xml`、`AgentMoneyModel`、`BonusReceiveLogModel` 等提供資料模型線索。

推測：

- 日收益計算任務可能在 `game_job` 或其他 repo，這輪未掃。
- 佣金計算 source of truth 與 projection 邊界還不清楚。

待確認：

- 收益計算 job 與 `agent_money` 產生流程。
- Redis `GameAgent:{rid}` 是否只是 projection。
- Mongo update 是否有樂觀鎖 / 條件式更新。
- 領取失敗是否有補單與人工修復。

履歷邊界：

- 可作為 money-like consistency 學習素材。
- 目前不可說 Nick 設計或負責代理分潤。

### 4. `game-record-dynamic-table-query`

中文名稱：遊戲戰績動態分表查詢
證據層級：專案存在 / code-backed；Nick 貢獻待確認
建議：中高價值，適合作為 troubleshooting / observability flow

為什麼重要：

- 玩家端與 partner 都會查遊戲戰績，涉及高流量查詢與動態分表。
- 對 owner 來說重點是查詢正確性、跨日分頁、金額倍率、查不到資料時如何排查。
- 可和 `app_bi game-round-record-query` 互補。

production 風險：

- 按日期組 table name 查詢，跨日分頁、skip / limit、total page 計算容易出錯。
- `getChannel` 從 Redis QMD 取 channel 與 `moneyRatio`，Redis 缺資料可能影響查詢。
- 某些 catch 只 log error 後回傳 partial / empty total，錯誤可觀測性待確認。
- partner 查詢與玩家查詢對 uid / accountId / channel 的轉換規則不同。

已確認 evidence：

- `ConfigurationController#gameLog`、`#gameLogPoint`、`#fishingLog`。
- `PartnerController#GameRecord`、`#SpinInfo`、`#SumSpinInfo`。
- `GameLogServiceImpl#getGameLog`、`gameLogPoint`、`getpartnerGameLog`、`getspinInfoRes`、`getspinInfoResCount`。
- `LogReelYmdDao` / `LogReelPointYmdDao` 與 `mapper/logone/*` 動態 table 查詢。

推測：

- 真正寫入遊戲戰績的 producer 不在 `game_api`，可能在 `iwin_gameserver`。
- 此 flow 更偏查詢 / troubleshooting，不是交易 side effect。

待確認：

- writer repo 與 log table schema。
- 查詢日期範圍上限與保護機制。
- 表不存在、Redis channel 缺失、partial failure 的回傳策略。

履歷邊界：

- 適合面試講「如何追遊戲局紀錄與跨日分表查詢」。
- 目前不適合作為正式履歷主 bullet。

### 5. `login-register-token-cache`

中文名稱：玩家登入註冊 / token / Redis cache
證據層級：專案存在 / code-backed；Nick 貢獻待確認
建議：高流量入口候選，但 money correctness 不如前 3 條

為什麼重要：

- 登入註冊是高流量入口，影響所有後續 API。
- 涉及 Redis 防同時登入 / cache、黑名單、停服白名單、SMS 驗證、第三方登入、shareinstall attribution。
- 若要面 Platform Backend，這條可談 auth / cache / abuse prevention。

production 風險：

- `getRedisKey` 用 `setIfAbsent` 防同時登入，finally delete；timeout / 例外時的鎖語意需確認。
- 註冊會寫 DB、Mongo log、Redis account cache 與統計計數，跨資源一致性待確認。
- IP / IMEI 限制依 Redis / PHP settings，cache 與 DB truth source 邊界待確認。
- shareinstall / Google / Facebook 分支多，容易有 attribution 或帳號合併邊界。

已確認 evidence：

- `LoginController#appLogin`、`#smsLogin`。
- `LoginServiceImpl#login`：解密、停服白名單、checkLogin、loginJudgment、token、gameSocket、last login MQ。
- `RegisteredServiceImpl#paramMapping`：檢查 IP / IMEI 限制、寫註冊 log、insert account、save Redis、統計。
- 近期 log 有 shareinstall 與 Google login 修正線索。

推測：

- `sendLastLoginMQ` 與 Redis queue 需要再深掃，這輪未追完整。
- 登入 token 的安全性、過期與多端策略需要 Step 2 / Step 3 再確認。

待確認：

- token TTL、刷新、登出與多端登入策略。
- Redis key 與 DB account 的一致性修復方式。
- 黑名單來源與效能改善 commit 的完整背景。
- Nick 是否參與過登入 / shareinstall / 黑名單改善。

履歷邊界：

- 可作為高流量入口分析素材。
- 沒有 Nick evidence 前，不可寫成登入系統設計成果。

## 建議第一條深挖 Flow

建議先做 `coupon-redeem-credit-grant`。

理由：

- 它直接碰 money correctness：優惠券上分、打碼要求、兌換紀錄、兌換次數。
- 近期 commit 最集中，容易從 path-specific history 追出變更原因。
- `game_api` 內可見完整 API 到 service orchestration，下一步只需補下游 GM handler 與 DB constraint。
- 它比單純遊戲列表或登入前設定更有 Senior / Owner 面試價值。

不直接選 `partner-deposit-withdraw-bill` 的原因：

- partner flow 價值更高，但需要更早補下游 GM / wallet / partner idempotency evidence；Step 2 先比較會更穩。
- coupon 近期變更密集，適合先建立 `game_api` 第一條 flow 的寫法與 evidence 範圍。

## 下一步要讀的 Code Path

若做 Step 2：

- `CouponRedeemController.java`
- `CouponRedeemServiceImpl.java`
- `CouponRecordServiceImpl.java`
- `CouponSettingServiceImpl.java`
- `CouponRecordDao.java`
- `CouponSettingDao.java`
- `mapper/tbwebmanager/*Coupon*.xml`
- `PartnerServiceImpl.java`
- `AgentShareServiceImpl.java`
- `GameLogServiceImpl.java`
- `LoginServiceImpl.java`
- path-specific git log / important diffs

若 Step 2 選定 coupon 後做 Step 3：

- 追加掃 GM command：`GmIntfcComponent`、`GMCommandNames.GM_CMD_DEPOSIT`、`SET_BET_TARGET_COUPON`
- 追加掃下游 repo：`iwin_gameserver` 或實際 GM handler 所在 repo
- 追加確認 DB constraint：`coupon_record`、`coupon_setting`
- 追加確認是否有補單 / 對帳 / admin repair flow

## 本次不更新項目

不更新：

- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`
- `senior-owner-playbook/04-interview-casebook.md`
- project-level `career-interview.md`
- `flows/` 單條 flow folder

原因：

- 本次只是 Step 1 candidate flow 盤點。
- 還沒有完成單條 flow Level 2。
- 沒有 Nick 本人 evidence，不可更新正式履歷 / 自傳。

## 下一步建議

只推薦一件事：

```text
iwin game_api Step 2
```

原因：

- Step 1 已建立 Top 5 candidate flows。
- 下一步應比較 flow 價值、evidence 強度、履歷可轉換性與下游掃描成本。
- Step 2 會產出 `step2-flow-comparison.md`，仍不更新正式履歷 / 自傳。
- 需要 commit；不需要 push，除非 Nick 明確要求。
