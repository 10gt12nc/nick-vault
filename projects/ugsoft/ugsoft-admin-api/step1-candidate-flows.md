# ugsoft-admin-api Step 1 Candidate Flows

日期: 2026-05-27

## Step 1 結論

`ugsoft-admin-api` 的 Flow Track 已建立 Step 1。這個 repo 不是單純 CRUD 後台，較值得保留的主線集中在:

1. admin login / auth / token refresh / 2FA / IP allowlist。
2. Game API / provider white IP control plane。
3. RequestLog RabbitMQ async ingestion。
4. connector BetRecord MQ ingestion + quota update。
5. risk / monitor alert RabbitMQ ingestion。
6. daily / hourly report Quartz job。

本輪原始只做 candidate flow 盤點，不建立 `flows/{flow}/`，也不更新 `05 / 08`。後續已完成 project-level Step 2，第一條 `connect-bet-record-mq-ingestion` 已完成 Step 5，第二條 `request-log-rabbitmq-admin-consumer` 已完成 Step 5；若繼續本 project，下一步應是第三條代表 flow Step 3。

## 掃描等級

- 本次深度: Level 1 Flow 掃描。
- 理由: Nick 指定 `ugsoft-admin-api Step 1`，目標是找 candidate flows 與 module 邊界，不是逐檔逐行深挖單條 flow。
- 未做: 未逐行讀所有 controller / service / mapper，未看完整 SQL schema，未做每個 commit diff，未建立 flow 學習包。

## Source Repo 狀態

來源 repo:

```text
/Users/nick/Git/ugsoft/ugsoft-admin-api
```

遠端狀態:

- 已執行 `git fetch --all --prune`，第二次使用 approval 後成功。
- local branch: `main`
- local HEAD: `0cc62e0e1a040e69b1650079d9ecfe92dd64380d`
- `origin/main`: `b1b83f64ffc971cc838ef935867a5a2234e3d201`
- ahead / behind: `0 / 42`
- source working tree: 乾淨
- 判斷方式: local working tree code、fetched `origin/main` log / remote file snapshots、path-specific history 交叉判斷。因本機工作樹落後 `origin/main` 42 commits，涉及 2026-04 以後 arnold commits 的內容只作 current behavior / team context，不當 Nick direct evidence。

Nick direct evidence:

- `git log --all --author='10gt12nc|Nick|nick'`: 約 206 筆。
- `git log origin/main --author='10gt12nc|Nick|nick'`: 約 190 筆。
- `arnold` 已由 Nick 確認為主管帳號，任何 `arnold` commits 只作主管 / 團隊 context 或 current behavior context，不當 Nick direct evidence。

## Module Map

| Layer | 代表路徑 | Step 1 判斷 |
| --- | --- | --- |
| Admin controller | `src/main/java/com/ps/domain/admin/controller/*` | 後台入口，包含 auth、merchant、white IP、request log、bet record、report、monitor、admin tool |
| Admin service | `src/main/java/com/ps/domain/admin/service/*` | 後台 control plane service；部分流程直接呼叫 mapper / Redis / RabbitMQ |
| Manage service | `src/main/java/com/ps/domain/manage/service/*` | report、agent、auth、transfer wallet 等 domain service |
| RabbitMQ consumer | `src/main/java/com/ps/domain/admin/rabbitMq/*` | RequestLog、BetRecord、quota、monitor alert 等非同步資料處理 |
| Quartz job | `src/main/java/com/ps/quartz/job/impl/*` | daily / hourly report、agent cache、game cache、quota sync |
| Mapper / SQL | `src/main/resources/mapper/*` | RequestLog、BetRecord、report、white IP、monitor alert、transfer wallet 查詢與寫入 |
| 下游 / 關聯 repo | `ugsoft-connector-api`、Game API runtime、RabbitMQ broker、Redis | 本輪只定位，不展開下游深掃 |

## Candidate Flows

### 1. `request-log-rabbitmq-admin-consumer`

中文名稱: RequestLog RabbitMQ 非同步入庫

入口 / code:

- `src/main/java/com/ps/domain/admin/rabbitMq/RequestLogListener.java`
- `src/main/java/com/ps/domain/admin/rabbitMq/RequestLogListenerProcessor.java`
- `src/main/resources/mapper/RequestLogMapper.xml`
- 查詢入口: `src/main/java/com/ps/domain/admin/controller/RequestLogController.java`

已確認:

- listener 從 RabbitMQ queue 收 message，解析 request / response / status / elapsedTime 等欄位。
- 依 event time 算 `ptDay`，用 `agentId + requestLog` 查重後才 insert。
- path history 有 Nick / `10gt12nc` direct commits: `814b024`、`f3a9d72`、`5f838f8`、`821bc2e`。
- 後續 `arnold` 修 request-log key 格式與 request log 修正，只作 current behavior context。

值得度: 高。

Senior 點:

- request log 從同步寫入改成 MQ async 後，主交易不能被 log DB 寫入拖垮。
- 需要處理 duplicate message、partition key / `ptDay`、message parse failure、lost message 與 observability。
- 可和 `antplay-slot-game-api request-log-rabbitmq-async` 對照成 producer / consumer 雙側 case。

履歷邊界:

- 可說參與 RequestLog RabbitMQ 非同步資料處理與入庫維護。
- 不說完整 RabbitMQ event platform owner、完整 DLQ / exactly-once 設計 owner。

### 2. `connect-bet-record-mq-ingestion`

中文名稱: Connector BetRecord MQ 入庫與 quota update

入口 / code:

- `src/main/java/com/ps/domain/admin/rabbitMq/betRecord/ConnectBetRecordListener.java`
- `src/main/java/com/ps/domain/admin/rabbitMq/betRecord/ConnectBetRecordConsumerService.java`
- `src/main/java/com/ps/domain/admin/rabbitMq/quota/QuotaUpdatePayload.java`
- `src/main/resources/mapper/BetRecordMapper.xml`

已確認:

- listener 接收 connector bet record message，service 解析 payload。
- 依 `ptDay + agentId + providerBetId` 做 duplicate check。
- 建立 `pt_bet_record`，補 `currency` default、amount normalization、`BetRecordStep.CREATE`。
- 寫入成功後 fire-and-forget publish quota update message。
- path history 有 Nick / `10gt12nc` direct commits: `98ad763`、`f641b04`、`821bc2e`、`c99a325`。
- 2026-04 後 `arnold` 將 id / duplicate / amount normalization 調整到最新行為，只作 current behavior context。

值得度: 高。

Senior 點:

- BetRecord 是後續 report / quota / reconciliation 的資料基礎，duplicate key、currency、amount 單位與 `ptDay` 錯了會影響報表與額度。
- quota update 是 async fire-and-forget，需說清楚 bet record write 成功但 quota publish 失敗的 failure window。
- 與 `ugsoft-connector-api request-bet-record-mq-sync` 有上下游關係，可形成 connector -> admin-api ingestion 的完整面試鏈。

履歷邊界:

- 可說參與 bet record MQ ingestion / duplicate check / currency / quota async supporting flow。
- 不說完整 wallet / ledger / quota system owner，也不說 exactly-once / outbox 已完整落地。

### 3. `game-api-provider-white-ip-control-plane`

中文名稱: Game API / provider white IP 後台控制面

入口 / code:

- `src/main/java/com/ps/domain/admin/controller/GameApiWhiteIpController.java`
- `src/main/java/com/ps/domain/admin/service/impl/GameApiWhiteIpServiceImpl.java`
- `src/main/resources/mapper/GameApiWhiteIpMapper.xml`
- `src/main/java/com/ps/domain/admin/controller/ProviderWhiteIpController.java`
- `src/main/java/com/ps/domain/admin/service/impl/ProviderWhiteIpServiceImpl.java`
- `src/main/resources/mapper/ProviderWhiteIpMapper.xml`

已確認:

- Game API white IP 支援新增、查詢、刪除，會同步 DB 與 Redis set。
- `WhitelistSync` 會把特定 merchant source IP 同步到關聯 agents，更新 DB 與 Redis；本文件不記錄實際 merchant code。
- provider white IP 支援 provider normalization、allowed providers、duplicate check。
- path history 有 Nick / `10gt12nc` direct commits: `f90a1eb`、`6cf3855`、`4c86d96`、`1dd366c`、`2fb2ce5`。
- 2026-04 後 `arnold` 有 provider white IP 全站共用、fanout 通知 connector-api reload、Redis key 大小寫對齊；這些只能作 current behavior context，不當 Nick direct evidence。

值得度: 高。

Senior 點:

- 這是 admin control plane 影響 runtime access control 的典型 flow。
- 關鍵風險在 DB / Redis cache 一致性、權限範圍、duplicate、同步多 agent 的 partial failure。
- 可延伸到 admin-api -> connector-api reload / runtime filter，但 Step 3 時必須補下游 evidence。

履歷邊界:

- 可說參與商戶 / provider 白名單後台控制面與 DB / Redis 同步維護。
- 不說完整 access-control platform owner 或完整 connector reload owner。

### 4. `admin-auth-token-refresh-security`

中文名稱: Admin login / JWT refresh / auth token / 2FA / IP allowlist

入口 / code:

- `src/main/java/com/ps/domain/admin/controller/AuthController.java`
- `src/main/java/com/ps/domain/admin/controller/AuthTokenController.java`
- `src/main/java/com/ps/domain/manage/service/AdminAuthService.java`
- `src/main/java/com/ps/domain/admin/service/AuthTokenService.java`
- `src/main/resources/mapper/AdminAuthMapper.xml`
- `src/main/resources/mapper/AuthTokenMapper.xml`

已確認:

- 後台 login / refresh / logout / admin list / auth token 有長期 commit history。
- Nick / `10gt12nc` direct commits 覆蓋 JWT payload / 401、disabled login、大小寫、超級代理、merchant auth、auth_token rename / kick user 等。
- 2026-05 `arnold` 修 refresh 偽造 username 漏洞與 login Redis 暴力破解防護；這是重要 current behavior / security context，但不是 Nick direct evidence。

值得度: 中高。

Senior 點:

- 安全性與權限邊界強，但 Step 3 需要避免把主管修的 P0 安全修正當成 Nick 經驗。
- 可講 auth flow、JWT claims / refresh boundary、roleType / agentId scope、IP allowlist、2FA、operation log。

履歷邊界:

- 可說參與後台登入 / JWT / RBAC / auth token 維護。
- 不說主導 P0 refresh 漏洞修復，不把 `arnold` security commits 當 Nick direct evidence。

### 5. `risk-monitor-alert-rabbitmq`

中文名稱: 風控 / monitor alert RabbitMQ 入庫

入口 / code:

- `src/main/java/com/ps/domain/admin/rabbitMq/createMonitorAlertListener.java`
- `src/main/java/com/ps/domain/admin/controller/MonitorAlertController.java`
- `src/main/java/com/ps/quartz/job/service/MonitorAlertService.java`
- `src/main/resources/mapper/MonitorAlertMapper.xml`

已確認:

- listener 從 RabbitMQ 接收 monitor alert message，建立 alert object 並 insert monitor alert table。
- path history 有 Nick / `10gt12nc` direct commits: `250811a`、`44d1f84`。
- 風控 monitor / alert 相關也有較多 `arnold` / team commits，只作 context。

值得度: 中。

Senior 點:

- 可支撐 observability / risk operation story。
- 但目前 direct evidence 比 RequestLog / BetRecord MQ 弱，且比較偏告警資料寫入，不一定比前兩條更值得先做 Step 3。

履歷邊界:

- 可作「風控通知 consumer / monitor alert supporting flow」。
- 不說完整 risk platform / alerting platform owner。

### 6. `daily-hourly-report-quartz-job`

中文名稱: Daily / hourly report Quartz job 與手動重跑

入口 / code:

- `src/main/java/com/ps/quartz/job/impl/UpdateAgentDailyTable.java`
- `src/main/java/com/ps/quartz/job/impl/UpdateAgentHourlyTable.java`
- `src/main/java/com/ps/domain/manage/service/AgentReportService.java`
- `src/main/java/com/ps/domain/manage/service/AgentReportReadonlyService.java`
- `src/main/java/com/ps/quartz/main/controller/ScheduleJobController.java`
- `src/main/resources/mapper/AllDailyReportMapper.xml`
- `src/main/resources/mapper/AllHourlyReportMapper.xml`

已確認:

- Quartz job 會依 agent / date window 產 daily / hourly report。
- `@DisallowConcurrentExecution` 避免同 job 併發。
- path history 有 Nick / `10gt12nc` direct commits: `ed4f153`、`f338fc2`、`6795199`、`6e69f17`、`7fe3328`、`fb675d0`、`2fb1b90`、`4579590`、`80391be`。
- 2026-04 後 report job 有 `arnold` 調整單位換算、AntPlay / DerPlay split columns 與統一 service path，這些是 current behavior context。

值得度: 中高。

Senior 點:

- 報表 job 很適合講 batch consistency、時間窗口、重跑、delete + insert、partial failure、幣別與金額單位。
- 但 iwin / antplay 已有不少 batch / projection case；Step 2 時要評估是否還需要再補這條。

履歷邊界:

- 可說參與 report job / Quartz / daily-hourly report 維護。
- 不說完整 BI / reporting platform owner。

### 7. `merchant-agent-scope-report-query`

中文名稱: 商戶 / 代理 scope 下的查詢與報表入口

入口 / code:

- `src/main/java/com/ps/domain/admin/controller/MerchantController.java`
- `src/main/java/com/ps/domain/admin/controller/AgentController.java`
- `src/main/java/com/ps/domain/admin/controller/AgentDailyController.java`
- `src/main/java/com/ps/domain/admin/controller/AgentHourlyReportController.java`
- `src/main/java/com/ps/domain/admin/controller/BetRecordController.java`
- `src/main/java/com/ps/domain/manage/service/AgentService.java`

已確認:

- 超級代理、超級商戶、merchant admin、agent hierarchy 與報表查詢有 Nick / `10gt12nc` direct commits。
- 這條涵蓋範圍大，容易變成 CRUD / 查詢集合，需 Step 2 再拆小。

值得度: 中。

Senior 點:

- 可用來講資料 scope、agent hierarchy、查詢權限、分頁 / query boundary。
- 但若沒有狀態轉移、MQ、job 或 consistency 問題，履歷面試價值低於前面幾條。

履歷邊界:

- 可作 admin control plane supporting flow。
- 不建議作第一條 Step 3。

## 初步排序

| Rank | Flow | 推薦 | 理由 |
| --- | --- | --- | --- |
| 1 | `connect-bet-record-mq-ingestion` | Step 2 重點比較 | Nick direct evidence 強，交易 / bet record / quota / duplicate / currency / MQ failure window 都有價值，且可接 `ugsoft-connector-api` 已完成 flow |
| 2 | `request-log-rabbitmq-admin-consumer` | Step 2 重點比較 | Nick direct evidence 強，可和 AntPlay / UGSoft request log async story 串起來，適合講非同步化與 observability |
| 3 | `game-api-provider-white-ip-control-plane` | Step 2 重點比較 | 後台 control plane 影響 runtime access control，DB / Redis / connector reload 邊界有 platform 味道 |
| 4 | `daily-hourly-report-quartz-job` | Step 2 比較 | batch / report consistency 好講，但既有 iwin / antplay batch 素材已不少 |
| 5 | `admin-auth-token-refresh-security` | Step 2 比較 | 權限 / 安全價值高，但要小心 `arnold` security fix 不是 Nick direct evidence |
| 6 | `risk-monitor-alert-rabbitmq` | Step 2 比較 | observability / risk supporting，direct evidence 較少 |
| 7 | `merchant-agent-scope-report-query` | 暫不建議先做 | 容易變 CRUD / 查詢 scope 集合，除非 Step 2 發現更強 evidence |

## Step 2 必須釐清

- 哪幾條 flow 是本批代表 flows，不要平均做全部。
- `connect-bet-record-mq-ingestion` 與 `request-log-rabbitmq-admin-consumer` 是否會和已完成的 `ugsoft-connector-api request-bet-record-mq-sync`、AntPlay request log async 重疊，或能形成上下游互補。
- white IP flow 是否要補看 `origin/main` 最新 provider white IP fanout reload，以及 `ugsoft-connector-api` reload consumer / cache path；這部分有 `arnold` current behavior context，不能當 Nick direct evidence。
- auth flow 若要做 Step 3，必須把 Nick direct commits 與 `arnold` P0 security fix 明確切開。
- report job 若要做 Step 3，要決定是否追 latest `origin/main` 統一 AgentReportService path，避免只看 local 落後版本。

## 不建議候選

- `official-web-v3` / `ugsoft-admin-web`: 前端 / 官網，不作 Backend 主線。
- `ugsoft-workspace`: supporting evidence，不作 runtime flow。
- 純 CRUD 類列表 / 查詢 endpoint: 除非和權限、資料一致性、MQ / job / runtime control plane 有關，否則不列 Step 3 優先。

## 下一步

`ugsoft-admin-api` Step 1 已完成；2026-05-27 後續 Step 2 也已完成，詳見 [step2-flow-comparison.md](step2-flow-comparison.md)。第一條代表 flow `connect-bet-record-mq-ingestion Step 5` 已完成，第二條代表 flow `request-log-rabbitmq-admin-consumer Step 5` 也已完成；下一步如果繼續本 project，應回到第三條代表 flow `game-api-provider-white-ip-control-plane Step 3`，不能跳到其他 project。

```text
ugsoft ugsoft-admin-api game-api-provider-white-ip-control-plane Step 3
```
