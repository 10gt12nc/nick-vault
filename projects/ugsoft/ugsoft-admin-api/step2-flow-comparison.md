# ugsoft-admin-api Step 2 Flow Comparison

日期: 2026-05-27

## Step 2 結論

`ugsoft-admin-api` Step 2 已完成。本批代表 flows 選 3 條，不平均做全部:

1. `connect-bet-record-mq-ingestion`
2. `request-log-rabbitmq-admin-consumer`
3. `game-api-provider-white-ip-control-plane`

排序理由:

- `connect-bet-record-mq-ingestion` 最能代表交易 / provider data ingestion / duplicate / currency / quota async failure window，且可和已完成的 `ugsoft-connector-api request-bet-record-mq-sync` 形成上游 job sync -> MQ -> admin-api consumer 的完整鏈。
- `request-log-rabbitmq-admin-consumer` 是 async audit / observability 素材，可和 AntPlay / UGSoft request log async story 串起來，但交易正確性低於 BetRecord。
- `game-api-provider-white-ip-control-plane` 是後台 control plane 影響 runtime access control 的代表，能補「admin API 不是只有 CRUD」的 Platform Backend 視角。
- `daily-hourly-report-quartz-job`、`admin-auth-token-refresh-security`、`risk-monitor-alert-rabbitmq` 保留為可選補強，不列本批必做。

本輪只做 Step 2 比較與本批代表 flow 選擇，不建立 `flows/{flow}/`，不更新 `05 / 08`。

## 掃描等級

- 本次深度: Level 1.5 Flow 比較。
- 理由: Step 2 需要比 Step 1 多看 path-specific history、remote snapshot 與上下游重疊，但仍不是單條 flow 的 Level 2 深掃。
- 未做: 未逐行讀所有 code、未做每個 commit diff、未驗證 production runtime / MQ broker / Redis / DB 實際設定、未掃 `ugsoft-admin-web`。

## Source Repo 狀態

來源 repo:

```text
/Users/nick/Git/ugsoft/ugsoft-admin-api
```

遠端狀態:

- 已執行 `git fetch --all --prune`，第一次因 sandbox `.git/FETCH_HEAD` 權限失敗，approval 後成功。
- local branch: `main`
- local HEAD: `0cc62e0e1a040e69b1650079d9ecfe92dd64380d`
- `origin/main`: `b1b83f64ffc971cc838ef935867a5a2234e3d201`
- ahead / behind: `0 / 42`
- source working tree: 乾淨
- 判斷方式: local working tree、fetched `origin/main` snapshots、path-specific history、Step 1 與 connector 已完成 flows 交叉判斷。本機工作樹落後 `origin/main` 42 commits，2026-04 後多數 `arnold` commits 僅作 current behavior / 主管 context，不當 Nick direct evidence。

Nick direct evidence:

- `git log --all --author='10gt12nc|Nick|nick'`: 約 206 筆。
- `git log origin/main --author='10gt12nc|Nick|nick'`: 約 190 筆。
- `arnold` 已由 Nick 確認為主管帳號，不是 Nick direct evidence。

## 候選 Flow 比較表

| Rank | Flow | 技術價值 | Nick evidence | 履歷 / 面試價值 | 風險與邊界 | Step 2 決策 |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | `connect-bet-record-mq-ingestion` | 高 | 強：`98ad763`、`f641b04`、`821bc2e`、`c99a325` | 交易資料入庫、duplicate、currency、quota async、MQ failure window | 最新 id / amount normalization 有 `arnold` current behavior；不可說完整 wallet / ledger / exactly-once owner | 本批第 1 條進 Step 3 |
| 2 | `request-log-rabbitmq-admin-consumer` | 高 | 強：`814b024`、`f3a9d72`、`5f838f8`、`821bc2e` | async audit、observability、request / response log ingestion、partition / duplicate | 價值偏 audit，不是交易 source of truth；不可說完整 RabbitMQ platform owner | 本批第 2 條 |
| 3 | `game-api-provider-white-ip-control-plane` | 中高 | 中高：`f90a1eb`、`6cf3855`、`4c86d96`、`1dd366c`、`2fb2ce5` | 後台 control plane -> DB / Redis / runtime access control | 2026-04 provider fanout reload 為 `arnold` context；Step 3 要補 connector reload / cache 下游 | 本批第 3 條 |
| 4 | `daily-hourly-report-quartz-job` | 中高 | 中高：`ed4f153`、`f338fc2`、`6795199`、`6e69f17`、`7fe3328`、`fb675d0` | batch / report consistency、時間窗口、重跑、金額單位、partial failure | iwin / antplay 已有多條 batch / projection case；最新 AgentReportService 統一路徑多為 `arnold` context | 可選補強，不列本批 |
| 5 | `admin-auth-token-refresh-security` | 中高 | 中：早期 auth / JWT / auth token 有 direct evidence | auth / RBAC / JWT / IP allowlist / 2FA / operation log | 2026-05 P0 refresh 漏洞與 login brute-force 為 `arnold` 修正，不能當 Nick claim | 可選補強，不列本批 |
| 6 | `risk-monitor-alert-rabbitmq` | 中 | 中低：`250811a`、`44d1f84` | risk alert consumer / observability supporting | implementation 與後續修正多為 team context，且偏告警入庫 | supporting，不列本批 |
| 7 | `merchant-agent-scope-report-query` | 中低 | 中 | agent / merchant scope、查詢權限、報表入口 | 範圍太散，容易變 CRUD / query collection | 暫不建議先做 |

## 本批代表 Flow

### 1. `connect-bet-record-mq-ingestion`

中文名稱: Connector BetRecord MQ 入庫與 quota update

選它的原因:

- 最貼近 Senior Backend 的 correctness 題材：provider bet record、duplicate check、`ptDay`、currency、amount normalization、quota async publish。
- 和 `ugsoft-connector-api request-bet-record-mq-sync` 可形成完整鏈路：connector job / callback 送 MQ，admin-api consumer 入庫。
- Nick / `10gt12nc` path-specific evidence 集中，包含 BetRecord MQ 初版、job 多單修正、BetRecord / RequestLog MQ 合併、currency default。

Step 3 要補:

- `ConnectBetRecordListener` / `ConnectBetRecordConsumerService` 的最新 `origin/main` behavior。
- `BetRecordMapper.xml` duplicate query 條件。
- `QuotaUpdateConsumer` / quota update message 是否只是 fire-and-forget supporting path。
- MQ publish / consume failure、DB write failure、quota publish failure 的邊界。
- `arnold` 後續 id / duplicate / amount normalization 只能作 current behavior context，不能當 Nick direct evidence。

不可誇大:

- 不說完整 wallet / ledger / quota owner。
- 不說 exactly-once / outbox / DLQ 已完整落地。
- 不說完整 bet record platform owner。

### 2. `request-log-rabbitmq-admin-consumer`

中文名稱: RequestLog RabbitMQ 非同步入庫

選它的原因:

- 可補 async audit / observability / request-response log 的 production flow。
- 和 `antplay-slot-game-api request-log-rabbitmq-async` 已完成素材能互補，形成 producer / consumer 視角。
- Nick / `10gt12nc` 有 #774 RequestLog 改 RabbitMQ 非同步執行 direct evidence。

Step 3 要補:

- producer / queue / routing key 是否與 game-api / connector-api 對齊。
- `RequestLogListenerProcessor` 查重與 insert 的 transaction boundary。
- `ptDay` / partition / request id 格式與 duplicate boundary。
- parse failure / insert failure / MQ redelivery / poison message 的處理。

不可誇大:

- 不說完整 audit platform。
- 不說完整 RabbitMQ architecture owner。
- 不說 exactly-once / DLQ / replay 已完整建立。

### 3. `game-api-provider-white-ip-control-plane`

中文名稱: Game API / provider white IP 後台控制面

選它的原因:

- 這是 admin-api 的典型 control plane：後台變更會影響 runtime access control。
- 可講 DB / Redis cache 一致性、權限範圍、duplicate check、多 agent sync、partial failure。
- 能補 `ugsoft-admin-api` 不只 MQ / report，也有 platform control plane 的面試素材。

Step 3 要補:

- Game API white IP 的 DB + Redis 更新流程。
- provider white IP 的 latest `origin/main` 行為，尤其 provider 全站共用與 fanout reload 只作 current behavior context。
- 是否需要補看 `ugsoft-connector-api` 的 reload / cache path；若沒有掃下游，必須標示待確認。
- operation log、RoleFilter / limited admin 邊界。

不可誇大:

- 不說完整 access-control platform owner。
- 不說完整 connector reload owner。
- 不把 `arnold` provider fanout reload commits 當 Nick direct evidence。

## 本批不做的候選

### `daily-hourly-report-quartz-job`

保留為可選。理由是它有 batch / report consistency 價值，也有 Nick direct evidence；但 Nick 已有 iwin `game_job`、AntPlay `slot-game-job`、`app_bi` 等多條 batch / projection / reporting case，目前再補一條 report job 的邊際價值低於 BetRecord MQ / RequestLog MQ / White IP control plane。

### `admin-auth-token-refresh-security`

保留為可選。理由是 auth / JWT / RBAC 價值高，但最新 P0 refresh mismatch 與 brute-force 防護是 `arnold` context，不可寫成 Nick direct evidence。若未來 JD 偏 security / IAM / platform control，可以回頭做。

### `risk-monitor-alert-rabbitmq`

保留為 supporting。理由是風控告警有 observability 價值，但 direct evidence 較少，且目前只是 alert message -> insert 的 supporting flow，不如 RequestLog / BetRecord MQ 代表性強。

### `merchant-agent-scope-report-query`

暫不建議先做。理由是範圍過散，容易變成 CRUD / query scope collection，不符合目前「每次一條 production flow」的收斂方向。

## 與既有素材的重疊 / 互補

| 既有素材 | 重疊 | 互補判斷 |
| --- | --- | --- |
| `ugsoft-connector-api request-bet-record-mq-sync` | BetRecord MQ / late data sync | Admin-api Step 3 可補下游 consumer /入庫 / quota boundary，互補高 |
| `ugsoft-connector-api provider-callback-bet-settle-to-mq` | provider callback -> MQ | Admin-api BetRecord consumer 可補 callback / job sync 後的入庫端，但不要混成完整 reconciliation |
| `antplay-slot-game-api request-log-rabbitmq-async` | RequestLog async producer / consumer | Admin-api RequestLog consumer 可補另一個 domain 的 audit ingestion 視角，互補中高 |
| iwin / AntPlay job projection cases | daily / hourly report job | 重疊偏高，因此 report job 暫不列本批 |
| 既有 auth / RBAC 履歷素材 | admin auth flow | 可支持履歷，但 security fix direct evidence 邊界敏感，暫不列本批 |

## 後續建議順序

第一條代表 flow 後續已完成 Step 5；第二條代表 flow `request-log-rabbitmq-admin-consumer` 已完成 Step 5；第三條代表 flow `game-api-provider-white-ip-control-plane` 已完成 Step 4。若繼續本 flow，下一步是 Step 5:

```text
ugsoft ugsoft-admin-api game-api-provider-white-ip-control-plane Step 5
```

原因:

- Step 3 已補 Game API white IP 的 DB + Redis 更新、provider white IP current behavior、operation log / RoleFilter 邊界，並明確切開 `arnold` 後續 provider fanout reload context。
- Step 4 已把 control plane -> runtime access-control、DB / Redis / fanout consistency、scope decision 與不可誇大邊界整理成正式面試 case。
- Step 5 要做單條 flow claim gate，判斷是否可作 project-level supporting evidence；不直接更新 `05 / 08`。

## Relationship Check

本輪事實變更:

- `ugsoft-admin-api` Flow Track Step 2 已完成。
- 本批代表 flows 選定 3 條：`connect-bet-record-mq-ingestion`、`request-log-rabbitmq-admin-consumer`、`game-api-provider-white-ip-control-plane`。
- 第一條 `connect-bet-record-mq-ingestion` 已完成 Step 5。
- 第二條 `request-log-rabbitmq-admin-consumer` 已完成 Step 5。
- 第三條 `game-api-provider-white-ip-control-plane` 已完成 Step 4；下一步建議同 flow Step 5。

需要同步的權威檔:

- `projects/ugsoft/ugsoft-admin-api/README.md`
- `projects/ugsoft/README.md`
- `projects/source-repo-inventory.md`
- `projects/source-repo-flow-audit.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/06-todo.md`
- `senior-owner-playbook/13-code-capability-map.md`

不更新:

- `05-resume-master-zh.md` / `08-application-autobiography-zh.md`: 本輪只是 Step 2，不是 Step 5 或 project contribution refresh。
- `04-interview-casebook.md`: 尚未建立單條 flow interview case。
- `17-salary-negotiation.md`: 沒有薪資策略變更。
