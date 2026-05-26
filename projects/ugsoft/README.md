# ugsoft

用途: 整理 ugsoft 系列 repo 的 Senior Backend / Platform Backend 履歷與面試素材。

來源 repo:

```text
/Users/nick/Git/ugsoft
```

## Project Status

| Project | 類型 | 狀態 | 履歷判斷 | 下一步 |
| --- | --- | --- | --- | --- |
| `ugsoft-admin-api` | Java / Spring Boot 後台 API、控制面、報表、RabbitMQ / Quartz | Career Track 已完成 rolling；Flow Track 未建立 | 可保守放「後台控制面與非同步資料處理」；不寫完整 UG 平台 owner | 如要深挖，先做 Step 1 / Step 2 選代表 flow |
| `ugsoft-connector-api` | provider connector / gateway、AntPlay / DerPlay adapter、transfer wallet、MQ | Career Track 已完成 rolling；Flow Track Step 1 已完成，Step 2 未建立 | 可保守放 provider connector / transfer wallet / MQ 素材；不寫完整 gateway owner | `ugsoft ugsoft-connector-api Step 2` |
| `ugsoft-admin-web` | 後台前端 | 未開始 | 通常只作入口 | 待 Nick 指定 |
| `official-web-v3` | 官網 | 未開始 | 不當主線 | 待 Nick 指定 |
| `ugsoft-workspace` | workspace / docs / harness / runbook | contribution consolidation 已完成 / rolling | supporting evidence；不放 standalone 正式履歷主成果 | 已收斂；回 `ugsoft-connector-api Step 2` |

## Claim Boundary

- `ugsoft-admin-api` 有 Nick / `10gt12nc` 大量 direct commits，範圍包含 login / JWT / RBAC、商戶 / provider 白名單、超級代理、報表查詢、風控監控、RabbitMQ request log / bet record、Quartz / report job 維護。
- `ugsoft-connector-api` 有 Nick / `10gt12nc` 大量 direct commits，範圍包含 AntPlay / DerPlay provider adapter、login / balance / transfer in-out / bet-settle / callback、request / bet record MQ、transfer wallet compensation、分表與 circuit breaker code-backed reliability。
- `ugsoft-workspace` 是 workspace / docs / harness repo。source git author 主要是 `arnold`，本次未命中 `10gt12nc` / `Nick` author；若 `arnold` 是 Nick 公司帳號，可作 workspace / docs / tooling 真實貢獻，否則待本人確認。無論如何都只作 cross-repo system reconstruction / runbook / harness supporting evidence，不反向包裝成 service runtime code。
- 可以放履歷的是「參與後台控制面與非同步資料處理開發維護」，不是「主導完整 UG 平台」。
- connector/provider gateway 可以保守使用 `ugsoft-connector-api` consolidation 結論，但完整 flow 面試包仍需 Step 1 / Step 2 後逐條深掃。

## 2026-05-26 Completeness Audit

- `ugsoft-admin-api` 與 `ugsoft-workspace` 目前仍只有 Career Track：project-level `contribution-claim-consolidation.md`。
- `ugsoft-connector-api` 已完成 Flow Track Step 1；尚無 Step 2 / `flows/{flow}/flow.md`。
- 因此只能說「UGSoft 履歷 claim 已有 rolling consolidation，且 connector 已開始 Flow Track」，不能說「UGSoft flow 已完整」或「已逐條深掃 flow」。
- `ugsoft-connector-api` Step 1 候選 flow 見 `projects/ugsoft/ugsoft-connector-api/step1-candidate-flows.md`。
- 若 Nick 之後指定補 `ugsoft-connector-api` flow，下一步必須先做 Step 2 比較候選 flow，不得直接跳單條 flow Step 3。
