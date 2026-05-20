# ugsoft

用途: 整理 ugsoft 系列 repo 的 Senior Backend / Platform Backend 履歷與面試素材。

來源 repo:

```text
/Users/nick/Git/ugsoft
```

## Project Status

| Project | 類型 | 狀態 | 履歷判斷 | 下一步 |
| --- | --- | --- | --- | --- |
| `ugsoft-admin-api` | Java / Spring Boot 後台 API、控制面、報表、RabbitMQ / Quartz | contribution consolidation 已完成 / rolling | 可保守放「後台控制面與非同步資料處理」；不寫完整 UG 平台 owner | 如要深挖，先做 Step 1 / Step 2 選代表 flow |
| `ugsoft-connector-api` | provider gateway / connector API 候選 | 未開始 | 待掃 | `ugsoft ugsoft-connector-api contribution claim consolidation` 或 Step 1 |
| `ugsoft-admin-web` | 後台前端 | 未開始 | 通常只作入口 | 待 Nick 指定 |
| `official-web-v3` | 官網 | 未開始 | 不當主線 | 待 Nick 指定 |
| `ugsoft-workspace` | workspace / docs | 未開始 | supporting evidence | 待 Nick 指定 |

## Claim Boundary

- `ugsoft-admin-api` 有 Nick / `10gt12nc` 大量 direct commits，範圍包含 login / JWT / RBAC、商戶 / provider 白名單、超級代理、報表查詢、風控監控、RabbitMQ request log / bet record、Quartz / report job 維護。
- 可以放履歷的是「參與後台控制面與非同步資料處理開發維護」，不是「主導完整 UG 平台」。
- connector/provider gateway 若要寫成更強的 provider integration，需要另掃 `ugsoft-connector-api`。
