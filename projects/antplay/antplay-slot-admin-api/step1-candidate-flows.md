# antplay-slot-admin-api Step 1 Candidate Flows

日期: 2026-05-28

## Step 1 結論

`antplay-slot-admin-api` 是 AntPlay slot 系列的後台 API / control plane repo。Career Track 已在 `contribution-claim-consolidation.md` 保守收斂；本輪 Step 1 只建立 Flow Track 候選清單，不建立單條 flow folder，也不更新 `05 / 08 / 04 / 17`。

本 repo 值得進 Step 2 的候選 flow 主要集中在三類:

1. 後台設定如何影響 runtime 或下游資料：Game API 白名單、商戶白名單、auth / RBAC / merchant scope。
2. 非同步資料處理與 audit：RequestLog RabbitMQ consumer、MonitorAlert RabbitMQ consumer、PlayerControl RabbitMQ。
3. 風控 / 報表 / Quartz：RTP / 暗池 / 活動風控監控、玩家單點控制、日 / 小時報表與分表查詢。

下一步必須是 `antplay antplay-slot-admin-api Step 2`，比較候選 flows 的風險、證據強度、module 邊界與履歷 / 面試價值；不得直接跳單條 flow Step 3。

## 掃描深度

- 本輪深度: Level 1 Flow 掃描。
- 目的: 找 candidate flows、建立 module 邊界、確認值得 Step 2 比較的 production flows。
- 未做: Level 2 單條 flow 深掃、Level 3 逐檔逐行 / 每個 commit diff。

## Source Repo 狀態

來源 repo:

```text
/Users/nick/Git/antplay/antplay-slot-admin-api
```

遠端 refs:

- 已嘗試 `git fetch --all --prune` 一次。
- 內網 remote 目前連線失敗，未能更新 remote refs。
- 依 KB 規則，本輪不反覆重試；以下結論依本地 refs / 本地工作樹保守判斷。

本地狀態:

| 項目 | 狀態 |
| --- | --- |
| branch | `main` |
| local HEAD | `2e15503d88de3af133cce17fbf3ff80262678419` |
| 本機既有 `origin/main` | `2e15503d88de3af133cce17fbf3ff80262678419` |
| local vs 本機既有 `origin/main` | `0 / 0` |
| source 工作樹 | clean |
| 最新遠端 | 未確認 |

作者足跡:

| Author | commit 數量 / 判斷 |
| --- | --- |
| `10gt12nc` | 約 182 筆 direct commits |
| `nick` | 約 11 筆 direct commits |
| `arnold` | 約 276 筆；Nick 已確認是主管，只作主管 / team / current behavior context |
| `eliot` / `gill` / `Derek` | team context，不作 Nick direct evidence |

## Module / Service 邊界

| 區塊 | 主要 path | 定位 |
| --- | --- | --- |
| Admin / merchant controller | `src/main/java/com/ps/domain/admin/controller/*` | 後台 API、商戶登入、白名單、報表、風控查詢、玩家控制 |
| Auth / RBAC / filter | `domain/admin/config/SecurityConfig.java`、`filter/*`、`security/*`、`util/JwtUtil.java` | JWT、role filter、merchant auth、white list filter |
| Admin services / mappers | `domain/admin/service/*`、`domain/admin/mapper/*`、`src/main/resources/mapper/*.xml` | 後台設定、查詢、DB 寫入 |
| RabbitMQ consumers | `domain/admin/rabbitMq/*`、`config/RabbitMQConfig.java` | request log、monitor alert、player control、cache refresh consumer |
| Quartz jobs | `quartz/job/impl/*`、`quartz/job/service/*` | RTP / 暗池 / voucher / player control / report jobs |
| Manage / report data | `domain/manage/*`、`domain/game/*` | agent、request log、bet record、report、slot data 查詢與分表支援 |
| External runtime boundary | `antplay-slot-game-api`、Redis、RabbitMQ | Step 1 只定位關聯；未重新深掃下游 repo |

## Candidate Flows

### 1. `request-log-rabbitmq-admin-consumer`

中文名稱: RequestLog RabbitMQ 非同步入庫 / audit consumer

業務目的:

- slot game API 或其他服務將 request log 丟進 RabbitMQ。
- admin-api consumer 解析 message，查重後寫入 request log table，支援後台 audit / 查詢。

主要 code path:

- `src/main/java/com/ps/domain/admin/rabbitMq/RequestLogListener.java`
- `src/main/java/com/ps/domain/admin/rabbitMq/RequestLogListenerProcessor.java`
- `src/main/resources/mapper/RequestLogMapper.xml`
- `src/main/java/com/ps/config/RabbitMQConfig.java`

Nick / `10gt12nc` evidence:

- `814b024` / `a66007d`: `feat(#774):RequestLog改丢rabbitmq非同步执行`
- `f3a9d72` / `5f838f8`: G2 request log MQ 調整
- `fa86544`: UAT config 修正

Senior 價值:

- event -> DB projection。
- consumer duplicate check。
- message parse failure / poison message。
- audit table 不是主交易 truth，但會影響查詢、客服與追查。
- 缺 outbox / DLQ evidence 時如何保守講 reliability。

不可誇大:

- 不寫完整 RabbitMQ platform owner。
- 不寫 exactly-once / outbox 已完成。
- 不把 request log audit 包成完整交易一致性。

Step 2 初步判斷: 高優先。它和 `antplay-slot-game-api/request-log-rabbitmq-async` 可形成 producer / consumer 兩側完整面試故事。

### 2. `risk-monitor-alert-rabbitmq`

中文名稱: 風控通知 RabbitMQ consumer / monitor alert 入庫

業務目的:

- 接收外部或其他服務送來的風控通知 message。
- 轉成 `MonitorAlerts` 寫入 monitor alert table，供後台查詢與風控操作。

主要 code path:

- `src/main/java/com/ps/domain/admin/rabbitMq/createMonitorAlertListener.java`
- `src/main/java/com/ps/domain/manage/data/repository/MonitorAlertMapper.java`
- `src/main/resources/mapper/MonitorAlertMapper.xml`
- `src/main/java/com/ps/quartz/job/service/MonitorAlertService.java`
- `src/main/java/com/ps/config/RabbitMQConfig.java`

Nick / `10gt12nc` evidence:

- `250811a`: `feat(#775): rabbitmq 收风控通知`
- `44d1f84`: queue name 調整
- `05156ef` / `8be68ca`: UAT / config 報錯修正

Senior 價值:

- 風控事件非同步落庫。
- alert type / level / message mapping。
- consumer failure、重複 alert、缺 schema validation 的風險。
- 告警資料和實際風控判斷的 source of truth 邊界。

不可誇大:

- 不寫完整風控平台 owner。
- 不寫完整 alerting / observability platform。

Step 2 初步判斷: 高優先。可和 request log MQ 合併成「admin-api RabbitMQ async ingestion」組合，或拆成獨立風控 alert case。

### 3. `game-api-whitelist-sync`

中文名稱: Game API 白名單控制面 / DB + Redis 同步

業務目的:

- 後台新增 / 查詢 / 刪除 Game API 白名單。
- DB 保存設定，Redis 保存 runtime 快取。
- 特定 merchant code 可同步白名單到一批下屬 agents。

主要 code path:

- `src/main/java/com/ps/domain/admin/controller/GameApiWhiteIpController.java`
- `src/main/java/com/ps/domain/admin/service/impl/GameApiWhiteIpServiceImpl.java`
- `src/main/java/com/ps/domain/admin/mapper/GameApiWhiteIpMapper.java`
- `src/main/resources/mapper/GameApiWhiteIpMapper.xml`
- Redis key: `RedisKey.GAME_API_WHITE_IP`

Nick / `10gt12nc` evidence:

- `f90a1eb`: `feat(#682): slot-game-api API白名单功能`
- `6cf3855`: delete / deleteRedis
- `4c86d96`: `agentId` 前端必傳
- `1dd366c`: status 小寫
- `0f0e559`: 同步 game-api `GAME_API_WHITE_IP`

Senior 價值:

- 後台 control plane 影響 runtime access-control。
- DB / Redis 雙寫一致性。
- delete DB 成功但 Redis 失敗、sync batch 部分成功的 failure window。
- role / limited admin / operation log 邊界。

不可誇大:

- 不寫完整 security platform。
- 不寫完整 game-api gateway owner。
- connector / runtime filter 需另掃下游才能升級。

Step 2 初步判斷: 高優先。這條和 UGSoft `game-api-provider-white-ip-control-plane` 類似，可作跨公司 / 跨專案 control plane 對照。

### 4. `rtp-darkpool-risk-monitor`

中文名稱: RTP / 暗池 / 活動風控監控與 Quartz alert

業務目的:

- Quartz job 或後台查詢從 bet / report / Redis / dark pool 相關資料抓取超標資料。
- 產生 monitor alerts 或提供後台風控查詢。

主要 code path:

- `src/main/java/com/ps/domain/admin/controller/MonitorController.java`
- `src/main/java/com/ps/quartz/job/impl/MonitorAgentRedisRtpExceed.java`
- `src/main/java/com/ps/quartz/job/impl/MonitorAgentRtpExceed.java`
- `src/main/java/com/ps/quartz/job/impl/MonitorAgentPlayerRtpExceed.java`
- `src/main/java/com/ps/quartz/job/impl/MonitorAgentVoucherRtpExceed.java`
- `src/main/java/com/ps/quartz/job/impl/MonitorDarkPoolRecord.java`
- `src/main/java/com/ps/quartz/job/service/*Monitor*Service.java`
- `src/main/resources/mapper/MonitorAgentRtpExceedMapper.xml`
- `src/main/resources/mapper/MonitorAgentPlayerRtpExceedMapper.xml`
- `src/main/resources/mapper/DarkPoolStoreRecordMapper.xml`

Nick / `10gt12nc` evidence:

- `1c2af07`: 驗證近 12h RTP 超過 100%
- `3b391f5`: 新增監控 job
- `57b101f`: `feat(#659): squash 商户游戏风控概况`
- `c7c0072`: riskLevel 調整
- `3486523`: 暗池調整寫記錄與備註
- `f338fc2` / `6795199`: 每日報表新增活動費與 job supporting evidence

Senior 價值:

- threshold / time window / query correctness。
- RTP、暗池、voucher、player risk 之間的資料邊界。
- Quartz job failure、重跑、alert duplicate / false positive。
- 報表 / monitor 不是交易 truth，但會影響營運決策。

不可誇大:

- 不寫完整 RTP 策略 owner。
- 不寫完整風控平台 owner。
- 不寫完整 slot math owner。

Step 2 初步判斷: 高優先，但範圍較大。Step 2 需要決定拆成 RTP monitor、dark pool monitor、voucher monitor，或用一條 risk monitor umbrella flow。

### 5. `player-control-admin-and-mq-projection`

中文名稱: 玩家單點控制後台 CRUD + MQ projection

業務目的:

- 後台建立 / 更新玩家單點控制設定。
- 清 Redis cache、寫操作記錄與 monitor alert。
- RabbitMQ consumer 接收 player control 結果，更新 control 累計值並寫入 record。

主要 code path:

- `src/main/java/com/ps/domain/admin/controller/PlayerControlController.java`
- `src/main/java/com/ps/domain/admin/service/PlayerControlService.java`
- `src/main/java/com/ps/domain/admin/rabbitMq/PlayerControlListener.java`
- `src/main/resources/mapper/PlayerControlMapper.xml`
- `src/main/java/com/ps/quartz/job/impl/PlayerControlRecordBackup.java`
- `src/main/java/com/ps/quartz/job/impl/PlayerControlRecordBakCleanup.java`

Nick / `10gt12nc` evidence:

- `5dc67cf`: `feat(#767): 单点控制，agentPlayer 建立`
- `e12a398`: 使用者不存在時建立
- `0aa7db8`: SQL 修正

Team / current behavior context:

- `PlayerControlController` / `PlayerControlListener` 後續有較多 `arnold` / `eliot` / `gill` commits，這些只能作 team context，不作 Nick direct evidence。

Senior 價值:

- control plane 設定如何影響玩家 runtime behavior。
- DB + Redis cache invalidation。
- MQ projection / manual upsert / record insertion。
- idempotency 依賴 bet id / unique key 的待確認風險。

不可誇大:

- 不寫完整玩家風控策略 owner。
- 不寫完整 runtime player-control owner。

Step 2 初步判斷: 中高優先。適合補 risk ops / control plane 深度，但 Nick direct evidence 比 request log / whitelist 更需要 Step 2 再確認。

### 6. `admin-merchant-auth-rbac-scope`

中文名稱: Admin / merchant auth、JWT、RBAC、白名單與 role scope

業務目的:

- 後台 admin / merchant login。
- JWT payload、refresh / 401、RoleFilter / WhiteListFilter、merchant auth provider、limited admin 之類權限與操作邊界。

主要 code path:

- `src/main/java/com/ps/domain/admin/controller/AuthController.java`
- `src/main/java/com/ps/domain/admin/controller/MerchantController.java`
- `src/main/java/com/ps/domain/admin/config/SecurityConfig.java`
- `src/main/java/com/ps/domain/admin/filter/JwtRequestFilter.java`
- `src/main/java/com/ps/domain/admin/filter/RoleFilter.java`
- `src/main/java/com/ps/domain/admin/filter/WhiteListFilter.java`
- `src/main/java/com/ps/domain/admin/security/MerchantAuthenticationProvider.java`
- `src/main/java/com/ps/domain/admin/util/JwtUtil.java`

Nick / `10gt12nc` evidence:

- `#171` 系列 admin API 初版 / login / logout / refresh。
- `aaef730`: JWT payload / 401 調整。
- `454cdcc`: `RoleTypeFilter` 技術客服。
- `e80b613` 與商戶白名單 / merchant login 相關 commits。

Senior 價值:

- 後台權限、merchant scope、role filter、white list。
- auth failure / 401 / refresh token 邊界。
- security / audit / operation log 的面試素材。

不可誇大:

- 不寫完整 IAM / SSO / security architecture owner。
- 不寫完整 RBAC 平台重構。

Step 2 初步判斷: 中高優先。履歷可用，但面試技術深度通常不如 MQ / risk / runtime control plane。

### 7. `super-agent-report-query-scope`

中文名稱: 超級代理 / 商戶玩家 / 投注 / request log / 報表查詢

業務目的:

- 支援 super agent / merchant 依角色查下級代理、玩家列表、盈虧、投注記錄、request log、每日 / 每小時報表。

主要 code path:

- `src/main/java/com/ps/domain/admin/controller/MerchantController.java`
- `src/main/java/com/ps/domain/admin/controller/AgentDailyController.java`
- `src/main/java/com/ps/domain/admin/controller/AgentHourlyReportController.java`
- `src/main/java/com/ps/domain/admin/controller/RequestLogController.java`
- `src/main/java/com/ps/domain/admin/controller/BetRecordController.java`
- `src/main/java/com/ps/domain/manage/service/*Report*`
- `src/main/resources/mapper/AllDailyReportMapper.xml`
- `src/main/resources/mapper/AllHourlyReportMapper.xml`
- `src/main/resources/mapper/BetRecordMapper.xml`
- `src/main/resources/mapper/RequestLogMapper.xml`

Nick / `10gt12nc` evidence:

- `2c9f48d`: `feat(#444): 超级代理 squash`
- `7d90e57`: 商戶玩家列表 / 盈虧 / 每日報表
- `c226fed`: request log / 投注記錄
- `5cf02b1`: 每日報表查詢修正

Senior 價值:

- 多租戶 scope / role filter。
- 分表 / partition query。
- report source of truth vs transaction source of truth。
- time range / timezone / paging / performance。

不可誇大:

- 不寫完整代理佣金 owner。
- 不寫完整 BI / report platform owner。

Step 2 初步判斷: 中優先。可作 supporting case；若已經有 game-job report projection，這條可能不如 MQ / whitelist / risk monitor。

### 8. `activity-reward-report-and-voucher-monitor`

中文名稱: 活動獎勵 / voucher 報表與超額監控

業務目的:

- 後台查活動獎勵、voucher total bet/win、活動費與活動 RTP。
- Quartz / monitor service 檢查 voucher 超額，產生 monitor alerts。

主要 code path:

- `src/main/java/com/ps/domain/admin/controller/ActivityConfController.java`
- `src/main/java/com/ps/domain/admin/controller/BetVouchersController.java`
- `src/main/java/com/ps/domain/admin/service/BetVouchersService.java`
- `src/main/java/com/ps/quartz/job/service/MonitorBetVoucherExceedService.java`
- `src/main/resources/mapper/BetVouchersMapper.xml`

Nick / `10gt12nc` evidence:

- `f338fc2` / `ed4f153` / `6795199`: 每日報表新增活動費與 job。
- `4579590` / `bcb4f9a` / `0fc3cd1`: 商戶活動獎勵列表修正。
- `80391be`: 除 0 報錯修正。

Senior 價值:

- reward / voucher report correctness。
- aggregation / amount / currency / divide-by-zero 風險。
- monitor threshold / alert duplicate。

不可誇大:

- 不寫完整 reward platform owner。
- 不寫完整 activity economy owner。

Step 2 初步判斷: 中優先。已有 `antplay-slot-game-job/activity-accumulated-bet-voucher` supporting flow，這條若做可補 admin / report side。

## Step 2 前的候選排序假設

這不是 Step 2 結論，只是 Step 1 的初步排序，正式排序要等 Step 2 比較 evidence、風險與邊界:

1. `request-log-rabbitmq-admin-consumer`
2. `risk-monitor-alert-rabbitmq`
3. `game-api-whitelist-sync`
4. `rtp-darkpool-risk-monitor`
5. `player-control-admin-and-mq-projection`
6. `admin-merchant-auth-rbac-scope`
7. `super-agent-report-query-scope`
8. `activity-reward-report-and-voucher-monitor`

## 不列入本批代表 flow

| 項目 | 原因 |
| --- | --- |
| 官網 / 前端入口 | 不是本 repo，且履歷主線較弱 |
| 純 config / environment 檔 | 可能含敏感設定，不作 KB 長期素材 |
| `target/` / `temp/logs/` | build artifact / local log，不納入 KB |
| 單純 controller CRUD | 除非能連到 runtime、MQ、Redis、Quartz 或風控 failure window，否則不作主候選 |
| `arnold` 後續 security / operation fixes | 可作 current behavior context，但不是 Nick direct evidence |

## Relationship Check

- `README.md`: 需要更新 Flow Track 狀態為 Step 1 已完成，下一步 Step 2。
- `projects/antplay/README.md`: 需要更新 antplay-slot-admin-api 狀態。
- `projects/source-repo-flow-audit.md`: 需要把「只有 contribution consolidation，沒有 Step 1」改成 Step 1 已完成。
- `projects/source-repo-inventory.md`: 需要同步 Step 1 狀態。
- `senior-owner-playbook/06-todo.md`: 需要同步下一步從 Step 1 / Step 2 改成 Step 2。
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`: 需要同步 Flow Track Step 1 狀態。
- `05 / 08 / 04 / 17`: 本輪不更新，因為 Step 1 只是候選 flow 盤點，不是單條 flow claim gate 或 project contribution refresh。

## Suggested Next

`antplay-slot-admin-api` 已完成 Step 1。依 KB 防跳 Step 規則，下一步只能做 Step 2，比較本批候選 flows 的技術點、風險、證據強度、module / repo / service 邊界，決定哪一條進 Step 3。

```text
antplay antplay-slot-admin-api Step 2
```
