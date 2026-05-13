# Evidence - point-control-admin-operation

掃描時間: 2026-05-13
參考 repo: `/Users/nick/Git/iwin/app_bi`
寫入位置: `/Users/nick/Git/nick/nick-vault/projects/iwin/app_bi/flows/point-control-admin-operation/`

## 重複檢查

已確認：

- `projects/iwin/app_bi` 在 Step 3 前只有：
  - `README.md`
  - `step2-flow-comparison.md`
- `projects/iwin/app_bi/flows/` 在本次 Step 3 前不存在。
- `point-control-admin-operation` 在 vault 中只有 Step 1 / Step 2 的候選與排序說明，沒有完整 flow 深挖文件。

## Branch / commit 參考

已確認：

- `/Users/nick/Git/iwin/app_bi` 目前掃描分支為 `main`。
- 最近主線 commit 包含：
  - `4a206a2 feat: coupon trade`
  - `57819d9 feat: 加上 生成玩家名单功能`
  - `840d14c Merge branch 'feature/modify_info' into 'main'`
  - `73258e1 feat(#feature/modify_info):每日游戏数据汇总修改info`
  - `baa6f0a feat(#feature/GSC-reason):logDefine2 加 GSC reason`
  - `311bd4b feat(#feature/game_summary_note): 每日游戏数据汇总 加上提示生成資料時間v2`
- point-control 相關檔案歷史有看到：
  - `d3b33ee feat(#RD-142): oplog ,訂單修復狀態搬到service，新開api給提现状态修正列表`
  - `0b57858 Revert "控制管理汇出功能 新增判断找不到log_user就跳过"`
  - `0ad2f51 控制管理汇出功能 新增判断找不到log_user就跳过`
  - `e83c729 feat(#first commit): first commit`

限制：

- commit log 只能證明檔案歷史，不足以證明 Nick 主導或開發此 flow。

## Route evidence

來源: `/Users/nick/Git/iwin/app_bi/app/route.php`

已確認：

- `point-control/allowlist/:id/logs` -> `PointControlAllowlistController::logs`。
- `point-control/allowlist/:id/delete` -> `PointControlAllowlistController::destroy`。
- `point-control/allowlist` GET/POST -> `PointControlAllowlistController::index/store`。
- `point-control/switch` GET/POST -> `PointControlSwitchController::index/update`。
- `point-control/:pointControlId/control-logs` -> `PointControlController::controlLogs`。
- `point-control/:pointControlId/operation-logs` -> `PointControlController::operationLogs`。
- `point-control` GET -> `PointControlController::index`。
- `point-control` POST -> `PointControlController::storeOrUpdate`。
- `point-control/:pointControlId` POST -> `PointControlController::storeOrUpdate`。
- `point-control/status` POST -> `PointControlController::updateStatus`。

判斷：

- 本 Step 3 選主線 `PointControlController`，allowlist / switch 先列為相關 extension，不在本次深挖主體內。

## Controller evidence

來源: `/Users/nick/Git/iwin/app_bi/app/admin/controller/PointControlController.php`

已確認：

- class 內定義 Redis keys：
  - `playerControl:playerControls:data`
  - `playerControl:freeRechargeBtns:data:`
  - `playerControl:whiteListPlayers:data`
- `_initialize()` 讀 request body、admin cookie `mid` / `uname`，並讀 channel list。
- `index()`：
  - 逐 channel 讀 Redis `playerControl:playerControls:data`。
  - 若 `controlLimitRemain <= 0`，把 DB `point_control.status` 更新為 `2`。
  - 查 `point_control` list。
  - 補玩家暱稱與 Redis remaining quota。
- `storeOrUpdate()`：
  - 驗證玩家存在。
  - 依玩家 ID 推導 channel / center。
  - 驗證 quota。
  - 新增或修改 `point_control`。
  - 寫入 Redis hash。
  - 發送 `SET_PLAYER_CONTROL` GM command。
  - 成功後 commit DB 並寫 Mongo `log_point_control`。
  - 失敗則 rollback DB。
- `updateStatus()`：
  - 更新 `point_control.status`。
  - status 為 `0` 時 Redis hDel 並送 `DELETE_PLAYER_CONTROL`。
  - 其他 status 時 Redis hSet 並送 `SET_PLAYER_CONTROL`。
  - GM 成功後 commit DB 並寫 Mongo log。
  - GM 失敗時 rollback DB。
- `batchControllerAdd()`：
  - 讀 xlsx。
  - 用 `DataListService::checkBatchAddControl()` 檢查。
  - 批次 insert `point_control`。
  - 寫 Redis。
  - 依 center 送 `SET_PLAYER_CONTROL`。
  - 寫 Mongo log。
- `batchControllerEdit()`：
  - 讀 xlsx。
  - 用 `DataListService::checkBatchEditControl()` 檢查。
  - 批次 update `point_control`。
  - status 為 `0` 的玩家 hDel，其他 hSet。
  - 分別送 `DELETE_PLAYER_CONTROL` / `SET_PLAYER_CONTROL`。
  - 寫 Mongo log。
- `pointControlDown()`：
  - 呼叫 `DataListService::exportPointData()` 產出匯出資料。
- `operationLogs()`：
  - 以玩家 ID 與時間範圍查 Mongo `log_point_control`。

待確認：

- `operationLogs()` 中 `quota` 欄位被連續指定兩次，第二次使用 `remaining_quota`，看起來可能覆蓋原 quota 顯示。這只是 code observation，需實測或問原作者確認。

## GM command evidence

來源: `/Users/nick/Git/iwin/app_bi/app/common.php`

已確認：

- `sendGmCommand($params, $index = null)` 會：
  - 連線 Redis config。
  - 依 channel select Redis DB。
  - 從 Redis hash `settings` 讀 `center_http`。
  - 依 `$index - 1` 選下游 HTTP endpoint。
  - 用 `Common::httpServer()` 發送 JSON。
  - 記錄 GM command path、params、result。
  - 若 result 不存在或 `Err > 0`，回 false。

安全處理：

- 本文件不記錄實際 HTTP endpoint、internal URL、IP 或完整敏感參數。

推測：

- `center_http` 是後台通知下游 center / Java / game service 的路由表。

待確認：

- 下游服務實際 repo 與 handler。
- `Err` 的完整定義。

## DataListService evidence

來源: `/Users/nick/Git/iwin/app_bi/app/business/DataListService.php`

已確認：

- `checkBatchAddControl()`：
  - 呼叫 `checkPointControl()`。
  - 用 `point_control.player_id` 檢查是否已存在。
- `checkBatchEditControl()`：
  - 呼叫 `checkPointControl()`。
  - 從 `point_control` 查 `id, player_id, coin_type, quota, difficulty, status`。
  - 若完全沒有變更，會從本次更新資料中移除。
- `batchUpdatePoint()`：
  - 逐筆更新 `point_control`。
- `insertEditPointData()`：
  - 組 Mongo `log_point_control` 寫入資料。
- `checkPointControl()`：
  - 檢查玩家 ID、收放分類型、額度、難度、狀態。
- `exportPointData()`：
  - 查 `point_control`。
  - 讀 Redis `playerControl:playerControls:data`。
  - 補玩家暱稱。
  - 轉換 quota / remaining quota。
- `batchInsertMongoPointData()`：
  - collection 設為 `log_point_control`，逐筆 insert。

## Model evidence

來源: `/Users/nick/Git/iwin/app_bi/app/admin/model/`

已確認：

- `PointControl` model 對應 table `point_control`。
- `PointControlSwitch` model 對應 table `point_control_switch`。
- `PointControlAllowlist` model 對應 table `point_control_allowlist`。
- `PointControlAllowlistLog` model 對應 table `point_control_allowlist_logs`。

## 已確認結論

- 這條 flow 是「後台控制操作 -> MySQL -> Redis -> GM command -> Mongo log」。
- 它會影響 runtime 狀態，不只是後台列表管理。
- 主要一致性風險集中在 MySQL transaction 之外的 Redis、GM command、Mongo log。
- 批量操作有更高風險，因為它會依 center 分組影響多個玩家。

## 推測結論

- Redis 很可能是 runtime 讀取控制狀態的關鍵資料。
- GM command 很可能是通知下游 reload / apply 控制資料。
- `index()` 具有補同步 status 的副作用，代表 status completion 可能依 Redis remain 狀態反推。

## 待確認清單

- 下游 GM command handler 在哪個 repo。
- `point_control.status` 的完整生命週期定義。
- Redis 與 MySQL 誰是 truth source。
- GM command 是否具備 idempotency。
- 批量操作第二次 retry 失敗後，實際系統是否有人工補償 SOP。
- 操作權限是否只靠 route auth，還是 controller 外有 middleware / permission check。

