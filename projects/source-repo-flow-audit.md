# Source Repo Flow Audit

用途：回應 Nick 要求，盤點 `/Users/nick/Git/antplay`（排除 `*-math`）、`/Users/nick/Git/iwin`、`/Users/nick/Git/ugsoft`、`/Users/nick/Git/DevOps` 下所有 project folder，對照 source git evidence 與 `nick-vault/projects` Flow Track / Career Track 狀態。

最近檢查日期：2026-05-27。

2026-05-26 AntPlay re-audit：已重新掃 `/Users/nick/Git/antplay` 下各 git repo 的 remote refs、local HEAD、Nick / `10gt12nc` commits、主要 code module 與 path history。結論是：AntPlay 目前沒有新的「必做」缺口；真正值得保留在待辦的只有 `antplay-slot-admin-api` Flow Track Step 1 / Step 2，且它是可選補強，不是通用投遞前必做。`antplay-slot-game-api`、`antplay-slot-game-job`、`*-math` 已有足夠代表 flows / consolidation；`antplay-push`、`platform-mock`、`math-core` 只作 supporting / 已收斂素材，不升級為新主線。

2026-05-26 iwin re-audit：已重新掃 `/Users/nick/Git/iwin` 下各 git repo 的 remote refs、local HEAD、Nick / `10gt12nc` commits、主要 code module、path history 與既有 `nick-vault/projects/iwin` KB。結論是：iwin 目前沒有新的「真正值得補」的 project Flow Track 缺口；`payment`、`game_api`、`game_job`、`third_games_api`、`iwin_gameserver`、`app_bi` 的代表 flows / consolidation 已足夠支撐目前 Senior Backend 投遞與面試。`payment-thirdparty-simulator` 有 Nick direct commits，但定位是 payment provider contract / callback 測試支撐，不升級成主履歷 flow；若要補，只作 payment case 的 supporting evidence。iwin 目前唯一保留的可選補強是 domain-level `iwin system map v1`，用來整理跨 repo 協作與 claim boundary，不是投遞前必做。

2026-05-26 UGSoft re-audit：已重新掃 `/Users/nick/Git/ugsoft` 下各 git repo 的 remote refs、local HEAD、Nick / `10gt12nc` commits、主要 code module、path history 與既有 `projects/ugsoft` KB。結論是：UGSoft 仍有真正值得補的 Flow Track，但範圍很收斂，不是全 repo 平均掃。第一順位維持 `ugsoft-connector-api transfer-wallet-in-out-query`，因為它已完成 Step 1 / Step 2，且 transfer in / out / query、provider adapter、request log、transaction / lookup、callback / MQ 都有高履歷與面試價值。2026-05-27 已完成該 flow Step 4；若繼續同 flow，下一步是 Step 5。第二順位是 `ugsoft-admin-api Step 1 / Step 2`，用來整理後台 control plane、白名單、RabbitMQ request / bet record consumer、Quartz report job 與 risk monitor。`official-web-v3`、`ugsoft-admin-web`、`ugsoft-workspace` 不列主待辦；workspace 只作 supporting evidence。

2026-05-26 DevOps re-audit：已重新掃 `/Users/nick/Git/DevOps/primestar` 下各 git repo 的 remote refs、local HEAD、Nick / `10gt12nc` / `arnold` commits、主要 manifests / docker-compose / CI / observability docs 與 path history。結論是：DevOps 沒有新的 Senior Backend 主履歷 Flow Track 必做缺口；`10gt12nc` / Nick author 未命中。Nick 已確認 `arnold` 是主管帳號，不是 Nick direct evidence。`antplay-docker-deploys` 的 `arnold` commits 只能作主管 / 團隊 deployment context 或 learning / supporting，不列 Nick 履歷、不升級 Flow Track 主線。`openobserve`、`kafka` 只有他人 commits，且 source 含敏感設定，僅作 learning-only，不列待辦。

## 判斷規則

- `已有`：已具備 Step 1 / Step 2 / 代表 flows，或已明確標成 supporting / interview-only。
- `缺 Flow Track 但值得補`：有 Nick / `10gt12nc` direct commits、後端 production flow 價值高，但目前只有 contribution consolidation 或尚未建立 Step 1 / Step 2。
- `可選加強`：有 domain / platform 補強價值，但不是目前 Senior 投遞前必做。
- `暫不建議`：官網、前端、workspace、mock、無 Nick direct commits、低履歷價值，除非 Nick 明確指定。

本輪只做 completeness audit 與待辦記錄，不開工單條 flow。部分 AntPlay 來源 repo fetch 失敗一次後依 KB 改用本地 refs / 本地工作樹保守判斷，未反覆重試；不寫內網 remote 細節。

## AntPlay

| Project folder | Git / code evidence | KB 狀態 | 值得深入的 flows | 標記 |
| --- | --- | --- | --- | --- |
| `antplay-bot` | git repo；未見 Nick direct commits | 無 KB | bot / ops automation，低 Senior backend 主線 | 暫不建議 |
| `antplay-push` | Nick direct commits 約 9，集中 WebSocket / Kafka listener / auth / response encryption 相關 | 無 KB | push encryption / message delivery 可作小型 supporting case，但履歷價值低於既有 big-win notification / request log async case | 可選 supporting，不放主待辦 |
| `antplay-push-grpc` | 未見 Nick direct commits | 無 KB | gRPC push delivery / retry 需另證明 production value | 暫不建議 |
| `antplay-slot-admin` | 前端 repo，未見 Nick direct commits；工作樹有既有髒檔 | 無 KB | 後台入口，不作主線 | 暫不建議 |
| `antplay-slot-admin-api` | Nick direct commits 約 195；admin / merchant auth、白名單、風控、RabbitMQ、Quartz；2026-05-26 re-audit 確認 source refs 與本機 `origin/main` 對齊 | 只有 contribution consolidation，沒有 Step 1 / Step 2 / flows | `request-log-rabbitmq-admin-consumer`、`risk-monitor-alert-rabbitmq`、`game-api-whitelist-sync`、`rtp-darkpool-risk-monitor`、`admin-auth-rbac-merchant-scope` | AntPlay 目前唯一真正值得補的 Flow Track 缺口；可選補強，不是必做 |
| `antplay-slot-game-api` | Nick direct commits 約 154；bet / settle / transfer wallet / MQ / sharding | Step 1 / Step 2 / 5 flows / consolidation 已完成 | 已有：`slot-bet-settle-rollback`、`transfer-wallet-money-in-out`、`request-log-rabbitmq-async`、`bet-record-sharding-schema-route`、`runtime-rtp-darkpool-player-control` | 已有，暫不新增 |
| `antplay-slot-game-job` | Nick direct commits 約 52；Kafka / Quartz / report projection / notification / partition | Step 1 / Step 2 / 5 flows / consolidation 已完成 | 已有：`proxy-user-data-report-projection`、`activity-accumulated-bet-voucher`、`big-win-notification`、`settle-pool-monitor-darkpool-sync`、`db-partition-job-report-routing` | 已有，暫不新增 |
| `antplay-slot-lib` | 未見 Nick direct commits | 無 KB | 若含 common SDK 才有價值，目前 evidence 不足 | 暫不建議 |
| `antplay-tg-notify` | 未見 Nick direct commits | 無 KB | Telegram notify / alert supporting | 暫不建議 |
| `antplay-tool-transfer-web` | 前端 / tool web；未見 Nick direct commits | 無 KB | transfer tool UI，除非要追 money repair 入口 | 暫不建議 |
| `buffer-id` | 未見 Nick direct commits | contribution consolidation 已完成，learning-only | ID generator / buffer id | 已收斂，暫不建議 |
| `math-core` | Nick direct commits 約 20；debugBet VO、fixedMultiBet、RTP type、symbol / jackpot contract | contribution consolidation 已完成 | 可作 `*-math` grouped claim 的 supporting evidence；`*-math` 已有 5 條代表 flow | 已收斂，不新增 Flow Track |
| `math-workspace` | Nick workspace docs commits 多，但不是 runtime service | contribution consolidation 已完成 | cross-math validation workflow | 已收斂，暫不建議 |
| `official-web` | 未見 Nick direct commits | 無 KB | 官網 | 暫不建議 |
| `official-web-v2` | 未見 Nick direct commits | 無 KB | 官網 | 暫不建議 |
| `platform-mock` | Nick direct commits 約 6，集中 bet / money_inout failure injection 與 revert | contribution consolidation 已完成 | provider failure testing / rollback supporting；不適合獨立主履歷 | 已收斂，不新增 Flow Track |
| `slot-admin` | local folder 非本輪可用 git repo | 無 KB | 無 | 暫不建議 |

## iwin

| Project folder | Git / code evidence | KB 狀態 | 值得深入的 flows | 標記 |
| --- | --- | --- | --- | --- |
| `app_bi` | Nick commits 少，且本機落後遠端；後台 / BI | Step 1 / Step 2 / 4 flows / consolidation 已完成 | 已有 control-plane / BI flows；主要作入口 | 已有，暫不新增 |
| `bi_share` | 未見 Nick direct commits | contribution consolidation negative 已完成 | legacy sharing / commission / BI，無主履歷價值 | 已收斂，暫不建議 |
| `game_api` | Nick direct commits 約 15，coupon 直接 evidence | Step 1 / Step 2 / 3 flows / consolidation 已完成 | 已有：coupon、partner deposit/withdraw、agent bonus | 已有，暫不新增 |
| `game_job` | Nick direct commits 約 45 | Step 1 / Step 2 / 5 flows / consolidation 已完成 | 已有 daily summary、Mongo backup、coin projection、payment cleaning、partition | 已有，暫不新增 |
| `iwin-workspace` | workspace docs commits 多，不是 runtime service | contribution consolidation 已完成 | cross-repo reconstruction supporting | 已收斂，暫不建議 |
| `iwin_client_unity` | client repo，未見 Nick direct commits | 無 KB | client side，不對 Backend 主線 | 暫不建議 |
| `iwin_gameserver` | Nick direct commits 約 98 | Step 1 / Step 2 / flows / consolidation 已完成 | 已有 transfer-in-out、center-http、spin settlement、bet target | 已有，暫不新增 |
| `k3s-deploy` | 未見 Nick direct commits；本機落後遠端且有既有髒檔 | Step 1 / Step 2 / 1 flow 已完成；無 contribution consolidation | 已有 `gameserver-phased-rollout` interview-only | 已有，除 JD 需要 Platform 再補 |
| `payment` | Nick direct commits 約 100 | Step 1 / Step 2 / 5 flows / consolidation 已完成；2026-05-26 已降回 provider integration 口徑 | 已有 provider callback、withdraw、provider request、manual repair、config selection | 已有，暫不新增 |
| `payment-thirdparty-simulator` | Nick direct commits 約 13，GoldenPay / NimTestPay simulator；2026-05-26 re-audit 確認含 order state、sign、callback、query、CI / deploy supporting path | 無 KB | `provider-simulator-contract-testing` 可支援 payment 測試故事，但不作主履歷，不升級 project Flow Track 主線 | supporting only，不放主待辦 |
| `shareinstall-back` | 未見 Nick direct commits | 無 KB | install attribution / tracking，價值未知 | 暫不建議 |
| `third_games_api` | Nick commits 少，主要測試 / merge；下游 evidence 在 gameserver | Step 1 / Step 2 / 4 flows / consolidation 已完成 | 已有 GSC / OneAPI / Antplay / GSC split flows | 已有，暫不新增 |

## UGSoft

| Project folder | Git / code evidence | KB 狀態 | 值得深入的 flows | 標記 |
| --- | --- | --- | --- | --- |
| `official-web-v3` | 官網，未見 Nick direct commits；2026-05-26 re-audit 後仍無 backend 主線 evidence | 無 KB | 官網 | 暫不建議 |
| `ugsoft-admin-api` | Nick direct commits 約 207；admin auth / 白名單 / report / risk / RabbitMQ / Quartz；2026-05-26 re-audit 確認近期 commits 仍集中 login、provider white IP、report job、BetRecord / RequestLog MQ | 只有 contribution consolidation，沒有 Step 1 / Step 2 / flows | `request-log-rabbitmq-admin-consumer`、`connect-bet-record-mq-ingestion`、`provider-white-ip-game-api-whitelist-sync`、`merchant-auth-rbac-token-kick`、`risk-monitor-alert-quartz`、`daily-hourly-report-job` | 缺 Flow Track 但值得補；第二順位 |
| `ugsoft-admin-web` | 前端 repo，未見 Nick direct commits；本機落後遠端 | 無 KB | 後台入口，不作主線 | 暫不建議 |
| `ugsoft-connector-api` | Nick direct commits 約 217；provider connector / transfer wallet / callback / MQ / circuit breaker；2026-05-26 re-audit 確認 transfer adapter、callback -> MQ、bet record sync job、currency / pt_day 修正都有 direct evidence | contribution consolidation + Step 1 / Step 2 已完成；`transfer-wallet-in-out-query` Step 4 已完成 | Step 2 已選：`transfer-wallet-in-out-query`、`provider-callback-bet-settle-to-mq`、`request-bet-record-mq-sync`；可選 `provider-client-login-launch-game` | 第一條 flow 已完成 Step 4；下一步可做 Step 5，或之後補第二條 callback / MQ flow |
| `ugsoft-workspace` | workspace / docs / harness；2026-05-26 re-audit 仍未見 `10gt12nc` / Nick author；Nick 已確認 `arnold` 是主管帳號，不是 Nick direct evidence | contribution consolidation 已完成 | workspace / migration runbook supporting | 已收斂，暫不建議 |

## DevOps / primestar

| Project folder | Git / code evidence | KB 狀態 | 值得深入的 flows | 標記 |
| --- | --- | --- | --- | --- |
| `primestar/antplay-api-deploy` | 未見 Nick direct commits | 無 KB | API deployment / rollout / rollback reference | learning-only / 暫不建議 |
| `primestar/antplay-docker-deploys` | 未見 `10gt12nc` / Nick commits；2026-05-26 re-audit 掃到 `arnold` commits 約 46；Nick 已確認 `arnold` 是主管帳號，不是 Nick direct evidence | 無 KB | 主管 / 團隊 k3s deployment / observability context；可輔助理解，不作 Nick Flow Track | learning-only / supporting，不放主履歷 |
| `primestar/antplay-web-deploy` | 未見 `10gt12nc` / Nick commits；少量 `arnold` web deploy domain / Dockerfile / CI commits；Nick 已確認 `arnold` 是主管帳號，不是 Nick direct evidence | 無 KB | web deploy / nginx / CI reference | 暫不建議 |
| `primestar/ci-template` | 未見 Nick direct commits | 無 KB | CI template | 暫不建議 |
| `primestar/ci-test` | 未見 Nick direct commits | 無 KB | CI sandbox | 暫不建議 |
| `primestar/kafka` | 未見 Nick / `arnold` direct commits；他人 commits，local learning docker-compose | 無 KB | Kafka KRaft / Kafdrop local learning-only | learning-only，不放待辦 |
| `primestar/openobserve` | 未見 Nick / `arnold` direct commits；他人 commits，local Fluent Bit / OpenObserve docs，source 含敏感設定不可搬入 vault | 無 KB | logs / Fluent Bit / OpenObserve learning-only | learning-only，不放待辦 |

## 建議收斂

全域候選與邊界如下；不代表必須全部開工。iwin re-audit 後，iwin 沒有新的 project Flow Track 主待辦：

1. `ugsoft-connector-api`：最值得補 Flow Track。2026-05-26 re-audit 後仍維持第一順位；Step 1 / Step 2 已完成，且 `transfer-wallet-in-out-query Step 4` 已於 2026-05-27 完成。它有真實 direct commits、交易 / provider / callback / MQ / transfer wallet，能補非 iwin 廣度；下一步可做同 flow Step 5。
2. `ugsoft-admin-api`：值得補 Flow Track，但優先低於 connector。2026-05-26 re-audit 後維持第二順位，可補 admin control plane / RabbitMQ / Quartz / risk monitor。若要做，先做 Step 1，再 Step 2，不直接跳單條 flow。
3. `antplay-slot-admin-api`：AntPlay 目前唯一真正值得補的 Flow Track 缺口；但目前已有 AntPlay game-api / game-job / `*-math` 主力 evidence，所以它是可選補強，不是投遞前必做。若 Nick 要補後台 / 風控 / admin control plane，先做 Step 1 / Step 2。
4. `payment-thirdparty-simulator`：2026-05-26 iwin re-audit 後降為 payment provider contract / callback 測試 supporting evidence；不作主線、不放主履歷、不列 project Flow Track 必做。
5. `antplay-docker-deploys`：Nick 已確認 `arnold` 是主管帳號，不是 Nick direct evidence；降為主管 / 團隊 deployment context 與 learning / supporting，不作 Nick Flow Track、不放主履歷。`openobserve` / `kafka` 經 re-audit 後降為 learning-only。

目前不建議：

- 官網、前端、workspace、bot、notify、tool web、mock、無 Nick direct commits repo。
- `antplay-push`、`platform-mock`、`math-core` 單獨開 Flow Track：都有 supporting evidence，但不比既有 game-api / game-job / `*-math` 代表 flows 更值得；除非 Nick 明確指定特定職缺需要 push / testing / math-core contract。
- `DevOps` repo 平均開 Flow Track：目前缺 `10gt12nc` / Nick direct evidence；Nick 已確認 `arnold` 是主管帳號，因此 `arnold` commits 只能作主管 / 團隊 context 或 learning-only，不作 Nick 履歷 claim。`openobserve` / `kafka` 是 learning-only。
- 對已完成 Step 1 / Step 2 / 代表 flows 的 iwin、AntPlay game-api、AntPlay game-job、`*-math` 反覆重做。
- 因「全掃焦慮」把所有 repo 都排成必做 flow。
