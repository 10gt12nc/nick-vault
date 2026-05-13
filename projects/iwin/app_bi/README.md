# iwin app_bi Step 1：候選 Flow 盤點

更新時間: 2026-05-13
掃描等級: Level 1 Flow 掃描
狀態: 已依新版 KB 重整

## 自動重讀紀錄

已重讀 KB:

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`

已重讀 vault:

- `projects/iwin/app_bi/README.md`
- `projects/iwin/app_bi/step2-flow-comparison.md`
- `projects/iwin/app_bi/flows/point-control-admin-operation/flow.md`
- `projects/iwin/app_bi/flows/point-control-admin-operation/evidence.md`
- `projects/iwin/app_bi/flows/point-control-admin-operation/interview.md`
- `projects/iwin/app_bi/flows/point-control-admin-operation/claim-boundary.md`

已重讀 code repo:

- `/Users/nick/Git/iwin/app_bi`
- 目前分支: `main`
- 遠端分支清單
- 近期主線 log
- path-specific log
- route / controller / service / model / public admin page / migration / crontab 入口

未重讀:

- 沒有 checkout 每個遠端分支。
- 沒有逐 commit diff。
- 沒有掃下游 repo: `game_api`、`game_job`、`iwin_gameserver`、`payment`、`third_games_api`。

## 舊文件狀態判斷

| 文件 | 狀態 | 判斷 |
| --- | --- | --- |
| `README.md` | 需重整 | 舊版已有 Step 1，但需要補新版 KB 的舊文件狀態、主軸 / 輔助邊界、下游未掃說明 |
| `step2-flow-comparison.md` | 需重整 | 排名可沿用，但需強化「後台入口不等於後端成果」 |
| `flows/point-control-admin-operation/*` | 需補 evidence | Step 3 已存在，但 `evidence.md` 掃描範圍與未掃邊界不夠新版 KB 標準 |

結論:

- 不直接跳 Step 4。
- 先重整 Step 1 / Step 2 / Step 3。
- 不更新履歷。

## 掃描等級判斷

本次 Step 1 使用 Level 1。

原因:

- Step 1 任務是找 candidate flows，不是深挖單一 flow。
- `app_bi` 是 PHP / ThinkPHP 後台與 BI 專案，多數內容是 control plane / reporting plane。
- 若要形成強面試或履歷 evidence，必須回到實際後端 / 下游 repo。

本次會做:

- 找出 `app_bi` 中最有 Senior / Owner 價值的候選 flow。
- 標清楚已確認、推測、待確認。
- 標清楚哪些只是後台入口，不能直接寫履歷。

本次不做:

- 不逐檔逐行拆整個 repo。
- 不逐 commit diff。
- 不更新履歷、自傳。
- 不把 `app_bi` 包裝成 Nick 主開發成果。

## 專案定位

已確認:

- `app_bi` 是 iwin 下的 PHP / ThinkPHP 後台與 BI 專案。
- 主要能力包含:
  - 後台 admin operation
  - BI / 報表查詢
  - 金流 / 提現查詢與人工修正入口
  - 遊戲局紀錄查詢
  - 每日遊戲資料彙總查詢
  - Redis 設定同步
  - 單點控制後台操作
  - RBAC / 權限判斷
  - Excel 匯出

推測:

- `app_bi` 多數 flow 是後台入口、查詢入口或設定同步入口。
- 真正交易 truth source、runtime consumer、遊戲結算、金流 callback 可能在其他後端 repo。

待確認:

- Nick 實際參與過哪些 `app_bi` 功能。
- 哪些 flow 有 MR / ticket / commit / production issue evidence。
- `point-control` GM command handler 與 runtime Redis consumer 在哪個 repo。

## 本次掃描範圍

已看 repo:

- `/Users/nick/Git/iwin/app_bi`
- `/Users/nick/Git/nick/nick-vault`

已看分支:

- 本地目前分支: `main`
- 遠端分支清單包含:
  - `origin/main`
  - `origin/beta`
  - `origin/coupontrade`
  - `origin/feature/GSC`
  - `origin/feature/PG`
  - `origin/feature/RD-152`
  - `origin/feature/RD-89`
  - `origin/feature/merchantSettingToRedis`
  - 其他較舊 feature / ci 分支

已看 git log:

- 近期主線 log。
- `PointControlController.php` / `DataListService.php` / `app/common.php` / `app/route.php` path-specific log。
- 關鍵字線索: `point-control`、`PointControl`、`sendGmCommand`、`RedisSynchronize`、`daily_game_record_summary`、`GameRound`、`repairOrder`、`permission`。

已看 code path:

- `app/route.php`
- `app/common.php`
- `app/common/controller/Base.php`
- `app/admin/controller/PointControlController.php`
- `app/admin/controller/RedisSynchronize.php`
- `app/admin/controller/GameData.php`
- `app/admin/controller/GameRound.php`
- `app/admin/controller/UserCurrency.php`
- `app/admin/controller/Auth.php`
- `app/business/DataListService.php`
- `app/business/DataReportService.php`
- `app/business/GameRoundService.php`
- `public/admin/gm/dandian/**`
- `public/admin/gameRound/**`
- `db/migrations/**`
- `crontab/crontab.txt`

未掃範圍:

- 未逐檔逐行掃完整 repo。
- 未逐一 checkout 遠端分支比對 diff。
- 未掃 `game_api`、`game_job`、`iwin_gameserver`、`payment`、`third_games_api`。
- 未確認 Nick 個人 commit / MR / ticket。

## Top Candidate Flows

### 1. `point-control-admin-operation`

中文名稱: 單點控制 / 營運控制操作

為什麼重要:

- 後台操作會寫 MySQL、Redis，並透過 `sendGmCommand()` 通知下游。
- 這不是單純列表 CRUD，而是後台 control plane 影響 runtime state。
- 已有 Step 3 舊版文件，適合先重整乾淨。

production 風險:

- MySQL transaction 管不到 Redis、GM command、Mongo log。
- Redis 寫入後 GM command 失敗，可能留下 DB rollback 但 Redis 已變更的狀態。
- 批量操作可能 partial success。
- GM command receiver / runtime consumer 未確認。

已確認 evidence:

- `app/route.php` 有 `point-control` routes。
- `PointControlController.php` 有 `storeOrUpdate()`、`updateStatus()`、`batchControllerAdd()`、`batchControllerEdit()`。
- `app/common.php` 有 `sendGmCommand()`。
- `DataListService.php` 有 point-control 批量校驗、Mongo log、匯出。
- `Base.php` 有登入與權限檢查框架。

推測:

- runtime 生效邏輯在下游遊戲 / GM / Java service。

待確認:

- GM command handler。
- runtime 是否直接讀 Redis key。
- GM command 是否 idempotent。
- Nick 是否實際維護過此 flow。

履歷邊界:

- 目前只作學習 / 面試分析素材。
- 不寫 Nick 主導單點控制。

### 2. `payment-order-status-repair`

中文名稱: 充值 / 提現訂單狀態修正

為什麼重要:

- 金流訂單人工修正具備高 money correctness 風險。
- 但 `app_bi` 只確認到後台修正入口，不是 payment source of truth。

production 風險:

- 人工修錯狀態造成入帳 / 出款錯誤。
- 與 payment repo 的訂單狀態機不一致。
- audit、approval、reconciliation 不足。

已確認 evidence:

- `DataListService::repairOrderService()`。
- `UserCurrency::repairOrder()`。
- `public/admin/conversion/*` 有充值 / 提現查詢與修正入口。
- git log 有 `RD-142`、`payment_order_search`、`RD-371` 相關線索。

待確認:

- `/Users/nick/Git/iwin/payment` 的 callback / order state machine / wallet update。

履歷邊界:

- 潛力高，但不能只靠 `app_bi` 寫履歷。

### 3. `admin-config-redis-sync`

中文名稱: 後台設定同步 Redis

為什麼重要:

- 典型 control plane -> runtime projection。
- 設定同步錯誤可能造成 runtime stale config。

已確認 evidence:

- `RedisSynchronize.php` 有多個 `save*ToRedis()`。
- migration 裡有同步 Redis 類 menu。
- git log 有 `feature/merchantSettingToRedis`、`RD-152`、`bugs/channel_publish`。

待確認:

- 每個 Redis key 的 runtime consumer。
- 是否有 retry / rollback / reconcile。

### 4. `daily-game-record-summary`

中文名稱: 每日遊戲資料彙總 / RTP 查詢

為什麼重要:

- 可訓練 projection、跨日統計、報表與交易 truth source 的分離。

已確認 evidence:

- `GameData.php`
- `DataReportService.php`
- git log 有 `daily_game_record_summary` 相關 commit。

待確認:

- producer repo，可能在 `game_job` 或其他後端。

### 5. `game-round-record-query`

中文名稱: 遊戲局紀錄查詢 / production troubleshooting 入口

為什麼重要:

- 適合當玩家申訴、派彩爭議、局號排查入口。

已確認 evidence:

- `GameRound.php`
- `GameRoundService.php`
- `public/admin/gameRound/**`
- migrations 有大量牌局查詢 menu。

待確認:

- game log writer。
- third-party provider record sync。

### 6. `app-bi-report-export`

中文名稱: BI 報表查詢與匯出

為什麼重要:

- 可練 report projection、查詢壓力、匯出邊界。

待確認:

- BI log producer。
- 匯出限制與 background job。
- 是否用於正式 reconciliation。

### 7. `admin-rbac-permission-check`

中文名稱: 後台 RBAC / 權限判斷

為什麼重要:

- 是所有高風險後台操作的安全前置邊界。

已確認 evidence:

- `Base::_initialize()` 會做登入與權限檢查。
- `app/route.php` 有 `auth/permission`。
- `Auth.php` 有 role / menu / permission 相關邏輯。

待確認:

- 每個高風險 operation 是否完整走後端權限。

## Step 1 結論

目前最適合先重整的是:

```text
point-control-admin-operation
```

原因:

- 它是 `app_bi` 內 evidence 最完整的 flow。
- 它能練 control plane、runtime side effect、DB / Redis / GM command / audit log 一致性。
- 但目前只能說確認到 `app_bi` 發送端與後台操作面，不能說完整後端 flow 已掃完。

下一步:

```text
app_bi Step 2 重整
```

不更新履歷。
