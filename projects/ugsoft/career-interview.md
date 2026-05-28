# UGSoft domain career / interview map

更新日期：2026-05-28

本檔把 UGSoft domain-level 大地圖轉成履歷與面試口徑。正式履歷仍以 `senior-owner-playbook/05-resume-master-zh.md` 與 `08-application-autobiography-zh.md` 為準；本檔只提供 UGSoft 系統視角與 claim boundary。

## 可以怎麼定位 UGSoft 經驗

保守主軸：

```text
參與 UGSoft provider connector 與後台控制面相關後端開發維護，範圍包含商戶 connector API、AntPlay / DerPlay provider adapter、transfer wallet / transaction query、provider callback、request / bet record MQ、後台 login / JWT / RBAC、商戶 / provider 白名單、RequestLog / BetRecord consumer、報表 / 風控與 Quartz job。能分清 provider contract、connector local state、MQ async audit、admin report view 與 whitelist control plane 的邊界。
```

這是 domain-level 面試定位。正式履歷要拆成 admin-api / connector-api project-level claim。

## 可放履歷的 UGSoft 素材

| Project | 可放履歷口徑 | 證據層級 | 不可擴張 |
| --- | --- | --- | --- |
| `ugsoft-connector-api` | 參與 provider connector / gateway、transfer wallet、provider callback、request / bet record MQ、job sync 維護 | 真實開發過 + code-backed | 不寫完整 provider gateway、完整 wallet / reconciliation owner |
| `ugsoft-admin-api` | 參與後台控制面、白名單、RequestLog / BetRecord consumer、報表 / 風控 / Quartz job 維護 | 真實開發過 + code-backed | 不寫完整 UG platform、完整 access-control / RabbitMQ architecture owner |

## Supporting 素材

| Project | 面試價值 | 語氣 |
| --- | --- | --- |
| `ugsoft-workspace` | cross-repo system reconstruction、runbook / harness / provider migration notes | supporting evidence |
| `ugsoft-admin-web` | 後台入口 | 不主打 |
| `official-web-v3` | 官網 | 不主打 |

## 面試大圖回答

```text
UGSoft 我會用 connector + admin 兩層來講。connector-api 比較靠近 provider gateway，處理商戶 connector API、AntPlay / DerPlay adapter、transfer wallet、callback、request / bet record MQ 和 job sync；admin-api 是後台控制面，處理登入 / RBAC、商戶 / provider 白名單、RequestLog / BetRecord consumer、報表和風控。我的重點會放在 provider contract mapping、transaction lookup、callback idempotency、MQ eventual consistency、admin query 不是 source of truth，以及白名單控制面如何同步到 runtime。
```

## 追問防線

| 追問 | 回答方向 |
| --- | --- |
| 你是不是 UGSoft owner？ | 不是。我掌握的是 connector / admin 代表 flows 與邊界，不包裝成整個平台 owner。 |
| connector 是否就是 wallet source of truth？ | 不一定。可講 transfer wallet / provider position / transaction query 的一致性與風險，但不說完整 wallet owner。 |
| MQ 是否保證 exactly-once？ | 不誇大。只講 duplicate guard、consumer idempotency、eventual consistency、report lag；沒有 evidence 不說 exactly-once。 |
| admin API 是否能代表 provider gateway？ | 不能。admin 是 control plane / query / consumer；provider gateway 邏輯要回到 connector-api。 |
| workspace 能不能當貢獻？ | 只能 supporting。workspace 是 docs / harness / runbook，不是 runtime service，也不能反推所有 service 開發都是 Nick 做的。 |

## 推薦讀法

1. 先讀 [architecture-map.md](architecture-map.md)，建立 connector / admin / workspace 分層。
2. 再讀 [integration-map.md](integration-map.md)，理解 transfer、callback、MQ、白名單的跨 repo 邊界。
3. 想補履歷 claim，回到 `ugsoft-admin-api` / `ugsoft-connector-api` 的 `contribution-claim-consolidation.md`。
4. 想練面試 case，回到單條 flow 的 `flow.md` 與 `career-interview.md`。
