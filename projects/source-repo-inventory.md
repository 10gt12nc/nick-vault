# Source Repo Inventory

用途: 記錄 Nick 本機可參考的來源 Git repo，避免每次 project / flow 任務都重新問路徑。

這份不是 evidence，也不是履歷 claim。做任何 flow 前仍要重讀 KB、檢查當下 branch / log / code path，不能只靠本清單。

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

- `ugsoft-admin-api`: contribution claim consolidation 已完成 / rolling。Nick / `10gt12nc` 有大量 direct commits，可保守作後台 API / control plane、RabbitMQ / Quartz / report job 類履歷素材；不寫完整 UG 平台或 provider gateway owner。
- `ugsoft-connector-api`: contribution claim consolidation 已完成 / rolling。Nick / `10gt12nc` 有大量 direct commits，可保守作 AntPlay / DerPlay provider connector、transfer wallet、callback、request / bet record MQ、分表與 provider reliability 類履歷素材；不寫完整 connector architecture / wallet / reconciliation owner。
- `ugsoft-workspace`: contribution claim consolidation 已完成 / rolling。這是 workspace / docs / harness / runbook repo，只支撐 cross-repo system reconstruction、工程規範、local / deploy harness 與 migration / release 風險整理；不放 standalone 正式履歷主成果，不反向包裝成 `ugsoft-admin-api` / `ugsoft-connector-api` service code。

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

- `antplay-slot-admin-api`: contribution claim consolidation 已完成 / rolling。Nick / `10gt12nc` 有大量 direct commits，可保守作後台 API / 商戶控制面、JWT / RBAC、商戶 / Game API 白名單、超級代理、玩家 / 投注 / request log / 報表查詢、RTP / 暗池 / 活動風控監控、RabbitMQ request log / 風控通知與 Quartz job 類履歷素材；不寫完整 AntPlay slot platform、game runtime、wallet / reconciliation 或完整 RabbitMQ owner。
- `antplay-slot-game-api`: contribution claim consolidation 已完成 / rolling。Nick / `10gt12nc` 有大量 direct commits，可保守作遊戲 API runtime、game init、bet / settle / rollback、轉帳錢包、bet record / request log 分表、RabbitMQ request log、Quartz 補通知、RTP / dark pool / player control 類履歷素材；不寫完整 AntPlay slot platform、完整遊戲數學 / RTP 策略、wallet / ledger / reconciliation 或完整 RabbitMQ / Kafka owner。
- `antplay-slot-game-job`: contribution claim consolidation 已完成 / refreshed；Flow Track Step 1 / Step 2 與五條代表 flows Step 5 已完成 / 2026-05-25。Nick / `10gt12nc` 有 direct commits，可保守作 Kafka / Quartz job、代理玩家報表 projection、activity accumulated bet supporting flow、big-win notification、report currency / key 修正與 db partition / job config 類履歷素材；不寫完整 Kafka event platform、完整 settle pool / risk / jackpot owner 或完整 BI / report platform owner。五條代表 flows 已回填 project-level claim 與 rolling resume package。
- `math-core`: contribution claim consolidation 已完成 / rolling。Nick / `10gt12nc` 有 direct commits，可保守作 slot math core contract、debugBet VO、currency / fixedMultiBet、RTP type、jackpot / symbol 類履歷素材；不寫完整 slot math framework、完整 RTP 策略或完整 simulator owner。
- `*-math`: contribution claim consolidation 已完成 / rolling / grouped。71 個 `*-math` repo 中本輪掃到 49 個有 Nick / `10gt12nc` direct commits；強 evidence 是 `sph-math`、`spn-math`、`sfm-math`、`setl-math`、`sdt-math`、`slc-math`。可保守作多個 slot math module 維護與驗證素材；不寫主導全部 math module 或完整遊戲數學 owner。
- `math-workspace`: contribution claim consolidation 已完成 / rolling。Nick 有 workspace KB / docs commits，只支撐 cross-math module reconstruction / validation workflow；不作 standalone 正式履歷主成果。
- `platform-mock`: contribution claim consolidation 已完成 / rolling。Nick / `10gt12nc` 有局部 bet / money_inout failure injection commits，只作 provider rollback / compensation 測試 supporting evidence；不作正式主成果。
- `buffer-id`: contribution claim consolidation 已完成 / rolling。未掃到 Nick / `10gt12nc` direct commits，只作 ID generator learning-only。

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
