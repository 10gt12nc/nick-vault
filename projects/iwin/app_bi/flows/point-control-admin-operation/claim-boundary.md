# Claim Boundary - point-control-admin-operation

用途: 限制履歷與面試說法，避免誇大
狀態: 已依新版 KB 重整

## 可以確認

已確認:

- 已針對 `/Users/nick/Git/iwin/app_bi` 的 `point-control` flow 做 code reading。
- 已確認 `app_bi` 後台入口會牽涉 MySQL、Redis、GM command、Mongo log。
- 已整理單筆新增 / 修改、狀態切換、列表同步、批量新增 / 修改、匯出與操作紀錄。
- 已標出主要 failure window:
  - Redis 成功但 GM command 失敗。
  - DB commit 成功但 Mongo log 失敗。
  - 批量 GM command partial success。
  - 查詢 API 同步 status。
  - 權限邊界不明。
- 已整理面試可講的保守版本。

## 目前不能確認

未確認:

- Nick 是否親自開發 `PointControlController`。
- Nick 是否設計 `point_control` table。
- Nick 是否設計 Redis key `playerControl:playerControls:data`。
- Nick 是否設計 GM command 機制。
- Nick 是否修過這條 flow 的 production issue。
- Nick 是否主導過重構、壓測、事故處理、上線或 owner decision。
- 下游 GM command handler 在哪個 repo。
- runtime consumer 如何使用 Redis。
- 此 flow 是否有正式 reconcile / retry / idempotency。

## 履歷不能寫

在沒有 MR / ticket / commit / 本人確認前，不要寫:

- 主導單點控制系統設計。
- 獨立完成玩家控制後台。
- 設計高交易 runtime 控制架構。
- 改善效能 X%。
- 解決重大 production incident。
- 擔任此 flow owner。
- 負責遊戲 runtime 控制核心。
- 建立完整 DB / Redis / GM command 一致性方案。

## 面試可以保守說

可以說:

- 我分析過 `app_bi` 的後台單點控制 flow。
- 我能從後台 API 追到 MySQL、Redis、GM command、Mongo log。
- 我能指出這類 flow 的一致性、補償、audit、權限與下游未確認風險。
- 我會保守區分「已確認 code evidence」與「下游待確認」。

## 履歷候選句

只有 Nick 確認有實際參與後，才考慮放入履歷:

> 參與後台控制類功能維護，梳理 MySQL、Redis、下游通知與操作紀錄之間的資料流，協助降低狀態不一致與操作追查風險。

目前不放入:

- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

## 需要 Nick 補充

若之後要升級成履歷素材，需要 Nick 補:

- 當時實際負責哪一段。
- 有無 ticket / MR / commit。
- 是新增、維護、debug、重構，還是事故排查。
- 是否有實際 production issue。
- 是否有修過 DB / Redis 不一致、GM command、Mongo log、批量操作或權限問題。

## 目前定位

此 flow 目前定位為:

- Senior / Owner 學習案例。
- 面試 flow 講解素材。
- 後續 decision-notes 的技術硬底子素材。

不是:

- 履歷正式 bullet。
- 自傳素材。
- Nick 主導成果。
