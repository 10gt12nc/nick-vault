# iwin bi_share contribution claim consolidation

更新時間：2026-05-20
掃描等級：Level 2 rolling / scoped negative project-level claim consolidation
證據層級：專案存在 / code-backed；分析素材 / learning-only；Nick bi_share direct contribution 未確認

## 結論

`bi_share` 已完成 rolling / scoped contribution claim consolidation。

結論很保守：

1. 不放正式履歷主成果。
2. 可作面試分析素材，用來說明 legacy Laravel / BI / 分享推廣 / 佣金報表系統如何從 route、controller、batch job、Redis、MongoDB 與 MySQL 追資料流。
3. 不可寫成 Nick 主導分享系統、佣金系統、BI 報表、GM repair、Laravel 升級或 k3s 遷移。

目前沒有 Nick / `10gt12nc` direct production commit，也沒有 Nick 本人確認 `bi_share` 實際開發範圍。若後續 Nick 補上本人確認、MR、ticket 或 production issue，可再把本文件回填成「本人確認 + code-backed，待 commit / ticket 補強」。

## 本次自動重讀

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`

已重讀共用索引 / 履歷素材：

- `projects/iwin/README.md`
- `projects/source-repo-inventory.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/06-todo.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`
- `senior-owner-playbook/13-code-capability-map.md`

`projects/iwin/bi_share` 本輪前尚不存在既有 Step / flow 文件；因此本文件只作 Career Track 的 rolling / scoped 收口，不宣稱 `bi_share` 已完成 Flow Track。

## Source repo 狀態

`/Users/nick/Git/iwin/bi_share`

- 已執行：`git fetch --all --prune`
- branch：`main`
- local HEAD：`5b26c076ae035d6414e5381b4611812e57355824`
- `origin/main`：`5b26c076ae035d6414e5381b4611812e57355824`
- ahead / behind：`0 / 0`
- 工作樹：乾淨
- 遠端分支：`origin/main`、`origin/beta`、`origin/feature/RD-128`、`origin/k3s`

2026-05-20 本輪 fetch 後，`origin/k3s` 有更新到 `86fa8669`；沒有 pull、merge、checkout、rebase 或修改公司 repo。

## 掃描範圍

本輪已掃：

- `routes/web.php`
- `routes/api.php`
- `app/Http/Controllers/Admin/ShareController.php`
- `app/Http/Controllers/Admin/ShareNationalController.php`
- `app/Http/Controllers/Admin/FinanceController.php`
- `app/Http/Controllers/Admin/FinanceNationalController.php`
- `app/Http/Controllers/Admin/CommissionController.php`
- `app/Http/Controllers/Admin/GMController.php`
- `app/Http/Controllers/Api/MyPushController.php`
- `app/Http/Controllers/Api/ShareController.php`
- `app/Jobs/ShareUser.php`
- `app/Jobs/BuildRelations.php`
- `app/Jobs/FixShareUserInRedis.php`
- `app/Jobs/FixShareUserInMySQL.php`
- `app/Jobs/ClearPerformanceInRedisByDate.php`
- `app/Jobs/Bonus/*`
- `app/Console/Commands/DailyCountCommand.php`
- `app/Console/Commands/WeeklyCountCommand.php`
- `app/Console/Commands/TopupWithdrawCommand.php`
- `app/Exports/*`
- `app/Handlers/CountIncomeTemp.php`
- `app/Handlers/CountIncomeS1Temp.php`
- `app/Handlers/Tree.php`

本輪只做 targeted 深掃與 claim 判斷，沒有逐行建立完整 flow package；若後續要進 Flow Track，需另做 Step 1 / Step 2。

## Nick / 10gt12nc evidence

已執行 repo-wide author log 搜尋：

```text
git log --all --author='10gt12nc|Nick|nick'
```

結果：未找到 Nick / `10gt12nc` authored commits。

已掃主要 app / routes / job / command / export path history，主要 production path commits：

| Commit | 日期 | 作者 | 摘要 | 判斷 |
| --- | --- | --- | --- | --- |
| `07af797e` | 2023-11-15 | arnold | first commit | 主要初始 code，不是 Nick evidence |
| `8f05d090` | 2023-12-27 | arnold | 修正財務管理 / 每天統計推廣稅收欄位 | finance report 維護，不是 Nick evidence |
| `770e4f69` | 2025-06-04 | arnold | fix like issue | 需另查細節；不是 Nick evidence |
| `d7e84e24` | 2026-05-08 | arnold | Laravel 6 -> 9、PHP 7.3 -> 8.1、vendor 全升級 | k3s / legacy upgrade evidence，不是 Nick evidence |
| `86fa8669` | 2026-05-19 | arnold | 註釋 dead api routes、`/test`、`/push`、schedule | dead code cleanup evidence，不是 Nick evidence |

判斷：

- `bi_share` 目前不能標成 Nick 真實開發過。
- 不能把 Laravel 升級、死碼註解、route cleanup、SQS 設定、k3s manifest 或 report bugfix 寫成 Nick 的履歷成果。
- 若 Nick 後續明確說「我做過 bi_share 某段」，可升級成本人確認 evidence，但仍要保守標成「本人確認，待 commit / ticket 補強」。

## code-backed 能力素材

### 1. 分享 / 推廣關係與邀請鏈

`routes/web.php` 的 admin `share` / `share_national` route 對應 `ShareController`、`ShareNationalController`，主要處理：

- 分享概況。
- 邀請明細。
- 分享明細。
- 邀請 / 分享匯出。
- 分享等級。
- 綁定與當日綁定查詢。

`ShareUser` job 從邀請資料來源同步 `vc_rid` 與 Redis `invited:*`、`info:*`、`zhishu:*`、`other:*`，維護直屬 / 非直屬關係、上級鏈與近期新增會員數。

可面試講：

- 後台看到的是 projection 與營運查詢入口。
- 真正要查一致性，要比對來源邀請資料、`vc_rid`、Redis relation projection 與每日 / 每週統計表。
- 重跑或 repair 要處理 parent chain、duplicate insert、Redis stale data 與同日新增計數一致性。

不可寫：

- Nick 開發分享系統。
- Nick 設計邀請關係樹。
- Nick 主導 Redis relation rebuild。

### 2. 佣金設定與臨時返佣

`CommissionController` 處理：

- 佣金區間設定。
- channel-based setting。
- 臨時返佣設定。
- Redis temporary setting 清理。

可面試講：

- 佣金設定是 runtime control plane，風險在區間重疊、跳號、channel 維度、臨時規則過期與 Redis / DB 不一致。
- Senior 追問可談 config validation、versioning、audit、dry-run、preview 與 reconciliation。

不可寫：

- Nick 開發佣金規則。
- Nick 設計佣金引擎。
- Nick 修復佣金 production incident。

### 3. 財務統計與報表匯出

`FinanceController` / `FinanceNationalController` 處理：

- 每日統計。
- 每週統計。
- 結算 / close money 類報表。
- 轉帳紀錄查詢。
- Excel 匯出。

`DailyCountCommand` / `WeeklyCountCommand` 從 MongoDB 每日分表與 `day_money` / `vc_daily` / `agent_weekly` 類 projection 聚合資料；匯出類別集中在 `app/Exports/*`。

可面試講：

- 這是 BI projection / report layer，不是交易 source of truth。
- 重點是時間窗口、資料日、delete + insert 重跑、MongoDB aggregate、分批查詢、匯出限制與 memory limit。
- 報表若與交易不一致，應往 payment / game log / game_job / gameserver source-of-truth 追。

不可寫：

- Nick 主導 BI 報表平台。
- Nick 優化匯出效能或改善 X%。
- Nick 負責完整財務統計 pipeline。

### 4. GM repair / 營運修復入口

`GMController` 提供關係修復入口，會 dispatch：

- `FixShareUserInRedis`
- `ClearPerformanceInRedisByDate`

可面試講：

- repair 入口必須理解 lock、重建資料範圍、Redis 清除後由下一輪 batch 重算、操作回饋與 audit。
- 這類工具可支援 production troubleshooting，但不能直接當作交易 correctness source。

不可寫：

- Nick 開發 GM repair。
- Nick 建立完整補償 / reconciliation 機制。

### 5. Legacy Laravel / k3s cleanup 題材

近期 `origin/k3s` 有 Laravel / PHP upgrade、middleware、queue config、route dead-code cleanup 類 commits。

可面試講：

- legacy Laravel 升級與 containerization 需要檢查 route 是否仍被呼叫、scheduled job 是否仍有效、queue connection 是否仍啟用、middleware / package 是否相容、env / secret 是否外洩。
- 但這只能當分析題材，不是 Nick evidence。

不可寫：

- Nick 主導 Laravel 6 -> 9。
- Nick 負責 `bi_share` k3s 遷移。
- Nick 清理 dead API routes。

## 可放履歷

目前不建議新增任何 `bi_share` 正式履歷 bullet。

原因：

- 未掃到 Nick / `10gt12nc` authored production commits。
- 未有 Nick 本人確認。
- `bi_share` 是 PHP / Laravel legacy BI / sharing / report system，不是 Nick 目前 Senior Java Backend 主力 claim。
- 已有更強的正式履歷素材：`payment`、`game_api coupon`、`game_job daily summary / GSC backup`。

## 可面試講

推薦口徑：

```text
我有分析過 legacy BI / 分享推廣 / 佣金報表類系統。這類系統不能只看後台頁面，要把 route、controller、batch job、Redis projection、MongoDB 統計表與 MySQL 關係表串起來看。像分享關係、佣金設定、每日 / 每週報表與 GM repair，真正的風險通常在資料來源與 projection 不一致、重跑窗口、Redis stale data、匯出大量資料與營運修復邊界。
```

這是 `分析過 / code-backed`，不是 `真實開發過`。

## 不可誇大

不可寫：

- Nick 主導 `bi_share`。
- Nick 負責完整分享 / 推廣 / 佣金系統。
- Nick 開發 Laravel BI 報表平台。
- Nick 主導 Laravel 6 -> 9 或 PHP 8.1 upgrade。
- Nick 負責 `bi_share` k3s 遷移。
- Nick 修復 finance report bug 或 dead route cleanup。
- Nick 建立完整佣金 reconciliation / GM repair / Redis rebuild 機制。
- 任何效能改善、事故改善或報表正確率量化數字。

## 是否更新 05 / 08

不更新 `senior-owner-playbook/05-resume-master-zh.md` 與 `08-application-autobiography-zh.md` 的正式履歷內容。

本輪只在 05 / 08 的 evidence note 補充 `bi_share` negative consolidation，避免之後誤把 `bi_share` 寫成 Nick 主力經驗。

## 下一步建議

`bi_share` 已完成 negative consolidation；`iwin_gameserver` contribution consolidation 也已完成。下一步回 Flow Track 補 `center-http-deposit-withdraw Step 4`。

```text
iwin iwin_gameserver center-http-deposit-withdraw Step 4
```
