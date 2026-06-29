# Repo Capability Audit

日期：2026-06-29

範圍：

```text
/Users/nick/Git/usproject
/Users/nick/Git/antplay
/Users/nick/Git/iwin
/Users/nick/Git/ugsoft
/Users/nick/Git/DevOps
/Users/nick/Git/nick/nick-vault
```

## 定位

這份是 repo-level capability audit，不是履歷 claim、不是 Flow Step、不是完整 code deep dive。

本輪目的不是證明 Nick 做過什麼，而是重新校正：

```text
哪些 repo / system / module 對 Senior Java Backend / Platform Backend 能力最有學習價值？
```

本輪刻意不預設 `payment` 最重要，也不把履歷、自傳、三個 Story 當中心。

## 掃描方式

本輪只做 repo-level 輕量盤點：

- 掃 `/Users/nick/Git` 下指定 domain 的 Git repo。
- 看 repo marker，例如 `pom.xml`、`package.json`、`Dockerfile`、`docker-compose.yml`、`README.md`。
- 對照現有 `projects/` KB、README、Step 文件與 contribution consolidation。
- 不深掃單條 flow。
- 不查 path-specific git history。
- 不更新 `04 / 05 / 08 / 17 / 19`。
- 不新增正式履歷 claim。

證據層級：`repo-level / marker-backed / KB-backed`。若要升級成 flow evidence，仍需另做 Step 1 / Step 2 / Step 3。

## 能力評分軸

價值分級看 transferable capability，不看 Payment 優先：

- Transaction / Consistency
- MQ / Event / Projection
- Wallet / Money Flow
- Provider / Integration
- Runtime / Thread / Push / Concurrency
- DB / Partition / Performance
- Redis / Cache / Lock
- Observability / Incident
- Deployment / Rollout
- Security / Auth
- Legacy / Refactor
- Architecture / Service Boundary
- Slot Math / RTP / Result Contract
- AI-assisted Reconstruction / Local Validation

## Repo 清單與類型

### usproject

| Repo | 類型 | 價值 | 理由 |
| --- | --- | --- | --- |
| `game-api` | Java game runtime API | 高 | Slot game runtime、math jar loading、game init / bet、token / session、加解密、本地測試；適合學 runtime boundary、API contract、game flow。 |
| `game-job` | Java job / report / sync candidate | 高 | README 薄，但 service 型態對應 job、report、settled bet sync；適合找 batch、retry、projection、rebuild。 |
| `game-push` | Java WebSocket / STOMP push service | 高 | README 明確描述 client handshake、session mapping、Kafka `push_user` consumer、push to frontend；適合學 async push、session lifecycle、backpressure、observability。 |
| `admin-api` | Java admin / control plane API | 中高 | 有 public single bet record API 文件；適合找 admin query、RBAC、operation control、report / audit。 |
| `nbt` | Java 21 microservice / migration base | 中高 | active migration / local playable / fake-first service；適合學 service extraction、AI-assisted reconstruction、validation harness，但目前不是 production claim。 |
| `usproject-deploy` | k3s deployment manifests | 高 | README 明確描述 k3s、Loki、Fluent Bit、Grafana、game-api math initContainers、game-web static initContainers、rollout gate；Platform / rollout / observability 價值高。 |
| `usproject-deploy-template` | GitLab CI template | 高 | math image / Cocos web image / Harbor / kubectl set image / rollout；適合學 release pipeline、artifact boundary、prod gate。 |
| `game-web` | nginx shell / static game hosting | 中 | 對 backend 非主線，但對 rollout、multi-game static package、service routing 有架構價值。 |
| `admin-web` | Vue admin frontend | 低 | 可作 admin operation 入口，不作 backend 主線。 |
| `client/*-client` | Cocos web clients | 低 | 對 backend 主線低；只在 game-web / deploy / release gate 需要時作 context。 |
| `math/*-math` | Java slot math modules | 中高 | 與 `game-api` runtime math loading、`usproject-deploy-template` math image pipeline 相連；若要學 math-module lifecycle 有價值。 |
| `usproject-workspace` | workspace / docs / orchestration | 中 | 可學 repo inventory、service relationship、AI governance；不作 production service claim。 |
| `nbt-playground` | local playground | 中低 | 支援 migration / validation；不作主線。 |

### antplay

| Repo | 類型 | 價值 | 理由 |
| --- | --- | --- | --- |
| `antplay-slot-game-api` | Java slot runtime API | 高 | Bet / settle / rollback、wallet transfer、request log、bet record sharding、runtime RTP / player control；Senior Backend 核心能力密集。 |
| `antplay-slot-game-job` | Java job / Kafka / Quartz / report | 高 | Kafka consumer、Quartz job、projection、report rebuild、partition job；Platform / event / batch / observability 價值高。 |
| `antplay-slot-admin-api` | Java admin / control plane | 高 | JWT / RBAC、white IP、merchant / provider config、report query、request log consumer；Security / control plane / audit 價值高。 |
| `math-core` | Java slot math core | 很高 | Slot math contract、debug bet、RTP / symbol / result contract；差異化最高，不應被 Payment 壓過。 |
| `*-math` group | Java slot math modules | 很高 | 多款 math module、RTP simulation、buy free、jackpot、feature state；適合學 domain validation、contract compatibility。 |
| `antplay-push` | Java push service | 中高 | push / realtime / delivery 方向；目前 KB 不深，值得作 runtime / backpressure candidate。 |
| `antplay-push-grpc` | gRPC push candidate | 中 | 若有 proto / delivery / streaming 可升級；目前需後續確認。 |
| `antplay-slot-lib` | Java shared library | 中高 | 可能支撐 slot runtime contract；若含 shared state / API model，適合學 boundary / compatibility。 |
| `buffer-id` | Java ID generator candidate | 中 | 若是 ID generator，可學 distributed ID / collision / ordering；目前 Nick direct evidence 不足。 |
| `platform-mock` | Java mock / failure injection | 中 | Provider failure / rollback / compensation testing supporting，適合學 failure simulation。 |
| `math-workspace` | workspace / validation workflow | 中 | 可學 cross-math validation、GDD / KB / result verification；不是 production service。 |
| `antplay-slot-admin` | PHP / Vue admin frontend | 低 | operation 入口，不作 backend 主線。 |
| `antplay-bot` / `antplay-tg-notify` | bot / notify | 中低 | 可學 notification / ops tooling，但不是主要 Senior Backend path。 |
| `official-web*` / tool web | frontend / official site | 低 | 不作 backend capability 主線。 |

### iwin

| Repo | 類型 | 價值 | 理由 |
| --- | --- | --- | --- |
| `iwin_gameserver` | Java game server / runtime | 很高 | Wallet mutation、third-party transfer、bet / settle / rollback、log reel、game runtime；比 Payment 更接近 money correctness 核心。 |
| `third_games_api` | Java third-party game API | 高 | Seamless wallet、provider callback、bet / settle / rollback、adapter boundary；Provider integration 但不限 Payment。 |
| `game_job` | Java job / BI / projection | 高 | Daily summary、backup、batch projection、partition table；適合學 report correctness、rebuild、batch risk。 |
| `game_api` | Java player / partner API | 高 | Coupon credit grant、partner deposit / withdraw、agent bonus；適合 API / money flow / state boundary。 |
| `payment` | Java payment provider integration | 高 | Provider request / callback / query / withdraw、sign、timeout unknown；高價值但只是其中一塊，不應獨占中心。 |
| `k3s-deploy` | deploy / rollout | 中高 | Rollout / rollback / config / observability interview value；Platform 加分。 |
| `app_bi` | PHP BI / admin / control plane | 中 | Control plane / report / Redis sync / admin operation；可輔助 owner story。 |
| `bi_share` | legacy PHP / BI / share | 中低 | legacy / BI context；Nick direct evidence 弱，先 supporting。 |
| `payment-thirdparty-simulator` | Java simulator | 中 | Provider contract / callback test supporting；可學 test harness，不作主線。 |
| `iwin-workspace` | workspace / cross-repo reconstruction | 中 | Legacy reconstruction / KB governance supporting；不是 production service。 |
| `iwin_client_unity` | Unity client | 低 | Backend 主線低。 |
| `shareinstall-back` | PHP tracking / install attribution | 中低 | 可能有 attribution / reward flow；需另確認價值。 |

### ugsoft

| Repo | 類型 | 價值 | 理由 |
| --- | --- | --- | --- |
| `ugsoft-connector-api` | Java provider connector / gateway | 很高 | Provider adapter、transfer wallet、callback、request / bet record MQ、job sync、circuit breaker；Platform Backend 很核心。 |
| `ugsoft-admin-api` | Java admin / control plane | 高 | JWT / RBAC、provider white IP、request / bet record consumer、Quartz / report job；Security / control plane / async ingestion。 |
| `ugsoft-workspace` | workspace / docs / harness | 中 | Cross-repo system reconstruction、runbook、migration risk；不是 service claim。 |
| `ugsoft-admin-web` | Vue admin frontend | 低 | operation 入口。 |
| `official-web-v3` | official frontend | 低 | Backend 能力價值低。 |

### DevOps / primestar

| Repo | 類型 | 價值 | 理由 |
| --- | --- | --- | --- |
| `kafka` | docker-compose / Kafka learning env | 中高 | 對 Kafka / consumer group / topic / local validation 有學習價值；目前不作 Nick production evidence。 |
| `openobserve` | docker-compose / observability env | 中高 | Observability / logs / dashboards / local validation 價值高；目前不作 direct evidence。 |
| `antplay-api-deploy` | deploy manifest candidate | 中 | API rollout / config / environment context；需確認內容。 |
| `antplay-docker-deploys` | deployment context | 中 | Team deployment context；`arnold` 不是 Nick，不能作 direct claim。 |
| `antplay-web-deploy` | web deploy | 中低 | 前端 deploy context。 |
| `ci-template` | CI template | 中 | 可學 CI standardization / release process；direct evidence 需另查。 |
| `ci-test` | CI sandbox | 低 | sandbox / test only。 |

### nick-vault

| Repo | 類型 | 價值 | 理由 |
| --- | --- | --- | --- |
| `nick-vault` | personal KB / career evidence system | 高 | 可學 AI-assisted knowledge management、claim boundary、interview prep、weekly capability builder；不是公司 production evidence。 |

## 高價值 repo 的 Top candidate topics

### usproject

| Repo | Candidate topic | Senior / Platform 能力價值 |
| --- | --- | --- |
| `game-api` | Game entry / token / session / secure API boundary | API design、auth/session、game runtime boundary、local debug vs production security。 |
| `game-api` + `math/*` + `usproject-deploy` | Runtime math jar loading via external math modules | Plugin/runtime extension、artifact boundary、compatibility、release gate。 |
| `game-job` | Bet / report / settled data sync job | Batch、retry、projection、rebuild、job idempotency。 |
| `game-push` | Kafka `push_user` -> WebSocket/STOMP push | Async delivery、session lifecycle、backpressure、push observability。 |
| `usproject-deploy` | k3s service topology + Loki / Fluent Bit / Grafana | Platform observability、rollout readiness、service ownership。 |
| `usproject-deploy-template` | Math/client image CI + initContainer rollout | Release engineering、versioning、artifact isolation、prod gate。 |
| `nbt` | Java 21 fake-first microservice migration | Service extraction、local validation, AI-assisted reconstruction, migration boundary。 |

### antplay

| Repo | Candidate topic | Senior / Platform 能力價值 |
| --- | --- | --- |
| `antplay-slot-game-api` | Bet / settle / rollback + wallet transfer | Money correctness、state transition、idempotency、rollback。 |
| `antplay-slot-game-api` | Bet record sharding / schema routing | High-traffic data、routing correctness、partition governance。 |
| `antplay-slot-game-job` | Kafka / Quartz projection and report rebuild | Event-driven projection、consumer lag、replay / rebuild、batch safety。 |
| `antplay-slot-admin-api` | White IP / RBAC / merchant control plane | Security、control plane、runtime config consistency。 |
| `math-core` + `*-math` | Slot result contract / RTP validation | Domain contract、simulation validation、compatibility。 |
| `antplay-push` | Push runtime / delivery reliability | Threading、realtime delivery、observability。 |
| `platform-mock` | Provider failure injection | Failure simulation、test harness、compensation validation。 |

### iwin

| Repo | Candidate topic | Senior / Platform 能力價值 |
| --- | --- | --- |
| `iwin_gameserver` | Third-party transfer in/out + wallet mutation | Core money flow、transaction boundary、provider wallet integration。 |
| `iwin_gameserver` | Game spin / settlement / log reel | Runtime state、bet log projection、audit trail。 |
| `third_games_api` | Seamless wallet bet / settle / rollback callbacks | Provider adapter, idempotency, rollback, out-of-order handling。 |
| `game_job` | Daily game data summary / backup / partition | Batch correctness、data reconciliation、report source of truth。 |
| `game_api` | Partner deposit / withdraw / coupon credit | API state machine、money flow, external contract。 |
| `k3s-deploy` | Gameserver phased rollout | Rollout / rollback, release risk, observability。 |
| `payment` | Provider request / callback / query / withdraw | Trust boundary、timeout unknown、signature、order state machine。 |

### ugsoft

| Repo | Candidate topic | Senior / Platform 能力價值 |
| --- | --- | --- |
| `ugsoft-connector-api` | Transfer wallet in/out/query | Provider wallet boundary、idempotency、partial success、query fallback。 |
| `ugsoft-connector-api` | Provider callback -> bet record / MQ | Event ingestion、source of truth、consumer idempotency。 |
| `ugsoft-connector-api` | Job-driven bet record sync / late data | Reconciliation-like thinking、watermark、replay / compensation。 |
| `ugsoft-admin-api` | RequestLog / BetRecord RabbitMQ consumer | Async audit log、consumer retry、DLQ / lag thinking。 |
| `ugsoft-admin-api` | White IP / provider control plane | AuthZ/control plane、config propagation、runtime safety。 |

### DevOps / primestar

| Repo | Candidate topic | Senior / Platform 能力價值 |
| --- | --- | --- |
| `kafka` | Local Kafka topology / topic / consumer group lab | MQ fundamentals、consumer lag、offset / retry learning; not production claim。 |
| `openobserve` | Observability stack lab | Logs / metrics / dashboard / alert thinking; can support incident training。 |
| `ci-template` | CI reuse and release guardrails | Delivery standardization, pipeline safety, artifact/version governance。 |
| `antplay-api-deploy` / `antplay-docker-deploys` | Deploy topology / rollout | Platform context, but claim boundary must be conservative。 |

## 目前 KB 是否疑似偏 Payment / 履歷導向

結論：有，主要偏在閱讀入口與求職路徑，不是資料本身只有 Payment。

### 偏差來源

1. `README.md` 的投遞前衝刺讀法把 Payment 放在主力 flows 第一位，容易讓後續 AI 把 Payment 當中心。
2. `05 / 08 / 17 / 19` 是 Career Track，天然會偏向能保守寫、能面試講、能快速建立市場定位的素材。
3. Payment 的 provider request / callback / query 很容易包裝成 90 秒故事，所以被反覆使用。
4. `company-system-deep-dive` 暫停後，缺一份獨立的 capability-first map，導致「學能力」時也容易回到「履歷最強 case」。

### 修正觀點

Payment 仍然高價值，但它只是以下 capability 的一個案例：

- Trust boundary
- Signature / callback
- Timeout unknown
- Order state machine
- Query fallback

更深或更稀缺的能力可能來自：

- `iwin_gameserver` / `ugsoft-connector-api`：wallet / provider / bet-settle money correctness。
- `antplay-slot-game-api`：slot runtime + wallet + sharding。
- `antplay-slot-game-job` / `game_job`：MQ / projection / batch / report correctness。
- `math-core` / `*-math`：slot math contract / RTP validation。
- `usproject-deploy` / `usproject-deploy-template`：artifact boundary / initContainer rollout / observability。
- `game-push` / `antplay-push`：push runtime / async delivery / session lifecycle。

## 最值得下一步深掃的 3 個候選

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

- 這是目前 KB 最缺的一塊，且不是 Payment。
- 能學 Platform Backend 很實用的 artifact boundary：math jar / static client package 不 bake 進主 image，而是用 initContainer 注入。
- 可延伸到 rollout、prod gate、observability、release risk、multi-game service topology。

保守邊界：

- 目前不寫成 Nick production owner。
- 先作 capability deep dive / architecture learning。

### 2. Push / realtime delivery chain

範圍：

```text
usproject/game-push
antplay/antplay-push
antplay/antplay-push-grpc
```

為什麼值得：

- 現有 KB 對 push / realtime / session lifecycle 幾乎沒有主線。
- 可補 Thread Pool、WebSocket/STOMP、Kafka -> push、session cleanup、backpressure、observability。
- 對 Platform Backend / runtime stability 很有價值。

保守邊界：

- 先做 repo-level Step 1 / Step 2，不直接寫履歷。

### 3. Slot math runtime + validation chain

範圍：

```text
antplay/math-core
antplay/*-math
usproject/math/*-math
usproject/game-api
```

為什麼值得：

- 差異化最高，且最不該被 Payment 壓過。
- 能學 result contract、RTP validation、runtime loading、compatibility、simulation、game domain correctness。
- 適合建立「Backend + domain validation」的長期能力，不只是求職故事。

保守邊界：

- 不寫成完整 math owner。
- 只講 module maintenance / validation / contract understanding。

## 暫不建議優先深掃

- 所有 frontend / official web：只作操作入口或 release context。
- `payment`：不是沒價值，而是已經整理很多；除非要修正特定錯誤，不應再當第一個深掃候選。
- workspace repo：除非目標是 AI workflow / KB governance，不應當 production service。
- DevOps repo 全量深掃：先挑 Kafka / OpenObserve / deploy topology 的 capability topic，不平均掃全部。

## 建議後續用法

若 Nick 要繼續，下一步不是「開新排程」，而是手動指定一個 capability-first audit / Step 1：

```text
請依 projects/repo-capability-audit.md，先做 usproject deployment + runtime packaging chain 的 Step 1。
只做候選系統與 repo relation map，不改履歷 / 自傳 / Story / 正式 flow。
```

或：

```text
請依 projects/repo-capability-audit.md，先做 push / realtime delivery chain 的 Step 1。
範圍限 usproject/game-push、antplay/antplay-push、antplay/antplay-push-grpc。
只找 candidate systems，不深掃單條 flow。
```

或：

```text
請依 projects/repo-capability-audit.md，先做 slot math runtime + validation chain 的 Step 1。
範圍限 antplay/math-core、antplay/*-math、usproject/math/*-math、usproject/game-api。
只做能力地圖與候選深掃題，不改履歷。
```
