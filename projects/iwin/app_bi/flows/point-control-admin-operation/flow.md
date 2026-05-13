# app_bi - point-control-admin-operation

Step: 3
狀態: 已建立第一版深挖 flow
範圍: iwin / app_bi / 後台單點控制操作

## 這條 flow 解決什麼

`point-control-admin-operation` 是後台對特定玩家設定、修改、停用單點控制的操作 flow。

它不是單純 PHP 後台 CRUD。從 code 可確認，後台操作會同時影響：

- MySQL `point_control` 控制名單與狀態。
- Game Server Redis hash `playerControl:playerControls:data`。
- 透過 `sendGmCommand()` 發送 GM command 給下游服務。
- Mongo `log_point_control` 操作紀錄。

這條 flow 的 Senior / Owner 價值在於：後台操作不是只改資料表，而是會改變 runtime 控制狀態；因此真正重點是 DB、Redis、GM command、操作紀錄之間的一致性與可追查性。

## 系統位置

已確認：

- 入口在 `app/route.php` 的 `point-control` 系列 routes。
- 主要 controller 是 `PointControlController`。
- 批量匯入、匯出與校驗邏輯部分放在 `DataListService`。
- 單點控制主表 model 對應 `point_control`。
- 相關延伸功能包含 allowlist 與 switch，但本 flow 只深挖主控制操作。

推測：

- `app_bi` 在這條 flow 中偏後台控制面，實際執行控制效果應由下游遊戲或 Java/GM 服務依 Redis 與 GM command 生效。
- 玩家 ID 的第 1 位與第 2 位被用來推導 channel / center，這是系統既有 sharding 或 routing 規則。

待確認：

- GM command 下游實際處理位置與失敗回應格式。
- Redis `center_http` 來源維護方式。
- 後台權限是否在進入 controller 前已完整攔截。

## 正常 flow - 單筆新增或修改

已確認：

1. Admin 從後台送出 `POST point-control` 或 `POST point-control/:pointControlId`。
2. `PointControlController::storeOrUpdate()` 讀取 `player_id`，用 `findUserByPlayerId()` 確認玩家存在。
3. code 依 `player_id` 推導 `channel` 與 `center`。
4. 讀取 admin cookie，例如 `mid`、`uname` 或 `nickname`。
5. 讀取控制參數：`type`、`coin_type`、`quota`、`difficulty`。
6. `quota` 會依 channel 做金額單位轉換，並檢查不可小於 0、不可超過上限。
7. 若是新增且玩家已存在於 `point_control`，直接回傳已被控制。
8. 開啟 MySQL transaction。
9. 新增或更新 `point_control`，狀態預設為 `1`，也就是控制中。
10. 寫入 Redis hash `playerControl:playerControls:data`，欄位包含控制難度、控制額度、剩餘額度、控制類型、時間戳、玩家 ID。
11. 組 GM command：`SET_PLAYER_CONTROL`。
12. 呼叫 `sendGmCommand($gmData, $center)`。
13. 若 GM command 成功，commit DB transaction，並寫入 Mongo `log_point_control`。
14. 若 GM command 失敗，rollback DB transaction，回傳操作失敗。

推測：

- Redis 寫入與 GM command 是讓下游 runtime 立即知道控制狀態。
- Mongo log 是後台稽核與操作紀錄查詢使用。

待確認：

- Redis 寫入後，下游是否直接讀 Redis，或只依賴 GM command reload。
- Mongo log 失敗時是否會被上層捕捉；目前單筆流程中 DB commit 在 Mongo insert 之前。

## 正常 flow - 狀態切換

已確認：

1. Admin 送出 `POST point-control/status`。
2. `PointControlController::updateStatus()` 檢查玩家存在。
3. 更新 `point_control.status`。
4. 若 status 為 `0`，刪除 Redis hash 中該玩家資料，並送出 `DELETE_PLAYER_CONTROL`。
5. 若 status 非 `0`，重新寫入 Redis hash，並送出 `SET_PLAYER_CONTROL`。
6. GM command 成功後 commit DB，並寫入 Mongo `log_point_control`。
7. GM command 失敗時 rollback DB。

推測：

- `status=0` 是控制取消。
- `status=1` 是控制中。
- `status=2` 是控制完成，主要由列表查詢時依 Redis 剩餘額度推回 MySQL。

待確認：

- `status=2` 是否只能由 runtime 消耗額度後被後台同步，還是也可能由其他服務寫入。

## 正常 flow - 列表、匯出、操作紀錄

已確認：

- `index()` 會遍歷各 channel 的 Redis `playerControl:playerControls:data`，若 `controlLimitRemain <= 0`，會把對應玩家 `point_control.status` 更新為 `2`。
- 列表會查 MySQL `point_control`，並補玩家暱稱與 Redis 中的剩餘額度。
- `pointControlDown()` 透過 `DataListService::exportPointData()` 匯出控制名單，資料來源包含 MySQL、Redis、玩家暱稱查詢。
- `operationLogs()` 查 Mongo `log_point_control`，以玩家與時間範圍查操作紀錄。

推測：

- 列表查詢同時扮演「狀態補同步」角色，這代表狀態完成不是完全由單一寫入點維護。

待確認：

- 是否有排程或其他服務也會同步 `status=2`。
- operation log 是否有後台以外的寫入者。

## 正常 flow - 批量新增與批量修改

已確認：

- 批量新增與修改都吃 xlsx。
- `DataListService::checkBatchAddControl()` 會檢查玩家是否已存在於 `point_control`。
- `DataListService::checkBatchEditControl()` 會檢查玩家是否存在，並移除完全沒有變更的資料。
- `checkPointControl()` 會檢查玩家 ID、收放分類型、額度、難度與狀態。
- 批量新增會批次 insert MySQL、逐筆寫 Redis、依 center 群組送 `SET_PLAYER_CONTROL` GM command，並寫 Mongo log。
- 批量修改會批次 update MySQL、依 status 決定 Redis hDel 或 hSet，並依 center 群組送 `DELETE_PLAYER_CONTROL` 或 `SET_PLAYER_CONTROL`。

推測：

- 批量 flow 是高風險操作，因為一次可能影響多個玩家 runtime 狀態。

待確認：

- xlsx 上傳是否有額外權限或審批流程。
- 大量玩家批量操作時的 timeout、partial success、人工補償流程。

## 狀態與資料模型

已確認：

- MySQL 主表：`point_control`。
- Redis 主 key：`playerControl:playerControls:data`。
- Mongo 操作紀錄：`log_point_control`。
- GM command：
  - `SET_PLAYER_CONTROL`
  - `DELETE_PLAYER_CONTROL`
- 相關欄位：
  - `player_id`
  - `type`
  - `coin_type`
  - `quota`
  - `difficulty`
  - `status`
  - `updated_by`
  - `channel`
  - Redis `controlLevel`
  - Redis `controlLimit`
  - Redis `controlLimitRemain`
  - Redis `controlType`
  - Redis `timeStamp`
  - Redis `userId`

推測：

- MySQL 是後台可查詢的主紀錄。
- Redis 是 runtime 生效資料。
- Mongo 是 audit / operation log。
- GM command 是通知下游重新套用控制狀態。

待確認：

- MySQL 與 Redis 誰是最終 truth source。
- 下游若重啟時是從 Redis、MySQL 還是 GM command cache 還原。

## Failure scenarios

### Redis 已寫入，但 GM command 失敗

已確認：

- 單筆新增/修改中，Redis `hSet` 在 `sendGmCommand()` 之前。
- 若 GM command 回 false，DB transaction rollback。
- code 中未看到對剛剛寫入的 Redis 做補償刪除或還原。

風險推測：

- 可能出現 DB 沒 commit，但 Redis 已有新控制資料的短暫或持續不一致。
- 如果下游直接讀 Redis，可能造成 runtime 狀態與後台 DB 不一致。

待確認：

- 下游是否只在 GM command 成功後才讀取 Redis。
- Redis 是否有其他 reconcile 機制清掉未成功的控制資料。

### DB commit 成功，但 Mongo log 失敗

已確認：

- 單筆新增/修改與狀態切換中，DB commit 在 Mongo insert 前。

風險推測：

- 若 Mongo insert 失敗，可能造成操作成功但缺少 audit log。

待確認：

- `MongoC::insert()` 失敗是否會 throw。
- 上層是否有全域 exception handler 會記錄這類錯誤。

### 批量 GM command partial success

已確認：

- 批量新增/修改依 center 分組呼叫 GM command。
- 第一次失敗會再呼叫一次，但 code 沒有明確檢查第二次結果後中止。

風險推測：

- 可能出現部分 center 成功、部分 center 失敗，但流程仍繼續寫 log / commit。

待確認：

- `sendGmCommand()` 的失敗是否一定 throw 或只回 false。
- GM command 是否具備冪等性。

### 列表查詢同步 status=2

已確認：

- `index()` 讀 Redis 後，如果 `controlLimitRemain <= 0`，會把 DB status 更新成 `2`。

風險推測：

- 查詢 API 具有寫入副作用，容易讓狀態更新責任分散。
- 若 Redis 資料不準，DB status 可能被同步成完成。

待確認：

- 是否有更正式的 completion event 或排程同步。

## Owner decision

如果我是這條 flow 的 owner，第一優先不是重寫架構，而是先把一致性與稽核邊界補清楚：

1. 明確定義 MySQL、Redis、GM command、Mongo log 的 truth source 與一致性責任。
2. 補 GM command 結果紀錄，至少包含成功、失敗、重試、center、cmd、player count。
3. 單筆操作若 GM 失敗，要補 Redis rollback 或設計可重放的 pending 狀態。
4. 批量操作要明確檢查重試後結果，不能只 retry 一次後繼續。
5. 補 reconcile flow：比對 MySQL `point_control` 與 Redis hash，找出 DB active 但 Redis 缺失、DB inactive 但 Redis 存在、Redis remain <= 0 但 DB status 未完成。
6. 批量操作應該有 dry-run / preview / 操作者確認，因為它是多玩家 runtime 影響操作。
7. 操作紀錄不要只依 Mongo 成功與否，至少要有 fallback log 或 outbox-like 紀錄。

## 這條 flow 的學習重點

- 後台操作一旦會影響 runtime，就不能只用 CRUD 角度看。
- Senior 要追的是資料狀態在 DB、Redis、下游服務與 audit log 之間如何流動。
- 這條 flow 很適合練習：
  - state transition
  - failure window
  - idempotency
  - retry / compensation
  - reconciliation
  - owner decision

