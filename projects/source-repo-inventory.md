# Source Repo Inventory

用途: 記錄 Nick 本機可參考的來源 Git repo，避免每次 project / flow 任務都重新問路徑。

這份不是 evidence，也不是履歷 claim。做任何 flow 前仍要重讀 KB、檢查當下 branch / log / code path，不能只靠本清單。

最新 completeness audit：

- `projects/source-repo-flow-audit.md`：盤點 AntPlay（排除 `*-math`）、iwin、UGSoft、DevOps / primestar 所有 project folders，標記已有 KB / 缺 Flow Track / 值得補 / 暫不建議。這份是下一輪補 Step 1 / Step 2 的入口，不代表已授權逐一開工。
- 2026-05-26 iwin re-audit：重新掃 `/Users/nick/Git/iwin` 各 repo 的 remote refs、Nick / `10gt12nc` commits、主要 module / path history 與既有 KB 後，結論是 iwin 沒有新的 project Flow Track 必做缺口；`payment-thirdparty-simulator` 降為 payment provider contract / callback 測試 supporting evidence，不列主待辦。
- 2026-05-28 iwin system map v1：已新增 `projects/iwin/architecture-map.md`、`projects/iwin/integration-map.md`、`projects/iwin/career-interview.md`，把 `payment`、`game_api`、`game_job`、`third_games_api`、`iwin_gameserver`、`app_bi`、`bi_share`、`k3s-deploy` 與 supporting repos 的協作關係收成 domain-level map。這是架構視角補強，不新增 Flow Track 主待辦。
- 2026-05-28 AntPlay / UGSoft system maps v1：已新增 `projects/antplay/architecture-map.md`、`projects/antplay/integration-map.md`、`projects/antplay/career-interview.md` 與 `projects/ugsoft/architecture-map.md`、`projects/ugsoft/integration-map.md`、`projects/ugsoft/career-interview.md`。這些是 domain-level 架構視角補強，不新增 Flow Track 主待辦。
- 2026-05-26 UGSoft re-audit / 2026-05-27 flow closure + refresh：重新掃 `/Users/nick/Git/ugsoft` 各 repo 的 remote refs、Nick / `10gt12nc` commits、主要 module / path history 與既有 KB 後，結論是 UGSoft Flow Track 已完成目前兩個高價值方向的本批收口；`ugsoft-connector-api` 本批三條代表 flow 已完成 Step 5 並已完成 `contribution claim consolidation refresh`。`ugsoft-admin-api Step 1 / Step 2` 已完成，`connect-bet-record-mq-ingestion Step 5`、`request-log-rabbitmq-admin-consumer Step 5`、`game-api-provider-white-ip-control-plane Step 5` 均已完成，且 `contribution claim consolidation refresh` 已完成；官網、前端、workspace 不列主待辦。
- 2026-05-26 DevOps re-audit：重新掃 `/Users/nick/Git/DevOps/primestar` 各 repo 的 remote refs、Nick / `10gt12nc` / `arnold` commits、manifests / docker-compose / CI / observability docs 與 path history 後，結論是 DevOps 沒有 Senior Backend 主履歷 Flow Track 必做缺口。Nick 已確認 `arnold` 是主管帳號，不是 Nick direct evidence；`antplay-docker-deploys` 只能作主管 / 團隊 deployment context 或 learning / supporting，不作 Nick 履歷 claim；`openobserve` / `kafka` 是 learning-only。
- 2026-06-22 usproject source triage：已新增 `projects/usproject/README.md` 與 `projects/usproject/ugsoft-apidoc-source-notes.md`。`/Users/nick/Git/antplay/ugsoft-apidoc` 被定位為 `/Users/nick/Git/usproject` 的 external API contract / supporting source，可用於理解 wallet mode、game entry、bet / settle / cancel、transfer order、bonus / free spin、signature、language / currency 邊界；不作 Nick direct evidence、不更新 `05 / 08`。

## 使用規則

- 只能讀這些公司 / 來源 repo，不能改。
- 真正長期整理內容只放 `nick-vault/projects/`。
- `*-workspace` 通常是維護 / KB / 工作區參考，不當成業務 code 主體；若有 contribution consolidation，也只能支撐工作方法與 cross-repo reconstruction，不反向包裝成子 repo 業務開發。
- `*-math` 先群組記錄；除非 Nick 指定，不逐一展開。
- 每次要寫履歷或自傳前，必須另補 Nick 本人 evidence，例如 MR / ticket / commit / production issue / 本人確認。

## iwin

來源路徑:

```text
/Users/nick/Git/iwin
```

Git repo:

- `app_bi`
- `bi_share`
- `game_api`
- `game_job`
- `iwin-workspace`
- `iwin_client_unity`
- `iwin_gameserver`
- `k3s-deploy`
- `payment`
- `payment-thirdparty-simulator`
- `shareinstall-back`
- `third_games_api`

初步用途:

- 核心後端候選: `payment`、`game_api`、`game_job`、`iwin_gameserver`、`third_games_api`
- 後台 / BI / control plane: `app_bi`、`bi_share`
- client / deploy / simulator / workspace: 依 flow 需要作參考；`iwin-workspace` 已完成 rolling consolidation，不新增 standalone 正式履歷主成果

整理狀態:

- 2026-05-26 re-audit：`payment`、`game_api`、`game_job`、`third_games_api`、`iwin_gameserver`、`app_bi` 代表 flows / contribution consolidation 已足夠支撐目前 Senior Backend 投遞與面試，沒有新的 iwin project Flow Track 必做缺口。
- `payment-thirdparty-simulator`: 有 Nick / `10gt12nc` direct commits，code 涵蓋 provider sign、order state、query、callback、GoldenPay / NimTestPay simulator 與 CI / deploy supporting path；定位只作 `payment` provider contract / callback 測試 supporting evidence，不新增主履歷 claim，不列 project Flow Track 主待辦。
- `iwin system map v1` 已完成：整理跨 repo collaboration / architecture / integration / claim boundary；後續除非 Nick 要 Level 3 架構審計或 JD-specific system design，不列投遞前必做。

## ugsoft

來源路徑:

```text
/Users/nick/Git/ugsoft
```

Git repo:

- `official-web-v3`
- `ugsoft-admin-api`
- `ugsoft-admin-web`
- `ugsoft-connector-api`
- `ugsoft-workspace`

初步用途:

- 核心後端候選: `ugsoft-admin-api`、`ugsoft-connector-api`
- 前端 / 官網: `ugsoft-admin-web`、`official-web-v3`
- workspace: `ugsoft-workspace`

整理狀態:

- `ugsoft-admin-api`: contribution claim consolidation 已完成 / refreshed；Step 1 / Step 2 已完成 / 2026-05-27，`connect-bet-record-mq-ingestion Step 5` 已完成，`request-log-rabbitmq-admin-consumer Step 5` 已完成，`game-api-provider-white-ip-control-plane Step 5` 已完成，且三條 flow 已回填 project-level claim。Nick / `10gt12nc` 有大量 direct commits，可保守作後台 API / control plane、RabbitMQ / Quartz / report job 類履歷素材；不寫完整 UG 平台或 provider gateway owner。目前 admin-api 已收斂，沒有預設下一步。
- `ugsoft-connector-api`: contribution claim consolidation 已完成 / refreshed；Flow Track Step 1 / Step 2 已完成，本批三條代表 flow `transfer-wallet-in-out-query`、`provider-callback-bet-settle-to-mq`、`request-bet-record-mq-sync` 均已完成 Step 5。Nick / `10gt12nc` 有大量 direct commits，可保守作 AntPlay / DerPlay provider connector、transfer wallet、callback、request / bet record MQ、job sync、分表與 provider reliability 類履歷素材；不寫完整 connector architecture / wallet / reconciliation owner。目前 connector 已收斂，沒有預設下一步。
- `ugsoft-workspace`: contribution claim consolidation 已完成 / rolling。這是 workspace / docs / harness / runbook repo，只支撐 cross-repo system reconstruction、工程規範、local / deploy harness 與 migration / release 風險整理；不放 standalone 正式履歷主成果，不反向包裝成 `ugsoft-admin-api` / `ugsoft-connector-api` service code。
- 2026-05-28 completeness audit / re-audit：`ugsoft-connector-api` 已補 Step 1 / Step 2。2026-05-27 三條代表 flow `transfer-wallet-in-out-query`、`provider-callback-bet-settle-to-mq`、`request-bet-record-mq-sync` 均已完成 Step 5，且已完成 project-level contribution refresh。`ugsoft-admin-api` 也已完成 Step 1 / Step 2，本批代表 flows 已選定；第一條 `connect-bet-record-mq-ingestion` 已完成 Step 5，第二條 `request-log-rabbitmq-admin-consumer` 已完成 Step 5，第三條 `game-api-provider-white-ip-control-plane` 已完成 Step 5，且已完成 project-level contribution refresh。`antplay-slot-admin-api` Step 1 / Step 2、`request-log-rabbitmq-admin-consumer Step 5` 與 `game-api-whitelist-sync Step 5` 已完成，且已完成 project-level contribution refresh；`official-web-v3`、`ugsoft-admin-web`、`ugsoft-workspace` 不列主待辦。
- `UGSoft system map v1` 已完成：整理 connector gateway / admin control plane / workspace supporting 的 collaboration / integration / claim boundary；後續除非 Nick 要 Level 3 架構審計或 JD-specific system design，不列投遞前必做。

## antplay

來源路徑:

```text
/Users/nick/Git/antplay
```

Git repo:

- `antplay-bot`
- `antplay-push`
- `antplay-push-grpc`
- `antplay-slot-admin`
- `antplay-slot-admin-api`
- `antplay-slot-game-api`
- `antplay-slot-game-job`
- `antplay-slot-lib`
- `antplay-tg-notify`
- `antplay-tool-transfer-web`
- `buffer-id`
- `math-core`
- `math-workspace`
- `official-web`
- `official-web-v2`
- `platform-mock`
- `*-math`

初步用途:

- 核心後端候選: `antplay-slot-game-api`、`antplay-slot-game-job`、`antplay-slot-admin-api`
- 共用 library / math: `antplay-slot-lib`、`math-core`、`*-math`
- push / bot / notify: `antplay-push`、`antplay-push-grpc`、`antplay-bot`、`antplay-tg-notify`
- 前端 / 工具 / mock / workspace: 依 flow 需要作參考

整理狀態:

- `antplay-slot-admin-api`: contribution claim consolidation 已完成 / refreshed，Step 1 / Step 2 已完成 / 2026-05-28，第一條代表 flow `request-log-rabbitmq-admin-consumer Step 5` 已完成，第二條代表 flow `game-api-whitelist-sync Step 5` 已完成。Nick / `10gt12nc` 有大量 direct commits，可保守作後台 API / 商戶控制面、JWT / RBAC、商戶 / Game API 白名單、超級代理、玩家 / 投注 / request log / 報表查詢、RTP / 暗池 / 活動風控監控、RabbitMQ request log / 風控通知與 Quartz job 類履歷素材；不寫完整 AntPlay slot platform、game runtime、wallet / reconciliation 或完整 RabbitMQ / security platform owner。
- `antplay-slot-game-api`: contribution claim consolidation 已完成 / rolling。Nick / `10gt12nc` 有大量 direct commits，可保守作遊戲 API runtime、game init、bet / settle / rollback、轉帳錢包、bet record / request log 分表、RabbitMQ request log、Quartz 補通知、RTP / dark pool / player control 類履歷素材；不寫完整 AntPlay slot platform、完整遊戲數學 / RTP 策略、wallet / ledger / reconciliation 或完整 RabbitMQ / Kafka owner。
- `antplay-slot-game-job`: contribution claim consolidation 已完成 / refreshed；Flow Track Step 1 / Step 2 與五條代表 flows Step 5 已完成 / 2026-05-25。Nick / `10gt12nc` 有 direct commits，可保守作 Kafka / Quartz job、代理玩家報表 projection、activity accumulated bet supporting flow、big-win notification、report currency / key 修正與 db partition / job config 類履歷素材；不寫完整 Kafka event platform、完整 settle pool / risk / jackpot owner 或完整 BI / report platform owner。五條代表 flows 已回填 project-level claim 與 rolling resume package。
- `math-core`: contribution claim consolidation 已完成 / rolling。Nick / `10gt12nc` 有 direct commits，可保守作 slot math core contract、debugBet VO、currency / fixedMultiBet、RTP type、jackpot / symbol 類履歷素材；不寫完整 slot math framework、完整 RTP 策略或完整 simulator owner。
- `*-math`: contribution claim consolidation 已完成 / rolling / grouped。71 個 `*-math` repo 中本輪掃到 49 個有 Nick / `10gt12nc` direct commits；強 evidence 是 `sph-math`、`spn-math`、`sfm-math`、`setl-math`、`sdt-math`、`slc-math`。可保守作多個 slot math module 維護與驗證素材；不寫主導全部 math module 或完整遊戲數學 owner。
- `math-workspace`: contribution claim consolidation 已完成 / rolling。Nick 有 workspace KB / docs commits，只支撐 cross-math module reconstruction / validation workflow；不作 standalone 正式履歷主成果。
- `platform-mock`: contribution claim consolidation 已完成 / rolling。Nick / `10gt12nc` 有局部 bet / money_inout failure injection commits，只作 provider rollback / compensation 測試 supporting evidence；不作正式主成果。
- `buffer-id`: contribution claim consolidation 已完成 / rolling。未掃到 Nick / `10gt12nc` direct commits，只作 ID generator learning-only。
- 2026-05-28 completeness audit / AntPlay re-audit：`antplay-slot-admin-api` Career Track 已完成 refreshed 收口，Flow Track Step 1 / Step 2、第一條代表 flow `request-log-rabbitmq-admin-consumer Step 5` 與第二條代表 flow `game-api-whitelist-sync Step 5` 也已完成。它屬於後台 / 風控 / admin control plane 廣度補強，不是投遞前必做。`antplay-slot-game-api`、`antplay-slot-game-job`、`*-math` 代表 flows 已足夠，暫不建議重做；`antplay-push`、`platform-mock`、`math-core` 只作 supporting / 已收斂素材，除非 Nick 明確指定。
- `AntPlay system map v1` 已完成：整理 runtime / job / admin control plane / math contract / supporting repos 的 collaboration / integration / claim boundary；後續除非 Nick 要 Level 3 架構審計或 JD-specific system design，不列投遞前必做。

## usproject

來源路徑:

```text
/Users/nick/Git/usproject
```

相關文件 mirror:

```text
/Users/nick/Git/antplay/ugsoft-apidoc
```

Git repo:

- `admin-api`
- `admin-web`
- `game-api`
- `game-job`
- `game-push`
- `game-web`
- `nbt`
- `nbt-playground`
- `usproject-deploy`
- `usproject-deploy-template`
- `usproject-workspace`

初步用途:

- 核心後端候選: `game-api`、`game-job`、`admin-api`
- 前端 / 操作入口: `game-web`、`admin-web`
- supporting / deploy / workspace: `game-push`、`nbt`、`nbt-playground`、`usproject-deploy`、`usproject-deploy-template`、`usproject-workspace`
- API contract mirror: `ugsoft-apidoc` 可作 wallet / game API contract supporting source

整理狀態:

- 2026-06-22 `ugsoft-apidoc Step 1 + Step 2` 已完成：`projects/usproject/ugsoft-apidoc-source-notes.md` 已整理 source safety boundary 與 usproject repo 對應。這只是 source triage，不是 code-backed Flow Track、不代表已掃 usproject 最新 code，也不新增正式履歷 claim。
- `game-api` README 顯示它是 slot game runtime，包含 math jar loading、game init / bet、本地加解密測試、player / guest token session、錯誤處理、下注通知與 Quartz 補償 memo；可優先和 API doc 的進入遊戲、單一錢包、bet / settle / cancel flow 對照。
- `admin-api` / `game-job` README 多為模板或 conventions，本輪只列為後續 candidate，不作 claim。
- 若後續要正式納入 Flow Track，必須先做 usproject project-level Step 1 / Step 2，並在掃 code repo 前確認 remote refs / local HEAD / branch 狀態；不得從 API doc 直接跳單條 flow Step 3。

## DevOps / primestar

來源路徑:

```text
/Users/nick/Git/DevOps/primestar
```

Git repo:

- `antplay-api-deploy`
- `antplay-docker-deploys`
- `antplay-web-deploy`
- `ci-template`
- `ci-test`
- `kafka`
- `openobserve`

初步用途:

- deploy / CI / observability / Kafka 參考。
- DevOps 不是履歷主戰場時，只補必要的 deployment、monitoring、Kafka 運維理解。
- 2026-05-26 completeness audit / re-audit：DevOps / primestar repo 未見 `10gt12nc` / Nick direct commits。`antplay-docker-deploys` 有大量 `arnold` commits，集中 k3s dev / UAT manifests、UGSoft admin / connector deployment、rolling update / probes、configmap / ingress、Fluent Bit / Loki / Grafana 與 apply SOP；Nick 已確認 `arnold` 是主管帳號，不是 Nick direct evidence，因此只作主管 / 團隊 context 或 learning / supporting，不作 Nick Flow Track 或履歷 claim。`openobserve` / `kafka` 無 Nick / `arnold` commits，source 含敏感設定，不搬內容進 vault，只作 learning-only。其他 repo 暫不建議。

## 下一步使用方式

做 domain map 時:

```text
請讀 projects/source-repo-inventory.md
再深掃 /Users/nick/Git/{domain}
建立 projects/{domain}/README.md、architecture-map.md、integration-map.md
```

做單條 flow 時:

```text
請讀 projects/source-repo-inventory.md
再讀該 domain / project 既有文件
再讀對應 code repo 的 branch、log、path-specific history、入口與主要資料流
```
