# Interview - point-control-admin-operation

更新時間: 2026-05-13
Step: 4
用途: 面試 case，不是履歷 claim
狀態: 舊 Step 4 稿；2026-05-14 Step 3 重整後需重新確認，不視為最新完成

> 注意：本文件是舊 Step 4 稿。`flow.md` / `evidence.md` 已於 2026-05-14 重新做 Step 3，因此本文件只能暫作參考；下一步應做 `app_bi point-control-admin-operation Step 4 重整`。

## Step 4 前檢查

已重讀:

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/iwin/app_bi/README.md`
- `projects/iwin/app_bi/step2-flow-comparison.md`
- `flow.md`
- `evidence.md`
- `decision-notes.md`
- `claim-boundary.md`
- `/Users/nick/Git/iwin/app_bi` 目前分支、近期 log、point-control 相關 path-specific log 與關鍵入口

既有 Step 狀態:

| Step | 狀態 | 判斷 |
| --- | --- | --- |
| Step 1 | 可沿用 | 已列 candidate flows，並標明 app_bi 主要是後台 / BI / control plane 入口 |
| Step 2 | 可沿用 | 已把 `point-control-admin-operation` 排為第一條，但保守標示不能直接寫履歷 |
| Step 3 | 可沿用 | 已有 `flow.md`、`evidence.md`、`decision-notes.md`、`claim-boundary.md`，並明確標出下游未掃 |

本次判斷:

- 沒有再把「下游定位」升級成新 Step。
- `decision-notes.md` 是 Step 3 補充，不是 Step 4 前的阻塞。
- Step 4 可以做，但只能轉成保守面試 case。
- 不更新履歷 master。

## 30 秒說法

我整理過 iwin `app_bi` 的後台單點控制 flow。它表面上是管理玩家控制名單，但 code 顯示操作會同時牽涉 MySQL、Redis、GM command 與 Mongo 操作紀錄，所以真正重點不是 CRUD，而是後台 control plane 如何影響 runtime 狀態。

這條 case 我會保守講：目前只確認到 `app_bi` 發送端與後台操作面，下游 GM receiver / runtime Redis consumer 還沒掃，所以它適合作為 production flow 分析與一致性風險討論，不適合直接包裝成我主導的後端成果。

## 3 分鐘版本

這條 flow 的入口是 `point-control` 系列 route，主要 controller 是 `PointControlController`。

單筆新增或修改時，後台會先驗證玩家存在，依玩家 ID 推出 channel / center，檢查控制類型、幣別、額度與難度。接著開 MySQL transaction，寫入或更新 `point_control`，再把控制狀態寫到 Redis `playerControl:playerControls:data`，然後透過 `sendGmCommand()` 發送 `SET_PLAYER_CONTROL` 給下游。GM command 成功後才 commit DB，commit 之後再寫 Mongo `log_point_control`。

狀態切換時，如果 status 是取消，會刪 Redis hash 並送 `DELETE_PLAYER_CONTROL`；如果是啟用，會寫 Redis 並送 `SET_PLAYER_CONTROL`。列表查詢還有一個特別點：它會讀 Redis 的 `controlLimitRemain`，如果小於等於 0，就更新 DB status 成控制完成。

這裡我會關注四個 production risk。第一，MySQL transaction 管不到 Redis、GM command、Mongo log。第二，Redis 寫在 GM command 前，如果 GM 失敗，DB 會 rollback，但目前 app_bi 端沒看到 Redis rollback。第三，批量操作依 center 分組送 GM command，第一次失敗會重送一次，但沒看到第二次失敗後明確中止或 partial failure 標記。第四，`PointControlController::_initialize()` 沒有呼叫 parent，權限邊界需要確認是否由其他入口保護。

如果我是 owner，我不會一開始就說要重構成事件驅動。我會先定位下游 GM receiver 與 runtime Redis consumer，再補 GM command 結果紀錄、批量 partial failure 標記、DB / Redis reconcile，以及高風險後台操作的權限與 audit 邊界。

## 面試官追問

### 這是你做的嗎？

保守回答:

> 這條目前我會定位成 code reading、flow 梳理與風險分析 case。沒有 MR、ticket、commit 或本人確認前，我不會說是我主導開發。可以確認的是，我能從後台入口追到 MySQL、Redis、GM command、Mongo log，並把已確認 evidence、合理推測與下游待確認分開。

### 這條 flow 最危險的是什麼？

最危險的是跨資源一致性。MySQL transaction 只保護 DB，不能保護 Redis、HTTP GM command、Mongo log。只要其中一個 side effect 成功、另一個失敗，就可能造成 runtime state、DB state、audit state 不一致。

### Redis 先寫一定錯嗎？

不能直接說錯。若下游設計是收到 GM command 後讀 Redis，那 Redis 先寫可能是既有契約。但 owner 要追問的是：GM command 失敗時 Redis 怎麼收斂？是否 rollback、pending、version、operation id 或 reconcile？目前 app_bi 端沒看到完整答案，所以要標待確認。

### 為什麼 DB transaction 不夠？

因為這條 flow 有外部 side effect。DB transaction 可以 rollback `point_control`，但不能自動 rollback Redis hSet / hDel，也不能保證 HTTP command 一定送達，更不能保證 Mongo audit log 寫入成功。

### 批量操作最大的風險是什麼？

partial success。批量會依 center 分組送 GM command，如果部分 center 成功、部分失敗，系統需要知道哪些玩家已套用、哪些未套用。只靠重送一次但沒有明確失敗狀態，面試時我會把它列為 owner 要補的觀測與補償點。

### 查詢為什麼會改狀態？

從 app_bi code 看，列表查詢會讀 Redis 的剩餘額度，若 `controlLimitRemain <= 0`，就更新 DB status 成完成。這可能是為了讓後台顯示跟 runtime 狀態對齊，但查詢帶寫入副作用會讓狀態責任分散。我會想確認是否有正式 completion event、排程或 reconcile。

### 權限邊界怎麼看？

我不會只看前端 menu。高風險後台操作一定要確認後端 action 是否有權限檢查與 audit。本 flow 看到 `Base` 有 permission check，但 `PointControlController::_initialize()` 沒呼叫 parent，所以要確認是否有其他 middleware / route guard，否則不能說權限完整。

### 如果要改善，你會先做什麼？

第一步補觀測與可追查性：每次 GM command 記錄 operation id、center、cmd、player count、第一次與第二次結果。第二步補收斂：Redis 已寫但 GM 失敗時，要 rollback、pending 或 reconcile。第三步補批量 partial failure 狀態。第四步確認下游 idempotency，避免重送造成重複副作用或舊狀態覆蓋新狀態。

## 可展示 Senior 能力

- 從後台 API 追到跨資源 side effect。
- 分辨 DB transaction 與外部 side effect 邊界。
- 把「已確認 code evidence」和「下游待確認」分開。
- 看到 Redis、GM command、Mongo audit log 之間的一致性問題。
- 用漸進式 owner decision 思考，不一開始空喊大重構。
- 能把後台 flow 降級為 control plane 入口，不硬包裝成完整 runtime owner。

## Lead / Architect 延伸

可以延伸討論:

- 是否需要 pending / applied / failed 狀態表。
- 是否要把同步 GM command 改成 worker / job 套用。
- 是否需要 operation id / version 來處理 idempotency 與 ordering。
- DB / Redis reconcile 的檢查規則。
- audit log 是否應該從 best effort 變成可補償紀錄。

但不能講成專案已經做到，除非後續掃到下游 evidence。

## 不能誇大

不能說:

- 我主導單點控制系統。
- 我設計 runtime 控制架構。
- 我是這條 flow owner。
- 我解決過這條 flow 的 production incident。
- 我完整掃完下游後端。
- 我確認這條 flow 已有完整 idempotency / retry / reconcile。

可以說:

- 我分析過這類後台 control plane flow。
- 我能從 code evidence 拆出一致性、補償、audit、權限與下游邊界。
- 我知道這類 flow 要如何從 feature engineer 的角度升級成 system owner 的檢查方式。

## 履歷候選句

目前不寫入 `senior-owner-playbook/05-resume-master-zh.md`。

只有 Nick 確認有實際參與後，才可考慮:

> 參與後台控制類功能維護，梳理 MySQL、Redis、下游通知與操作紀錄之間的資料流，協助降低狀態不一致與操作追查風險。

若沒有補 Nick evidence，這句只留在本 flow 內作候選，不放進正式履歷。

## Step 4 結論

這條 flow 可以作為面試中的「production flow 分析」案例，重點是:

- control plane side effect
- cross-resource consistency
- partial failure
- audit / permission boundary
- owner decision

它目前不能作為正式履歷主成果，因為缺 Nick 實際參與 evidence，也缺下游 runtime evidence。

## 舊稿後續狀態

舊 Step 5 曾在 `claim-boundary.md` 判定:

- 不更新正式履歷 / 自傳。
- 本 flow 留作面試分析 case。
- 未補 Nick evidence 前，不升級成正式履歷 claim。

下一步只推薦一件事:

```text
app_bi point-control-admin-operation Step 4 重整
```

原因:

- Step 3 已於 2026-05-14 重新重整。
- 舊 Step 4 / Step 5 不能再視為最新完成。
- 下一步要先重新產出保守面試 case，不更新履歷。
