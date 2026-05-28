# antplay-slot-admin-api Step 2 Flow Comparison

日期: 2026-05-28

## Step 2 結論

本輪比較 `antplay-slot-admin-api Step 1` 的候選 flows，目標不是平均整理後台功能，而是選出最值得進 Step 3 的 production flow。

Step 2 排序結論:

1. `request-log-rabbitmq-admin-consumer`
2. `game-api-whitelist-sync`
3. `risk-monitor-alert-rabbitmq`
4. `rtp-darkpool-risk-monitor`
5. `player-control-admin-and-mq-projection`
6. `admin-merchant-auth-rbac-scope`
7. `super-agent-report-query-scope`
8. `activity-reward-report-and-voucher-monitor`

第一條進 Step 3 的 flow 選定:

```text
request-log-rabbitmq-admin-consumer
```

理由:

- Nick / `10gt12nc` direct commits 明確，包含 `#774` RequestLog 改 RabbitMQ 非同步、G2 / UAT 修正與 request log mapper / listener path。
- code path 小而完整，能從 RabbitMQ consumer、JSON payload、duplicate check、`@UseSchema`、transaction insert、request log table 一路講清楚。
- 可和既有 `antplay-slot-game-api/request-log-rabbitmq-async` producer case 組成 producer / consumer 兩側故事，補足 AntPlay 非同步 audit / request log 的跨 repo 觀點。
- 面試價值集中在 event-driven ingestion、idempotency、poison message、audit table correctness、failure observability；比純後台 CRUD 更容易對標 Senior Backend。
- 履歷不需要立刻更新 `05 / 08`。本 Step 只決定 Flow Track 順序；要等單條 flow Step 5 或 project-level refresh 才回填履歷。

## 掃描深度

- 本輪深度: Level 1.5 / Step 2 comparison。
- 目的: 比較候選 flows 的 evidence、風險、上下游邊界、面試價值與履歷邊界，選出第一條進 Step 3 的 flow。
- 未做: 單條 flow Level 2 深掃、逐檔逐行 Level 3、下游 repo 最新遠端確認。

## Source Repo 狀態

來源 repo:

```text
/Users/nick/Git/antplay/antplay-slot-admin-api
```

遠端 refs:

- 已嘗試 `git fetch --all --prune` 一次。
- 因內網 remote 目前不可達，未能更新 remote refs。
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

本次重讀:

- vault KB: `AGENTS.md`、`00-operating-rules.md`、`09-ai-prompt-manual.md`、`03-flow-learning-package-template.md`
- project files: `README.md`、`step1-candidate-flows.md`、`contribution-claim-consolidation.md`
- source code: request log MQ、monitor alert MQ、Game API whitelist、player control、monitor / Quartz 主要入口與 path-specific history

未重讀 / 未確認:

- 未重新深掃 `antplay-slot-game-api`；只用既有 KB 認定 request log producer case 可形成對照。
- 未逐檔逐行掃完整 admin-api。
- 未確認內網遠端最新狀態。

## 比較維度

| 維度 | 說明 |
| --- | --- |
| Evidence 強度 | Nick / `10gt12nc` direct commits、path-specific history、是否能支撐真實開發過 |
| Production flow 價值 | 是否涉及 MQ、Redis、DB、Quartz、runtime control plane、audit / risk / report correctness |
| 面試可講性 | 是否能講 state、failure window、idempotency、retry / compensation、observability |
| 邊界乾淨度 | 是否能清楚說「我做過 / code-backed / 不能誇大」 |
| 與既有證據包互補 | 是否補 AntPlay / UGSoft / iwin 既有 cases 的缺口，而不是重複低價值素材 |

## Candidate Flow 比較

### 1. `request-log-rabbitmq-admin-consumer`

中文名稱: RequestLog RabbitMQ 非同步入庫 / audit consumer

主要 code path:

- `src/main/java/com/ps/domain/admin/rabbitMq/RequestLogListener.java`
- `src/main/java/com/ps/domain/admin/rabbitMq/RequestLogListenerProcessor.java`
- `src/main/resources/mapper/RequestLogMapper.xml`
- `src/main/java/com/ps/config/RabbitMQConfig.java`

Nick / `10gt12nc` direct evidence:

- `814b024` / `a66007d`: `feat(#774):RequestLog改丢rabbitmq非同步执行`
- `f3a9d72` / `5f838f8`: G2 request log MQ 調整
- `fa86544`: UAT config 修正
- path-specific log 另掃到 `bce581d` MyBatis 導入 supporting context

已確認 code 行為:

- `RequestLogListener` 監聽 `RabbitMq.REQUEST_LOG_QUEUE_KEY`。
- consumer 解析 JSON message，組出 `RequestLog`。
- 依 `time` 算 `ptDay`。
- 先用 `queryRequestLogExists` 查是否已存在，再用 `insertRequestLog` 寫入。
- insert 包在 `@Transactional` 與 `@UseSchema` 下。
- exception 目前以 log error 收斂；Step 3 要再確認 ack / retry / DLQ 行為與 Rabbit listener container 設定。

Senior / Owner 價值:

- 非同步 audit ingestion，不是單純 CRUD。
- 可講 duplicate check 與 idempotency 風險: query-then-insert 不是天然原子，需看 DB key / transaction isolation。
- 可講 poison message / parse error / consumer failure / ack timing / log-only 失敗窗口。
- 可和 game-api producer case 連成跨 repo event flow。

邊界:

- 可說真實開發過 + code-backed。
- 不說完整 RabbitMQ platform owner、exactly-once、outbox 或完整交易一致性。
- request log 是 audit / troubleshooting table，不是下注結算 source of truth。

Step 2 判斷: Rank 1。第一條進 Step 3。

### 2. `game-api-whitelist-sync`

中文名稱: Game API 白名單控制面 / DB + Redis 同步

主要 code path:

- `src/main/java/com/ps/domain/admin/controller/GameApiWhiteIpController.java`
- `src/main/java/com/ps/domain/admin/service/impl/GameApiWhiteIpServiceImpl.java`
- `src/main/java/com/ps/domain/admin/mapper/GameApiWhiteIpMapper.java`
- `src/main/resources/mapper/GameApiWhiteIpMapper.xml`
- Redis key: `RedisKey.GAME_API_WHITE_IP`

Nick / `10gt12nc` direct evidence:

- `f90a1eb`: `feat(#682): slot-game-api API白名单功能`
- `6cf3855`: delete / deleteRedis
- `4c86d96`: `agentId` 前端必傳
- `1dd366c`: status 小寫

已確認 code 行為:

- 後台可新增、查詢、刪除 Game API 白名單。
- 新增時先查同 agent / IP 是否重複，寫 DB 後寫 Redis set。
- 刪除時先查 IP，再刪 DB 與 Redis set。
- `sync_ss00172r001` 會把特定 merchant 的 source IPs 同步到符合 prefix 的 agent，流程包含 Redis delete / add 與 DB delete / batch insert。
- controller 受 `RoleFilter` 與 limited admin 限制，並寫 admin operation log。

Senior / Owner 價值:

- 後台 control plane 直接影響 runtime access-control。
- 可講 DB / Redis 雙寫一致性、batch sync 部分成功、operation log、limited admin、角色 scope。
- 可和 UGSoft `game-api-provider-white-ip-control-plane` 對照，形成跨專案 control plane 經驗。

邊界:

- 可說真實開發過 + code-backed。
- 下游 runtime filter / game-api 使用 Redis 的實際判斷未在本輪深掃；不能說完整 gateway / security platform owner。

Step 2 判斷: Rank 2。非常值得做，但因已有 UGSoft 類似白名單 case，先讓 RequestLog MQ 補 AntPlay async ingestion 廣度。

### 3. `risk-monitor-alert-rabbitmq`

中文名稱: 風控通知 RabbitMQ consumer / monitor alert 入庫

主要 code path:

- `src/main/java/com/ps/domain/admin/rabbitMq/createMonitorAlertListener.java`
- `src/main/java/com/ps/domain/manage/data/repository/MonitorAlertMapper.java`
- `src/main/resources/mapper/MonitorAlertMapper.xml`
- `src/main/java/com/ps/quartz/job/service/MonitorAlertService.java`
- `src/main/java/com/ps/config/RabbitMQConfig.java`

Nick / `10gt12nc` direct evidence:

- `250811a`: `feat(#775): rabbitmq 收风控通知`
- `44d1f84`: queue name 調整
- `05156ef` / `8be68ca`: UAT / config 報錯修正

已確認 code 行為:

- consumer 監聽 `RabbitMq.MONITOR_ALERT_QUEUE_KEY`。
- JSON payload 取 `agentId`、`agentName`、`type`、`message`、`alertLevel`。
- 呼叫 `MonitorAlertService.createMonitorAlert` 建 entity 後單筆 insert。
- exception 目前以 log error 收斂。

Senior / Owner 價值:

- 風控 event -> monitor alert DB。
- 可講 alert duplicate、schema validation、consumer failure、告警資料是否等於風控 truth。
- 與 request log MQ 類似，可作第二條 MQ consumer case 或合併比較。

邊界:

- 可說真實開發過 + code-backed。
- 不說完整風控平台、完整 observability platform 或完整 alert architecture owner。

Step 2 判斷: Rank 3。技術型態和 Rank 1 很接近，先做 Rank 1；本 flow 可在 Rank 1 Step 3 / Step 4 當對照或後續第二條 MQ case。

### 4. `rtp-darkpool-risk-monitor`

中文名稱: RTP / 暗池 / 活動風控監控與 Quartz alert

主要 code path:

- `src/main/java/com/ps/domain/admin/controller/MonitorController.java`
- `src/main/java/com/ps/quartz/job/impl/MonitorAgentRedisRtpExceed.java`
- `src/main/java/com/ps/quartz/job/impl/MonitorAgentRtpExceed.java`
- `src/main/java/com/ps/quartz/job/impl/MonitorDarkPoolRecord.java`
- `src/main/java/com/ps/quartz/job/service/*Monitor*Service.java`
- monitor / dark pool 相關 mapper XML

Nick / `10gt12nc` direct evidence:

- `57b101f`: `feat(#659): squash 商户游戏风控概况`
- `c7c0072`: riskLevel 調整
- `1c2af07`: 驗證近 12h RTP 超過 100%
- `208f5e7` / `4e84635`: monitor job supporting evidence
- `1fb630a`: `db_target_rtp` supporting evidence

已確認 code 行為:

- `MonitorController` 有 player RTP exceed 與 agent risk summary API，限制 time range。
- `MonitorAgentRedisRtpExceed` job 會呼叫 monitor service。
- `MonitorAgentRtpExceed` 目前有 team context 顯示暫停執行，需 Step 3 釐清 current behavior。
- `MonitorDarkPoolRecord` job 會比對暗池資料並寫 log。

Senior / Owner 價值:

- 風控 / RTP / dark pool / Quartz / threshold / time window 都很有深度。
- 可講 false positive / false negative、job retry、query window、monitor alert source of truth。

邊界:

- 範圍太大，Step 3 前要先拆清楚是 RTP monitor、dark pool monitor、還是 voucher monitor。
- 不說完整 RTP 策略、slot math、完整風控平台 owner。

Step 2 判斷: Rank 4。很有價值，但不適合第一條，因為範圍較散且部分 current behavior 受 team commits 影響。

### 5. `player-control-admin-and-mq-projection`

中文名稱: 玩家單點控制後台 CRUD + MQ projection

主要 code path:

- `src/main/java/com/ps/domain/admin/controller/PlayerControlController.java`
- `src/main/java/com/ps/domain/admin/service/PlayerControlService.java`
- `src/main/java/com/ps/domain/admin/rabbitMq/PlayerControlListener.java`
- `src/main/resources/mapper/PlayerControlMapper.xml`
- `src/main/java/com/ps/quartz/job/impl/PlayerControlRecordBackup.java`
- `src/main/java/com/ps/quartz/job/impl/PlayerControlRecordBakCleanup.java`

Nick / `10gt12nc` direct evidence:

- `5dc67cf`: `feat(#767): 单点控制，agentPlayer 建立`
- `e12a398`: 使用者不存在時建立

已確認 code 行為:

- controller 建立 / 更新 / 查詢 / 刪除 player control，會清 Redis、寫 operation log、可寫 monitor alert。
- `PlayerControlListener` 監聽 `player_control` queue，收到 message 後用 native SQL update 累加 bet / win，再 insert record。
- comment 提到 record idempotence 依賴 `bet_id` unique key，但本輪尚未確認 DB constraint。

Senior / Owner 價值:

- control plane 影響 runtime player behavior。
- 有 Redis cache invalidation、MQ projection、manual upsert、BigDecimal amount 累加風險。

邊界:

- 後續 `PlayerControlController` / listener 有較多 team commits；Nick direct evidence 相對 Rank 1 / 2 少。
- 不說完整玩家風控策略 owner。

Step 2 判斷: Rank 5。可作後續 deep case，但先補 direct evidence 更強、範圍更乾淨的 Rank 1 / 2。

### 6. `admin-merchant-auth-rbac-scope`

中文名稱: Admin / merchant auth、JWT、RBAC、白名單與 role scope

主要 code path:

- `src/main/java/com/ps/domain/admin/controller/AuthController.java`
- `src/main/java/com/ps/domain/admin/controller/MerchantController.java`
- `src/main/java/com/ps/domain/admin/config/SecurityConfig.java`
- `src/main/java/com/ps/domain/admin/filter/*`
- `src/main/java/com/ps/domain/admin/security/*`
- `src/main/java/com/ps/domain/admin/util/JwtUtil.java`

Nick / `10gt12nc` direct evidence:

- `#171` admin API 初版 / login / logout / refresh
- `aaef730`: JWT payload / API JWT 過期回 401
- `454cdcc`: `RoleTypeFilter` 技術客服
- `e80b613` 與商戶白名單 / merchant login 相關 commits

Senior / Owner 價值:

- 可講 auth / role / merchant scope / white list。
- 履歷可用，但面試較容易被追成完整 security architecture，而 evidence 不適合誇大。

邊界:

- 不說完整 IAM / SSO / security architecture owner。
- 不說完整 RBAC 平台重構。

Step 2 判斷: Rank 6。適合作 supporting / 履歷 context，不作本批第一條主 flow。

### 7. `super-agent-report-query-scope`

中文名稱: 超級代理 / 商戶玩家 / 投注 / request log / 報表查詢

主要 code path:

- `src/main/java/com/ps/domain/admin/controller/MerchantController.java`
- `src/main/java/com/ps/domain/admin/controller/AgentDailyController.java`
- `src/main/java/com/ps/domain/admin/controller/RequestLogController.java`
- `src/main/java/com/ps/domain/admin/controller/BetRecordController.java`
- `src/main/resources/mapper/AllDailyReportMapper.xml`
- `src/main/resources/mapper/BetRecordMapper.xml`
- `src/main/resources/mapper/RequestLogMapper.xml`

Nick / `10gt12nc` direct evidence:

- `2c9f48d`: `feat(#444): 超级代理 squash`
- `5cf02b1`: 每日報表查詢修正
- `e82e94c`: 商戶 request log 時間範圍查詢
- `5bde7d4`: 查詢時間範圍最多 7 天

Senior / Owner 價值:

- 多租戶 scope、查詢限制、分表 / report query、paging / performance。

邊界:

- 多數是後台查詢面；已有 game-job report projection 與其他 report cases，差異化較低。
- 不說完整 BI / report platform owner。

Step 2 判斷: Rank 7。只作 supporting，暫不進本批 Step 3。

### 8. `activity-reward-report-and-voucher-monitor`

中文名稱: 活動獎勵 / voucher 報表與超額監控

主要 code path:

- `src/main/java/com/ps/domain/admin/controller/ActivityConfController.java`
- `src/main/java/com/ps/domain/admin/controller/BetVouchersController.java`
- `src/main/java/com/ps/domain/admin/service/BetVouchersService.java`
- `src/main/java/com/ps/quartz/job/service/MonitorBetVoucherExceedService.java`
- `src/main/resources/mapper/BetVouchersMapper.xml`

Nick / `10gt12nc` direct evidence:

- `f338fc2` / `ed4f153` / `6795199`: 每日報表新增活動費與 job
- `bcb4f9a` / `0fc3cd1`: 商戶活動獎勵列表時間參數
- `4579590`: 商戶活動獎勵列表修正

Senior / Owner 價值:

- voucher / reward report correctness、amount aggregation、divide-by-zero、monitor threshold。

邊界:

- 已有 `antplay-slot-game-job/activity-accumulated-bet-voucher` supporting flow，這條容易重複。
- 不說完整 reward platform / activity economy owner。

Step 2 判斷: Rank 8。暫不建議本批做。

## 本批代表 flow 建議

若 Nick 想把 `antplay-slot-admin-api` 做成可面試的後台 control plane / risk ops 補強，本批最多做三條即可:

1. `request-log-rabbitmq-admin-consumer`
2. `game-api-whitelist-sync`
3. `rtp-darkpool-risk-monitor` 或 `risk-monitor-alert-rabbitmq`

目前第一條先做 `request-log-rabbitmq-admin-consumer`。做完後再依當時履歷缺口決定是否補 `game-api-whitelist-sync`；不要把八條候選都變成必做 backlog。

## Relationship Check

- `README.md`: 已更新 Flow Track 狀態為 `request-log-rabbitmq-admin-consumer Step 4` 已完成，下一步為 Step 5。
- `projects/antplay/README.md`: 已更新 antplay-slot-admin-api 狀態。
- `projects/source-repo-flow-audit.md`: 已同步 Step 4 已完成與下一步 Step 5。
- `projects/source-repo-inventory.md`: 已同步 Step 4 已完成。
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`: 已新增 Step 2 row。
- `senior-owner-playbook/06-todo.md`: 已同步下一步從 Step 4 改成 `request-log-rabbitmq-admin-consumer Step 5`。
- `contribution-claim-consolidation.md`: 已同步 final flow 狀態為「Step 4 已完成 / Step 5 待做」。
- `05 / 08 / 04 / 17`: 本輪不更新，因為 Step 2 只做 Flow Track 排序，不是單條 flow claim gate 或 project contribution refresh。

## Suggested Next

第一條代表 flow 的 Step 4 已完成。下一步應做 Step 5，完成單條 flow claim gate，判斷這個 request log MQ consumer case 能否回填 project-level consolidation / 05 / 08。

```text
antplay antplay-slot-admin-api request-log-rabbitmq-admin-consumer Step 5
```
