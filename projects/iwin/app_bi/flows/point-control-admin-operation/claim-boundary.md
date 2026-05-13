# Claim Boundary - point-control-admin-operation

用途: 限制履歷與面試說法，避免誇大

## 可以確認的事

已確認：

- 已針對 `/Users/nick/Git/iwin/app_bi` 的 `point-control` flow 做 code reading。
- 已整理出後台 API、MySQL、Redis、GM command、Mongo log 的 flow。
- 已標記主要 failure window 與待確認問題。
- 已依 Senior / Owner 角度整理面試說法。

## 目前不能確認的事

未確認：

- Nick 是否親自開發 `PointControlController`。
- Nick 是否設計 `point_control` table。
- Nick 是否設計 Redis key `playerControl:playerControls:data`。
- Nick 是否設計 GM command 機制。
- Nick 是否修過這條 flow 的 production issue。
- Nick 是否主導過這條 flow 的重構、壓測、事故處理或上線。

## 履歷不能這樣寫

在沒有 MR / ticket / commit / 事故紀錄前，不要寫：

- 主導單點控制系統設計。
- 獨立完成玩家控制後台。
- 設計高交易 runtime 控制架構。
- 改善效能 X%。
- 解決重大 production incident。
- 擔任此 flow 的 owner。
- 負責遊戲 runtime 控制核心。

## 可以保守用於面試的說法

可以說：

- 我有分析過後台單點控制 flow，理解後台操作如何透過 MySQL、Redis、GM command 影響 runtime 狀態。
- 我會從一致性、重試、補償、稽核紀錄與 reconcile 角度檢查這類 flow。
- 這類 flow 讓我練習把後台 CRUD 往 production owner 角度拆解，而不是只看 Controller / Service。

更保守的履歷候選句，需等 Nick 確認有實際參與後才可放入履歷：

- 參與後台控制類功能維護，梳理 MySQL、Redis、下游通知與操作紀錄之間的資料流，協助降低狀態不一致與操作追查風險。

## 若 Nick 確認有實際開發，可補問

需要 Nick 補充：

- 當時負責哪一段？
- 有沒有 ticket / MR / commit？
- 是新增、維護、debug、重構，還是事故排查？
- 有沒有實際 production 問題？
- 有沒有修過 Redis / DB 不一致？
- 有沒有補過操作紀錄、匯出、批量操作或權限？

## 目前定位

這份 flow 目前定位為：

- Senior / Owner 學習案例。
- 面試 flow 講解素材。
- 後續若有證據，可轉成 career-interview 或履歷素材。

但目前不直接更新履歷 master，也不寫入自傳。

