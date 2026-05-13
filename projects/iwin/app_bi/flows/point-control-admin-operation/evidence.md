# Evidence - point-control-admin-operation

更新時間: 2026-05-13
掃描等級: Level 2-lite Flow 重整
狀態: 已依新版 KB 補掃描範圍與未確認邊界

## 為什麼是 Level 2-lite

這次是 Step 3 重整，已選定單一 flow，所以不是 Level 1。

但本次仍不是 Level 3，原因:

- 未逐檔逐行掃完整 repo。
- 未逐一 checkout 遠端分支。
- 未逐 commit diff 追每筆修改原因。
- 尚未掃下游 GM receiver / runtime consumer。

所以本文件只能證明:

```text
app_bi 後台控制面 / 發送端 / audit 入口
```

不能證明:

```text
完整後端 runtime flow
Nick 主導開發
完整 system owner
```

## 自動重讀紀錄

已重讀 KB:

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`

已重讀 vault:

- `projects/iwin/app_bi/README.md`
- `projects/iwin/app_bi/step2-flow-comparison.md`
- `flows/point-control-admin-operation/flow.md`
- `flows/point-control-admin-operation/evidence.md`
- `flows/point-control-admin-operation/interview.md`
- `flows/point-control-admin-operation/claim-boundary.md`

已重讀 code repo:

- `/Users/nick/Git/iwin/app_bi`

已看分支 / log:

- 目前分支: `main`
- 遠端分支清單已看。
- 近期主線 log 已看。
- path-specific log 已看:
  - `app/admin/controller/PointControlController.php`
  - `app/business/DataListService.php`
  - `app/common.php`
  - `app/route.php`

已看 code path:

- `app/route.php`
- `app/common.php`
- `app/common/controller/Base.php`
- `app/admin/controller/PointControlController.php`
- `app/admin/model/PointControl.php`
- `app/admin/model/PointControlSwitch.php`
- `app/admin/model/PointControlAllowlist.php`
- `app/admin/model/PointControlAllowlistLog.php`
- `app/business/DataListService.php`
- `public/admin/gm/dandian/**`
- `db/migrations/menuAdd0800.sql`

未掃:

- 未掃下游 GM receiver。
- 未掃 runtime Redis consumer。
- 未掃 `game_api` / `game_job` / `iwin_gameserver`。
- 未確認 Nick 個人 MR / ticket / commit。
- 未做 Level 3 commit diff timeline。

## 舊文件狀態

既有 Step 3 文件狀態: `需補 evidence`

原因:

- 舊版已能說明 flow，但掃描等級與未掃範圍不夠清楚。
- 下游未掃邊界需要放大。
- 面試與 claim boundary 需要更保守。

## Route evidence

來源: `/Users/nick/Git/iwin/app_bi/app/route.php`

已確認:

- `point-control` GET -> `PointControlController::index`
- `point-control` POST -> `PointControlController::storeOrUpdate`
- `point-control/:pointControlId` POST -> `PointControlController::storeOrUpdate`
- `point-control/status` POST -> `PointControlController::updateStatus`
- `point-control/:pointControlId/control-logs` -> `PointControlController::controlLogs`
- `point-control/:pointControlId/operation-logs` -> `PointControlController::operationLogs`
- `point-control/allowlist` 系列 -> `PointControlAllowlistController`
- `point-control/switch` 系列 -> `PointControlSwitchController`

判斷:

- 本次主 flow 只重整 `PointControlController` 的控制名單新增 / 修改 / 狀態切換 / 批量操作。
- allowlist / switch 是相關 extension，不作本次主線。

## Controller evidence

來源: `/Users/nick/Git/iwin/app_bi/app/admin/controller/PointControlController.php`

已確認:

- Redis key:
  - `playerControl:playerControls:data`
  - `playerControl:freeRechargeBtns:data:`
  - `playerControl:whiteListPlayers:data`
- `index()`:
  - 遍歷 channel Redis。
  - 讀 `playerControl:playerControls:data`。
  - 若 `controlLimitRemain <= 0`，更新 MySQL `point_control.status = 2`。
  - 查 `point_control` list。
  - 補玩家暱稱與 Redis 剩餘額度。
- `storeOrUpdate()`:
  - 驗證玩家存在。
  - 由 `player_id` 推導 channel / center。
  - 開啟 MySQL transaction。
  - 寫 `point_control`。
  - 寫 Redis hash。
  - 送 `SET_PLAYER_CONTROL` GM command。
  - GM 成功後 commit DB。
  - commit 後寫 Mongo `log_point_control`。
  - GM 失敗時 rollback DB。
- `updateStatus()`:
  - 更新 `point_control.status`。
  - status 為 0 時 Redis hDel 並送 `DELETE_PLAYER_CONTROL`。
  - status 非 0 時 Redis hSet 並送 `SET_PLAYER_CONTROL`。
  - GM 成功後 commit DB 並寫 Mongo log。
  - GM 失敗時 rollback DB。
- `batchControllerAdd()`:
  - 檢查 xlsx size / ext。
  - `DataListService::checkBatchAddControl()` 校驗。
  - 批量 insert MySQL。
  - 寫 Redis。
  - 依 center 分組送 `SET_PLAYER_CONTROL`。
  - 失敗時重送一次，但未看到第二次結果後中止。
  - 寫 Mongo log。
  - commit DB。
- `batchControllerEdit()`:
  - 檢查 xlsx。
  - `DataListService::checkBatchEditControl()` 校驗。
  - 批量 update MySQL。
  - 依 status hDel / hSet Redis。
  - 分組送 `DELETE_PLAYER_CONTROL` / `SET_PLAYER_CONTROL`。
  - 失敗時重送一次，但未看到第二次結果後中止。
  - 寫 Mongo log。
  - commit DB。
- `operationLogs()`:
  - 查 Mongo `log_point_control`。
  - 目前 code 連續指定 `quota`，第二次使用 `remaining_quota`，可能覆蓋原 quota 欄位。

待確認:

- 上述 `operationLogs()` 欄位覆蓋是否實際造成 UI 顯示問題。
- 批量 GM command 第二次失敗時，是否有其他層處理。

## GM command evidence

來源: `/Users/nick/Git/iwin/app_bi/app/common.php`

已確認:

- `sendGmCommand($params, $index = null)`:
  - 讀 Redis `settings.center_http`。
  - 依 `$index - 1` 選下游 endpoint。
  - 用 `Common::httpServer()` 發送 JSON。
  - 記錄 path / params / result。
  - result 不存在或 `Err > 0` 時回 false。

安全處理:

- 不記錄 endpoint、internal URL、IP 或敏感參數。

推測:

- `center_http` 是後台通知下游 center / GM / Java service 的路由表。

待確認:

- 下游 handler 所在 repo。
- `Err` 完整語意。
- 是否有 request id / operation id / version 防重。

## Permission evidence

來源: `/Users/nick/Git/iwin/app_bi/app/common/controller/Base.php`

已確認:

- `Base::_initialize()` 會做登入檢查。
- `permission_check` 預設為 true。
- 會讀 Redis 中的 `{mid}:menuData`。
- 若 path / controller action 不在權限資料，會查 `menu.basic`，否則回 `NO_AUTH`。

待確認:

- `PointControlController::_initialize()` 裡 `parent::_initialize()` 被註解，是否代表此 controller 未走 Base 權限檢查，或由其他入口 / middleware 補上。
- `point-control` route 是否另有前置權限保護。
- 高風險批量操作是否有二次確認 / approval。

判斷:

- 權限邊界不能直接說完整已確認。

## DataListService evidence

來源: `/Users/nick/Git/iwin/app_bi/app/business/DataListService.php`

已確認:

- `insertAllPointData()`:
  - 組 MySQL `point_control` insert data。
  - 依 player id 第二位分 center。
  - 組 Mongo `log_point_control` data。
- `checkBatchAddControl()`:
  - 呼叫 `checkPointControl()`。
  - 檢查玩家是否已存在於 `point_control`。
- `checkBatchEditControl()`:
  - 檢查玩家是否存在於 `point_control`。
  - 若 coin type / quota / difficulty / status 完全未變，會從更新資料移除。
- `batchUpdatePoint()`:
  - 逐筆 update `point_control`。
- `insertEditPointData()`:
  - 組批量更新 Mongo log。
- `checkPointControl()`:
  - 檢查資料格式、玩家 ID、收放分類型、額度、難度、狀態。
  - 批量最多 20 筆。
  - 檢查 xlsx 內玩家 ID 不重複。
  - 查 `log_user` 確認玩家存在。
- `exportPointData()`:
  - 查 `point_control`。
  - 讀 Redis `playerControl:playerControls:data`。
  - 補玩家暱稱與剩餘額度。
- `batchInsertMongoPointData()`:
  - 寫 Mongo `log_point_control`。

## DB / Redis / Mongo / external side effect

已確認:

- MySQL:
  - `point_control`
  - `point_control_switch`
  - `point_control_allowlist`
  - `point_control_allowlist_logs`
- Redis:
  - `playerControl:playerControls:data`
  - `settings.center_http`
- Mongo:
  - `log_point_control`
- External side effect:
  - `sendGmCommand()` HTTP call

未確認:

- 下游 runtime 是否使用 Redis 作 source of truth。
- 下游是否只在 GM command 成功後 reload。
- 下游是否會反寫狀態。

## 已確認結論

- 這條 flow 在 `app_bi` 內可確認為:

```text
後台操作
-> MySQL point_control
-> Redis playerControl projection
-> GM command
-> Mongo operation log
```

- 主要 Senior / Owner 價值是 control plane side effect 與跨資源一致性。
- 批量操作比單筆更高風險，因為會分 center 發送 GM command。

## 推測結論

- Redis 可能是 runtime 控制狀態 projection。
- GM command 可能通知下游 reload / apply 控制資料。
- `index()` 查詢時同步 status=2，代表 completion state 可能依 Redis remain 反推。

## 待確認清單

- GM command receiver repo。
- runtime Redis consumer。
- `point_control.status` 完整生命週期。
- MySQL、Redis、GM command 誰是 source of truth。
- GM command idempotency。
- 批量 partial failure 的實際 SOP。
- `PointControlController` 權限檢查是否完整。
- Nick 實際參與 evidence。

## 本次不能主張

不能主張:

- 已完整掃完後端 runtime。
- 已確認 Nick 主導或開發。
- 已確認此 flow 有完整 idempotency / retry / reconcile。
- 已確認 `app_bi` 是 truth source。
- 已確認可以寫入履歷 master。
