# iwin app_bi contribution claim consolidation

更新時間：2026-05-19
掃描等級：Level 2 limited / negative project-level claim consolidation
證據層級：專案存在 / code-backed；分析素材 / learning-only；Nick app_bi direct contribution 未確認

## 結論

`app_bi` 已完成 project-level limited / negative consolidation。

結論很保守：

1. 不放正式履歷主成果。
2. 可作面試分析素材，用來說明「如何從後台 / BI / control plane 入口追真正 production flow」。
3. 不可寫成 Nick 主導完整後台、BI owner、payment repair owner、game report owner、runtime config owner，或後台到下游完整後端 owner。

`app_bi` 的價值是入口，不是 claim source。真正可放履歷的後端成果，應以 `payment`、`game_job`、`game_api`、`iwin_gameserver`、`third_games_api` 等後端 / runtime repo 的 direct evidence 為準。

## 本次自動重讀

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`

已重讀 project 文件：

- `projects/iwin/app_bi/README.md`
- `projects/iwin/app_bi/architecture-map.md`
- `projects/iwin/app_bi/career-interview.md`
- `projects/iwin/app_bi/step1-candidate-flows.md`
- `projects/iwin/app_bi/step2-flow-comparison.md`
- `projects/iwin/app_bi/flows/point-control-admin-operation/flow.md`
- `projects/iwin/app_bi/flows/point-control-admin-operation/career-interview.md`
- `projects/iwin/app_bi/flows/point-control-admin-operation/materials/evidence.md`
- `projects/iwin/app_bi/flows/point-control-admin-operation/materials/claim-boundary.md`
- `projects/iwin/app_bi/flows/admin-config-redis-sync/flow.md`
- `projects/iwin/app_bi/flows/admin-config-redis-sync/career-interview.md`
- `projects/iwin/app_bi/flows/admin-config-redis-sync/materials/evidence.md`
- `projects/iwin/app_bi/flows/admin-config-redis-sync/materials/claim-boundary.md`
- `projects/iwin/app_bi/flows/daily-game-record-summary/flow.md`
- `projects/iwin/app_bi/flows/daily-game-record-summary/career-interview.md`
- `projects/iwin/app_bi/flows/daily-game-record-summary/materials/evidence.md`
- `projects/iwin/app_bi/flows/daily-game-record-summary/materials/claim-boundary.md`
- `projects/iwin/app_bi/flows/game-round-record-query/flow.md`
- `projects/iwin/app_bi/flows/game-round-record-query/career-interview.md`
- `projects/iwin/app_bi/flows/game-round-record-query/materials/evidence.md`
- `projects/iwin/app_bi/flows/game-round-record-query/materials/claim-boundary.md`

已重讀共用索引 / 履歷素材：

- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/04-interview-casebook.md`
- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/06-todo.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

## Source repo 狀態

`/Users/nick/Git/iwin/app_bi`

- 已執行：`git fetch --all --prune`
- branch：`main`
- local HEAD：`4a206a28ab8f5be4329602cdc510ee9ea41efb25`
- `origin/main`：`fd9881fc417e01f960d758b4b91ba1a10b507855`
- ahead / behind：`0 / 4`
- 工作樹：乾淨
- 遠端分支：`origin/main`、`origin/beta`、`origin/coupontrade`、`origin/feature/GSC`、`origin/feature/ModifyfirstRechargeAwardList`、`origin/feature/PG`、`origin/feature/RD-152`、`origin/feature/RD-20`、`origin/feature/RD-26`、`origin/feature/RD-89`、`origin/feature/gitlab-ci`、`origin/feature/merchantSettingToRedis`、`origin/feature/shareRoulette`、`origin/feature/xsczcx_index_add_column`、`origin/gitlab-test`、`origin/k3s`、`origin/k3s-laravel`、`origin/test-ci`

本輪只 fetch remote refs，沒有 pull、merge、checkout、rebase 或修改公司 repo。

注意：本地 `main` 落後 `origin/main` 4 commit；本輪可用現有 flow 文件與 remote log 做 consolidation，但不能宣稱已用最新 working tree 逐檔驗證 app_bi。

## Nick / 10gt12nc evidence

repo-wide Nick / `10gt12nc` author log 在 `app_bi` 目前只看到 KB / catalog 類 commit 與 revert：

- `8e2c997`：`kb: 深度掃描後建立知識庫（只讀參考）`
- `defe2a2`：`kb: 新增 app_bi 配置點、查詢位置與跨系統關聯說明`
- `c737f8b`：revert app_bi KB catalog
- `fd9881f`：revert app_bi KB catalog / `CLAUDE.md`

判斷：

- 這些不是 `app_bi` production flow 的 direct contribution evidence。
- 不能用來說 Nick 開發或維護 `point-control-admin-operation`、`admin-config-redis-sync`、`daily-game-record-summary`、`game-round-record-query`。

已掃 app_bi 主要 flow path history，主要 author 為 Arnold / Gill / Administrator；目前未看到 Nick / `10gt12nc` 在 app_bi 主要 flow path 的 direct commits。

## flow-level claim 收口

| Flow | 證據層級 | project-level 判斷 |
| --- | --- | --- |
| `point-control-admin-operation` | 專案存在 / code-backed；Nick direct contribution 未確認 | 可面試講 control plane、DB / Redis / GM command / audit 邊界；不放履歷 |
| `admin-config-redis-sync` | 專案存在 / code-backed；Nick direct contribution 未確認 | 可面試講 DB -> Redis projection、runtime stale config、reload / reconcile；不放履歷 |
| `daily-game-record-summary` | 專案存在 / code-backed；game_job producer 有更強 evidence | app_bi 只作報表入口；履歷 claim 以 `game_job contribution-claim-consolidation.md` 為準 |
| `game-round-record-query` | app_bi 查詢端 code-backed；iwin_gameserver writer 有 Nick 線索 | app_bi 不放履歷；若要升級應另做 iwin_gameserver flow |
| `payment-order-status-repair` | app_bi 人工入口 code-backed | 不在 app_bi 放履歷；payment 已有 project-level consolidation，人工修復仍只作面試素材 |

## 可面試講

可以用 `app_bi` 講：

- 後台入口不是 source of truth，真正判斷要追到後端 state machine / runtime writer。
- BI 報表是 projection / troubleshooting view，不是交易 truth。
- 後台人工修正入口要回到 `payment` repo 看 order state、callback、wallet side effect、audit 與 reconciliation。
- Redis sync 是 control plane -> runtime config projection，要注意 stale config、partial update、reload ack、version / checksum 與 reconcile。
- 遊戲局查詢是客服 / troubleshooting 入口，要追 `log_reel` writer、wallet / provider transaction 與 settlement truth。

推薦面試口徑：

```text
我會把 app_bi 定位成後台與 BI 入口，不把它直接當交易真相。像報表、人工修正、Redis 設定同步、遊戲局查詢，我會先用它定位使用者操作與查詢欄位，再往 payment、game_job、iwin_gameserver 或 third_games_api 追真正的 state transition、source of truth、failure window 與補償邊界。
```

## 不放正式履歷

目前不建議新增 app_bi 正式履歷 bullet。

原因：

- 沒有 Nick 本人 app_bi flow MR / ticket / production issue / 本人確認。
- `10gt12nc` 在 app_bi 的 commits 目前是 KB / catalog 建立與 revert，不是 production feature direct commits。
- app_bi 多數是後台入口 / 查詢 / control plane，不是 money / wallet / settlement source of truth。
- 已有更強的正式履歷素材：`payment`、`game_job`、`game_api coupon`。

如果真的要在履歷中提到，只能被包含在泛化描述中：

```text
參與或分析後台營運工具、報表查詢、設定同步與操作稽核等場景，能從後台入口追查下游 payment / game / reporting flow。
```

這句仍不是 app_bi 的獨立成果 bullet。

## 不可誇大

不可寫：

- Nick 主導 `app_bi`。
- Nick 負責完整後台系統。
- Nick 是 BI owner / control-plane owner。
- Nick 開發 `point-control-admin-operation`、`admin-config-redis-sync`、`daily-game-record-summary` 或 `game-round-record-query`。
- Nick 主導 payment order repair、遊戲局查詢、報表平台或 Redis 設定中心。
- 用 app_bi 入口證明 Nick 負責完整 payment / wallet / game settlement / BI pipeline。
- 寫任何改善後台效能、查帳效率、報表正確率或事故改善 X%。

## 是否更新 05 / 08

不更新 `senior-owner-playbook/05-resume-master-zh.md` 與 `senior-owner-playbook/08-application-autobiography-zh.md` 的正式履歷內容。

理由：

- 這是 limited / negative consolidation。
- 結論是不新增 app_bi 正式履歷成果。
- 既有 05 / 08 已有泛化的「後台營運功能 / 報表查詢 / RBAC / 權限 / 營運工具」描述，足以涵蓋 app_bi 的輔助定位。

## 下一步建議

下一步回到 `game_api`，把已完成 Step 4 的第三順位 flow 做 Step 5，避免 `game_api` 卡在未完成代表 flows 而不能 project-level consolidation。

```text
iwin game_api agent-bonus-receive-transfer Step 5
```
