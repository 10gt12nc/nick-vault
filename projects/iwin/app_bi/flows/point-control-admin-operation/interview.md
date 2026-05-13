# Interview - point-control-admin-operation

用途: 面試練習，不是履歷 claim
原則: 不誇大；沒有 MR / ticket / commit evidence 前，不說主導或獨立完成。

## 30 秒說法

我有整理過一條後台單點控制 flow。它表面上是後台管理玩家控制名單，但實際上會同時寫 MySQL、Redis，並透過 GM command 通知下游服務，最後再寫 Mongo 操作紀錄。

這條 flow 的重點不是 CRUD，而是後台操作如何影響 runtime 狀態，以及 DB、Redis、GM command、audit log 之間怎麼維持一致性。比較需要注意的風險是 Redis 寫入發生在 GM command 與 DB commit 前後，失敗時要考慮補償、重試與 reconcile。

## 3 分鐘說法

我會用 production flow 的角度看這條後台單點控制。

入口是 `point-control` 系列 API。單筆新增或修改時，系統會先驗證玩家存在，依玩家 ID 推出 channel / center，接著檢查 quota、difficulty、coin type 等參數。資料會先寫進 MySQL `point_control`，再寫到 game server Redis 的 `playerControl:playerControls:data`，然後透過 `sendGmCommand()` 發送 `SET_PLAYER_CONTROL` 給下游服務。GM command 成功後才 commit DB，並寫 Mongo `log_point_control` 作為操作紀錄。

取消或切換狀態時，流程類似，只是 status 為取消時會刪 Redis hash，並送 `DELETE_PLAYER_CONTROL`。另外列表查詢也不是單純查 DB，它會讀 Redis 剩餘額度，如果 `controlLimitRemain <= 0`，會把 DB status 同步成控制完成。

我會特別關注幾個風險。第一，Redis 寫入是在 GM command 前，如果 GM 失敗，DB 會 rollback，但 code 裡沒有看到 Redis rollback，可能會有 DB 與 Redis 不一致。第二，單筆流程 DB commit 後才寫 Mongo，如果 Mongo 寫失敗，可能造成操作成功但稽核紀錄不完整。第三，批量新增或修改依 center 分組送 GM command，第一次失敗會重試一次，但沒有明確檢查第二次結果後中止，可能需要補 partial success 的處理。

如果我是 owner，我不會第一步就重寫，而是先補觀測與一致性邊界：記錄 GM command 結果、明確 retry 後失敗策略、增加 DB/Redis reconcile、批量操作 dry-run，以及定義 MySQL、Redis、GM command 誰是 truth source。

## 面試官可能問：這是你做的嗎？

保守回答：

這條目前我會說是我做過 code reading、flow 梳理與風險分析；如果沒有 MR、ticket 或 commit evidence，我不會說是我主導開發。我的重點是能讀懂這種後台控制面如何接到 runtime 狀態，並指出一致性、重試與稽核風險。

## 面試官可能問：這條 flow 最危險的是什麼？

可以回答：

最危險的是跨資料源一致性。這條 flow 不是單一 DB transaction 就結束，它同時涉及 MySQL、Redis、HTTP GM command、Mongo log。MySQL transaction 管不到 Redis、下游 HTTP 與 Mongo，所以失敗時要設計補償與 reconcile，而不是只看 DB commit / rollback。

## 面試官可能問：你會怎麼改？

可以回答：

我會分階段做。

第一階段先補觀測與風險可見性：記錄每次 GM command 的 cmd、center、player count、結果與錯誤，批量操作要能看出哪些 center 成功、哪些失敗。

第二階段補一致性保護：GM 失敗時要 rollback Redis 或寫 pending 狀態，讓後續 reconcile 能處理；批量 retry 後還失敗要中止或標記 partial failure，不能安靜 commit。

第三階段做 reconcile：定期比對 MySQL active 名單與 Redis hash，找出 DB active 但 Redis 缺失、DB inactive 但 Redis 還存在、Redis remain 已 0 但 DB status 未完成。

## 面試官可能問：這是不是 distributed transaction？

可以回答：

不是正式 distributed transaction。它比較像是後台操作跨 MySQL、Redis、HTTP command、Mongo log 的 eventual consistency 問題。MySQL transaction 只能保護 DB 內部，Redis 和 HTTP side effect 需要靠 idempotency、retry、compensation、reconciliation 來收斂。

## 面試官可能問：GM command 要怎麼做到 idempotent？

可以回答：

目前 code evidence 只看到 cmd、adminUid、userIds、channel，沒有看到 request id 或 operation id，所以不能確認有 idempotency。

如果要補，我會讓每次操作有 operation id，GM command 接收端以 operation id 或 player + version 判斷重複請求。對 `SET_PLAYER_CONTROL` 這種覆蓋式操作，可以用 version / timestamp 避免舊請求覆蓋新狀態；對 `DELETE_PLAYER_CONTROL` 則要確認刪除重放是安全的。

## 面試官可能問：為什麼列表查詢會更新 status？

可以回答：

從 code 看，列表查詢會讀 Redis 的 `controlLimitRemain`，如果小於等於 0，就把 DB status 更新成控制完成。這表示查詢 API 同時承擔狀態補同步。這樣做可能是為了讓後台狀態跟 runtime 剩餘額度對齊，但缺點是查詢有寫入副作用，狀態責任比較分散。我會想確認是否有更正式的 completion event 或排程同步。

## Senior 可展示能力

- 能從後台 API 追到 runtime side effect。
- 能分辨 DB transaction 與外部 side effect 的邊界。
- 能指出 Redis / GM command / Mongo log 的一致性風險。
- 能提出漸進式改善，而不是一開始就重寫。
- 能把 CRUD-looking 後台功能講成 production owner 會關心的 flow。

## Lead / Architect 追問方向

- MySQL、Redis、GM command 誰是 truth source？
- GM command 如何防 duplicate、out-of-order、partial success？
- 批量操作要不要 outbox / job 化？
- 操作 audit log 要不要和 DB commit 綁在同一個可靠路徑？
- Redis 與 DB reconcile 的 owner 是誰？
- 後台高風險操作是否需要審批、dry-run、回滾或雙人確認？

