# iwin app_bi Step 1：候選 Flow 盤點

更新時間：2026-05-14
掃描等級：Level 1 Flow 掃描
狀態：已依新 KB 重整
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 本次結論

`app_bi` 是後台 / BI / control plane 專案。它適合用來找「後台入口如何影響 production flow」，但不適合單獨拿來寫完整後端成果。

目前最值得延伸的候選 flow：

1. `payment-order-status-repair`：金流訂單人工修正入口，價值最高，但必須轉去 `payment` repo 補 source of truth。
2. `point-control-admin-operation`：後台控制操作，已完成 Step 5；目前仍只確認到 `app_bi` 發送端。
3. `admin-config-redis-sync`：設定同步 Redis，已完成 Step 3；適合補 Step 4 面試素材。
4. `daily-game-record-summary`：每日遊戲資料彙總 / 報表投影，需補 producer repo。
5. `game-round-record-query`：遊戲局紀錄查詢 / troubleshooting 入口，需補 log writer。

不更新履歷。沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認前，所有候選 flow 都只當 `專案存在 / code-backed` 或 `分析素材 / learning-only`。

## 自動重讀紀錄

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`

已重讀 vault：

- `projects/iwin/app_bi/README.md`
- `projects/iwin/app_bi/step2-flow-comparison.md`
- `projects/iwin/app_bi/flows/point-control-admin-operation/flow.md`
- `projects/iwin/app_bi/flows/point-control-admin-operation/evidence.md`
- `projects/iwin/app_bi/flows/point-control-admin-operation/interview.md`
- `projects/iwin/app_bi/flows/point-control-admin-operation/claim-boundary.md`
- `projects/iwin/app_bi/flows/admin-config-redis-sync/flow.md`
- `projects/iwin/app_bi/flows/admin-config-redis-sync/evidence.md`
- `projects/iwin/app_bi/flows/admin-config-redis-sync/claim-boundary.md`

已重讀 code repo：

- `/Users/nick/Git/iwin/app_bi`
- 目前分支：`main`
- 遠端分支清單
- 近期主線 log
- path-specific log
- route / controller / service / model / public view / migration / crontab 入口

未重讀：

- 未 checkout 每個遠端分支。
- 未逐 commit diff。
- 未掃下游 repo：`game_api`、`game_job`、`iwin_gameserver`、`payment`、`third_games_api`。
- 未確認 Nick 個人 MR / ticket / commit。

## 舊文件狀態判斷

| 文件 | 狀態 | 判斷 |
| --- | --- | --- |
| `README.md` | 已重整 | 已從 Step 1 報告改回專案入口 |
| `step1-candidate-flows.md` | 已重整 | 本文件是新的 Step 1 主文件 |
| `step2-flow-comparison.md` | 需小幅重整 | ranking 可沿用，但需同步本文件的新候選與證據層級 |
| `flows/point-control-admin-operation/*` | 舊平鋪格式 / 可沿用但需補 evidence | 已有 Step 5，不更新履歷；之後再遷移到 `materials/` |
| `flows/admin-config-redis-sync/*` | 舊平鋪格式 / 可沿用但需補 Step 4 | 已完成 Step 3，下一步可轉面試素材 |

## 掃描等級判斷

本次使用 Level 1。

原因：

- Step 1 任務是找 candidate flows，不是深挖單一 flow。
- `app_bi` 多數是後台 / BI / control plane 入口。
- 強 evidence 需要回到實際後端 / 下游 repo。

本次做：

- 重新整理 `app_bi` 候選 flow。
- 標清楚已確認、推測、待確認。
- 標清楚哪些只是後台入口，不可直接寫履歷。

本次不做：

- 不逐檔逐行掃完整 repo。
- 不逐 commit diff。
- 不更新履歷 / 自傳。
- 不把 `app_bi` 包裝成 Nick 主開發成果。

## 本次掃描範圍

已看 repo：

- `/Users/nick/Git/iwin/app_bi`
- `/Users/nick/Git/nick/nick-vault`

source repo 狀態：

- `/Users/nick/Git/iwin/app_bi` 工作區乾淨。
- 目前分支：`main`

已看分支：

- 本地：`main`
- 遠端清單包含：`origin/main`、`origin/beta`、`origin/coupontrade`、`origin/feature/GSC`、`origin/feature/PG`、`origin/feature/merchantSettingToRedis`、`origin/feature/RD-152`、`origin/feature/RD-89` 等。

已看 git log：

- 近期主線 log，包含 `coupon trade`、每日遊戲資料彙總、channel / merchant setting Redis、payment order search / repair、point control 等線索。
- path-specific log：`PointControlController.php`、`DataListService.php`、`app/common.php`、`app/route.php`、`RedisSynchronize.php`、`GameData.php`、`GameRound.php`、`UserCurrency.php`、`Base.php`。

已看 code path：

- `app/route.php`
- `app/common.php`
- `app/common/controller/Base.php`
- `app/admin/controller/PointControlController.php`
- `app/admin/controller/RedisSynchronize.php`
- `app/admin/controller/GameData.php`
- `app/admin/controller/GameRound.php`
- `app/admin/controller/UserCurrency.php`
- `app/admin/controller/Auth.php`
- `app/admin/controller/ActivityManage.php`
- `app/business/DataListService.php`
- `app/business/DataReportService.php`
- `app/business/GameRoundService.php`
- `public/views/**` 相關入口
- `db/migrations/**`
- `crontab/crontab.txt`

## 專案定位

已確認：

- `app_bi` 是 PHP / ThinkPHP 後台與 BI 專案。
- 它包含：
  - 後台 admin operation
  - BI / 報表查詢
  - 金流 / 提現查詢與人工修正入口
  - 遊戲局紀錄查詢
  - 每日遊戲資料彙總查詢
  - Redis 設定同步
  - 單點控制後台操作
  - RBAC / 權限判斷
  - Excel 匯出
  - 活動 / 兌換碼營運工具

推測：

- 多數 flow 是後台入口、查詢入口或設定同步入口。
- 真正交易 truth source、runtime consumer、遊戲結算、金流 callback 可能在其他後端 repo。

待確認：

- Nick 實際參與過哪些 `app_bi` 功能。
- 哪些 flow 有 MR / ticket / commit / production issue evidence。
- `point-control` GM command handler 與 runtime Redis consumer 在哪個 repo。
- payment repair 是否有後端狀態機 / 對帳 / wallet side effect。

## Top Candidate Flows

### 1. `payment-order-status-repair`

中文名稱：充值 / 提現訂單狀態修正
證據層級：專案存在 / code-backed；Nick 貢獻待確認
建議：不要只在 `app_bi` 深挖，應轉去 `/Users/nick/Git/iwin/payment`

為什麼重要：

- 金流訂單人工修正是 money correctness 高價值題。
- 錯誤修正可能導致入帳、出款、狀態機、對帳不一致。
- 比起單純後台 CRUD，更接近 Senior / Owner 面試會問的 production risk。

已確認 evidence：

- `DataListService::repairOrderService()` 會依 `billNos` 推出 `payment_order_{yyyy_m}` table，更新 status / remark / last_modify_time。
- `UserCurrency::repairOrder()` 是後台修正入口之一。
- `public/views/gm/txsq/**` 有修復訂單 UI 線索。
- git log 有 `RD-142`、`payment_order_search`、跨月查單與訂單修復相關線索。

推測：

- 真正訂單狀態機、callback、wallet side effect 不在 `app_bi`。

待確認：

- `/Users/nick/Git/iwin/payment` 的 callback / order state machine / wallet update。
- 是否有審核、audit、reconciliation、二次確認。
- Nick 是否實際維護過此 flow。

履歷邊界：

- 潛力高，但目前不可寫進正式履歷。
- 只能說「後台人工修正入口可作為金流 flow 追查起點」。

### 2. `point-control-admin-operation`

中文名稱：單點控制 / 營運控制操作
狀態：已完成 Step 1-5，不更新履歷
證據層級：專案存在 / code-backed；Nick 貢獻待確認

為什麼重要：

- 後台操作會寫 MySQL、Redis，並透過 `sendGmCommand()` 通知下游。
- 這不是單純列表 CRUD，而是 control plane 影響 runtime state。

已確認 evidence：

- `app/route.php` 有 `point-control` routes。
- `PointControlController.php` 有 `storeOrUpdate()`、`updateStatus()`、`batchControllerAdd()`、`batchControllerEdit()`。
- `app/common.php` 有 `sendGmCommand()`。
- `DataListService.php` 有 point-control 批量校驗、Mongo log、匯出。
- `Base.php` 有登入與權限檢查框架。

推測：

- runtime 生效邏輯在下游遊戲 / GM / Java service。

待確認：

- GM command handler。
- runtime 是否直接讀 Redis key。
- GM command 是否 idempotent。
- Nick 是否實際維護過此 flow。

履歷邊界：

- 目前只作學習 / 面試分析素材。
- 不寫 Nick 主導單點控制。

### 3. `admin-config-redis-sync`

中文名稱：後台設定同步 Redis
狀態：已完成 Step 3，下一步 Step 4
證據層級：專案存在 / code-backed；Nick 貢獻待確認

為什麼重要：

- 典型 control plane -> runtime projection。
- 設定同步錯誤可能造成 runtime stale config。
- 可練 cache consistency、config rollout、partial success、rollback / reconcile。

已確認 evidence：

- `RedisSynchronize.php` 有多個 `save*ToRedis()`。
- `Base::addSettings()` / Redis settings pattern 被多處使用。
- migration 裡有同步 Redis 類 menu。
- git log 有 `feature/merchantSettingToRedis`、`RD-152`、`bugs/channel_publish`。

推測：

- 部分 Redis key 會被前台 / payment / game runtime 讀取。

待確認：

- 每個 Redis key 的 runtime consumer。
- 是否有 retry / rollback / reconcile。
- 是否有版本或生效確認。

履歷邊界：

- 目前只確認 Redis 寫入端。
- 未掃 consumer 前，不寫成完整設定同步 owner。

### 4. `daily-game-record-summary`

中文名稱：每日遊戲資料彙總 / RTP 查詢
證據層級：專案存在 / code-backed；Nick 貢獻待確認

為什麼重要：

- 可訓練 projection、跨日統計、報表與交易 truth source 的分離。
- 近期主線 log 有多筆 `daily_game_record_summary` 修改。

已確認 evidence：

- `GameData.php` / `DataReportService.php` 有報表查詢與彙總處理。
- git log 有每日遊戲資料彙總、SQL 修正、金額倍率、提示生成資料時間等線索。

推測：

- 寫入端 / producer 可能在 `game_job` 或其他資料處理 repo。

待確認：

- `daily_rtp_total` / `log_game_daily_record` 或相關報表表的 producer。
- 報表延遲、補跑、reconciliation。

履歷邊界：

- 可作報表 / projection 分析素材。
- 不可單靠 `app_bi` 寫成交易資料 owner。

### 5. `game-round-record-query`

中文名稱：遊戲局紀錄查詢 / production troubleshooting 入口
證據層級：專案存在 / code-backed；Nick 貢獻待確認

為什麼重要：

- 適合面試排查故事：玩家申訴、派彩爭議、局號查詢。
- 能練 log writer / reader、跨日分表、第三方 provider record 差異。

已確認 evidence：

- `GameRound.php` 有多種遊戲局查詢入口。
- `GameRoundService.php` 參與查詢服務。
- `public/views/gameRound/**` 有多個牌局查詢頁。
- migrations 有多個 game round menu。

推測：

- 寫入端在 `iwin_gameserver`、`third_games_api` 或 `game_api`。

待確認：

- log writer。
- 分表規則。
- 查詢範圍與效能限制。

履歷邊界：

- 偏 troubleshooting 素材。
- 未掃 log writer 前，不寫完整遊戲局資料鏈路。

### 6. `app-bi-report-export`

中文名稱：BI 報表查詢與匯出
證據層級：專案存在 / code-backed；Nick 貢獻待確認

為什麼重要：

- 對營運重要，但容易停在查詢 / Excel 匯出。
- 適合補「報表不是 source of truth」與效能風險觀念。

已確認 evidence：

- `DataReportService.php` 有多種報表查詢 / export 處理。
- `extend/PHPExcel`、`public/excel/**`、多個 `*Down` / `excel` 入口可支援匯出線索。

推測：

- 大量匯出可能造成 DB / PHP / Mongo 壓力。

待確認：

- row limit、background job、報表 producer、補跑機制。

履歷邊界：

- 目前履歷價值低到中。
- 不建議優先深挖，除非有真實效能問題 evidence。

### 7. `admin-rbac-permission-check`

中文名稱：後台 RBAC / 權限判斷
證據層級：專案存在 / code-backed；Nick 貢獻待確認

為什麼重要：

- 是所有高風險後台操作的安全邊界。
- 可補「前端隱藏 menu 不等於後端授權」的風險思考。

已確認 evidence：

- `Base::_initialize()` 有 permission check，會檢查 Redis 中的 menuData、pathInfo / classActionName。
- `Auth::permission()` 是前端 / 後台判斷權限入口之一。
- `menu` migration 會定義 classaction / basic / oplog 等欄位。

推測：

- 權限 cache stale 或 menu mapping 不完整可能造成授權錯誤。

待確認：

- 高風險 controller 是否都有後端權限 enforcement。
- role / menu 更新後 cache 刷新策略。

履歷邊界：

- 除非有實際修權限或安全事故 evidence，否則只當系統理解素材。

### 8. `coupon-trade-admin-operation`

中文名稱：兌換碼 / Coupon Trade 營運操作
證據層級：專案存在 / code-backed；Nick 貢獻待確認

為什麼重要：

- 近期主線有 `coupon trade` 與 `couponTradeRecord`。
- 涉及活動兌換碼、使用名單、有效時間、停用、紀錄查詢。

已確認 evidence：

- `ActivityManage.php` 有 `getCouponTradeList()`、`addCouponTrade()`、`couponTradeRecord()`、`disableCouponTrade()` 等線索。
- `public/views/activityManage/couponTrade/**` 有管理頁、紀錄查詢與新增 / 編輯 UI。
- git log 有 `feat: coupon trade`、`feat: 新增couponTradeRecord`。

推測：

- 兌換碼實際使用端可能在其他前台 / API repo。

待確認：

- 使用端 API。
- 兌換碼是否有 idempotency / concurrency / used_count 一致性問題。
- 是否會影響 wallet / bonus / activity ledger。

履歷邊界：

- 目前價值中低。
- 未掃使用端前，不寫成完整活動 / 錢包成果。

## 不適合直接寫履歷的原因

目前所有候選 flow 都至少有一個限制：

- 只看到後台 / BI / control plane 入口。
- 未確認下游後端 repo。
- 未確認 Nick 個人 MR / ticket / commit。
- 未確認 production issue 或具體改善結果。

因此這批 Step 1 只用於：

- 選下一條值得深挖的 flow。
- 建立面試分析素材。
- 決定要轉去哪個後端 repo 補 evidence。

不直接更新：

- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

## 下一步建議

只推薦一件事：

```text
app_bi Step 2 重整
```

原因：

- Step 1 已從 README 拆成獨立文件。
- Step 2 需要同步本文件的新候選排序、`coupon-trade-admin-operation`、證據層級與後台入口邊界。
- 不更新履歷。
