# app_bi - point-control-admin-operation

更新時間: 2026-05-13
Step: 3 重整
掃描等級: Level 2-lite
狀態: 已依新版 KB 重整

## 這條 flow 解決什麼

`point-control-admin-operation` 是後台對玩家單點控制名單做新增、修改、停用、批量操作與查詢的 flow。

它在 `app_bi` 中不是單純 CRUD，因為後台操作會影響:

- MySQL `point_control`
- Redis `playerControl:playerControls:data`
- GM command: `SET_PLAYER_CONTROL` / `DELETE_PLAYER_CONTROL`
- Mongo `log_point_control`

但要注意：目前只確認到 `app_bi` 後台操作面與 GM command 發送端。下游 GM receiver / runtime consumer 尚未掃，所以不能說完整後端 flow 已完成。

## 系統位置

已確認:

- 產品 / domain: iwin
- 專案: `app_bi`
- 類型: PHP / ThinkPHP 後台與 BI / control plane
- 主入口: `app/route.php` 的 `point-control` routes
- 主 controller: `PointControlController`
- 批量校驗 / 匯出 / log 組裝: `DataListService`
- 操作紀錄: Mongo `log_point_control`

推測:

- `app_bi` 是控制面，runtime 生效邏輯應在下游遊戲 / GM / Java service。
- Redis 是 runtime projection 或控制狀態資料源之一。

待確認:

- GM command handler。
- runtime Redis consumer。
- 下游是否還有正式 state machine。
- Nick 是否實際參與此 flow。

## 正常流程：單筆新增 / 修改

已確認:

1. Admin 呼叫 `POST point-control` 或 `POST point-control/:pointControlId`。
2. `storeOrUpdate()` 驗證玩家存在。
3. 由 `player_id` 推導 channel / center。
4. 讀 admin cookie: `mid`、`uname` / `nickname`。
5. 讀控制參數: `type`、`coin_type`、`quota`、`difficulty`。
6. 檢查 quota 不小於 0 且不超過上限。
7. 若新增且玩家已存在 `point_control`，直接回傳已被控制。
8. 開啟 MySQL transaction。
9. 新增或更新 `point_control`，狀態設為 `1`。
10. 寫入 Redis hash `playerControl:playerControls:data`。
11. 組 `SET_PLAYER_CONTROL` GM command。
12. 呼叫 `sendGmCommand($gmData, $center)`。
13. GM 成功後 commit DB。
14. commit 後寫 Mongo `log_point_control`。
15. GM 失敗時 rollback DB。

待確認:

- Redis 已寫但 GM 失敗時是否有其他清理機制。
- Mongo log 失敗是否會被全域 handler 補記。

## 正常流程：狀態切換

已確認:

1. Admin 呼叫 `POST point-control/status`。
2. `updateStatus()` 驗證玩家存在。
3. 更新 `point_control.status`。
4. 若 status 為 `0`，Redis hDel 並送 `DELETE_PLAYER_CONTROL`。
5. 若 status 非 `0`，Redis hSet 並送 `SET_PLAYER_CONTROL`。
6. GM 成功後 commit DB，並寫 Mongo log。
7. GM 失敗時 rollback DB。

推測:

- status `0` = 控制取消。
- status `1` = 控制中。
- status `2` = 控制完成。

待確認:

- status `2` 是否只由 `index()` 依 Redis remain 補同步。
- 下游是否也會改變或回報狀態。

## 正常流程：列表 / 匯出 / 操作紀錄

已確認:

- `index()` 會讀各 channel Redis，若 `controlLimitRemain <= 0`，把 DB status 更新為 `2`。
- 列表查 MySQL `point_control`，再補玩家暱稱與 Redis 剩餘額度。
- `pointControlDown()` 透過 `DataListService::exportPointData()` 匯出。
- `operationLogs()` 查 Mongo `log_point_control`。

風險:

- 查詢 API 有寫入副作用。
- Redis 狀態不準時，DB status 可能被同步成錯誤結果。
- `operationLogs()` 可能有欄位覆蓋問題，需實測確認。

## 正常流程：批量新增 / 修改

已確認:

- 批量操作吃 xlsx。
- 最大 20 筆。
- 會檢查玩家存在、重複玩家、欄位格式、控制狀態。
- 批量新增:
  - insert MySQL。
  - 寫 Redis。
  - 依 center 分組送 `SET_PLAYER_CONTROL`。
  - 寫 Mongo log。
- 批量修改:
  - update MySQL。
  - 依 status hDel / hSet Redis。
  - 分組送 `DELETE_PLAYER_CONTROL` / `SET_PLAYER_CONTROL`。
  - 寫 Mongo log。

風險:

- sendGmCommand 失敗後會重送一次，但未看到第二次失敗後中止。
- 可能產生 partial success。
- 批量操作影響多個玩家 runtime 狀態，應比單筆更重視 audit / dry-run / rollback。

## 資料與狀態

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
- GM command:
  - `SET_PLAYER_CONTROL`
  - `DELETE_PLAYER_CONTROL`

待確認:

- MySQL / Redis / downstream runtime 誰是 source of truth。
- 下游若重啟，是從 Redis、MySQL 還是自身 cache 還原。

## Failure Window

### Redis 成功，GM command 失敗

已確認:

- 單筆流程中 Redis hSet 在 GM command 之前。
- GM command 回 false 時 DB rollback。
- 未看到 Redis rollback。

風險:

- DB 沒 commit，但 Redis 已變更。
- 若下游直接讀 Redis，可能生效一段髒狀態。

### DB commit 成功，Mongo log 失敗

已確認:

- 單筆流程 commit DB 後才寫 Mongo。

風險:

- 操作成功但 audit log 不完整。

### 批量 GM command partial success

已確認:

- 依 center 分組送 GM。
- 第一次失敗會再送一次。
- 未看到第二次失敗後中止或標記 partial failure。

風險:

- 部分 center 已套用，部分 center 未套用。
- DB / Redis / Mongo 仍可能 commit。

### 查詢同步 status=2

已確認:

- `index()` 讀 Redis `controlLimitRemain <= 0` 後更新 DB status=2。

風險:

- 查詢造成寫入副作用。
- status completion 責任分散。

### 權限邊界不明

已確認:

- `Base::_initialize()` 有權限檢查邏輯。
- 但 `PointControlController::_initialize()` 中 `parent::_initialize()` 被註解。

待確認:

- `point-control` 是否由其他入口保護。
- 批量 / 狀態切換是否一定有後端權限防護。

## Owner Decision

如果我是這條 flow 的 owner，優先順序會是:

1. 先確認下游 GM receiver 與 Redis consumer，補完整 flow 邊界。
2. 定義 MySQL、Redis、GM command、Mongo log 的 source of truth 與責任。
3. 補 GM command 結果紀錄，包括 center、cmd、player count、第一次 / 第二次結果。
4. Redis 寫入後 GM 失敗，要有 rollback、pending 狀態或 reconcile。
5. 批量操作要明確處理 partial failure，不能安靜 commit。
6. 補 DB / Redis reconcile: DB active 但 Redis 缺失、DB inactive 但 Redis 存在、Redis remain 已 0 但 DB 未完成。
7. 確認後台權限與高風險批量操作是否需要二次確認。

## 本 flow 的價值

適合練:

- control plane 影響 runtime
- cross-resource consistency
- Redis projection
- GM command / downstream timeout
- audit log
- partial failure
- owner decision

不適合現在寫:

- Nick 主導此 flow。
- 完整後端 runtime owner。
- 改善效能或事故修復。

## 下一步

下一步只推薦一件事:

```text
app_bi point-control-admin-operation Step 5
```

原因:

- Step 3 已重整，且 `decision-notes.md` 已補。
- Step 4 已轉成保守面試 case。
- 下一步應照主線檢查是否更新履歷 / 自傳。

不更新履歷。
