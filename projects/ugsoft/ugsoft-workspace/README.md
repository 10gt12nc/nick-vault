# ugsoft-workspace

`ugsoft-workspace` 是 UGSoft 的 workspace / docs / harness repo，不是 runtime service。它的價值在於把 `ugsoft-admin-web`、`ugsoft-admin-api`、`ugsoft-connector-api` 與 provider 文件、環境流程、release / deploy runbook 串成可維護的 project-level 工作區。

## Status

| 項目 | 狀態 |
| --- | --- |
| contribution claim consolidation | 已完成 / rolling / 2026-05-20 |
| Flow Track | 不建議當 flow 主題 |
| 履歷判斷 | supporting evidence；不放 standalone 正式履歷主成果 |
| 主要價值 | cross-repo system reconstruction、工程規範、harness / release / runbook、provider migration / SerialSyncJob 設計備忘 |

## Claim Boundary

可保守說:

- 梳理 UGSoft 三專案邊界、admin-api / connector-api 關係、provider API 契約與 Redis / RabbitMQ / Quartz 類執行態。
- 維護 workspace-level AI / 工程規範、local verify、dev / UAT deploy harness、release discipline 與 handoff / decision log 模板。
- 整理 provider migration runbook、SerialSyncJob / `serial` 回寫風險備忘、AntPlay / DerPlay relation docs，作為面試時的系統重建與 owner thinking supporting evidence。

不可誇大:

- 不寫成 `ugsoft-workspace` 本身是 production service。
- 不用 workspace docs 反向宣稱 Nick 主導 `ugsoft-admin-api` 或 `ugsoft-connector-api` 的業務功能。
- 不寫完整 DevOps owner、完整 release owner、完整 UAT / PROD owner、完整 provider migration owner。
- 不寫已獨立落地所有 docs 內的設計；實作仍要回 source service repo 與 ticket / MR / release evidence。

## Files

- [contribution-claim-consolidation.md](/Users/nick/Git/nick/nick-vault/projects/ugsoft/ugsoft-workspace/contribution-claim-consolidation.md)
