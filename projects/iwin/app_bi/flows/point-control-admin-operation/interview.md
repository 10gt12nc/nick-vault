# Interview - point-control-admin-operation

用途: 面試練習，不是履歷 claim
狀態: 已依新版 KB 重整

## 30 秒說法

我有整理過 iwin `app_bi` 的後台單點控制 flow。它表面上是後台管理玩家控制名單，但 code 顯示它會同時寫 MySQL、Redis，並透過 GM command 通知下游服務，最後寫 Mongo 操作紀錄。

這條 flow 的重點不是 CRUD，而是後台 control plane 怎麼影響 runtime 狀態，以及 MySQL、Redis、GM command、audit log 之間的一致性風險。不過我會保守講：目前只確認到 `app_bi` 發送端，下游 receiver / runtime consumer 還要再掃。

## 3 分鐘說法

這條 flow 的入口是 `point-control` 系列 route，主要在 `PointControlController`。

單筆新增或修改時，後台會先驗證玩家存在，依玩家 ID 推出 channel / center，檢查 quota、difficulty、coin type 等參數。接著開 MySQL transaction，新增或更新 `point_control`，再把控制狀態寫到 Redis `playerControl:playerControls:data`，然後透過 `sendGmCommand()` 發送 `SET_PLAYER_CONTROL` 給下游。GM command 成功後才 commit DB，commit 後再寫 Mongo `log_point_control`。

取消或狀態切換時也類似，只是 status 為取消時會刪 Redis hash，並送 `DELETE_PLAYER_CONTROL`。列表查詢也有一個特別點：它會讀 Redis 的 `controlLimitRemain`，如果小於等於 0，就把 DB status 更新成控制完成。

我會關注幾個風險。第一，Redis 寫在 GM command 前，如果 GM 失敗，DB 會 rollback，但目前沒看到 Redis rollback，可能造成 DB 與 Redis 不一致。第二，DB commit 後才寫 Mongo，若 Mongo log 失敗，可能操作成功但稽核紀錄不完整。第三，批量操作依 center 分組送 GM command，第一次失敗會重試一次，但目前沒看到第二次失敗後中止或 partial failure 標記。第四，`PointControlController::_initialize()` 沒有呼叫 parent，權限是否由其他地方保護需要確認。

如果我是 owner，我會先定位下游 GM receiver 與 runtime Redis consumer，再補 GM command 結果紀錄、批量 partial failure 處理、DB / Redis reconcile、以及高風險操作的權限與 audit 邊界。

## 被問：這是你做的嗎？

保守回答:

> 這條目前我會說是我做過 code reading、flow 梳理與風險分析。沒有 MR / ticket / commit evidence 前，我不會說是我主導開發。我能講的是：我能從後台入口追到 MySQL、Redis、GM command、Mongo log，並指出一致性、補償、稽核與下游未確認邊界。

## 被問：最危險的是什麼？

回答:

> 最危險的是跨資源一致性。這不是單一 DB transaction 可以保證的 flow。MySQL transaction 管不到 Redis、HTTP GM command、Mongo log。尤其 Redis 先寫、GM command 後送，如果 GM 失敗但 Redis 已改，就可能出現 DB rollback 但 runtime projection 已變更。

## 被問：你會怎麼改？

回答:

> 我會分階段，不會一開始就重寫。第一步先補觀測：每次 GM command 要記錄 center、cmd、player count、第一次與第二次結果。第二步補一致性：GM 失敗時要 rollback Redis、寫 pending 狀態，或至少讓 reconcile 能補。第三步補批量 partial failure 處理，不讓失敗被安靜吞掉。第四步做 DB / Redis reconcile，定期找出 active / inactive / remain 狀態不一致的資料。

## 被問：這是不是 distributed transaction？

回答:

> 不是正式 distributed transaction，比較像跨 MySQL、Redis、HTTP command、Mongo log 的 eventual consistency 問題。DB transaction 只能保護 DB 內部，其他 side effect 要靠 idempotency、retry、compensation、reconciliation 收斂。

## 被問：GM command 怎麼做 idempotency？

回答:

> 目前 app_bi code evidence 只看到 cmd、adminUid、userIds、channel，沒有看到 operation id 或 version，所以不能確認 idempotency。若要補，我會考慮 operation id 或 player + version，讓下游能判斷重複或舊請求。對 SET 這種覆蓋式操作，也要避免舊請求覆蓋新狀態。

## 被問：查詢為什麼會更新 status？

回答:

> 從 code 看，列表查詢會讀 Redis 的 `controlLimitRemain`，如果小於等於 0，就把 DB status 更新成 2。這可能是為了讓後台狀態跟 runtime 剩餘額度對齊，但缺點是查詢有寫入副作用，completion state 的責任比較分散。我會想確認是否有更正式的 completion event 或排程。

## 可展示能力

- 能從後台 API 追到 runtime side effect。
- 能分辨 DB transaction 與外部 side effect 邊界。
- 能指出 Redis / GM command / Mongo log 的一致性風險。
- 能保守標記下游未掃，不腦補。
- 能提出漸進式改善，而不是空談重構。

## 不能誇大

不能說:

- 我主導單點控制。
- 我設計 runtime 控制架構。
- 我解決 production incident。
- 我完整掃完下游後端。
- 我負責此 flow owner。

可以說:

- 我分析過這類後台 control plane flow。
- 我能從 evidence 拆出一致性、audit、補償與下游邊界。
