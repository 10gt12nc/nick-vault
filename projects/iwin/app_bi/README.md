# iwin app_bi Step 1：候選 Flow 盤點

更新時間: 2026-05-13
掃描等級: Level 1 Flow 掃描

## 本次掃描等級判斷

建議等級: Level 1

原因:

- 這次任務是 `app_bi Step 1`，目標是找 candidate flows，不是深挖單一 flow。
- Step 1 不應逐檔逐行拆完整 repo，也不應逐 commit diff 追每筆修改。
- `app_bi` 是 PHP / ThinkPHP 後台與 BI 類專案，很多 flow 只是操作入口或查詢入口；若要寫成強 evidence，需要後續回到實際後端 / 下游 repo 深挖。

本次會掃:

- vault 內既有 `app_bi` 文件與已完成 flow。
- `app_bi` 主分支、遠端分支清單、近期 git log。
- route、controller、business service、crontab、public admin page 入口。
- path-specific / keyword git log，用來找可能有價值的 production flow。

本次不掃:

- 不逐檔逐行拆完整 repo。
- 不逐 commit diff 追每筆修改原因。
- 不掃下游 GM command receiver / game server / Java 後端。
- 不更新履歷、自傳或 master resume。

## 重複 Flow 檢查

已確認:

- `projects/iwin/app_bi/flows/point-control-admin-operation/` 已存在，已有:
  - `flow.md`
  - `evidence.md`
  - `interview.md`
  - `claim-boundary.md`
- `projects/iwin/app_bi/step2-flow-comparison.md` 已存在，承接舊版 Step 1 做過候選 flow 比較。
- `senior-owner-playbook/01-senior-owner-flow-inventory.md` 有 app_bi / iwin 類高階候選名稱，例如:
  - `app-bi-report-export`
  - `daily-game-record-summary`
  - `point-control-operations`
  - `third-game-provider-reporting`
  - `admin-reporting-reconciliation`
  - `admin-rbac-operations`

判斷:

- 可以重做 Step 1，因為 KB 已更新，需要補掃描等級、分支邊界與後台 / 下游邊界。
- 不應刪掉已存在的 `point-control-admin-operation` flow。
- 不應把 Step 1 直接升級成履歷 claim。

## 本次掃描範圍

已看 repo:

- `/Users/nick/Git/iwin/app_bi`
- `/Users/nick/Git/nick/nick-vault`

已看分支:

- 本地目前分支: `main`
- 遠端分支清單已看，包含:
  - `origin/main`
  - `origin/coupontrade`
  - `origin/beta`
  - `origin/feature/PG`
  - `origin/feature/GSC`
  - `origin/feature/merchantSettingToRedis`
  - `origin/feature/RD-152`
  - `origin/feature/RD-89`
  - 其他較舊 feature branches

已看 git log:

- `git log --oneline --decorate -30`
- keyword log: `daily_game_record_summary`、`game_summary`、`point`、`control`、`merchantSetting`、`redis`、`PG`、`GSC`、`RD-146`、`RD-152`、`RD-142`、`pay`、`order`、`coupon`
- path-specific log:
  - `PointControlController.php`
  - `RedisSynchronize.php`
  - `GameData.php`
  - `GameRound.php`
  - `DataListService.php`
  - `DataReportService.php`
  - `app/route.php`

已看 code path:

- `app/route.php`
- `app/common.php`
- `app/admin/controller/*`
- `app/business/*`
- `app/api/controller/v1/*`
- `app/validateService/*`
- `crontab/crontab.txt`
- `public/admin/**/*.html`
- `db/migrations/*`
- `.gitlab-ci.yml`

未掃範圍:

- 沒有逐檔逐行讀完整 repo。
- 沒有逐一 checkout 每個遠端分支做 diff。
- 沒有掃 `game_api`、`game_job`、`iwin_gameserver`、`payment`、`third_games_api` 等下游後端 repo。
- 沒有掃 DevOps CI template 內容；`.gitlab-ci.yml` 只確認有引用外部 template。

只作入口參考、不作履歷 claim:

- 後台頁面、BI 查詢、Excel 匯出、RBAC、point-control 後台操作入口。
- 沒有 Nick 本人確認或 MR / ticket / commit 前，不寫 Nick 主導 `app_bi`。

## 專案定位

已確認:

- `app_bi` 是 ThinkPHP / PHP 後台與 BI 專案。
- 主要內容包含:
  - 後台 admin controller
  - BI / 報表查詢
  - 支付 / 提現查詢與狀態修正入口
  - 遊戲局紀錄查詢
  - 每日遊戲資料彙總查詢
  - Redis 設定同步
  - 單點控制後台操作
  - 後台 RBAC / 權限管理
  - Excel 匯出
  - 少量 API endpoint
  - crontab 觸發活動或 IP 資料更新

推測:

- `app_bi` 多數 flow 是 control plane / reporting plane，不一定是交易 truth source。
- 真正 runtime 生效、遊戲結算、金流 callback、資料彙總 producer 可能在其他後端 repo。

待確認:

- Nick 實際維護過哪些 `app_bi` flow。
- 哪些 flow 有對應 MR / ticket / commit。
- 哪些 flow 的下游實作在 `game_api`、`game_job`、`iwin_gameserver`、`payment`、`third_games_api`。

## 核心入口

### HTTP / Admin route

已確認:

- `app/route.php` 有明確 route:
  - game pool: `GamePoolController`
  - game goldcoin: `GameGoldcoinController`
  - point-control: `PointControlController`
  - point-control allowlist: `PointControlAllowlistController`
  - point-control switch: `PointControlSwitchController`
  - auth permission: `Auth::permission`

### Admin controller

已確認:

- `app/admin/controller/` 有大量後台 controller，例如:
  - `PayData.php`
  - `WithdrawalData.php`
  - `GameData.php`
  - `GameRound.php`
  - `RedisSynchronize.php`
  - `PointControlController.php`
  - `Auth.php`
  - `UserCurrency.php`
  - `ActivityManage.php`
  - `GamePoolController.php`
  - `GameGoldcoinController.php`

### Business service

已確認:

- `app/business/DataListService.php`
- `app/business/DataReportService.php`
- `app/business/GameRoundService.php`
- `app/business/ValidateService.php`

### API controller

已確認:

- `app/api/controller/v1/Pay.php`
- `app/api/controller/v1/Bill.php`
- `app/api/controller/v1/Bind.php`

判斷:

- API controller 存在，但本次 Step 1 未深挖其是否為前台正式 API。

### Scheduled job

已確認:

- `crontab/crontab.txt` 有定時呼叫 `Timer/activity`。
- 同檔另有 IP 資料更新指令。

安全處理:

- 不記錄任何 internal URL、IP、token 或實際 production endpoint。

## Top Candidate Flows

### 1. `point-control-admin-operation`

中文名稱: 單點控制 / 營運控制操作

為什麼重要:

- 後台操作會寫 DB、Redis，並透過 GM command 影響 runtime 狀態。
- 比單純報表更接近 Senior / Owner 要看的 production control plane。
- 已經有 Step 3 flow 文件，可以繼續補 failure / consistency / 下游 evidence。

production 風險:

- DB、Redis、GM command、Mongo log 之間不一致。
- 後台批量操作部分成功、部分失敗。
- 操作 audit log 不完整。
- 下游 GM command timeout 或重送造成狀態不一致。

已確認 evidence:

- `app/route.php` 有 `point-control` 系列 route。
- `PointControlController.php` 有 `storeOrUpdate()`、`updateStatus()`、`batchControllerAdd()`、`batchControllerEdit()`。
- `app/common.php` 有 `sendGmCommand()`，會從 Redis `settings.center_http` 取下游 HTTP endpoint 再送 command。
- `DataListService.php` 有 point control 批量校驗、匯出、Mongo log 寫入。
- 已有 `projects/iwin/app_bi/flows/point-control-admin-operation/`。

推測:

- 這條 flow 的下游生效邏輯在 game server / Java / GM command receiver，不在 `app_bi`。

待確認:

- GM command handler 所在 repo。
- runtime consumer 如何讀 Redis key。
- Nick 是否實際參與維護此功能。

履歷邊界:

- 目前只可作學習與面試分析素材。
- 不可直接寫 Nick 主導單點控制。

### 2. `admin-config-redis-sync`

中文名稱: 後台設定同步 Redis

為什麼重要:

- 後台設定同步到 Redis 是典型 control plane -> runtime projection。
- 設定錯或同步漏掉，可能造成支付、商戶、VIP、渠道、活動、遊戲列表等 runtime 設定不一致。

production 風險:

- DB 設定更新但 Redis 未同步。
- Redis 同步後下游服務未讀到或讀到舊值。
- 多 channel / 多設定部分同步成功。
- 沒有版本、回滾、同步結果紀錄時，問題難查。

已確認 evidence:

- `RedisSynchronize.php` 有多個 `save*ToRedis()`:
  - `saveSettingToRedis`
  - `savePayClientSettingToRedis`
  - `saveMerchantSettingToRedis`
  - `saveLayerToRedis`
  - `saveConvenientPaySettingToRedis`
  - `saveVipPaySettingToRedis`
  - `savePayTypeSettingToRedis`
  - `saveChannelToRedis`
  - `saveGameListToRedis`
  - `saveSettingsV2ToRedis`
- git log 有 Redis 設定同步相關 commit:
  - `feature/merchantSettingToRedis`
  - `RD-152`
  - `RD-89`
  - `bugs/channel_publish`

推測:

- `app_bi` 負責寫 Redis projection，真正讀取者在其他後端或 runtime service。

待確認:

- 每個 Redis key 的下游 consumer。
- 是否有 retry / wait sync / reconcile。
- 是否有操作 log 與 rollback。

履歷邊界:

- 若 Nick 沒有實際參與，只能說「分析後台設定同步模式」，不能說主導設定中心。

### 3. `daily-game-record-summary`

中文名稱: 每日遊戲資料彙總 / RTP 查詢

為什麼重要:

- 遊戲投注、派彩、RTP、留存與 daily summary 是博弈平台核心觀測資料。
- 這條適合訓練 projection、資料延遲、跨日、Redis 今日資料與歷史表合併。

production 風險:

- 今日 Redis 與 daily table 重複或漏算。
- 金額倍率轉換錯誤。
- report 被誤用成交易 truth source。
- daily summary job 延遲導致營運誤判。

已確認 evidence:

- `GameData.php` 有:
  - `getTodaySingleGameData`
  - `getTodayAllGameData`
  - 遊戲彙總與 daily / history 相關查詢
- `DataReportService.php` 有 daily / player bet / export 相關方法。
- git log 有多筆 `daily_game_record_summary`、`game_summary_note` 相關 commit。

推測:

- `app_bi` 主要讀取與展示結果，資料 producer 可能在 `game_job` 或其他統計 job。

待確認:

- `daily_rtp_total`、`log_game_daily_record` 等資料產生端。
- Redis 今日資料寫入端。
- 是否有正式 reconciliation。

履歷邊界:

- 只讀 `app_bi` 不足以寫 Nick 做遊戲資料彙總後端；要先讀 producer。

### 4. `game-round-record-query`

中文名稱: 遊戲局紀錄查詢 / production troubleshooting 入口

為什麼重要:

- 玩家申訴、派彩爭議、局號查詢、第三方遊戲排查都會用到。
- 適合練「後台查詢入口 -> log table -> record parse -> production issue 排查」。

production 風險:

- 查詢時間區間過大造成 DB 壓力。
- daily table 分表跨日漏查。
- `common_data` 解析與實際 game log schema 不一致。
- 第三方遊戲與自研遊戲資料格式不一致。

已確認 evidence:

- `public/admin/gameRound/*.html` 有大量局紀錄查詢頁。
- `GameRound.php` 有多個 `{game}GameRound()` 類方法。
- `GameRoundService.php` 存在。
- git log 有 PG / GSC / ANTPLAY 局查詢與 reason / status 相關 commit，例如 `feature/PG`、`feature/GSC`、`RD-146`。

推測:

- `app_bi` 是查詢與排查入口，真實局紀錄產生端在 game server、third game API 或 log writer。

待確認:

- `log_reel_*` 寫入端。
- third-party provider record sync 的 source of truth。
- Nick 是否實際用這條 flow 排查 production。

履歷邊界:

- 可作「排查入口理解」；不能直接寫遊戲結算 owner。

### 5. `payment-order-status-repair`

中文名稱: 充值 / 提現訂單狀態修正

為什麼重要:

- 金流訂單狀態修正是高風險後台操作。
- 只要涉及充值 / 提現 / 訂單狀態，Senior 要關注 money correctness、audit、權限、補償與不可逆操作。

production 風險:

- 訂單狀態誤修造成入帳或出款錯誤。
- 跨月資料查詢 / 修正錯誤。
- 操作紀錄不足導致追查困難。
- 與真正 payment service 狀態不一致。

已確認 evidence:

- `public/admin/conversion/tixian.html` 有訂單修正入口。
- `public/admin/conversion/onlinepay*.html` 有充值訂單查詢、匯出、狀態變更入口。
- `DataListService.php` 有 `repairOrderService()`。
- `UserCurrency.php` 有 `repairOrder()`。
- git log 有:
  - `RD-142` 訂單修復 / 提现状态修正列表
  - `payment_order_search` 跨月資料整合 / 查詢修正
  - `RD-371` 訂單審核 redis lock

推測:

- 真正 payment callback / ledger / wallet 可能不在 `app_bi`，`app_bi` 是人工修正入口。

待確認:

- `payment` repo 的訂單狀態主流程。
- 修正後是否觸發錢包、對帳、通知或補償。
- 審核 redis lock 實際 code path。

履歷邊界:

- 這條很有價值，但只讀 `app_bi` 不能寫金流 owner；應優先轉去 `payment` repo 做 Level 2 / Level 3。

### 6. `app-bi-report-export`

中文名稱: BI 報表查詢與匯出

為什麼重要:

- 報表是營運決策與問題排查入口。
- Senior 價值在於資料來源、延遲、projection vs truth source、匯出壓力與查詢邊界。

production 風險:

- 報表資料被誤認成交易真相。
- 大範圍匯出拖垮 PHP / DB / Mongo。
- 分頁結果與匯出結果不一致。
- BI log producer 延遲或資料缺漏。

已確認 evidence:

- `PayData.php`、`WithdrawalData.php` 有支付 / 提現曲線與統計。
- `DataReportService.php` 有 export daily data / player bet data。
- `public/admin/conversion/*onlinepay*.html` 有 Excel 匯出入口。
- 多個 controller 使用 `Excel::down()`。

推測:

- `app_bi` 多半是 reporting consumer；資料 producer 可能在其他 job / backend。

待確認:

- BI log / Mongo log producer。
- 匯出 row limit、背景任務或限流。
- 報表是否用於正式 reconciliation。

履歷邊界:

- 可說理解 BI 報表查詢與匯出風險；不宜當第一個強履歷 case。

### 7. `admin-rbac-permission-check`

中文名稱: 後台 RBAC / 權限判斷

為什麼重要:

- 後台控制面若權限錯誤，會造成營運誤操作或越權操作。
- 對所有高風險 admin operation 都是前置安全邊界。

production 風險:

- 前端 hide menu 但後端未攔。
- 權限 cache / DB 不一致。
- 角色異動後 session / cache 未失效。
- 高風險操作沒有二次確認或 audit。

已確認 evidence:

- `app/route.php` 有 `auth/permission` route。
- `Auth.php` 有 role、role menu、members 與 `permission()`。

推測:

- 這可能是後台通用權限檢查，但本次 Step 1 未確認所有 controller 是否都經此檢查。

待確認:

- Login / middleware / frontend call path。
- 每個高風險操作是否都有後端權限防護。

履歷邊界:

- 除非有實際維護 evidence，否則不寫 RBAC 主導。

## 建議第一條深挖 Flow

建議:

```text
point-control-admin-operation
```

理由:

- 已經有 Step 3 學習包，可在同一條 flow 上補齊，不需要再開新線。
- 這條最符合 Senior / Owner: 後台操作影響 runtime、跨 DB / Redis / GM command / Mongo log。
- 但目前仍有重大邊界: 只確認到 `app_bi` 後台與 GM command 發送端，尚未確認下游接收端與 runtime consumer。

## 下一步要讀的 Code Path

如果繼續 `point-control-admin-operation`，下一步不是更新履歷，而是補 evidence 邊界:

- `projects/iwin/app_bi/flows/point-control-admin-operation/evidence.md`
- `/Users/nick/Git/iwin/app_bi/app/common.php`
- `/Users/nick/Git/iwin/app_bi/app/admin/controller/PointControlController.php`
- `/Users/nick/Git/iwin/app_bi/app/business/DataListService.php`
- 下游候選 repo:
  - `/Users/nick/Git/iwin/game_api`
  - `/Users/nick/Git/iwin/game_job`
  - `/Users/nick/Git/iwin/iwin_gameserver`

如果要改選金流方向:

- `payment-order-status-repair` 應轉去 `/Users/nick/Git/iwin/payment` 做 Step 1 / Step 2。

## Step 1 結論

已確認:

- `app_bi` 適合作為後台操作入口、BI 查詢入口、production troubleshooting 入口來讀。
- 最值得延續的是 `point-control-admin-operation`，但它目前仍不是完整後端 flow，因為下游 runtime 未掃。
- `admin-config-redis-sync`、`daily-game-record-summary`、`payment-order-status-repair` 都有價值，但都需要回到後端 / producer repo 才能成為強 case。

不可誇大:

- 不能說 Nick 主導 `app_bi`。
- 不能說 Nick 主導 point-control、Redis sync、daily summary、payment repair。
- 不能把後台查詢入口包裝成交易一致性設計成果。
- 不能把 BI 報表 projection 當成 source of truth。

待補:

- Nick 實際參與的 MR / ticket / commit。
- 下游 repo 的真實 code path。
- 每條高價值 flow 的 source of truth 與 failure boundary。

