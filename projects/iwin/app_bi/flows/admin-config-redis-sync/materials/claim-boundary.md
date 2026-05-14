# Claim Boundary - app_bi 後台設定同步 Redis

狀態: Step 5 履歷 / 自傳更新判定

## Step 5 結論

不更新正式履歷 / 自傳。

本 flow 目前適合保留為「面試分析素材 / learning-only」，不適合寫入：

- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

原因：

- 已確認的是 `app_bi` 後台同步端、部分 `app_bi` API 讀取端與 Redis projection pattern。
- path-specific git history 看到的 `82d2419`、`b141309`、`8ea905d`、`ea9bf27` 作者不是 Nick，不能當成 Nick 個人實作 evidence。
- 尚未掃到下游 runtime consumer，例如 `game_api`、`game_job`、`iwin_gameserver`、`payment`、`third_games_api`。
- 尚未看到 Nick 本人 MR / ticket / commit / production issue / 本人確認。
- 沒有同步成功率、事故率、延遲或 before-after metric，不能寫量化改善。

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

## 可留作面試 case

Step 4 面試稿可以聚焦:

- 30 秒: 設定同步不是 CRUD，是 control plane -> runtime projection。
- 3 分鐘: 用支付設定 / 商戶設定 / 活動設定例子講 state transition。
- 追問: partial success、reload failure、欄位缺漏、reconcile、audit。

仍不更新正式履歷。

## 未來若要升級成履歷素材

需要先補到至少一種 Nick 本人 evidence：

- Nick 本人 commit / MR / code review。
- 相關 ticket / issue / production incident。
- Nick 本人確認實際參與範圍。
- 下游 runtime consumer 的完整 code evidence。

若未來補到 evidence，仍只能先用保守版本：

```text
參與後台設定同步 Redis 相關功能維護，協助釐清 MySQL 設定來源、Redis projection、同步狀態與 runtime 生效邊界，提升設定變更排查與維護可理解性。
```

沒有上述 evidence 前，不寫入正式履歷。

## Step 5 後下一步

回到 `app_bi` candidate ranking，選下一條未完成 flow。

只推薦一件事：

```text
app_bi daily-game-record-summary Step 3
```

原因：

- `admin-config-redis-sync` 已完成 Step 5，且不更新正式履歷 / 自傳。
- `point-control-admin-operation` 已完成 Step 5，且不更新正式履歷 / 自傳。
- 下一步應回到同 project candidate ranking，選下一條未完成 flow。
- `daily-game-record-summary` 是同 project 下一條仍有 Senior / Owner 價值的候選 flow。
