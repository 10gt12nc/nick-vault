# Company System Deep Dive Project Index

狀態：候選專案清單，不是自動掃描授權。

## 使用方式

每次 deep dive 必須先選 `一個專案`，再選 `一個 topic`。

格式：

```text
Project
Topic
Scope
Curriculum area
Non-goal
```

例如：

```text
Project: iwin
Topic: Payment callback
Scope: payment callback controller / MQ producer / consumer / order state
Curriculum area: Provider Integration + Transaction / Consistency + MQ / Async
Non-goal: 不整理整個 iwin 大系統
```

## 專案清單

| Project | Local Path | 預設 deep dive 方向 | 主要價值 | 邊界 |
| --- | --- | --- | --- | --- |
| `iwin` | `/Users/nick/Git/iwin` | payment、gameserver、game_job、deployment 中的 production flow、code map、failure window、legacy constraint | Payment provider、wallet / transfer、report batch、legacy reconstruction、rollout / observability | 不全掃大型系統；不把主管 / team commit 當 Nick direct evidence |
| `antplay` | `/Users/nick/Git/antplay` | slot game api、slot game job、star math 中的 money correctness、projection、partition、math contract edge case | Bet / settle / rollback、wallet transfer、MQ projection、partition、slot math contract | Math 題目只能講 Backend 理解與 result contract，不講成 math owner |
| `ugsoft` | `/Users/nick/Git/ugsoft` | provider connector、admin、auth、RBAC、JWT 的 module boundary / code reading / integration design | Provider connector、JWT / RBAC / admin flow、integration boundary | 未盤點前不寫成正式履歷 claim |
| `usproject` | `/Users/nick/Git/usproject` | 先確認專案性質，再決定是否有可用 deep dive | 可作補充系統閱讀或 side context | 未確認前不當公司專案 evidence |
| `DevOps` | `/Users/nick/Git/DevOps` | rollout、health check、monitoring、config / secret boundary 的 production thinking | Deployment / rollback / health check / monitoring decision | 不包裝成 DevOps owner，不全掃 infra |

## 專案輪詢規則

第一輪優先順序：

1. `iwin`：最貼近 Provider Integration、payment callback、wallet / transfer、legacy。
2. `antplay`：最貼近 Wallet / Bet-Settle / MQ、projection、slot domain。
3. `ugsoft`：補 Provider Connector、Admin、Auth、RBAC、JWT 類經驗。
4. `DevOps`：只作 rollout / observability / deployment decision 加分。
5. `usproject`：先確認定位；未確認前只作 optional context。

預設輪詢：

```text
iwin -> antplay -> ugsoft -> iwin -> antplay -> DevOps -> iwin -> antplay -> usproject
```

若當週 Nick 正在準備某個 JD 或面試，就依 JD / 面試追問調整。

## 不做的事

- 不一次掃五個專案。
- 不平均掃所有 module。
- 不建立公司 code 詳細流水帳。
- 不保存 secret、商戶、內部 URL、真實交易資料。
- 不把 `code-backed`、`team context`、`learning-only` 包裝成 Nick direct owner。
- 不把 Spring Cloud / microservice / Kafka / K8s 現代化想像變成自動改造任務。
