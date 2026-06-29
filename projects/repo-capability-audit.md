# Repo Capability Audit

日期：2026-06-29

狀態：`Frozen reference`。這份只作 capability map、偏差校正、優缺點參考與下一步候選池；不是目前主線、不是待辦 backlog、不是履歷證據，也不代表要把列出的 repo 都掃完。

範圍：

```text
/Users/nick/Git/usproject
/Users/nick/Git/antplay
/Users/nick/Git/iwin
/Users/nick/Git/ugsoft
/Users/nick/Git/DevOps
/Users/nick/Git/nick/nick-vault
```

## 這份文件怎麼用

這份是 repo-level capability audit，不是履歷 claim、不是 Flow Step、不是完整 code deep dive。

它的目的只有一個：

```text
先從能力價值看 repo / system / architecture topic，
不要一開始就被 Payment、履歷、自傳或三個 Story 綁住。
```

本輪只看 repo marker、README、既有 KB 與高層結構；沒有深掃單條 flow、沒有查 path-specific git history、沒有更新 `04 / 05 / 08 / 17 / 19`，也沒有新增正式履歷 claim。

證據層級：`repo-level / marker-backed / KB-backed`。若要升級成可面試 flow evidence，仍需另做 Step 1 / Step 2 / Step 3。

## 一頁結論

目前 KB 的確有「Payment / 履歷導向」偏差，但這不是因為 Payment 一定最有價值，而是因為它最容易包裝成求職故事。

真正值得重新拉出來的能力主線有五條：

| 主線 | 代表 repo / system | 能力價值 |
| --- | --- | --- |
| Runtime + money correctness | `iwin_gameserver`、`third_games_api`、`ugsoft-connector-api`、`antplay-slot-game-api` | Wallet、bet / settle / rollback、state transition、idempotency、provider boundary。 |
| MQ / projection / batch | `antplay-slot-game-job`、`iwin/game_job`、`ugsoft-admin-api`、`game-job` | Consumer、projection、report rebuild、batch idempotency、lag / replay。 |
| Runtime packaging / rollout | `usproject-deploy`、`usproject-deploy-template`、`k3s-deploy` | Artifact boundary、initContainer、CI/CD、rollout、observability。 |
| Push / realtime delivery | `game-push`、`antplay-push`、`antplay-push-grpc` | WebSocket / STOMP、Kafka to push、session lifecycle、backpressure。 |
| Slot math runtime / validation | `math-core`、`*-math`、`game-api` | Result contract、RTP validation、runtime loading、domain correctness。 |

Payment 仍然是高價值 topic，但它只代表：

- Trust boundary
- Signature / callback
- Timeout unknown
- Order state machine
- Query fallback

它不應該繼續壓過 wallet、runtime、projection、push、deploy、math validation 這些能力。

## 下一步最值得深掃的 3 個候選

本輪只列候選，不自動開工。

### 1. usproject deployment + runtime packaging chain

範圍：

```text
usproject-deploy
usproject-deploy-template
game-api
game-web
math/*-math
client/*-client
```

為什麼值得：

- 目前 KB 最缺這塊，而且完全不是 Payment。
- 能學 Platform Backend 重要能力：artifact boundary、math jar / static package、initContainer、rollout gate、observability。
- 對「看懂公司整體部署與 runtime 組裝」很有價值。

保守邊界：

- 先當 capability deep dive / architecture learning。
- 不寫成 Nick production owner。

### 2. Push / realtime delivery chain

範圍：

```text
usproject/game-push
antplay/antplay-push
antplay/antplay-push-grpc
```

為什麼值得：

- 現有 KB 對 push、realtime、session lifecycle 幾乎沒有主線。
- 可補 Thread Pool、WebSocket / STOMP、Kafka -> push、session cleanup、backpressure、observability。
- 對 Platform Backend / runtime stability 有價值，而且和 Payment 完全不同。

保守邊界：

- 先做 repo-level Step 1 / Step 2。
- 不直接寫履歷。

### 3. Slot math runtime + validation chain

範圍：

```text
antplay/math-core
antplay/*-math
usproject/math/*-math
usproject/game-api
```

為什麼值得：

- 差異化最高，也最不該被 Payment 壓過。
- 能學 result contract、RTP validation、runtime loading、compatibility、simulation、game domain correctness。
- 適合建立「Backend + domain validation」的長期能力，不只是求職故事。

保守邊界：

- 不寫成完整 Math Owner。
- 只講 module maintenance、validation、contract understanding。

## 高價值 repo / topic 快速地圖

### usproject

| Repo / group | 類型 | 價值 | Top candidate topic |
| --- | --- | --- | --- |
| `game-api` | Java game runtime API | 高 | Game entry、token / session、secure API、runtime math loading。 |
| `game-job` | Java job / report / sync | 高 | Bet / report / settled data sync、batch idempotency、projection rebuild。 |
| `game-push` | Java WebSocket / STOMP push | 高 | Kafka `push_user` -> push、session lifecycle、backpressure。 |
| `usproject-deploy` | k3s deployment manifests | 高 | k3s topology、Loki / Fluent Bit / Grafana、initContainer rollout。 |
| `usproject-deploy-template` | GitLab CI template | 高 | Math image、Cocos web image、Harbor、kubectl rollout。 |
| `admin-api` | Java admin / control plane | 中高 | Admin query、RBAC、operation control、report / audit。 |
| `nbt` | Java 21 microservice / migration base | 中高 | Service extraction、fake-first validation、AI-assisted reconstruction。 |
| `math/*-math` | Java slot math modules | 中高 | Math-module lifecycle、runtime compatibility。 |
| frontend / client / playground | Web / local support | 低到中 | 只作 release context 或 local validation，不作 backend 主線。 |

### antplay

| Repo / group | 類型 | 價值 | Top candidate topic |
| --- | --- | --- | --- |
| `antplay-slot-game-api` | Java slot runtime API | 很高 | Bet / settle / rollback、wallet transfer、bet record sharding。 |
| `antplay-slot-game-job` | Java Kafka / Quartz / report job | 高 | Projection、report rebuild、partition job、consumer lag。 |
| `antplay-slot-admin-api` | Java admin / control plane | 高 | JWT / RBAC、white IP、merchant / provider config、audit。 |
| `math-core` | Java slot math core | 很高 | Slot result contract、debug bet、RTP / symbol validation。 |
| `*-math` group | Java slot math modules | 很高 | Simulation、buy free、jackpot、feature state、contract compatibility。 |
| `antplay-push` / `antplay-push-grpc` | push / realtime candidate | 中到中高 | Delivery reliability、streaming、session / backpressure。 |
| `antplay-slot-lib` | shared library | 中高 | Runtime contract、shared model、compatibility boundary。 |
| `platform-mock` | mock / failure injection | 中 | Provider failure simulation、rollback / compensation test harness。 |
| frontend / official / bot | web / tool / notify | 低到中低 | 只作 operation context，不作 backend 主線。 |

### iwin

| Repo | 類型 | 價值 | Top candidate topic |
| --- | --- | --- | --- |
| `iwin_gameserver` | Java game server / runtime | 很高 | Wallet mutation、third-party transfer、bet / settle / rollback、log reel。 |
| `third_games_api` | Java third-party game API | 高 | Seamless wallet、provider callbacks、adapter boundary。 |
| `game_job` | Java job / projection / BI | 高 | Daily summary、backup、batch projection、partition table。 |
| `game_api` | Java player / partner API | 高 | Partner deposit / withdraw、coupon credit、money state boundary。 |
| `payment` | Java payment provider integration | 高 | Provider request / callback / query / withdraw。高價值，但不是唯一中心。 |
| `k3s-deploy` | deploy / rollout | 中高 | Gameserver phased rollout、rollback、config / observability。 |
| `app_bi` | PHP BI / admin / control plane | 中 | Report、Redis sync、admin operation。 |
| simulator / workspace / frontend | support / context | 低到中 | 可作 test harness 或 reconstruction context，不作主要 claim。 |

### ugsoft

| Repo | 類型 | 價值 | Top candidate topic |
| --- | --- | --- | --- |
| `ugsoft-connector-api` | Java provider connector / gateway | 很高 | Transfer wallet、provider callback、bet record MQ、job sync、circuit breaker。 |
| `ugsoft-admin-api` | Java admin / control plane | 高 | JWT / RBAC、white IP、RabbitMQ consumer、Quartz / report job。 |
| `ugsoft-workspace` | workspace / docs / harness | 中 | Cross-repo reconstruction、runbook、migration risk。 |
| frontend / official web | web | 低 | Operation 入口，不作 backend 主線。 |

### DevOps / primestar

| Repo | 類型 | 價值 | Top candidate topic |
| --- | --- | --- | --- |
| `kafka` | docker-compose / Kafka lab | 中高 | Topic、consumer group、offset、consumer lag learning。 |
| `openobserve` | docker-compose / observability lab | 中高 | Logs、dashboard、alert、incident training。 |
| `ci-template` | CI template | 中 | Release standardization、artifact / version governance。 |
| deploy repos | deploy context | 中 | Rollout / config / topology context；claim 必須保守。 |
| `ci-test` | sandbox | 低 | sandbox only。 |

### nick-vault

| Repo | 類型 | 價值 | Top candidate topic |
| --- | --- | --- | --- |
| `nick-vault` | personal KB / career evidence system | 高 | AI-assisted knowledge management、claim boundary、interview prep、weekly capability builder。不是公司 production evidence。 |

## Repo 清單附錄

這裡只保留大分類，避免主閱讀面被完整清單淹沒。

### 高價值

- `usproject/game-api`
- `usproject/game-job`
- `usproject/game-push`
- `usproject/usproject-deploy`
- `usproject/usproject-deploy-template`
- `antplay/antplay-slot-game-api`
- `antplay/antplay-slot-game-job`
- `antplay/antplay-slot-admin-api`
- `antplay/math-core`
- `antplay/*-math`
- `iwin/iwin_gameserver`
- `iwin/third_games_api`
- `iwin/game_job`
- `iwin/game_api`
- `iwin/payment`
- `ugsoft/ugsoft-connector-api`
- `ugsoft/ugsoft-admin-api`

### 中價值 / 視題目升級

- `usproject/admin-api`
- `usproject/nbt`
- `usproject/math/*-math`
- `usproject/usproject-workspace`
- `antplay/antplay-push`
- `antplay/antplay-push-grpc`
- `antplay/antplay-slot-lib`
- `antplay/platform-mock`
- `antplay/buffer-id`
- `iwin/k3s-deploy`
- `iwin/app_bi`
- `iwin/payment-thirdparty-simulator`
- `iwin/iwin-workspace`
- `ugsoft/ugsoft-workspace`
- `DevOps/kafka`
- `DevOps/openobserve`
- `DevOps/ci-template`
- DevOps deploy repos

### 低價值 / 暫不優先

- frontend / official web repos
- client repos
- playground / sandbox repos
- workspace repos 若本輪目標不是 KB governance / reconstruction
- DevOps 全量深掃

## 暫不建議

- 不要平均掃所有 repo。
- 不要把所有 frontend / official web 都拉進主線。
- 不要把 workspace repo 當 production service evidence。
- 不要再把 `payment` 當第一個預設深掃候選；除非要修正特定錯誤或補 callback / query 細節。
- 不要開第二排程；需要時手動指定 capability-first Step 1 即可。

## 可直接複製的後續 prompt

### Deployment / runtime packaging

```text
請依 projects/repo-capability-audit.md，先做 usproject deployment + runtime packaging chain 的 Step 1。
範圍限 usproject-deploy、usproject-deploy-template、game-api、game-web、math/*-math、client/*-client。
只做候選系統與 repo relation map，不改履歷 / 自傳 / Story / 正式 flow。
```

### Push / realtime delivery

```text
請依 projects/repo-capability-audit.md，先做 push / realtime delivery chain 的 Step 1。
範圍限 usproject/game-push、antplay/antplay-push、antplay/antplay-push-grpc。
只找 candidate systems，不深掃單條 flow，不改履歷。
```

### Slot math runtime + validation

```text
請依 projects/repo-capability-audit.md，先做 slot math runtime + validation chain 的 Step 1。
範圍限 antplay/math-core、antplay/*-math、usproject/math/*-math、usproject/game-api。
只做能力地圖與候選深掃題，不改履歷。
```
