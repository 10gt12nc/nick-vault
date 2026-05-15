# Claim Boundary - point-control-admin-operation

更新時間：2026-05-14
完成狀態：所屬 flow 已完成 Step 5
文件角色：`materials/claim-boundary.md` 履歷 / 自傳邊界附錄
掃描等級：Level 2 Flow 深掃延伸
證據層級：分析素材 / learning-only；code 功能為專案存在 / code-backed；Nick 貢獻待確認
格式狀態：新版結構

## Step 5 結論

本 flow 不更新正式履歷與自傳。

不更新：

- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

原因：

- 目前只確認到 `/Users/nick/Git/iwin/app_bi` 的後台操作端、Redis projection、GM command sender 與 Mongo 操作紀錄。
- 未確認下游 GM receiver / runtime Redis consumer。
- 未確認 Nick 本人 MR / ticket / commit / production issue / 本人確認。
- `app_bi` 是後台 / BI / control plane 專案，不應把後台入口硬包裝成完整後端 owner 成果。
- 正式履歷 master 目前已有較泛化且保守的「後台控制面與營運工具」描述，不需要為此 flow 新增更強 claim。

## 本次自動重讀與檢查範圍

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
- `projects/iwin/app_bi/flows/point-control-admin-operation/career-interview.md`
- `projects/iwin/app_bi/flows/point-control-admin-operation/materials/evidence.md`
- `projects/iwin/app_bi/flows/point-control-admin-operation/materials/interview.md`
- `projects/iwin/app_bi/flows/point-control-admin-operation/materials/claim-boundary.md`
- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`

已重讀 code repo：

- `/Users/nick/Git/iwin/app_bi`
- 目前分支：`main`
- 工作區狀態：乾淨
- path-specific log：`PointControlController.php`、`DataListService.php`、`app/common.php`、`app/route.php`

已看重要 history：

- `e83c729 feat(#first commit): first commit`
- `0ad2f51 控制管理汇出功能 新增判断找不到log_user就跳过`
- `0b57858 Revert "控制管理汇出功能 新增判断找不到log_user就跳过"`
- `98e89fb feat(#RD-146): app bi新增antplay`

未完成：

- 未 checkout 每個遠端分支逐一比對。
- 未逐檔逐行掃完整 `app_bi`。
- 未逐 commit diff 掃所有相關 commit。
- 未掃下游 GM receiver。
- 未掃 runtime Redis consumer。
- 未掃 `game_api`、`game_job`、`iwin_gameserver`。
- 未確認 Nick 個人 MR / ticket / commit / production issue。

## 已確認

可確認的是專案裡存在這條後台 flow：

- `point-control` route 存在。
- `PointControlController` 有新增 / 修改 / 狀態切換 / 批量操作。
- flow 會寫 MySQL `point_control`。
- flow 會寫 Redis `playerControl:playerControls:data`。
- flow 會送 `SET_PLAYER_CONTROL` / `DELETE_PLAYER_CONTROL` GM command。
- flow 會寫 Mongo `log_point_control`。
- 批量操作會依 center 分組送 GM command。
- `app_bi` 端存在 DB transaction 管不到 Redis / GM command / Mongo log 的一致性邊界。
- 權限 enforcement 邊界仍需確認，不能說完整已確認。

## 不能確認

目前不能確認：

- Nick 是否親自開發 `PointControlController`。
- Nick 是否設計 `point_control` table。
- Nick 是否設計 Redis key `playerControl:playerControls:data`。
- Nick 是否設計 GM command 機制。
- Nick 是否修過這條 flow 的 production issue。
- Nick 是否主導過重構、壓測、事故處理、上線或 owner decision。
- 下游 GM command handler 在哪個 repo。
- runtime consumer 如何使用 Redis。
- 此 flow 是否有正式 reconcile / retry / idempotency。

## 正式履歷不能寫

在沒有 MR / ticket / commit / 本人確認前，不要寫：

- 主導單點控制系統設計。
- 獨立完成玩家控制後台。
- 設計高交易 runtime 控制架構。
- 改善效能 X%。
- 解決重大 production incident。
- 擔任此 flow owner。
- 負責遊戲 runtime 控制核心。
- 建立完整 DB / Redis / GM command 一致性方案。

## 面試可以保守說

可以說：

- 我分析過 `app_bi` 的後台單點控制 flow。
- 我能從後台 API 追到 MySQL、Redis、GM command、Mongo log。
- 我能指出這類 flow 的一致性、補償、audit、權限與下游未確認風險。
- 我會保守區分「已確認 code evidence」與「下游待確認」。

不能說成：

- 我主導這條 flow。
- 我是這條 flow 的 owner。
- 我完整掃完後端 runtime。
- 我確認這條 flow 已具備完整 idempotency / retry / reconcile。

## 未來可升級成履歷素材的條件

若之後 Nick 要把這條 flow 升級成正式履歷素材，最低需要補：

- Nick 實際負責的任務範圍。
- MR / ticket / commit / 上線紀錄其中至少一種 evidence。
- 是否實際修過 DB / Redis / GM command / Mongo log / 權限 / 批量操作問題。
- 是否有下游 runtime repo evidence，能證明不只是後台入口。
- 若要寫成 production case，還需要事故、排查、修復、驗證或營運協作證據。

有上述 evidence 後，才可考慮非常保守的候選句：

```text
參與後台控制類功能維護，協助梳理 MySQL、Redis、下游通知與操作紀錄之間的資料流，並釐清狀態不一致、批量操作與操作追查風險。
```

目前這句仍不放入正式履歷。

## 本 flow 目前定位

目前定位：

- Senior / Owner 學習案例。
- 面試 flow 講解素材。
- control plane 影響 runtime state 的分析素材。
- 後續若補下游 repo，可銜接 GM command / Redis consumer 深挖。

不是：

- 履歷正式 bullet。
- 自傳素材。
- Nick 主導成果。
- 完整後端 runtime flow。

## Step 5 後下一步

本 flow 已完成 Step 1-5。

下一步回到同 project candidate ranking，只推薦一件事：

```text
payment Step 1
```

原因：

- `point-control-admin-operation` 已完成 Step 5，且不更新履歷 / 自傳。
- `admin-config-redis-sync` 也已完成 Step 5。
- `daily-game-record-summary` Step 5 已完成，且不更新正式履歷 / 自傳。
- `game-round-record-query` Step 5 已完成；下一步轉 `payment Step 1`，回到真正 money correctness source of truth。
- 它目前仍只作報表 / projection 分析素材，不更新履歷。
