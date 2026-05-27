# ugsoft-workspace Contribution Claim Consolidation

日期: 2026-05-20

## 結論

`ugsoft-workspace` 是 supporting evidence，不是獨立履歷主成果。它可以支撐 Nick 的「跨 repo 系統重建、工程規範化、AI / harness 協作、release / deploy runbook、provider migration 風險整理」能力，但不能把 workspace 文件反向包裝成 `ugsoft-admin-api` 或 `ugsoft-connector-api` 的 production code 貢獻。

本 repo 的 git author 主要是 `arnold`，本次掃描沒有命中 `10gt12nc` / `Nick` author。Nick 已確認 `arnold` 是主管帳號，不是 Nick direct evidence。因此本 repo 不能視為 Nick 真實做過的 workspace / docs / tooling 貢獻；只能標為「專案存在 / code-backed + 主管 / 團隊 context」。正式履歷不新增 `ugsoft-workspace` standalone bullet。

可保守使用的說法:

> 透過 workspace / runbook / code-reading 文件梳理 UGSoft 三專案邊界、provider API 契約、Redis / RabbitMQ / Quartz 執行態、local verify / dev / UAT deploy harness 與 migration / release 風險，支援後續後端維護與問題排查。

不要寫:

- 不得寫成主導完整 UGSoft workspace / DevOps / release owner。
- 不得寫成主導完整 provider migration 或 production runbook 執行。
- 主導 `ugsoft-admin-api` / `ugsoft-connector-api` production 功能，除非回到對應 service repo 的 commits / MR / ticket 補證據。
- 完整落地 SerialSyncJob、AntPlay migration、deploy harness 到所有環境。

## Evidence Summary

| 類別 | 判斷 | Evidence |
| --- | --- | --- |
| repo 類型 | supporting evidence | `README.md` 明確定位為 UGSoft 三核心專案理解入口、需求開發查核區、Harness Engineer 規範入口 |
| 直接 author | 非 Nick evidence | `git log --all --author='10gt12nc|Nick|nick'` 無命中；`shortlog --all` 顯示 40 筆 `arnold` commits；Nick 已確認 `arnold` 是主管帳號 |
| cross-repo system reconstruction | code-backed / docs-backed | `docs/01`、`docs/07`、`docs/08`、`docs/10`、`docs/12~15` |
| provider API / contract | code-backed / docs-backed | `docs/05` 整理 connector API endpoint、sign、transfer、bet record contract |
| infra / async / job | code-backed / docs-backed | `docs/06`、`docs/21` 整理 Redis ID_STORE、RabbitMQ、Quartz / SerialSyncJob 風險 |
| local / dev / UAT harness | code-backed / tooling-backed | `Makefile`、`workspace.manifest`、`scripts/bootstrap-workspace.sh`、`scripts/verify-*`、`scripts/deploy-*` |
| provider migration runbook | docs-backed / 待實作 evidence | `docs/22` 整理 AntPlay 配置 migration 步驟與 Redis / serial 風險；不可宣稱已主導 prod migration |

## Source Scan Record

來源 repo:

```text
/Users/nick/Git/ugsoft/ugsoft-workspace
```

遠端狀態:

- 已執行 `git fetch --all --prune` 更新 remote refs。
- local branch: `main`
- local HEAD: `c2a45aedcf109b9d5c72d0e22fd52ca7da0d56f7`
- remote HEAD: `origin/main`
- `origin/main`: `c2a45aedcf109b9d5c72d0e22fd52ca7da0d56f7`
- local vs `origin/main`: `0 / 0`，本機與 remote main 一致。
- source repo 工作樹乾淨。

remote branches:

- `origin/main`
- `origin/codex/ugsoft-workspace-analysis`
- `origin/codex/ugsoft-workspace-harness`
- `origin/claude/festive-ptolemy-e16adc`
- `origin/claude/funny-lederberg-6ba7ad`

本次掃描範圍:

- vault KB: `AGENTS.md`、`00-operating-rules.md`、`09-ai-prompt-manual.md`、`03-flow-learning-package-template.md`
- vault ugsoft docs: `projects/ugsoft/README.md`、`ugsoft-admin-api` / `ugsoft-connector-api` consolidation、source inventory、flow inventory、05 / 08 / todo
- source git: `git log --all`、`shortlog --all`、`ls-tree origin/main`、remote branch list、key commit `show --stat`
- source docs / tooling: `README.md`、`docs/01`、`docs/05`、`docs/16`、`docs/21`、`docs/22`、`Makefile`、`workspace.manifest`、`scripts/*`、`proposals/*`

未完成 / 未宣稱:

- 未掃 `ugsoft-workspace` 的第三方 provider 靜態 HTML / JS 內容逐行；該部分是外部 API 文件鏡像，不應作 Nick 開發成果。
- 未把 `docs/22` 內 SQL / migration runbook 當成已執行 production migration。
- 未把 workspace deploy scripts 當成完整 DevOps ownership。
- 未做 `ugsoft-admin-api` / `ugsoft-connector-api` 新一輪 Flow Track Step 1；service runtime claim 仍以各自 consolidation 為準。

## Important Commit Evidence

### 初始 cross-repo 分析與 provider 文件

- `b65762e`：新增 UGSoft workspace analysis，建立 admin-web / admin-api / connector-api analysis、跨專案 relation、system architecture、dev plan、AntPlay / DerPlay relation docs，並納入第三方 provider API 文件。

可支撐:

- cross-repo code reading / service boundary reconstruction。
- provider 文件對照與後端 flow 前置分析。

不可誇大:

- 這是 docs / analysis，不是 service runtime code commit。

### local verify / deploy-dev harness

- `1f71060`：新增 local verify + deploy-dev harness，包含 `Makefile`、workspace manifest、bootstrap、verify-admin-api / verify-admin-web、deploy-dev、三個 repo 的 CI proposal 與 local-to-dev workflow。

可支撐:

- 工程環境標準化、降低 Java / Node 版本漂移風險。
- 以 wrapper / Makefile 把 local verify 與 dev deploy 操作收斂成一致入口。

不可誇大:

- 不說完整 CI/CD owner；proposals 是否採用仍要看各 service repo / GitLab evidence。

### UAT deploy tooling

- `66d78be` / `32a6ca2`：新增 UAT deploy 指令與文件，支援 kustomize apply / rollout restart / rollout status / notification 類流程，並加上使用邊界。

可支撐:

- 了解 dev / UAT / PROD release discipline 的差異。
- 能把 deployment 操作風險寫成可執行 runbook。

不可誇大:

- 不說完整 UAT / PROD owner，不寫實際上線事故或改善數字。

### SerialSyncJob 設計備忘

- `2102d48`：同步 SerialSyncJob 設計備忘與 Job 列表，整理 Redis ID_STORE -> DB serial 回寫、`number` 不回退、Redis 丟失風險、30 秒回寫窗口與手動恢復 SOP。

可支撐:

- 面試可講 Redis seed / DB fallback / ID collision / periodic sync 的 owner thinking。
- 可搭配 `ugsoft-connector-api` 的 `BufferIdService`、transfer / bet record id 風險來講。

不可誇大:

- 不說已主導實作 SerialSyncJob，除非回 source service repo 補 commit / MR evidence。

### AntPlay -> UGSoft configuration migration runbook

- `6ae1bb7` / `c2a45ae` / `e831b86`：整理 AntPlay 配置 migration 至 UGSoft 的步驟清單，包含 agent、agent_key、game、serial、white IP、admin auth、agent relations、Redis rebuild / rollout 類風險提示。

可支撐:

- migration planning、資料依賴順序、cache rebuild / serial / wallet 暫停風險意識。

不可誇大:

- 不說已負責或完成 production migration。這是 runbook evidence，production execution 要另補 ticket / issue / 操作紀錄 / 本人確認。

## Resume Claim

正式履歷不建議新增 `ugsoft-workspace` 單獨 bullet。若要放，只能併在「接手複雜既有系統、跨 repo 梳理與可維護性」這類方法論句子中:

- 透過 workspace / runbook / code-reading 文件重建 UGSoft admin / connector / provider 邊界，整理 API contract、Redis / RabbitMQ / Quartz 執行態、local verify / deploy harness 與 migration 風險，支援後續維護、交接與問題排查。

更好的履歷主 bullet 仍是:

- `ugsoft-admin-api`：後台 API / control plane / RabbitMQ / Quartz。
- `ugsoft-connector-api`：provider connector / gateway / transfer wallet / MQ / provider fail-fast。

## Interview Use

可面試講:

- 如何從 workspace docs 反推三專案邊界: admin-web 是入口，admin-api 是後台設定 / 控制面，connector-api 是交易 / provider 對接 runtime。
- 為什麼 workspace 不等於 service ownership: docs 只能支撐理解與方法，production claim 要回 source repo commits。
- SerialSyncJob 設計中為什麼要避免 `serial.number` 回退，以及 Redis 全量丟失後為什麼會有 ID collision 風險。
- provider migration runbook 為什麼要把 agent / key / game / serial / white IP / Redis rebuild / rollout 順序拆清楚。
- local verify / deploy harness 如何降低多人協作時的環境漂移與 release 操作錯誤。

不要講:

- 我主導 UGSoft 全部部署。
- 我主導完整 AntPlay migration。
- 我設計並落地完整 serial reconciliation。
- 我是 workspace / DevOps / release owner。

## 05 / 08 更新口徑

`05-resume-master-zh.md` / `08-application-autobiography-zh.md` 只補 supporting boundary，不新增正式主成果:

> `ugsoft-workspace` 已完成 rolling consolidation。它只支撐跨 repo code reading、系統邊界重建、工程規範、harness / runbook 與 migration 風險整理能力；不作 standalone 履歷主成果，也不反向包裝成 `ugsoft-admin-api` / `ugsoft-connector-api` 的 service code 貢獻。

## Suggested Next

`ugsoft-workspace` 已收斂；`ugsoft-connector-api` Flow Track Step 1 / Step 2、本批三條代表 flow Step 5 與 `contribution claim consolidation refresh` 也已完成。`ugsoft-admin-api Step 1 / Step 2` 已完成，第一條 `connect-bet-record-mq-ingestion Step 3` 也已完成；下一步若繼續 UGSoft，可選 `ugsoft-admin-api connect-bet-record-mq-ingestion Step 4`，但這是可選非 iwin 廣度補強，不是投遞前必做。
