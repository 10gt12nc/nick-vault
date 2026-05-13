# Source Repo Inventory

用途: 記錄 Nick 本機可參考的來源 Git repo，避免每次 project / flow 任務都重新問路徑。

這份不是 evidence，也不是履歷 claim。做任何 flow 前仍要重讀 KB、檢查當下 branch / log / code path，不能只靠本清單。

## 使用規則

- 只能讀這些公司 / 來源 repo，不能改。
- 真正長期整理內容只放 `nick-vault/projects/`。
- `*-workspace` 通常是維護 / KB / 工作區參考，不當成業務 code 主體。
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
- client / deploy / simulator / workspace: 依 flow 需要作參考

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
