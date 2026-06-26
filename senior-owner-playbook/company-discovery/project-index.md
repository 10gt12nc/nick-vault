# Company Discovery Project Index

狀態：候選專案清單，不是自動掃描授權。

## 使用方式

每次 discovery 必須先選 `一個專案`，再選 `一個 discovery target`。

格式：

```text
Project
Discovery target
Scope searched
Expected unknown
Non-goal
```

例如：

```text
Project: iwin
Discovery target: MQ publish fail / callback accepted but not processed
Scope searched: payment callback producer / producer utility / related git history
Expected unknown: 是否存在只 log 不補償的 durable enqueue failure window
Non-goal: 不重述 payment callback happy path
```

## 專案清單

| Project | Local Path | 預設 discovery 方向 | 主要價值 | 邊界 |
| --- | --- | --- | --- | --- |
| `iwin` | `/Users/nick/Git/iwin` | payment、gameserver、game_job、deployment 中的 failure window、git history、legacy constraint | Payment provider、wallet / transfer、report batch、legacy reconstruction、rollout / observability | 不全掃大型系統；不把主管 / team commit 當 Nick direct evidence |
| `antplay` | `/Users/nick/Git/antplay` | slot game api、slot game job、star math 中的 money correctness、projection、partition、math contract edge case | Bet / settle / rollback、wallet transfer、MQ projection、partition、slot math contract | Math 題目只能講 Backend 理解與 result contract，不講成 math owner |
| `ugsoft` | `/Users/nick/Git/ugsoft` | provider connector、admin、auth、RBAC、JWT 的 unknown boundary / legacy issue | Provider connector、JWT / RBAC / admin flow、integration boundary | 未盤點前不寫成正式履歷 claim |
| `usproject` | `/Users/nick/Git/usproject` | 先確認專案性質，再找是否有可用 discovery | 可作補充系統閱讀或 side context | 未確認前不當公司專案 evidence |
| `DevOps` | `/Users/nick/Git/DevOps` | rollout、health check、monitoring、config / secret boundary 的 hidden risk | Deployment / rollback / health check / monitoring decision | 不包裝成 DevOps owner，不全掃 infra |

## 專案輪詢規則

第一輪優先順序：

1. `iwin`：最可能挖到 provider callback、payment、wallet、legacy failure window。
2. `antplay`：最可能挖到 bet-settle、wallet、projection、partition 的 risk。
3. `ugsoft`：補 provider connector、admin、auth、RBAC、JWT 類 unknown。
4. `DevOps`：只挖 rollout / observability / deployment risk。
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
