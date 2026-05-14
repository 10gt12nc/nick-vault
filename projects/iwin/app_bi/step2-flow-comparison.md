# iwin app_bi Step 2：候選 Flow 技術點與風險比較

更新時間：2026-05-14
掃描等級：Level 1 Flow 掃描 / 候選 flow 比較
狀態：已依 2026-05-14 Step 1 重整
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 本次結論

`app_bi` 的價值不是「證明 Nick 做了完整後端系統」，而是當作 iwin 後台 / BI / control plane 的入口，幫助後續追真正高價值的 production flow。

Step 2 重新排序後，結論分兩層：

1. 最高 Senior / Owner 價值是 `payment-order-status-repair`，但它不適合只在 `app_bi` 深挖，必須回到 `payment` repo 找 source of truth。
2. 若本輪繼續留在 `app_bi`，最乾淨的下一步是 `daily-game-record-summary Step 3`，因為 `point-control-admin-operation` 與 `admin-config-redis-sync` 都已完成 Step 5，且都不更新履歷 / 自傳。

不更新履歷。沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認前，本文件所有 flow 都只作 `專案存在 / code-backed` 或 `分析素材 / learning-only`。

## 自動重讀紀錄

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`

已重讀 vault：

- `projects/iwin/app_bi/README.md`
- `projects/iwin/app_bi/step1-candidate-flows.md`
- `projects/iwin/app_bi/step2-flow-comparison.md`
- `projects/iwin/app_bi/flows/point-control-admin-operation/flow.md`
- `projects/iwin/app_bi/flows/admin-config-redis-sync/flow.md`

已重讀 code repo：

- `/Users/nick/Git/iwin/app_bi`
- 目前分支：`main`
- 遠端分支清單
- 近期主線 log
- path-specific log
- route / controller / service / public admin page / migration 入口

本次實際看過的 key paths：

- `app/route.php`
- `app/common.php`
- `app/common/controller/Base.php`
- `app/admin/controller/PointControlController.php`
- `app/admin/controller/RedisSynchronize.php`
- `app/admin/controller/ActivityManage.php`
- `app/admin/controller/UserCurrency.php`
- `app/admin/controller/GameData.php`
- `app/admin/controller/GameRound.php`
- `app/business/DataListService.php`
- `app/business/DataReportService.php`
- `app/business/GameRoundService.php`
- `public/views/**` 相關後台入口
- `db/migrations/**` 相關 menu / schema 線索

未重讀 / 未完成：

- 未 checkout 每個遠端分支。
- 未逐 commit diff。
- 未掃下游 repo：`payment`、`game_api`、`game_job`、`iwin_gameserver`、`third_games_api`。
- 未確認 Nick 個人 MR / ticket / commit。

## 舊文件狀態判斷

| 文件 | 狀態 | 判斷 |
| --- | --- | --- |
| `README.md` | 需同步 | Step 2 已重整，下一步不再是 Step 2 |
| `step1-candidate-flows.md` | 可沿用 | 已有掃描範圍、證據層級、候選 flow |
| `step2-flow-comparison.md` | 本次已重整 | 舊版 ranking 以 `point-control` 為首，已改成價值排序 / 下一步排序分開 |
| `flows/point-control-admin-operation/*` | 舊平鋪格式 / 已完成 Step 5 | 不更新履歷 / 自傳；保留為面試分析素材 |
| `flows/admin-config-redis-sync/*` | 舊平鋪格式 / 可沿用 | 已完成 Step 5，不更新履歷 / 自傳 |

## 比較前提

本文件只比較 Senior / Platform Backend / System Owner 價值。

不做：

- 不建立新 flow folder。
- 不深挖單條 flow。
- 不逐檔逐行。
- 不逐 commit diff。
- 不更新履歷 / 自傳。
- 不把 `app_bi` 包裝成 Nick 主開發成果。
- 不把後台入口寫成完整後端 owner。

## 價值排序

| 排名 | Flow | 中文名稱 | Senior / Owner 價值 | 目前 evidence | 最大缺口 | 建議 |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | `payment-order-status-repair` | 金流訂單狀態修正 | 高 | `app_bi` 有人工修正入口與跨月查單 history | `payment` source of truth 未掃 | 後續轉 `payment Step 1`，不在 `app_bi` 硬挖 |
| 2 | `point-control-admin-operation` | 單點控制 / 營運控制操作 | 中高 | Step 5 已完成；MySQL / Redis / GM command / Mongo log | 下游 GM receiver 未掃；Nick 貢獻待確認 | 保留為面試分析素材 |
| 3 | `admin-config-redis-sync` | 後台設定同步 Redis | 中高 | 已 Step 5；Redis projection 與欄位漏投影 history 清楚 | runtime consumer 未掃 | 先保留，不更新履歷 |
| 4 | `daily-game-record-summary` | 每日遊戲資料彙總 | 中高 | 查詢 / 報表入口與近期 SQL 修正 history | producer / 補跑機制未掃 | 下一步做 Step 3 |
| 5 | `game-round-record-query` | 遊戲局紀錄查詢 | 中 | 查詢入口與多 provider record 頁面 | log writer 未掃 | 適合後續 troubleshooting case |
| 6 | `admin-rbac-permission-check` | 後台 RBAC / 權限判斷 | 中 | `Base::_initialize()` / `Auth::permission()` 有權限框架 | 高風險 controller enforcement 未逐條確認 | 作為輔助邊界，不單獨優先 |
| 7 | `coupon-trade-admin-operation` | 兌換碼 / Coupon Trade 營運操作 | 中低到中 | 近期主線有 `coupon trade` code 與 UI | 使用端 / wallet side effect 未掃 | 暫列候選，不優先 |
| 8 | `app-bi-report-export` | BI 報表查詢與匯出 | 中低 | 報表查詢與 Excel export 線索 | producer / row limit / background job 未掃 | 低優先，除非有真實效能問題 |

## 下一步排序

這裡不是重排價值，而是「下一個最適合叫 AI 做什麼」。

1. `app_bi daily-game-record-summary Step 3`
   - 原因：同 project 中 `point-control-admin-operation` 與 `admin-config-redis-sync` 都已完成 Step 5，下一條未完成且仍有 Senior / Owner 價值的是每日遊戲資料彙總。
   - 產出：報表 projection、producer 待確認、補跑 / reconcile / 延遲資料風險的主研究報告。
   - 是否更新履歷：否。
2. `payment Step 1`
   - 原因：`payment-order-status-repair` 價值最高，但強 evidence 不在 `app_bi`。
   - 產出：金流 repo 的 candidate flows，不把 app_bi 人工入口硬當完整 payment owner。
   - 是否更新履歷：否，至少等 payment flow Step 4 / Step 5。
3. `app_bi game-round-record-query Step 3`
   - 原因：可作 troubleshooting case，但要先承認只看到查詢端。
   - 產出：遊戲局查詢入口、log writer 待確認、查詢效能與爭議排查邊界。
   - 是否更新履歷：否。

本輪只推薦第一項，避免跨 project 跳太快。

## Flow 比較詳述

### 1. `payment-order-status-repair`

中文名稱：金流訂單狀態修正
證據層級：專案存在 / code-backed；Nick 貢獻待確認

已確認：

- `DataListService::repairOrderService()` 會依訂單號推 payment order month table，更新 status / remark / last modify time。
- `UserCurrency::repairOrder()` 是後台修正入口之一。
- `public/views/gm/txsq/**` 有修復訂單 UI 線索。
- path-specific log 有 `RD-142`、`payment_order_search`、跨月查單與訂單修復相關線索。

Senior / Owner 價值：

- money correctness。
- 訂單狀態機。
- 人工修正、audit、reconciliation。
- callback / wallet side effect 可能牽涉一致性。

推測：

- `app_bi` 只負責人工入口，真正訂單 source of truth、callback、wallet side effect 在 `payment` 或其他後端。

待確認：

- `payment` repo 的 order state machine。
- callback 重送 / timeout / idempotency。
- wallet 入帳 / 出款 side effect。
- manual repair 是否有 approval、audit、reconcile。
- Nick 是否實際維護過。

履歷邊界：

- 目前不可寫正式履歷。
- 可當作「從後台人工修正入口追金流狀態機」的學習題。

### 2. `point-control-admin-operation`

中文名稱：單點控制 / 營運控制操作
證據層級：專案存在 / code-backed；Nick 貢獻待確認
狀態：已完成 Step 5；不更新履歷 / 自傳

已確認：

- `app/route.php` 有 `point-control` routes。
- `PointControlController` 有新增 / 修改 / 狀態切換 / 批量操作。
- flow 涉及 MySQL `point_control`、Redis `playerControl:playerControls:data`、`sendGmCommand()`、Mongo `log_point_control`。
- 既有 `flow.md` 已標清楚「只確認到 `app_bi` 發送端」。

Senior / Owner 價值：

- control plane 影響 runtime state。
- DB / Redis / GM command / audit log 跨資源一致性。
- partial success、retry、rollback、權限邊界。

推測：

- runtime 生效邏輯在下游 GM / game service。

待確認：

- GM command receiver。
- runtime Redis consumer。
- GM command 是否 idempotent。
- Nick 是否實際維護過。

履歷邊界：

- 不更新履歷。
- 只能當面試分析素材，不能說 Nick 主導單點控制。

### 3. `admin-config-redis-sync`

中文名稱：後台設定同步 Redis
證據層級：專案存在 / code-backed；Nick 貢獻待確認
狀態：已完成 Step 5

已確認：

- `RedisSynchronize.php` 有多個 `save*ToRedis()` method。
- 多種設定從 MySQL 查出後投影到 Redis：`settings`、`clientSettings`、`serviceSettings`、`channel:{code}`、`activity:conf:*`、`Game:List:*`。
- 部分同步會送 `sendGmCommand()` 通知 runtime reload。
- git history 有欄位漏投影與同步邊界修正，例如 `whatsapp`、`notEmbeddedMerchantList`、merchant status filter。

Senior / Owner 價值：

- control plane -> runtime projection。
- cache consistency。
- config rollout / rollback。
- partial success 與 stale config。
- observability / reconcile。

推測：

- 多個 runtime service 會讀 Redis projection。
- `waitSyn` 是 UI / 後台提示，不是強一致保證。

待確認：

- runtime consumer。
- reload ack / retry / compensation。
- DB vs Redis reconcile。
- Nick 是否實際維護過。

履歷邊界：

- 不更新履歷。
- Step 4 可轉成保守面試 case，重點放「我如何分析設定同步風險」而不是「我主導設定中心」。
- Step 5 已判定不更新 `05-resume-master-zh.md` / `08-application-autobiography-zh.md`。

### 4. `daily-game-record-summary`

中文名稱：每日遊戲資料彙總 / RTP 查詢
證據層級：專案存在 / code-backed；Nick 貢獻待確認

已確認：

- `GameData.php` / `DataReportService.php` 有報表查詢與彙總處理線索。
- 近期 log 有 `daily_game_record_summary`、SQL 修正、金額倍率、提示生成資料時間等修改。

Senior / Owner 價值：

- 報表 projection。
- 查詢端與 truth source 的邊界。
- 跨日統計、補跑、延遲資料。
- 報表是否可被營運誤當交易真相。

推測：

- producer 可能在 `game_job` 或資料處理 repo。

待確認：

- `daily_rtp_total` / `log_game_daily_record` 寫入端。
- 補跑 / reconcile。
- 報表延遲標示與查詢效能限制。

履歷邊界：

- 目前只當報表 / projection 分析素材。
- 不寫成交易資料 owner。

### 5. `game-round-record-query`

中文名稱：遊戲局紀錄查詢 / production troubleshooting 入口
證據層級：專案存在 / code-backed；Nick 貢獻待確認

已確認：

- `GameRound.php` 有多種遊戲局查詢入口。
- `GameRoundService.php` 參與查詢服務。
- `public/views/gameRound/**` 有多個牌局查詢頁。
- migrations 有 game round menu 線索。

Senior / Owner 價值：

- 玩家申訴 / 派彩爭議 / 局號查詢。
- production troubleshooting。
- log schema、跨日分表、provider record 差異。

推測：

- 寫入端在 `iwin_gameserver`、`third_games_api` 或 `game_api`。

待確認：

- log writer。
- 分表規則。
- 查詢範圍與效能限制。

履歷邊界：

- 可作 troubleshooting 素材。
- 未掃 log writer 前，不寫完整遊戲局資料鏈路。

### 6. `admin-rbac-permission-check`

中文名稱：後台 RBAC / 權限判斷
證據層級：專案存在 / code-backed；Nick 貢獻待確認

已確認：

- `Base::_initialize()` 有 permission check，會看 Redis 中的 menu data / path。
- `Auth::permission()` 是前端 / 後台權限判斷入口之一。
- menu migration 定義 classaction / basic / oplog 等欄位。

Senior / Owner 價值：

- 高風險後台操作的 safety boundary。
- 前端 hide menu 不等於後端授權。
- 權限 cache stale / menu mapping 錯誤。

推測：

- 個別 controller 可能有 override 或 parent init 邊界，需要逐條確認。

待確認：

- 高風險操作是否都有後端 enforcement。
- role / menu 更新後 cache refresh 策略。
- Nick 是否修過權限問題。

履歷邊界：

- 除非有實際修權限或安全事故 evidence，不單獨寫履歷。

### 7. `coupon-trade-admin-operation`

中文名稱：兌換碼 / Coupon Trade 營運操作
證據層級：專案存在 / code-backed；Nick 貢獻待確認

已確認：

- 近期主線 log 有 `feat: coupon trade`、玩家名單生成、`couponTradeRecord`。
- `ActivityManage.php` 有 `getCouponTradeList()`、`addCouponTrade()`、`disableCouponTrade()`、`couponTradeRecord()`、`couponTradeDetail()`。
- `public/views/activityManage/couponTrade/**` 有列表、新增 / 編輯、停用、紀錄查詢 UI。

Senior / Owner 價值：

- 活動兌換碼的使用資格、有效時間、停用、使用紀錄。
- 若接到 wallet / bonus，才會提升為 money correctness 題。

推測：

- 實際使用端不在 `app_bi`。

待確認：

- 使用端 API。
- 兌換是否有 concurrency / idempotency / used_count 一致性問題。
- 是否影響 wallet、bonus、activity ledger。
- Nick 是否實際維護過。

履歷邊界：

- 目前價值中低，不優先。
- 未掃使用端前，不寫完整活動 / 錢包成果。

### 8. `app-bi-report-export`

中文名稱：BI 報表查詢與匯出
證據層級：專案存在 / code-backed；Nick 貢獻待確認

已確認：

- `DataReportService.php` 有多種報表查詢 / export 處理。
- `extend/PHPExcel`、`public/excel/**`、多個 `*Down` / export 入口支持匯出線索。

Senior / Owner 價值：

- 營運查詢效能。
- 報表資料與 truth source 分離。
- 大查詢 / 匯出對 DB、PHP、Mongo 的壓力。

推測：

- 大量匯出可能造成同步查詢壓力，但本次未看到 production incident。

待確認：

- row limit。
- background job。
- producer / denormalized table。
- reconciliation 規則。

履歷邊界：

- 目前不建議深挖。
- 除非補到真實效能問題或優化 evidence。

## 維度比較

| Flow | Money correctness | Transaction / State | Idempotency / Retry | Cache / Redis | Downstream / Callback | Observability / Recovery | 面試價值 | 履歷價值 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `payment-order-status-repair` | 高 | 高 | 高待確認 | 中 | 高待確認 | 高待確認 | 高 | 高但未證明 |
| `point-control-admin-operation` | 中 | 高 | 中高 | 高 | 高待確認 | 中 | 高 | 中但不可寫 |
| `admin-config-redis-sync` | 中 | 中 | 中 | 高 | 中待確認 | 中高 | 高 | 中但不可寫 |
| `daily-game-record-summary` | 中 | 中高 | 中 | 中 | 中待確認 | 中 | 中高 | 中但不可寫 |
| `game-round-record-query` | 中 | 中 | 低 | 低 | 中待確認 | 中高 | 中 | 中低 |
| `admin-rbac-permission-check` | 中 | 中 | 低 | 中 | 低 | 中 | 中 | 中低 |
| `coupon-trade-admin-operation` | 待確認 | 中 | 中待確認 | 低 | 中待確認 | 中 | 中 | 中低 |
| `app-bi-report-export` | 低 | 低 | 低 | 低 | 低 | 中 | 中低 | 低 |

## 只看到後台 / BI 入口的邊界

目前不適合直接寫履歷：

- `payment-order-status-repair`：只確認到人工修正入口，未確認 payment source of truth。
- `point-control-admin-operation`：只確認到 `app_bi` 發送端，未確認 GM receiver / runtime consumer。
- `admin-config-redis-sync`：只確認到 Redis 寫入端與部分讀取端，未確認 runtime consumer。
- `daily-game-record-summary`：只確認到查詢 / 展示端，未確認 producer。
- `game-round-record-query`：只確認到查詢端，未確認 log writer。
- `admin-rbac-permission-check`：只確認到權限框架，未逐條確認高風險操作 enforcement。
- `coupon-trade-admin-operation`：只確認到營運管理端，未確認使用端。
- `app-bi-report-export`：只確認到報表 consumer，未確認 BI log producer。

## Step 2 結論

目前已完成：

```text
point-control-admin-operation Step 1-5
admin-config-redis-sync Step 1-5
```

本次 Step 2 已補齊：

- 新 Step 1 候選排序。
- `coupon-trade-admin-operation`。
- 價值排序與下一步排序分離。
- 每條 flow 的證據層級。
- 「後台 / BI 入口不可寫履歷」邊界。

下一步只推薦一件事：

```text
app_bi daily-game-record-summary Step 3
```

原因：

- `point-control-admin-operation` 已完成 Step 5，且不更新履歷 / 自傳。
- `admin-config-redis-sync` 已完成 Step 5。
- `daily-game-record-summary` 是同 project 下一條未完成且仍有 Senior / Owner 價值的 flow。
