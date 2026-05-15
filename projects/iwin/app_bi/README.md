# iwin app_bi

本資料夾整理 `/Users/nick/Git/iwin/app_bi` 的專案知識。

`app_bi` 是 PHP / ThinkPHP 後台與 BI 專案，主要價值是理解 iwin 後台操作入口、BI / 報表查詢、設定同步、金流人工修正入口、遊戲紀錄查詢與權限邊界。它不是目前 Nick 履歷裡的主要後端成果來源；沒有下游後端 repo evidence 前，不把 `app_bi` 包裝成完整後端 owner 或真實開發成果。

## 讀檔順序

1. [step1-candidate-flows.md](step1-candidate-flows.md)：Step 1 候選 flow 盤點。
2. [step2-flow-comparison.md](step2-flow-comparison.md)：Step 2 候選 flow 風險與價值比較。
3. [architecture-map.md](architecture-map.md)：app_bi 作為後台 / BI / control plane 的定位地圖。
4. [career-interview.md](career-interview.md)：project-level 保守面試素材與履歷邊界。
5. `flows/{flow-name}/flow.md`：單條 flow 的主研究報告。
6. `flows/{flow-name}/career-interview.md`：該 flow 的保守面試 / 履歷素材。
7. `flows/{flow-name}/materials/`：證據、技術決策、詳細面試稿與 claim 邊界附錄。

## 目前狀態

| 文件 / flow | 狀態 | 說明 |
| --- | --- | --- |
| `step1-candidate-flows.md` | 已重整 | Level 1 掃描，重新獨立成 Step 1 主文件 |
| `step2-flow-comparison.md` | 已重整 | 已同步新 Step 1 候選排序、證據層級與後台入口邊界 |
| `architecture-map.md` | 已補齊 | 只作定位地圖，不取代 flow 深挖 |
| `career-interview.md` | 已補齊 | project-level 保守素材；不更新正式履歷 |
| `flows/point-control-admin-operation/` | Step 5，新版可讀結構 | `flow.md` 已補白話導讀、Code 分層、架構圖、流程圖；不更新正式履歷 |
| `flows/admin-config-redis-sync/` | Step 5，新版可讀結構 | `flow.md` 已補白話導讀、Code 分層、架構圖、流程圖；不更新正式履歷 |
| `flows/daily-game-record-summary/` | Step 5，新版可讀結構 | 已確認 app_bi 查詢端與 game_job producer；已轉保守面試 case；已判定不更新正式履歷 |
| `flows/game-round-record-query/` | Step 5，新版可讀結構 | 已確認 app_bi 查詢端與 iwin_gameserver log writer 線索；已轉保守面試 case；已判定不更新正式履歷 |

## 專案定位

已確認：

- PHP / ThinkPHP 後台與 BI 專案。
- 入口包含 `app/route.php`、`app/admin/controller/*`、`app/business/*`、`public/views/**`、`db/migrations/**`。
- 常見能力：後台操作、報表查詢、Excel 匯出、Redis 設定同步、金流訂單查詢 / 修正入口、遊戲局紀錄查詢、RBAC / 權限判斷。

推測：

- `app_bi` 多數 flow 是 control plane / reporting plane，不是交易 source of truth。
- 真正交易狀態、遊戲結算、payment callback、runtime consumer 多半在其他 repo。

待確認：

- Nick 實際參與過哪些 `app_bi` 功能。
- 哪些功能有 Nick 本人 MR / ticket / commit / production issue evidence。
- `point-control`、Redis sync、payment repair 的下游後端實作位置。

## 最新 code 狀態補記

2026-05-15 已依最新 KB 補做 remote refs 檢查：

- `/Users/nick/Git/iwin/app_bi`：已 fetch；本地 `main=4a206a2`，`origin/main=fd9881f`，本地落後 4 commit；未 pull、未 checkout、未改公司 repo。
- `/Users/nick/Git/iwin/payment`：已 fetch；目前分支 `k3s` 與 `origin/k3s` 同步在 `e8be8a1`；僅作下一步候選 repo，不代表已掃 payment flow。

因此：`app_bi` 既有分析可作已讀學習素材，但若未來要重新升級 app_bi evidence 或 claim，必須先由 Nick 決定是否更新本地 app_bi 工作樹或改看遠端 diff。

## 履歷邊界

目前只能說：

- 可用來理解 / 分析 iwin 後台入口、BI 查詢、營運操作與設定同步流程。
- 可作為面試時說明「我如何從後台入口追後端 flow」的輔助素材。

目前不能說：

- Nick 主導 `app_bi`。
- Nick 負責完整後台系統。
- Nick 是這些 flow 的 owner。
- 單靠 `app_bi` evidence 寫金流、遊戲結算、runtime 一致性成果。

## 下一步建議

只推薦一件事：

```text
payment Step 1
```

原因：

- Step 1 / Step 2 已重整乾淨。
- `point-control-admin-operation` 已完成 Step 5，且不更新正式履歷 / 自傳。
- `admin-config-redis-sync` 已完成 Step 5。
- `daily-game-record-summary` Step 5 已完成，已判定不更新正式履歷 / 自傳。
- `game-round-record-query` Step 5 已完成，已判定不更新正式履歷 / 自傳。
- `app_bi` 主要分析 flow 已收斂；下一步應轉去真正 money correctness source of truth。
- `payment Step 1` 會先找金流 repo 的 candidate flows，不會直接把 app_bi 人工修正入口寫成完整 payment owner。
