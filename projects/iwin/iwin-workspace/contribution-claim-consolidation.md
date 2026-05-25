# iwin-workspace Contribution Claim Consolidation

更新時間：2026-05-20
掃描等級：Level 2 / Level 3-oriented rolling consolidation
證據層級：真實做過 KB / docs / workspace 維護；不等於業務 service 開發

## 結論

`iwin-workspace` 有大量 Nick / `10gt12nc` direct commits，但它本質是 workspace / KB / docs / environment index，不是 production service 本體。它可以支撐 Nick 的工作方法與系統理解能力，但不應新增 standalone 正式履歷主成果。

可安全使用的結論：

- Nick / `10gt12nc` 真實維護過 iwin workspace KB、跨專案關聯文件、dev-k3s / 舊 dev-linux 運維索引、payment / gameserver / app_bi / game_api / third_games_api 等分析材料。
- 這些 evidence 可以支撐「接手文件不足的複雜系統，透過 code reading、git history、schema、運維文件與 KB 整理重建 production flow」。
- 正式履歷若要寫，應放在方法論 / supporting context，不作獨立專案主成果。

不可使用的結論：

- 不能把 `iwin-workspace` 寫成 Nick 開發完整 iwin 平台。
- 不能把 workspace 裡的 `Projects/` 或 `.work/` 子 repo 快取，視為 Nick 在本 repo 直接開發那些業務 service。
- 不能把 workspace docs 裡的 payment / gameserver / k3s 記錄，反向包裝成 Nick 主導完整 payment、gameserver、DevOps 或 SRE。
- 不能搬運任何 `.env`、internal host、credential、JumpServer 細節、production URL。

## Source repo 狀態

### iwin-workspace

- 路徑：`/Users/nick/Git/iwin/iwin-workspace`
- 已執行：`git fetch --all --prune`
- local branch：`arnold`
- local HEAD：`9ae9eafc8a0455f070bd7e475338384743a52360`
- remote HEAD：`origin/arnold` = `9ae9eafc8a0455f070bd7e475338384743a52360`
- ahead / behind：`0 / 0`
- working tree：乾淨
- remote refs：`origin/arnold`、`origin/main`、`origin/Nick-app-bi-knowledge-base`、`origin/Nick-payment-knowledge-base`、`origin/feature/ai-cross-project-kb-scan`、`origin/feature/ai-payment-dev`、`origin/claude/ecstatic-blackburn-65b33e`、`origin/claude/flamboyant-nash-becfd2`

## 掃描範圍

已掃：

- repo root / docs 結構。
- `README.md`、`docs/README.md`、`docs/INDEX.md`。
- Nick / `10gt12nc` repo-wide commit log。
- representative commit stat：workspace KB 建立、payment / app_bi / iwin_gameserver / game_api / third_games_api cross-project KB、JumpServer wrapper、GoldenPay / NimTestPay / dev-k3s 記錄。
- path classification：`docs/`、`tools/`、`Projects/`、`.work/`。
- 既有 `nick-vault` 對 `iwin-workspace` 的引用與排序。

未掃 / 不納入：

- 未讀 `.env`。
- 未搬 workspace 裡的環境配置、internal host、credential、JumpServer 細節。
- 未逐檔重讀所有 docs / SQL dump，因本輪目標是 contribution claim consolidation，不是重建 workspace 內容。
- 未把 `Projects/` / `.work/` 子 repo 當作 `iwin-workspace` 自身的直接業務開發 evidence；它們應由各 project 自己的 source repo consolidation 判斷。

## Direct evidence 分層

| 範圍 | 代表 commits / path | 判斷 |
| --- | --- | --- |
| workspace KB / docs scaffold | `74a0c68`、`66fed56`、`96faba3`、`4a2cf40` | 真實做過 KB / docs / cross-project map |
| payment / provider verification notes | GoldenPay / NimTestPay 系列 `docs: record ...` commits | supporting evidence；正式 payment claim 仍以 `projects/iwin/payment/contribution-claim-consolidation.md` 為準 |
| iwin_gameserver KB / catalog | `97ea1bc`、`29234f4`、`4a2cf40` | supporting evidence；正式 gameserver claim 仍以 `projects/iwin/iwin_gameserver/contribution-claim-consolidation.md` 為準 |
| dev-k3s / environment docs | `docs/dev-k3s...`、k3s login / deploy / Redis recovery 類 commits | 可面試講環境理解 / troubleshooting；不寫完整 DevOps owner |
| tools / JumpServer wrapper | `6e18954`、`f762bb6` | 可作本機工具與運維入口整理 evidence；不寫 JumpServer / infra owner |
| `Projects/` / `.work/` 子 repo | 本機快取 / sync 工作區 | 不作 `iwin-workspace` 的業務功能 direct evidence |

## 可放履歷

不新增 standalone 主成果 bullet。

可在既有自我推薦 / 工作方法描述中保守吸收：

```text
透過跨 repo code reading、schema / Redis / MQ / 運維文件與 git history 梳理，建立 payment、gameserver、game_api、game_job、third_games_api 等核心 flow 的可查證知識庫與交接索引。
```

這句只能作 supporting context，不代表 Nick 主導全部 service。

## 可面試講

- 可以說 `iwin-workspace` 是用來整理 iwin 多 repo 的 workspace / KB / docs / environment index。
- 可以講如何用 workspace 追跨 repo flow：先定位 service、再對照 schema / Redis / MQ / config / git history，最後回 source repo 做 evidence gate。
- 可以講 GoldenPay / NimTestPay / gameserver / dev-k3s 類紀錄如何幫助整理 project-level contribution，但面試中的技術成果仍應回到對應 project。

## 不可誇大

- 不寫「主導 iwin-workspace 完整平台」。
- 不寫「負責完整 iwin 平台文件化 / DevOps / SRE」。
- 不寫「開發 payment / gameserver / game_api 全部功能」，除非對應 project consolidation 已支撐。
- 不寫「改善部署效率 / 故障恢復時間 X%」。
- 不寫任何敏感連線、機器資訊、secret、token、internal URL。

## 05 / 08 更新判斷

- `05-resume-master-zh.md`：只補 claim boundary note，不新增 standalone work experience bullet。
- `08-application-autobiography-zh.md`：只補 supporting context note，不新增主成果段落。
- 真正的履歷主成果仍由 `payment`、`game_api`、`game_job`、`iwin_gameserver` 等 project-level consolidation 支撐。

## 收斂狀態

原本的下一步已完成：

```text
- 全域下一步狀態：目前沒有預設 project flow 下一步；請以 senior-owner-playbook/01-senior-owner-flow-inventory.md 與 senior-owner-playbook/06-todo.md 為準。
```

原因：

- `iwin-workspace` 的 claim 已收斂為 supporting evidence，不值得繼續當主線深挖。
- `iwin_gameserver center-http-deposit-withdraw` 已完成 Step 3，下一步轉 Step 4 面試 case，對 Senior Backend / money correctness 更有價值。
