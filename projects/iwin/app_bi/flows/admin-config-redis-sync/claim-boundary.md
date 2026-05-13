# Claim Boundary - app_bi 後台設定同步 Redis

狀態: Step 3 邊界

## 可以安全說

- 已分析 `app_bi` 後台設定同步 Redis 的 control plane flow。
- 已確認多個後台設定會從 MySQL 投影到 Redis。
- 已確認此 flow 涉及支付設定、商戶設定、玩家層級、活動設定、遊戲清單、渠道資訊等配置類資料。
- 已整理 Redis projection 的一致性風險、partial success、reload、audit、reconcile 問題。
- 可在面試中用它說明「後台設定不是單純 CRUD，真正風險在 runtime 是否拿到正確設定」。

## 必須保守說

- 這是 `app_bi` 發送端 / 後台入口分析。
- 目前只確認部分 `app_bi` API 會讀 Redis 設定。
- 下游 runtime consumer 尚未掃。
- 目前未確認 Nick 本人實作這些 Redis 同步功能。

## 不可以寫進履歷

- 主導 Redis 設定同步平台。
- 設計完整設定中心。
- 負責全鏈路 runtime config owner。
- 修復渠道商 whatsapp Redis bug。
- 完成商戶設定 status 投影邊界調整。
- 改善同步成功率 / 延遲 / 事故率 X%。

除非補到:

- Nick 本人 commit / MR。
- ticket / issue。
- production incident 記錄。
- code review / 需求文件。
- metric 或 before-after evidence。

## 可以作為面試素材

可講成:

> 我分析過後台設定同步 Redis 的 flow。這類功能表面上是按鈕同步，但實際上是 MySQL source of truth 到 Redis projection，再到 runtime reload 的一致性問題。我會特別看欄位投影是否完整、多 channel 是否 partial success、reload 失敗怎麼處理、是否有 waitSyn / opLog / reconcile。

避免講成:

> 我主導了 app_bi 設定同步平台。

## Step 4 可轉換方向

Step 4 面試稿可以聚焦:

- 30 秒: 設定同步不是 CRUD，是 control plane -> runtime projection。
- 3 分鐘: 用支付設定 / 商戶設定 / 活動設定例子講 state transition。
- 追問: partial success、reload failure、欄位缺漏、reconcile、audit。

仍不更新正式履歷。
