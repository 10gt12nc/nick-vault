# Evidence - point-control-admin-operation

更新時間：2026-05-14
狀態：Step 5 已完成；本文件為 evidence 附錄
掃描等級：Level 2 Flow 深掃
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 本次實際掃描範圍

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
- `projects/iwin/app_bi/flows/point-control-admin-operation/evidence.md`
- `projects/iwin/app_bi/flows/point-control-admin-operation/decision-notes.md`
- `projects/iwin/app_bi/flows/point-control-admin-operation/interview.md`
- `projects/iwin/app_bi/flows/point-control-admin-operation/claim-boundary.md`

已讀 code repo：

- `/Users/nick/Git/iwin/app_bi`

已看 branch / log：

- 目前分支：`main`
- 遠端分支清單已看。
- 近期主線 log 已看。
- path-specific log 已看：
  - `app/admin/controller/PointControlController.php`
  - `app/business/DataListService.php`
  - `app/common.php`
  - `app/route.php`
  - `app/admin/model/PointControl.php`
  - `app/admin/model/PointControlSwitch.php`
  - `app/admin/model/PointControlAllowlist.php`
  - `app/admin/model/PointControlAllowlistLog.php`
  - `public/admin/gm/dandian/**`
  - `db/migrations/menuAdd0800.sql`

已讀 code path：

- `app/route.php`
- `app/common.php`
- `app/common/controller/Base.php`
- `app/admin/controller/PointControlController.php`
- `app/business/DataListService.php`
- `public/admin/gm/dandian/**` 檔案清單

已看重要 commit / history：

- `e83c729 feat(#first commit): first commit`
- `0ad2f51 控制管理汇出功能 新增判断找不到log_user就跳过`
- `0b57858 Revert "控制管理汇出功能 新增判断找不到log_user就跳过"`
- `98e89fb feat(#RD-146): app bi新增antplay`

未掃：

- 未 checkout 每個遠端分支逐一比對。
- 未逐檔逐行掃完整 `app_bi`。
- 未逐 commit diff 掃所有相關 commit。
- 未掃下游 GM receiver。
- 未掃 runtime Redis consumer。
- 未掃 `game_api`、`game_job`、`iwin_gameserver`。
- 未確認 Nick 個人 MR / ticket / commit / production issue。

## 掃描等級判斷

本次使用 Level 2。

原因：

- Nick 指定 `point-control-admin-operation Step 3 重整`，這是單條 flow 深挖。
- Step 3 需要追 route、controller、service、Redis、GM command、Mongo log、path-specific history。
- 目前尚未定位下游 GM receiver / runtime consumer，直接做 Level 3 逐檔逐 commit 的成本高但履歷 evidence 仍不足。

本次不宣稱：

- 完整逐檔逐行。
- 完整後端 runtime flow。
- Nick 真實開發過。

## Step 5 補掃紀錄

已於 2026-05-14 重新判定履歷 / 自傳邊界。

已補讀：

- `projects/iwin/app_bi/flows/point-control-admin-operation/interview.md`
- `projects/iwin/app_bi/flows/point-control-admin-operation/claim-boundary.md`
- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`

Step 5 判定：

- 不更新 `05-resume-master-zh.md`。
- 不更新 `08-application-autobiography-zh.md`。
- 原因是目前只有專案存在 / code-backed evidence，沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認。

## Route evidence

來源：`/Users/nick/Git/iwin/app_bi/app/route.php`

已確認：

- `GET point-control` -> `PointControlController::index`
- `POST point-control` -> `PointControlController::storeOrUpdate`
- `POST point-control/:pointControlId` -> `PointControlController::storeOrUpdate`
- `POST point-control/status` -> `PointControlController::updateStatus`
- `GET point-control/:pointControlId/control-logs` -> `PointControlController::controlLogs`
- `GET point-control/:pointControlId/operation-logs` -> `PointControlController::operationLogs`
- `point-control/allowlist` 系列 route 存在。
- `point-control/switch` 系列 route 存在。

判斷：

- 本次 Step 3 主線是控制名單新增 / 修改 / 狀態切換 / 批量操作。
- allowlist / switch 是相關 extension，不作本次主線。

## Controller evidence

來源：`/Users/nick/Git/iwin/app_bi/app/admin/controller/PointControlController.php`

已確認 Redis key：

- `playerControl:playerControls:data`
- `playerControl:freeRechargeBtns:data:`
- `playerControl:whiteListPlayers:data`

本次主線使用：

- `playerControl:playerControls:data`

已確認 method：

- `index()`
  - 遍歷 channel Redis。
  - 讀 `playerControl:playerControls:data`。
  - 若 `controlLimitRemain <= 0`，更新 MySQL `point_control.status = 2`。
  - 查 `point_control` list。
  - 補玩家暱稱與 Redis 剩餘額度。
- `storeOrUpdate()`
  - 驗證玩家存在。
  - 由 `player_id` 推導 channel / center。
  - 開啟 MySQL transaction。
  - 寫 `point_control`。
  - 寫 Redis hash。
  - 送 `SET_PLAYER_CONTROL` GM command。
  - GM 成功後 commit DB。
  - commit 後寫 Mongo `log_point_control`。
  - GM 失敗時 rollback DB。
- `updateStatus()`
  - 更新 `point_control.status`。
  - status 為 0 時 Redis hDel 並送 `DELETE_PLAYER_CONTROL`。
  - status 非 0 時 Redis hSet 並送 `SET_PLAYER_CONTROL`。
  - GM 成功後 commit DB 並寫 Mongo log。
  - GM 失敗時 rollback DB。
- `batchControllerAdd()`
  - 檢查 xlsx size / ext。
  - `DataListService::checkBatchAddControl()` 校驗。
  - 批量 insert MySQL。
  - 寫 Redis。
  - 依 center 分組送 `SET_PLAYER_CONTROL`。
  - 失敗時重送一次，但未看到第二次結果後中止。
  - 寫 Mongo log。
  - commit DB。
- `batchControllerEdit()`
  - 檢查 xlsx。
  - `DataListService::checkBatchEditControl()` 校驗。
  - 批量 update MySQL。
  - 依 status hDel / hSet Redis。
  - 分組送 `DELETE_PLAYER_CONTROL` / `SET_PLAYER_CONTROL`。
  - 失敗時重送一次，但未看到第二次結果後中止。
  - 寫 Mongo log。
  - commit DB。
- `operationLogs()`
  - 查 Mongo `log_point_control`。
  - `quota` 先套用 `quota`，下一行又用 `remaining_quota` 覆蓋，需確認 UI 影響。
- `pointControlDown()`
  - 呼叫 `DataListService::exportPointData()` 產生匯出資料。

## DataListService evidence

來源：`/Users/nick/Git/iwin/app_bi/app/business/DataListService.php`

已確認：

- `insertAllPointData()`
  - 組 MySQL `point_control` insert data。
  - 依 player id 第二位分 center。
  - 組 Mongo `log_point_control` data。
- `checkBatchAddControl()`
  - 呼叫 `checkPointControl()`。
  - 檢查玩家是否已存在於 `point_control`。
- `checkBatchEditControl()`
  - 檢查玩家是否存在於 `point_control`。
  - 若 coin type / quota / difficulty / status 完全未變，會從更新資料移除。
- `batchUpdatePoint()`
  - 逐筆 update `point_control`。
- `insertEditPointData()`
  - 組批量更新 Mongo log。
- `checkPointControl()`
  - 檢查欄位格式、玩家 ID、收放分類型、額度、難度、狀態。
  - 批量最多 20 筆。
  - 檢查 xlsx 內玩家 ID 不重複。
  - 查 `log_user` 確認玩家存在。
- `exportPointData()`
  - 查 `point_control`。
  - 讀 Redis `playerControl:playerControls:data`。
  - 補玩家暱稱與剩餘額度。
- `batchInsertMongoPointData()`
  - 寫 Mongo `log_point_control`。

## GM command evidence

來源：`/Users/nick/Git/iwin/app_bi/app/common.php`

已確認：

- `sendGmCommand($params, $index = null)` 讀 Redis `settings.center_http`。
- 若有 `$index`，用 `$index - 1` 選 URL。
- 用 `Common::httpServer()` 發送 JSON。
- 回傳結果不存在或 `Err > 0` 時回 false。
- 會記錄 GM 命令路徑、參數與結果到 log。

安全處理：

- 本文件不記錄 endpoint、internal URL、IP 或敏感參數。

推測：

- `center_http` 是後台通知下游 center / GM / Java service 的路由表。

待確認：

- 下游 handler 所在 repo。
- `Err` 完整語意。
- 是否有 request id / operation id / version 防重。

## Permission evidence

來源：`/Users/nick/Git/iwin/app_bi/app/common/controller/Base.php`

已確認：

- `Base::_initialize()` 有登入與權限檢查。
- `permission_check` 預設為 true。
- 會讀 Redis 中的 `{mid}:menuData`。
- 若 path / controller action 不在權限資料，會查 `menu.basic`，否則回 `NO_AUTH`。
- `PointControlController::_initialize()` 中 `parent::_initialize()` 被註解。

待確認：

- `point-control` route 是否另有前置權限保護。
- `PointControlController` 不呼叫 parent 是否代表繞過 Base 權限。
- 批量 / 狀態切換是否有其他 approval 或二次確認。

判斷：

- 權限邊界不能說完整已確認。

## Git history evidence

已確認：

- `e83c729`：point-control 相關 code 多數來自初始 commit。
- `0ad2f51`：曾補「控制管理匯出找不到 log_user 就跳過」。
- `0b57858`：上述匯出防呆被 revert。
- `98e89fb`：`sendGmCommand()` 通用邏輯因 antplay 新增有調整，但不能直接等同 point-control flow 修改。

解讀：

- 這些 history 可證明 point-control / 匯出 / GM command 周邊確實存在維護痕跡。
- 目前沒有看到 Nick 本人 commit。
- 不能用這些 commit 寫 Nick 個人履歷成果。

## 已確認 / 推測 / 待確認

### 已確認

- `point-control` route 存在。
- `PointControlController` 會操作 MySQL `point_control`。
- flow 會寫 Redis `playerControl:playerControls:data`。
- flow 會送 `SET_PLAYER_CONTROL` / `DELETE_PLAYER_CONTROL` GM command。
- flow 會寫 Mongo `log_point_control`。
- 批量操作最多 20 筆，且會依 center 分組送 GM command。
- 查詢列表會依 Redis remain 更新 DB status=2。
- 權限邊界有疑點，需要再確認。

### 推測

- Redis 是 runtime 控制狀態 projection。
- GM command 通知下游 runtime 套用或刪除控制。
- 下游服務可能收到 GM command 後讀 Redis。

### 待確認

- GM command receiver repo。
- runtime Redis consumer。
- `point_control.status` 完整生命週期。
- MySQL、Redis、GM command 誰是 source of truth。
- GM command idempotency。
- 批量 partial failure 的實際 SOP。
- `PointControlController` 權限檢查是否完整。
- Nick 實際參與 evidence。

## Secret / 安全檢查

本文件只記錄：

- repo path
- class / method
- table / Redis key 名稱
- commit hash 與 commit message

未寫入：

- token
- password
- private key
- internal IP
- production URL
- 客戶資料

## 本次不能主張

不能主張：

- 已完整掃完後端 runtime。
- 已確認 Nick 主導或開發。
- 已確認此 flow 有完整 idempotency / retry / reconcile。
- 已確認 `app_bi` 是 truth source。
- 已確認可以寫入履歷 master。
