# Claim Boundary - game-api-provider-white-ip-control-plane

日期: 2026-05-27

## Evidence 分層

| Claim | 層級 | 說明 |
| --- | --- | --- |
| Nick 參與 Game API white IP 後台控制面 | 真實開發過 + code-backed | `f90a1eb`、`6cf3855`、`4c86d96`、`1dd366c` direct evidence |
| Nick 參與 provider white IP 後台表 / CRUD 初版 | 真實開發過 + code-backed | `2fb2ce5` direct evidence |
| Game API white IP 影響 connector runtime filter | code-backed context | `ugsoft-connector-api WhiteIpFilter` 讀 Redis set；下游 context，不當 admin-api owner |
| Provider white IP fanout reload connector cache | 主管 / team context + code-backed | `caf9fe7`、`30fff32` 為 `arnold` commits；可講 latest behavior，不當 Nick direct evidence |

## 可放履歷的保守素材

此 flow Step 4 先不直接更新 `05 / 08`。若後續 Step 5 或 project contribution refresh 採用，可用:

- 參與 UGSoft 後台 Game API / provider IP 白名單控制面開發維護，處理後台 CRUD、DB / Redis 同步、權限範圍與操作紀錄。

## 可面試講

- control plane 設定如何影響 runtime access-control。
- DB source of truth 與 Redis / in-memory cache runtime view 的一致性邊界。
- DB + Redis / fanout 無法原子提交時的 partial failure。
- provider white IP agent-scoped vs global-scoped 的 scope decision。
- 權限 gate、limited admin、operation log 在 runtime access-control 設定中的重要性。

## 不可誇大

- 不說主導完整 access-control platform。
- 不說完整 connector reload owner。
- 不把 `arnold` 的 provider fanout reload / 全站共用重構寫成 Nick direct evidence。
- 不說已建立完整強一致 DB / Redis / MQ cache coherence。
- 不說這條 flow 等同完整 provider gateway、wallet、ledger 或 money correctness。
- 不說有量化改善或 production incident owner，除非 Nick 之後補 ticket / incident evidence。

## Step 4 判定

- 可作正式面試 case: 是。
- 可直接更新 `05 / 08`: 否。Step 4 只完成口說與追問防線，尚未做單條 flow claim gate。
- 可作 project-level supporting evidence: 待 Step 5 判斷。

## 待 Step 5 判斷

- 本 flow 是否能單獨升級為 project-level履歷 supporting evidence。
- Provider white IP latest behavior 中哪些只能列為「我能分析 / code-backed」，哪些可以列入「我參與」。
- 是否需要在 project contribution refresh 中把白名單 control plane 從泛稱「商戶 / provider 白名單」拆成更具體 bullet。
