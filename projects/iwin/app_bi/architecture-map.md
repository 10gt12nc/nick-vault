# iwin app_bi Architecture Map

更新時間：2026-05-19
文件角色：project-level 定位地圖
證據層級：專案存在 / code-backed；Nick 貢獻依三層 claim gate 判斷

## 定位

`app_bi` 是 PHP / ThinkPHP 後台與 BI / control plane 專案。它主要不是交易 source of truth，而是提供營運、客服、管理者與工程排查使用的後台入口。

本地 source repo 最新性：

| Repo | 狀態 |
| --- | --- |
| `/Users/nick/Git/iwin/app_bi` | 已 fetch；本地 `main=4a206a2`，`origin/main=fd9881f`，本地落後 4 commit；未 pull / 未 checkout |
| `/Users/nick/Git/iwin/payment` | 已 fetch；`k3s=e8be8a1` 與 `origin/k3s=e8be8a1` 同步；僅作下一步候選 |

## 分層地圖

```text
後台使用者 / 營運 / 客服 / 工程排查
-> public/views/** 前端頁面
-> app/admin/controller/** ThinkPHP controller
-> app/business/** business helper
-> app/admin/model/** model
-> MySQL / Redis / log DB / Mongo log
-> 下游 runtime / payment / game / log writer repo（待各 flow 確認）
```

## 已完成的主要 flow

| Flow | 類型 | 狀態 | 履歷 |
| --- | --- | --- | --- |
| `contribution-claim-consolidation` | project-level claim 收口 | 已完成 | limited / negative；不放正式履歷主成果 |
| `point-control-admin-operation` | 後台控制面 / control plane | Step 5 | 不更新正式履歷 |
| `admin-config-redis-sync` | 設定同步 / Redis projection | Step 5 | 不更新正式履歷 |
| `daily-game-record-summary` | 報表查詢 / batch projection | Step 5 | 不更新正式履歷 |
| `game-round-record-query` | 玩家申訴 / troubleshooting 查詢 | Step 5 | 不更新正式履歷 |

## 邊界

可以用 `app_bi` 來理解：

- 後台操作如何觸發或查詢 production flow。
- BI / reporting / troubleshooting 與 source of truth 的差異。
- control plane 對 Redis、GM command、log DB、payment 人工入口的影響。

不能用 `app_bi` 直接宣稱：

- Nick 主導 app_bi。
- Nick 是後台系統 owner。
- app_bi 就是金流 / 遊戲結算 / wallet source of truth。
- 已完整確認下游 runtime consumer、payment callback、遊戲結算或 wallet correctness。

## 下一步

只推薦一件事：

```text
iwin game_api agent-bonus-receive-transfer Step 5
```

原因：

- app_bi project-level claim 已完成 limited / negative consolidation。
- 真正 money correctness 已回到 payment repo 收口；game_job 也已完成 project-level consolidation。
- 下一步回 `game_api` 未完成的代表 flow。
