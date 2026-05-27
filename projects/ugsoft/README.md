# ugsoft

用途: 整理 ugsoft 系列 repo 的 Senior Backend / Platform Backend 履歷與面試素材。

來源 repo:

```text
/Users/nick/Git/ugsoft
```

## Project Status

2026-05-26 re-audit：已重新掃 `/Users/nick/Git/ugsoft` 各來源 repo 的 remote refs、local HEAD、Nick / `10gt12nc` commits、主要 module / path history 與既有 KB。結論：UGSoft 仍有真正值得補的 Flow Track，但只保留收斂後兩個方向：第一順位 `ugsoft-connector-api transfer-wallet-in-out-query` 已於 2026-05-27 完成 Step 4，第二順位 `ugsoft-admin-api Step 1 / Step 2`。官網、前端與 workspace 不列主待辦；workspace 只作 cross-repo reconstruction / runbook supporting evidence。

| Project | 類型 | 狀態 | 履歷判斷 | 下一步 |
| --- | --- | --- | --- | --- |
| `ugsoft-admin-api` | Java / Spring Boot 後台 API、控制面、報表、RabbitMQ / Quartz | Career Track 已完成 rolling；Flow Track 未建立；2026-05-26 re-audit 後仍值得補 | 可保守放「後台控制面與非同步資料處理」；不寫完整 UG 平台 owner | 第二順位；如要深挖，先做 Step 1 / Step 2 選代表 flow |
| `ugsoft-connector-api` | provider connector / gateway、AntPlay / DerPlay adapter、transfer wallet、MQ | Career Track 已完成 rolling；Flow Track Step 1 / Step 2 已完成；`transfer-wallet-in-out-query` Step 4 已完成 | 可保守放 provider connector / transfer wallet / MQ 素材；不寫完整 gateway owner | `transfer-wallet-in-out-query Step 5` |
| `ugsoft-admin-web` | 後台前端 | 未開始 | 通常只作入口 | 待 Nick 指定 |
| `official-web-v3` | 官網 | 未開始 | 不當主線 | 待 Nick 指定 |
| `ugsoft-workspace` | workspace / docs / harness / runbook | contribution consolidation 已完成 / rolling | supporting evidence；不放 standalone 正式履歷主成果 | 已收斂；connector 第一條 flow 已到 Step 4 |

## Claim Boundary

- `ugsoft-admin-api` 有 Nick / `10gt12nc` 大量 direct commits，範圍包含 login / JWT / RBAC、商戶 / provider 白名單、超級代理、報表查詢、風控監控、RabbitMQ request log / bet record、Quartz / report job 維護。
- `ugsoft-connector-api` 有 Nick / `10gt12nc` 大量 direct commits，範圍包含 AntPlay / DerPlay provider adapter、login / balance / transfer in-out / bet-settle / callback、request / bet record MQ、transfer wallet compensation、分表與 circuit breaker code-backed reliability。
- `ugsoft-workspace` 是 workspace / docs / harness repo。source git author 主要是 `arnold`，本次未命中 `10gt12nc` / `Nick` author；Nick 已確認 `arnold` 是主管帳號，不是 Nick direct evidence。因此 `ugsoft-workspace` 只能作主管 / 團隊 context、workspace learning 或 cross-repo system reconstruction / runbook / harness supporting evidence，不作 Nick 真實開發貢獻，也不反向包裝成 service runtime code。
- 可以放履歷的是「參與後台控制面與非同步資料處理開發維護」，不是「主導完整 UG 平台」。
- connector/provider gateway 可以保守使用 `ugsoft-connector-api` consolidation 結論，但完整 flow 面試包仍需 Step 1 / Step 2 後逐條深掃。

## 2026-05-26 Completeness Audit

- `ugsoft-admin-api` 與 `ugsoft-workspace` 目前仍只有 Career Track：project-level `contribution-claim-consolidation.md`。
- `ugsoft-connector-api` 已完成 Flow Track Step 1 / Step 2；第一條代表 flow `transfer-wallet-in-out-query` 已建立 `flows/transfer-wallet-in-out-query/flow.md`。
- 因此只能說「UGSoft 履歷 claim 已有 rolling consolidation，且 connector 第一條代表 flow 已完成 Step 4」，不能說「UGSoft 全部 flow 已完整」或「已逐條深掃所有代表 flow」。
- `ugsoft-connector-api` Step 1 候選 flow 見 `projects/ugsoft/ugsoft-connector-api/step1-candidate-flows.md`。
- `ugsoft-connector-api` Step 2 比較排序見 `projects/ugsoft/ugsoft-connector-api/step2-flow-comparison.md`。
- 若 Nick 之後繼續同一條 flow，下一步應做 `transfer-wallet-in-out-query Step 5`，完成單條 flow claim gate。
