# iwin app_bi

本資料夾整理 `/Users/nick/Git/iwin/app_bi` 的專案知識。

`app_bi` 是 PHP / ThinkPHP 後台與 BI 專案，主要價值是理解 iwin 後台操作入口、BI / 報表查詢、設定同步、金流人工修正入口、遊戲紀錄查詢與權限邊界。它不是目前 Nick 履歷裡的主要後端成果來源；沒有下游後端 repo evidence 前，不把 `app_bi` 包裝成完整後端 owner 或真實開發成果。

## 讀檔順序

1. [step1-candidate-flows.md](step1-candidate-flows.md)：Step 1 候選 flow 盤點。
2. [step2-flow-comparison.md](step2-flow-comparison.md)：Step 2 候選 flow 風險與價值比較。
3. `flows/{flow-name}/flow.md`：單條 flow 的主研究報告。
4. 舊平鋪格式的 `evidence.md` / `decision-notes.md` / `interview.md` / `claim-boundary.md`：目前仍可讀，之後重整時再遷移到 `materials/`。

## 目前狀態

| 文件 / flow | 狀態 | 說明 |
| --- | --- | --- |
| `step1-candidate-flows.md` | 已重整 | Level 1 掃描，重新獨立成 Step 1 主文件 |
| `step2-flow-comparison.md` | 已重整 | 已同步新 Step 1 候選排序、證據層級與後台入口邊界 |
| `flows/point-control-admin-operation/` | Step 3 已重整，舊平鋪格式 | 2026-05-14 已重整主報告與 evidence；舊 Step 4 / Step 5 暫不視為最新完成 |
| `flows/admin-config-redis-sync/` | Step 5，舊平鋪格式 | 已檢查履歷 / 自傳；不更新正式履歷，保留為面試分析素材 |

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
app_bi point-control-admin-operation Step 4 重整
```

原因：

- Step 1 / Step 2 已重整乾淨。
- `point-control-admin-operation` 舊 Step 3 品質不足，已於 2026-05-14 重整。
- 舊 Step 4 / Step 5 不能再視為最新完成。
- 下一步應先重整同一條 flow 的 Step 4 面試 case，不更新履歷。
