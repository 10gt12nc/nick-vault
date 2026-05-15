# Interview - point-control-admin-operation

更新時間：2026-05-14
完成狀態：所屬 flow 已完成 Step 5
文件角色：`materials/interview.md` 詳細面試稿附錄
掃描等級：Level 2 Flow 深掃延伸
證據層級：分析素材 / learning-only；code 功能為專案存在 / code-backed；Nick 貢獻待確認
格式狀態：新版結構

## 本次結論

這條 flow 可以轉成面試 case，但只能保守講成：

```text
我分析過一條後台單點控制 flow。
它表面上是後台管理玩家控制名單，
但實際會跨 MySQL、Redis、GM command 與 Mongo audit log。
我會從 transaction boundary、partial failure、reconcile、audit 與權限邊界拆風險。
```

不能講成：

```text
我主導單點控制系統。
我設計 runtime 控制架構。
我修復 production 單點控制事故。
```

原因：目前只確認到 `app_bi` 後台操作端、Redis projection、GM command sender 與 Mongo 操作紀錄；未掃下游 GM receiver / runtime Redis consumer，也未確認 Nick 本人 MR / ticket / commit / production issue。

## Step 4 前檢查

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`

已重讀 vault：

- `projects/iwin/app_bi/README.md`
- `projects/iwin/app_bi/step1-candidate-flows.md`
- `projects/iwin/app_bi/step2-flow-comparison.md`
- 本 flow 的 `flow.md`
- 本 flow 的 `career-interview.md`
- 本 flow 的 `materials/evidence.md`
- 本 flow 的 `materials/decision-notes.md`
- 本 flow 的 `materials/interview.md`
- 本 flow 的 `materials/claim-boundary.md`

已重讀 code repo：

- `/Users/nick/Git/iwin/app_bi`
- 目前分支：`main`
- 工作區乾淨
- path-specific log：`PointControlController.php`、`DataListService.php`、`app/common.php`、`app/route.php`

既有 Step 狀態：

| Step | 狀態 | 判斷 |
| --- | --- | --- |
| Step 1 | 可沿用 | 已列 candidate flows，並標明 app_bi 主要是後台 / BI / control plane 入口 |
| Step 2 | 可沿用 | 已同步 app_bi candidate ranking 與下一步排序 |
| Step 3 | 已完成 | 已重整 `flow.md` / `materials/evidence.md` |
| Step 4 | 已完成 | 已轉成保守面試 case |
| Step 5 | 已重整 | 已判定不更新正式履歷 / 自傳 |

本次不做：

- 不更新正式履歷 / 自傳。
- 不宣稱 Nick 實作。
- 不新增 Step。
- 不把「下游定位」升級成新任務名稱。

## 30 秒版本

我分析過一條後台單點控制 flow。它表面上是後台新增、修改或停用玩家控制名單，但 code 顯示一次操作會同時牽涉 MySQL `point_control`、Redis `playerControl:playerControls:data`、HTTP GM command，以及 Mongo `log_point_control` 操作紀錄。

我會保守定位成 code reading 與 production risk analysis case：目前只確認到 `app_bi` 後台發送端，下游 GM receiver / runtime Redis consumer 還沒掃，所以我不會說自己主導或完整負責這條 flow。

## 3 分鐘版本

這條 flow 的入口是 `point-control` 系列 route，主 controller 是 `PointControlController`。

單筆新增或修改時，後台會先驗證玩家存在，依 `player_id` 推出 channel / center，檢查控制類型、幣別、額度與難度。接著開 MySQL transaction，寫入或更新 `point_control`，再把控制狀態寫到 Redis `playerControl:playerControls:data`，然後透過 `sendGmCommand()` 發送 `SET_PLAYER_CONTROL` 給下游。GM command 成功後才 commit DB，commit 後再寫 Mongo `log_point_control`。

狀態切換時，如果 status 是取消，會刪 Redis hash 並送 `DELETE_PLAYER_CONTROL`；如果是啟用，會寫 Redis 並送 `SET_PLAYER_CONTROL`。批量新增 / 修改會先做 xlsx 校驗，最多 20 筆，再依 center 分組送 GM command。

我會特別看幾個風險。

第一，MySQL transaction 管不到 Redis、HTTP GM command、Mongo log。單筆流程裡 Redis 寫在 GM command 前，如果 GM 失敗，DB 會 rollback，但目前 `app_bi` 端沒看到 Redis rollback。

第二，批量操作有 partial success 風險。code 會依 center 分組送 command，第一次失敗會重送一次，但沒看到第二次失敗後明確中止、rollback 或 partial failure state。

第三，列表查詢會讀 Redis `controlLimitRemain`，如果小於等於 0，就反寫 DB status = 2。這讓查詢帶寫入副作用，也讓 completion state 的責任分散。

第四，權限邊界需要確認。`Base::_initialize()` 有權限檢查，但 `PointControlController::_initialize()` 註解掉 `parent::_initialize()`，所以不能直接說這條高風險後台操作的後端權限完整。

如果我是 owner，我不會一開始就喊大重構。我會先定位下游 GM receiver 和 runtime Redis consumer，再補 GM command 結果紀錄、批量 partial failure 狀態、DB / Redis reconcile，以及高風險後台操作的權限與 audit 邊界。

## 面試主軸

### 面試官問：這不就是後台 CRUD 嗎？

可以回答：

不是只看 controller 有沒有 insert / update。這條 flow 的重點是一次後台操作會跨 MySQL、Redis、HTTP GM command 和 Mongo log。MySQL transaction 只能保護 DB，保護不了 Redis、下游 command 或 audit log。所以我會把它當成 control plane 影響 runtime state 的 flow，而不是單純 CRUD。

### 面試官問：你能證明什麼？

可以回答：

我能證明 code 裡有 `point-control` route，主線會寫 `point_control`，寫 Redis `playerControl:playerControls:data`，送 `SET_PLAYER_CONTROL` / `DELETE_PLAYER_CONTROL` GM command，並寫 Mongo `log_point_control`。我也能證明批量操作會依 center 分組送 command，且第一次失敗後會重送一次。

不能證明的是：我本人主導這些功能，或下游 runtime 全鏈路已掃完。

### 面試官問：最危險的 failure window 是什麼？

可以回答：

我會先看 Redis 成功但 GM command 失敗。因為 Redis hSet / hDel 在 GM command 前，如果 GM command 回 false，DB 會 rollback，但目前 `app_bi` 端沒看到 Redis rollback。這可能造成 DB 未 commit，但 Redis 已變更。若下游直接讀 Redis，就可能短暫或持續看到髒狀態。

### 面試官問：批量操作的風險是什麼？

可以回答：

批量新增 / 修改會依 center 分組送 GM command。這代表可能 A center 成功、B center 失敗。code 有第一次失敗後重送一次，但沒看到第二次失敗後建立 partial failure state、標記哪些 center 失敗，或讓後台可重送失敗部分。Senior 要看的不是重送一次，而是失敗能不能被看見、被追蹤、被補償。

### 面試官問：Redis 先寫一定錯嗎？

可以回答：

不能直接說錯，要看下游契約。若下游收到 GM command 後讀 Redis，先寫 Redis 可能是既有設計。但 owner 要追問：GM command 失敗時 Redis 怎麼收斂？是否 rollback、pending、version、operation id 或 reconcile？目前只在 `app_bi` sender 端，還沒看到完整答案，所以我會標待確認。

### 面試官問：查詢為什麼會改狀態？

可以回答：

從 code 看，列表查詢會讀 Redis 的 `controlLimitRemain`，如果小於等於 0，就更新 DB status 成控制完成。這可能是為了讓後台顯示跟 runtime 狀態對齊，但風險是查詢帶寫入副作用，completion state 不再只由明確事件推進。我會想確認是否有正式 completion event、排程或 reconcile。

### 面試官問：權限邊界怎麼看？

可以回答：

我不會只看前端 menu。高風險後台操作一定要確認後端 action 是否有權限檢查與 audit。這條 flow 裡我看到 `Base` 有 permission check，但 `PointControlController::_initialize()` 沒呼叫 parent，所以要確認是否有其他 middleware / route guard。未確認前不能說權限完整。

### 面試官問：如果你接手，會先改什麼？

可以回答：

我會先補可觀測性和收斂能力，而不是直接大重構。

第一，定位下游 GM receiver / Redis consumer，確認 Redis 和 GM command 的契約。第二，補 GM command 結果紀錄，例如 operation id、center、cmd、player count、第一次 / 第二次結果。第三，補批量 partial failure state，讓失敗 center 可查、可重送、可人工處理。第四，補 DB / Redis reconcile，找出 DB active 但 Redis 缺失、DB inactive 但 Redis 還存在、Redis remain 已 0 但 DB 未完成的狀態。

## Senior 追問清單

- MySQL、Redis、GM command 誰是 source of truth？
- Redis 寫入成功但 GM command 失敗怎麼收斂？
- 批量操作部分 center 成功、部分失敗時，後台怎麼知道？
- 重送 GM command 是否 idempotent？
- `point_control.status = 2` 是由誰推進？
- 查詢 API 反寫 DB 是否可接受？
- Mongo audit log 失敗會不會讓操作不可追？
- 權限檢查是在 controller、middleware 還是 route 前處理？
- 需要 operation id / version 嗎？
- 要不要 DB / Redis reconcile job？

## Lead / Architect 追問清單

- 這種 control plane flow 要同步 HTTP command，還是改成 pending state + worker？
- 如果 runtime 只讀 Redis，Redis rebuild 來源是 DB 還是事件 log？
- 如果多個 admin 同時修改同一玩家控制狀態，ordering 怎麼處理？
- 批量操作要不要每筆 player 有獨立狀態？
- GM command timeout 要當失敗、未知，還是待查？
- audit log 是 best effort 還是必須成功？
- 權限是否需要 two-man rule 或二次確認？
- 如何設計 dashboard 看 DB / Redis / runtime 套用狀態差異？

## 可展示 Senior 能力

- 從後台 route 追到 MySQL / Redis / GM command / Mongo log。
- 分辨 DB transaction 與外部 side effect 邊界。
- 把已確認 evidence、合理推測、待確認分開。
- 看到批量操作 partial success 與下游 idempotency 問題。
- 用漸進式 owner decision 思考，不一開始空喊大重構。
- 能把後台 flow 降級為 control plane 入口，不硬包裝成完整 runtime owner。

## 可說 / 不可說

### 可以說

- 我分析過 `app_bi` 的後台單點控制 flow。
- 我能從後台 API 追到 MySQL、Redis、GM command、Mongo log。
- 我能指出跨資源一致性、批量 partial failure、audit、權限與下游未確認風險。
- 我會保守區分已確認 code evidence 與下游待確認。

### 必須保守說

- 這是 `app_bi` 後台入口與 GM command sender 分析。
- 下游 GM receiver / runtime Redis consumer 尚未掃。
- Nick 個人實作貢獻待確認。

### 不能說

- 我主導單點控制系統。
- 我設計 runtime 控制架構。
- 我是這條 flow owner。
- 我解決過這條 flow 的 production incident。
- 我完整掃完下游後端。
- 我確認這條 flow 已有完整 idempotency / retry / reconcile。

## 履歷候選句

目前不建議寫進正式履歷。

如果未來 Nick 補到本人 evidence，才可考慮非常保守的版本：

```text
參與後台控制類功能維護，協助梳理 MySQL、Redis、下游通知與操作紀錄之間的資料流，並釐清狀態不一致、批量操作與操作追查風險。
```

目前證據層級不足，不放入：

- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

## Step 4 結論

這條 flow 已可作為保守面試 case。

目前定位：

- `flow.md`：主研究報告。
- `career-interview.md`：Nick 預設閱讀的保守面試 / 履歷素材。
- `materials/evidence.md`：code / commit evidence。
- `materials/decision-notes.md`：技術硬底子與設計選項。
- `materials/claim-boundary.md`：Step 5 履歷 / 自傳邊界，已判定不更新正式履歷 / 自傳。
- `materials/interview.md`：本 Step 4 詳細面試稿附錄。

下一步只推薦一件事：

```text
app_bi daily-game-record-summary Step 5
```

原因：

- 本 flow 已完成 Step 1-5。
- Step 5 已判定不更新正式履歷 / 自傳。
- `daily-game-record-summary` Step 4 已完成，已轉成保守面試 case。
- 下一步應做 Step 5，判定是否更新正式履歷 / 自傳。
