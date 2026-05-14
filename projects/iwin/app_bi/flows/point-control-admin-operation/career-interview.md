# Career Interview - point-control-admin-operation

更新時間：2026-05-14
用途：單條 flow 的保守面試 / 履歷素材
證據層級：分析素材 / learning-only；code 功能為專案存在 / code-backed；Nick 貢獻待確認

## 結論

這條 flow 可以當面試分析案例，不更新正式履歷 / 自傳。

可講成：

```text
我分析過一條後台單點控制 flow。它表面上是後台管理玩家控制名單，但實際會跨 MySQL、Redis、GM command 與 Mongo audit log。我會從 transaction boundary、partial failure、reconcile、audit 與權限邊界拆風險。
```

不能講成：

```text
我主導單點控制系統。
我設計 runtime 控制架構。
我修復 production 單點控制事故。
```

原因：

- 目前只確認到 `app_bi` 後台操作端、Redis projection、GM command sender 與 Mongo 操作紀錄。
- 未掃下游 GM receiver / runtime Redis consumer。
- 未確認 Nick 本人 MR / ticket / commit / production issue。

## 面試 30 秒版

我分析過一條後台單點控制 flow。它表面上是後台新增、修改或停用玩家控制名單，但 code 顯示一次操作會同時牽涉 MySQL `point_control`、Redis `playerControl:playerControls:data`、HTTP GM command，以及 Mongo `log_point_control` 操作紀錄。

我會保守定位成 code reading 與 production risk analysis case：目前只確認到 `app_bi` 後台發送端，下游 GM receiver / runtime Redis consumer 還沒掃，所以我不會說自己主導或完整負責這條 flow。

## 面試 3 分鐘版

這條 flow 的入口是 `point-control` 系列 route，主 controller 是 `PointControlController`。

單筆新增或修改時，後台會驗證玩家存在，依 `player_id` 推出 channel / center，檢查控制類型、幣別、額度與難度。接著開 MySQL transaction，寫入或更新 `point_control`，把控制狀態寫到 Redis `playerControl:playerControls:data`，再透過 `sendGmCommand()` 發送 `SET_PLAYER_CONTROL` 給下游。GM command 成功後才 commit DB，commit 後再寫 Mongo `log_point_control`。

我會特別看幾個風險：

- MySQL transaction 管不到 Redis、HTTP GM command、Mongo log。
- Redis 寫在 GM command 前，GM 失敗時目前 `app_bi` 端沒看到 Redis rollback。
- 批量操作依 center 分組送 command，可能 partial success。
- 列表查詢會讀 Redis remain 後反寫 DB status=2，查詢帶寫入副作用。
- 權限邊界需要確認，不能只看前端 menu。

如果我是 owner，我會先定位下游 GM receiver 和 runtime Redis consumer，再補 GM command 結果紀錄、批量 partial failure 狀態、DB / Redis reconcile，以及高風險後台操作的權限與 audit 邊界。

## 可展示能力

- 從後台 route 追到 MySQL / Redis / GM command / Mongo log。
- 分辨 DB transaction 與外部 side effect 邊界。
- 把已確認 evidence、合理推測、待確認分開。
- 看出批量操作 partial success 與下游 idempotency 問題。
- 把後台 flow 降級為 control plane 入口，不硬包裝成完整 runtime owner。

## 履歷候選句

目前不建議寫進正式履歷。

如果未來 Nick 補到本人 evidence，才可考慮非常保守的版本：

```text
參與後台控制類功能維護，協助梳理 MySQL、Redis、下游通知與操作紀錄之間的資料流，並釐清狀態不一致、批量操作與操作追查風險。
```

目前證據層級不足，不放入：

- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

## Claim Boundary

可以說：

- 我分析過 `app_bi` 的後台單點控制 flow。
- 我能從後台 API 追到 MySQL、Redis、GM command、Mongo log。
- 我能指出跨資源一致性、批量 partial failure、audit、權限與下游未確認風險。
- 我會保守區分已確認 code evidence 與下游待確認。

不能說：

- 我主導單點控制系統。
- 我設計 runtime 控制架構。
- 我是這條 flow owner。
- 我解決過這條 flow 的 production incident。
- 我完整掃完下游後端。
- 我確認這條 flow 已有完整 idempotency / retry / reconcile。

## 詳細附錄

- 主研究報告：`flow.md`
- 證據附錄：`materials/evidence.md`
- 技術決策附錄：`materials/decision-notes.md`
- 面試稿附錄：`materials/interview.md`
- 履歷邊界附錄：`materials/claim-boundary.md`
