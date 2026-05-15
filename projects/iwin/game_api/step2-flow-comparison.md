# iwin game_api Step 2：候選 Flow 比較

更新時間：2026-05-15
掃描等級：Level 1.5 / Step 2 比較掃描
狀態：已建立
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 本次結論

建議第一條深挖：

```text
coupon-redeem-credit-grant
```

原因：

- 它是 `game_api` 內目前最清楚的 money correctness flow：玩家用優惠券換取上分，並同步設定打碼要求。
- `main` 分支近期 commit 集中在 coupon 功能，容易追 path-specific history。
- `origin/k3s` 分支已有 coupon 防重複提交的修正，代表這條 flow 的併發 / 雙領風險不是抽象猜測，而是 code-backed 的風險焦點。
- Step 3 可以從 `game_api` API / Service / DAO 追到 GM command，再標清楚下游 GM handler 未掃邊界。

第二順位是 `partner-deposit-withdraw-bill`。它的價值甚至可能更高，但需要較早補下游 wallet / GM handler / partner idempotency evidence；以目前 Step 2 來看，coupon 更適合作為 `game_api` 第一條完整 flow 學習包。

不更新正式履歷 / 自傳。沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認前，本文件只作候選 flow 比較與學習規劃。

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

既有文件狀態：

| 文件 | 狀態 | 判斷 |
| --- | --- | --- |
| `README.md` | 可沿用，已小幅同步 | Step 1 後的專案入口乾淨；本輪只把 Step 2 狀態與下一步改成 coupon Step 3 |
| `step1-candidate-flows.md` | 可沿用 | 已有 Top 5 flow、證據層級、未掃範圍；但 Step 1 發生在新 fetch 規則前，本輪補遠端狀態 |
| `step2-flow-comparison.md` | 本輪新建 | Step 2 主文件 |

## Code Repo 最新狀態

已 fetch：

- 已對 `/Users/nick/Git/iwin/game_api` 執行 `git fetch --all --prune`。
- 只更新 remote refs；未 `pull`、未 merge、未 checkout、未 rebase、未改公司 repo 工作樹。

分支 / HEAD：

| 項目 | 值 |
| --- | --- |
| 目前 local branch | `main` |
| local HEAD | `39bb6e38210bb79c6e68a6a6d818cb87986d39f0` |
| `origin/main` HEAD | `39bb6e38210bb79c6e68a6a6d818cb87986d39f0` |
| local `main` vs `origin/main` | ahead 0 / behind 0 |
| `origin/k3s` HEAD | `c43bba5a27493d28e4e8a00db27c08f98ca4fff2` |

本次遠端狀態判斷：

- `main` 已確認和 `origin/main` 同步。
- `origin/k3s` 有 main 之後的容器化 / 安全性 / config 管理變更。
- `origin/k3s` 包含 coupon 防重複提交相關修正；Step 3 必須納入比較，不能只看 `main`。

未做：

- 未 checkout `origin/k3s`。
- 未逐 commit diff 全掃 `origin/k3s`。
- 未掃下游 GM command handler。
- 未掃 `iwin_gameserver`、`game_job`、`payment`、`third_games_api`。
- 未確認 Nick 本人 MR / ticket / production issue。

## 本次補讀 Code Path

已看：

- `CouponRedeemController#redeem`
- `CouponRedeemServiceImpl#redeemCoupon`
- `CouponRecordServiceImpl`
- `CouponSettingServiceImpl`
- `mapper/tbwebmanager/CouponRecordDao.xml`
- `mapper/tbwebmanager/CouponSettingDao.xml`
- `PartnerController` 的 `NewPay` / `Withdraw` / `BillInfo` / `BillList`
- `PartnerServiceImpl#newPay`
- `PartnerServiceImpl#withdraw`
- `PartnerServiceImpl#saveCoin`
- `PartnerServiceImpl#updCoin`
- `PartnerServiceImpl#queryBillInfos`
- `ValidatedServiceImpl`
- `PartnerLogServiceImpl#checkSign`
- `AgentShareServiceImpl#transferReceive`
- `AgentShareServiceImpl#bonusReceive`
- `ShareCommonService#checkBonusReceiveLook`
- `GameLogServiceImpl` 動態分表查詢方法
- `mapper/logone/LogReelYmdDao.xml`
- `mapper/logone/LogReelPointYmdDao.xml`
- `LoginServiceImpl#getToken`
- `LoginServiceImpl#sendLastLoginMQ`
- `RedisServiceImpl#setIfAbsent`
- `RedisServiceImpl#saveTask`
- `origin/k3s` 相對 `origin/main` 的重點 diff / log

## 評分維度

| 維度 | 說明 |
| --- | --- |
| Senior / Owner 價值 | 是否能談 money correctness、state transition、idempotency、failure window、reconciliation |
| Evidence 強度 | 目前 code 是否足以支撐 Step 3 深挖 |
| 下游依賴成本 | 是否需要先大量掃其他 repo 才能說清楚 |
| 履歷轉換潛力 | 若未來補到 Nick evidence，是否可能轉成保守履歷素材 |
| 風險清晰度 | failure window 是否可明確定位 |

## 排名總表

| 排名 | Flow | 建議 | Senior / Owner 價值 | Evidence 強度 | 下游成本 | 履歷潛力 | 主要理由 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | `coupon-redeem-credit-grant` | 先做 Step 3 | 高 | 高 | 中 | 中高 | 近期 main commit + k3s 修正都指向 coupon；money / idempotency / GM command failure window 清楚 |
| 2 | `partner-deposit-withdraw-bill` | 第二條候選 | 很高 | 中高 | 高 | 高 | 真正 partner money API，但下游 wallet / GM / partner idempotency 要補很多 |
| 3 | `agent-bonus-receive-transfer` | 第三條候選 | 高 | 中 | 高 | 中 | Mongo money balance + Redis projection + GM 上分，需補收益計算 job |
| 4 | `game-record-dynamic-table-query` | 後續作 troubleshooting case | 中 | 高 | 中 | 中 | 動態分表與查詢正確性清楚，但不是 money side effect 主線 |
| 5 | `login-register-token-cache` | 後續作 high-traffic / auth case | 中 | 中高 | 中 | 中 | 高流量入口，但 money correctness 較弱，且分支太多 |

## 1. `coupon-redeem-credit-grant`

中文名稱：優惠券兌換上分 / 打碼要求
建議：已完成 Step 4，下一步做 Step 5 履歷 / 自傳邊界判定
證據層級：專案存在 / code-backed；Nick 貢獻待確認

### 已確認

- `CouponRedeemController#redeem` 提供 `GET /coupons/redeem`，從 query 讀 `couponCode`，從 request 取 IP，轉給 service。
- `CouponRedeemServiceImpl#redeemCoupon` 會：
  - 檢查 Authorization header。
  - 解 token 並用 Redis `loginToken:{openId}` 驗證登入狀態。
  - 查 `coupon_setting` 是否存在且有效。
  - 查 `coupon_record` 檢查同 uid 同 coupon 是否已用過。
  - 查 `coupon_record` 檢查同 IP 同 coupon 是否已達上限。
  - 查 `log_user` 取得玩家、充值次數、最後充值時間、accountId、centerId、cps、channel。
  - 依 user type 判斷全體、充值、當日充值、3 天內充值、指定 user list。
  - 呼叫 GM command 上分。
  - 呼叫 GM command 設定打碼要求。
  - 插入 `coupon_record`。
  - 更新 `coupon_setting.used_count + 1`。
- `CouponRecordDao.xml` 是先查後寫，`insertCouponRecord` 未在 mapper 層看到 duplicate handling。
- `CouponSettingDao.xml` 的 `updateCouponSettingCountPlus` 是單純 `used_count = IFNULL(used_count, 0) + 1`，未看到 max count 條件式防護。
- `origin/k3s` 有 coupon 防重複提交修正：在 service 裡加 Redis lock key，成功取得 lock 才繼續兌換，finally delete lock。

### Senior / Owner 價值

高。

這條 flow 可以自然談：

- source of truth：`coupon_setting`、`coupon_record`、玩家錢包 / GM 下游。
- transaction boundary：MySQL 查寫、Redis token / lock、GM command 不是同一個 transaction。
- idempotency：同 uid 同 coupon、同 IP 上限、Redis lock、DB unique constraint 待確認。
- failure window：上分成功但打碼失敗、打碼成功但 record 失敗、record 成功但 count 失敗。
- reconciliation：coupon record、GM wallet log、打碼 log、設定 used count 如何對帳。

### 風險

已確認風險：

- `main` 上同 uid / 同 IP 檢查是先查後寫，沒有看到同一 transaction 或 DB unique constraint evidence。
- 上分與打碼是兩個 GM command，可能存在部分成功。
- GM command 和 `coupon_record` / `coupon_setting` 更新不在同一 transaction。
- `origin/k3s` 已針對重複提交加 Redis lock，表示這條 flow 的併發風險已被後續分支處理過。

待確認風險：

- Redis lock 的 TTL 單位與 `RedisServiceImpl#setIfAbsent` 實作是否一致。
- lock finally delete 是否可能刪掉另一請求的新 lock。
- `coupon_record` 實際 DB schema 是否有 unique index。
- `coupon_setting` 是否有總量欄位與超額保護。
- GM command 是同步落帳、RPC、queue 還是其他通道。

### 下游成本

中。

Step 3 已補讀：

- `GmIntfcComponent`
- `GMCommandNames.GM_CMD_DEPOSIT`
- `GMCommandNames.SET_BET_TARGET_COUPON`
- 實際 GM handler 所在 repo，可能是 `iwin_gameserver`
- coupon 相關 DB schema / migration，如果本 repo 沒有 schema，要標待確認

### 履歷邊界

目前不可更新正式履歷。

若未來補到 Nick evidence，可以保守轉成：

- 參與 / 維護優惠券兌換 flow 的一致性與防重複提交分析。
- 不能寫「主導設計優惠券系統」或「改善 X%」。

### 結論

第一順位。它已完成 `game_api` 第一份完整 flow 學習包與保守面試案例。

Step 4 已完成：

- `flows/coupon-redeem-credit-grant/career-interview.md`
- `flows/coupon-redeem-credit-grant/materials/interview.md`
- `flows/coupon-redeem-credit-grant/materials/claim-boundary.md`

下一步是 Step 5：判定是否能形成履歷 / 自傳安全 claim。目前仍缺 Nick 本人 evidence，預期不更新正式履歷。

## 2. `partner-deposit-withdraw-bill`

中文名稱：Partner API 上分 / 下分 / 查單
建議：第二順位；coupon Step 5 收斂後再做
證據層級：專案存在 / code-backed；Nick 貢獻待確認

### 已確認

- `PartnerController` 暴露 `NewPay`、`Withdraw`、`BillInfo`、`BillList` 等 API。
- `ValidatedServiceImpl` 會檢查必要欄位，例如 agentId、accountId、time、value、withdrawType、billNos、start / end。
- `PartnerLogServiceImpl#checkSign` 有 time window 檢查與 sign 比對。
- `PartnerServiceImpl#newPay`：
  - 產生 `billNos`。
  - 呼叫 `saveCoin` 寫 Mongo 分日 collection，status 初始為 1。
  - 呼叫 `GM_CMD_DEPOSIT`。
  - 成功後 `updCoin(status=0)`。
- `PartnerServiceImpl#withdraw`：
  - 產生 `billNos`。
  - 呼叫 `saveCoin` 寫 Mongo。
  - 呼叫 `GM_CMD_WITHDRAW`。
  - 成功後 `updCoin(status=0)`。
- `BillInfo` / `BillList` 依 `billNos` 或 start / end 推出分日 collection 查詢。

### Senior / Owner 價值

很高。

這是最接近正式 partner money API 的 flow，可以談：

- partner request validation / sign / replay window。
- internal billNos 與 external business order 的 idempotency。
- Mongo order log status transition。
- GM command 成功 / timeout / failure 語意。
- 查單與 reconciliation。

### 風險

已確認風險：

- `saveCoin` 先寫 status=1，再呼叫 GM，再更新 status=0。
- 如果 GM 成功但 `updCoin` 失敗，partner 查單可能看到 pending / failed。
- 如果 `saveCoin` 成功但 GM timeout，是否重試與補償不明。
- `billNos` 由系統產生；目前未看到外部 partner order id 作為 idempotency key。

待確認風險：

- 一般 Exception 分支是否會把 status 改失敗；目前 `newPay` / `withdraw` 的 `FmsServiceException` 有 `updCoin(status=4)`，其他 Exception 需細看。
- GM handler 是否用 billNos 去重。
- partner 重送時如何避免重複上分 / 下分。
- 查單跨日、分頁與 collection 不存在時的行為。

### 下游成本

高。

要講完整，必須補：

- GM command handler。
- wallet / coin source of truth。
- Mongo `coin_change_log_*` 實際定位，是查單 log、補償依據，還是帳本投影。
- partner API 外部合約是否有 order id / retry 規則。

### 履歷邊界

目前不可更新正式履歷。

若補到 Nick evidence，這條比 coupon 更可能形成 Platform Backend / partner integration 素材；但在 evidence 不足前，不可寫成 Nick 主導。

### 結論

第二順位。價值高，但 Step 3 成本比 coupon 高。

## 3. `agent-bonus-receive-transfer`

中文名稱：代理佣金領取 / 轉帳
建議：第三順位；適合 consistency / projection 案例
證據層級：專案存在 / code-backed；Nick 貢獻待確認

### 已確認

- `PartnerController` 有多個代理分潤 API，例如查佣金、領取、轉帳、收益列表。
- `AgentShareServiceImpl#transferReceive` 會：
  - 檢查轉帳金額。
  - 檢查不能轉給自己。
  - 用 `ShareCommonService#checkBonusReceiveLook` 做短時間防重複提交。
  - 檢查收益計算 job 是否執行中。
  - 查下線關係。
  - 更新轉出者 Mongo `agent_money.money` / `transferMoney`。
  - upsert 轉入者 Mongo `agent_money`。
  - 更新 Redis `GameAgent:{rid}` 的 money projection。
  - 插入兩筆 `agent_transfer_Money` 紀錄。
- `AgentShareServiceImpl#bonusReceive` 會：
  - 查代理與可領佣金。
  - 呼叫 GM 上分。
  - 清 Mongo money、更新 totalMoney。
  - 更新 Redis `GameAgent:{rid}`。
  - 插入領取紀錄。

### Senior / Owner 價值

高。

這條可談：

- Mongo truth source vs Redis projection。
- 佣金計算 job 與 API 領取之間的狀態邊界。
- 防重複提交鎖是否足夠。
- GM 上分與 Mongo / Redis 更新順序。
- 轉帳雙邊更新與 audit log。

### 風險

已確認風險：

- Mongo、Redis、GM command 不在同一 transaction。
- `transferReceive` 更新兩邊 Mongo 後才寫 Redis；中間失敗可能 projection mismatch。
- `bonusReceive` GM 上分成功後，清 Mongo / Redis / insert log 中任一步失敗，都可能造成重領或帳不一致。
- `checkBonusReceiveLook` 註解寫 20 秒，實作傳入 5；時間語意需確認。

待確認風險：

- `@Transactional` 對 MongoDynamicTemplate、Redis、GM command 是否有效。
- 日收益計算 job 在哪個 repo，是否與 API 互斥完整。
- Mongo update 是否有條件式更新防止並發扣減。
- 是否有人工補償或對帳報表。

### 下游成本

高。

需要補 `game_job` 或收益計算來源，否則只能看到 API 讀寫端。

### 履歷邊界

目前不可寫成 Nick 成果。可作為 money-like consistency 學習素材。

### 結論

第三順位。適合做深入 consistency case，但不適合作為第一條，因為計算來源未定位。

## 4. `game-record-dynamic-table-query`

中文名稱：遊戲戰績動態分表查詢
建議：後續作 troubleshooting / observability case
證據層級：專案存在 / code-backed；Nick 貢獻待確認

### 已確認

- 玩家端入口：`ConfigurationController#gameLog`、`gameLogPoint`、`fishingLog`。
- partner 入口：`PartnerController#GameRecord`、`SpinInfo`、`SumSpinInfo`。
- `GameLogServiceImpl` 會依 start / end 產生日期列表，組 `log_reel_{date}` 或 `log_reel_point_{date}` 查詢。
- mapper 使用 `${dbName}.${table}` 動態表名。
- 查詢會依 channel 的 `moneyRatio` 做金額轉換。

### Senior / Owner 價值

中。

這條不是 money side effect 主線，但適合談：

- 大表 / 分日表查詢。
- 跨日分頁與 total count。
- 查不到戰績時如何 troubleshooting。
- game writer / log server / API query 的資料延遲。

### 風險

已確認風險：

- 動態 table name 需要保證日期與 dbName 來源安全。
- 多日查詢用 skip / limit 跨表扣減，容易有邊界錯誤。
- 部分 catch 只 log error 後返回目前累積結果，可能讓 caller 看起來是空資料而不是錯誤。
- `getChannel` 依 Redis QMD channel config，缺資料時行為待確認。

待確認風險：

- 寫入端在哪個 repo，是否有延遲 / retry / 補寫。
- 表不存在時 mapper exception 如何向上呈現。
- 查詢日期範圍是否有限制。

### 下游成本

中。

要完整需要追 `iwin_gameserver` 或 log writer；但即使只看 API 端，也能先形成查詢一致性案例。

### 履歷邊界

目前不適合正式履歷主 bullet。可作為面試 troubleshooting 素材。

### 結論

第四順位。適合和 `app_bi game-round-record-query` 合併成跨系統查詢視角，但不是 game_api 第一條。

## 5. `login-register-token-cache`

中文名稱：玩家登入註冊 / token / Redis cache
建議：後續作 high-traffic auth / cache case
證據層級：專案存在 / code-backed；Nick 貢獻待確認

### 已確認

- `LoginController#appLogin` 進入 `LoginServiceImpl#login`。
- `LoginServiceImpl#login` 會：
  - 解密 request。
  - 用 Redis `setIfAbsent` 做短時間防同時登入。
  - 檢查渠道與停服白名單。
  - 依登入類型走註冊或既有帳號。
  - 建 token 並寫 Redis `loginToken:{openId}`，TTL 約 5 天。
  - 推送 last login Redis queue。
- `RegisteredServiceImpl#paramMapping` 會：
  - 查 Redis / PHP settings 的 IP / IMEI 註冊上限。
  - 寫 Mongo 註冊 log。
  - 寫 account DB。
  - 寫 Redis account cache。
  - 初始化統計。

### Senior / Owner 價值

中。

這條可談：

- auth token / Redis session。
- 防同時登入。
- 註冊 abuse prevention。
- Redis queue 與 BI projection。
- 高流量入口的可用性。

### 風險

已確認風險：

- 登入短鎖 finally delete，需確認是否可能刪掉新請求 lock。
- 註冊寫 DB、Mongo、Redis、統計，跨資源一致性未確認。
- last login queue 是 Redis list + task key，consumer / retry 未掃。
- IP / IMEI 限制依 Redis 設定與 account count，source of truth 邊界待確認。

待確認風險：

- token refresh / logout / 多端登入策略。
- Redis token 與 account DB 不一致時如何修復。
- 黑名單效能修正的完整背景。
- shareinstall attribution 是否會影響 CPS / 推廣歸因。

### 下游成本

中。

不一定要大量掃下游，但分支很多，Step 3 容易發散。

### 履歷邊界

目前不可寫成 Nick 登入系統成果。

### 結論

第五順位。值得做，但先做 money flow 更符合 Senior / Owner 主軸。

## 建議下一步

只推薦一件事：

```text
iwin game_api coupon-redeem-credit-grant Step 5
```

為什麼現在做它：

- Step 4 已把 coupon flow 收斂成保守面試案例。
- 同一條 flow 還沒完成 Step 5，依 KB 不應直接跳新 flow。
- Step 5 只做履歷 / 自傳 claim 邊界判定，不預設更新正式履歷。

會產出什麼：

- 更新 `materials/claim-boundary.md` 的 Step 5 判定。
- 如 evidence 不足，明確標示不更新 `05-resume-master-zh.md` / `08-application-autobiography-zh.md`。
- 同步 README / inventory 下一步。

是否更新履歷：

- 只有補到 Nick 本人 MR / ticket / commit / production issue / 本人確認才考慮。
- 目前預期不更新正式履歷 / 自傳。

是否需要 commit / push：

- Step 5 完成後需要 commit。
- 是否 push 依 Nick 指示；若需要，AI 直接執行 `git push` 觸發 approval。
