# antplay-slot-game-job Step 1 Candidate Flows

日期: 2026-05-22

## 0. 閱讀定位

本檔是 `antplay-slot-game-job` 的 Flow Track Step 1，用來找出值得進 Step 2 比較的 production flows。這不是 class summary，也不是履歷最終結論。

掃描深度: Level 1 flow scan。

證據層級:

- Project-level: 真實開發過 + code-backed。既有 contribution consolidation 已確認 Nick / `10gt12nc` 在 Kafka consumer、Quartz job、代理玩家報表 projection、activity accumulated bet、big-win notification、report currency / key 修正、db partition / job config 有 direct commits。
- Flow-level: 目前是候選 flow 盤點。每條 flow 是否能放履歷，仍要等 Step 2 / Step 3 / Step 4 / Step 5 逐條補 evidence 與 claim boundary。

本 Step 不更新 `05-resume-master-zh.md`、`08-application-autobiography-zh.md` 或 `17-salary-negotiation.md`。`antplay-slot-game-job` 的履歷口徑已先由 project-level contribution consolidation 支撐；本檔只補 Flow Track 選題。

## 1. 本輪重讀範圍

Vault / KB:

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `projects/CONVENTIONS.md`
- `projects/source-repo-inventory.md`
- `projects/antplay/antplay-slot-game-job/README.md`
- `projects/antplay/antplay-slot-game-job/contribution-claim-consolidation.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/06-todo.md`

Source repo:

- `/Users/nick/Git/antplay/antplay-slot-game-job`
- `KafkaJobApplication`
- `com.ps.domain.kafka.service.*`
- `com.ps.domain.kafka.service.settle_pool.*`
- `com.ps.quartz.job.impl.*`
- `com.ps.quartz.main.*`
- `com.ps.datasource.*`
- `com.ps.domain.kafka.gameData.*`
- path-specific commit log、author summary、remote branch list、主要 consumer / job entry

## 2. Source Repo 狀態

| 項目 | 狀態 |
| --- | --- |
| source path | `/Users/nick/Git/antplay/antplay-slot-game-job` |
| local branch | `master` |
| local HEAD | `d847357` |
| local `origin/master` | `d847357` |
| ahead / behind | `0 / 0` |
| source working tree | clean |
| remote refs | `git fetch --all --prune` 失敗一次；依 KB 不反覆重試 |
| 最新性判斷 | 未確認最新遠端；本 Step 依本地 refs / 本地 working tree 保守分析 |
| Java 掃描量 | `src/main/java` 約 328 個 Java files |
| author 線索 | `10gt12nc` 在 all refs 約 51 commits，`nick` 約 1 commit |

注意: source remote 是內網環境，vault 不記錄內網 URL / IP / sensitive remote detail。

## 3. Module / Service 邊界

| Layer | 主要 code path | 本輪觀察 |
| --- | --- | --- |
| App entry | `KafkaJobApplication` | Spring Boot job / event processing app，不是 game runtime API 主服務 |
| Kafka consumers | `com.ps.domain.kafka.service.*` | 以 `settled_bets` 為主要事件來源，分流到 report projection、big-win notification、activity accumulated bet、settle pool monitor |
| Quartz jobs | `com.ps.quartz.job.impl.*` | report summary、table creation、push / notify compensation、cache refresh、Redis id persist 等排程 |
| Report projection | `ProxyUserDataConsumerService`、`ReportAgentPlayerService`、`ReportAgentPlayerJob`、`ReportAgentPlayerRepository*` | settled bet event 匯入 daily report，再由 Quartz 做 summary / backup / delete |
| Notification | `BigWinConsumerService`、`GameDataCache`、`KafkaProducerService` | 消費 settled bet，判斷大獎後組 push user message；通知不是交易 source of truth |
| Activity / voucher | `ActivityAccumateBetConsumerService`、`BetVoucherUtils` | 依活動設定、agent / player / currency 累積投注，透過 Redis + DB 判斷 voucher 發放 |
| Settle pool / risk | `SettlePoolMonitorConsumerService`、`GroupSettleTypeRecord`、`MainHandler`、`SyncDbFromRedis` | 依 bet type / activity / player control 分群，寫 normal / activity / player control dark pool 類資料；Nick claim 要保守 |
| Data routing / partition | `SchemaAwareDataSource`、`UseSchema`、`CreateBetRecordDailyTable`、`CreateRequestLogDailyTable`、repository impl | 分表 / schema routing 影響 report / job / query path，但本輪只作候選 flow |
| Copied domain / support | `com.ps.domain.game.*`、`com.ps.domain.api.*`、`com.ps.domain.admin.*` | job repo 內有 runtime domain / admin / API 支援類；Step 1 不把它們平均整理成 game-api 成果 |

## 4. 候選 Flow 排序

| Rank | Flow | 中文名稱 | Senior Backend 價值 | Nick evidence | Step 3 成本 | 履歷用途 |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | `proxy-user-data-report-projection` | 代理玩家報表 event projection / summary | 高 | `#386`、`#590`、`#702`、`fix ag_report_player` direct commits；code-backed 強 | 中高 | 可強化 Kafka / Quartz / report projection 主面試題；Step 5 後再回填 project claim |
| 2 | `activity-accumulated-bet-voucher` | 活動累積投注 / voucher 發放 | 高 | `nick` merge `feature/accumate_bet`；current code 由 settled bet + Redis + DB voucher 組成；flow-level direct diff 待追 | 中高 | 可補 activity reward / Redis idempotency / voucher boundary；先不單獨寫履歷 |
| 3 | `big-win-notification` | 中大獎通知 / push user message | 中高 | `#303` direct commits + 後續多人修改；code-backed 清楚 | 中 | 可作 derived notification / async event 面試題；不寫完整 push platform owner |
| 4 | `settle-pool-monitor-darkpool-sync` | settle pool monitor / dark pool Redis -> DB sync | 中高 | current code 高價值，但 2026 後續多為 Arnold / Eliot commits；Nick owner boundary 弱 | 高 | 只作 code-backed 面試候選；不寫完整 settle pool / risk / jackpot owner |
| 5 | `db-partition-job-report-routing` | bet / request / report 分表與 job path routing | 中 | `db_partition v2` direct commit + `@UseSchema` / table job path；多人後續調整 | 中高 | 可作 high-traffic data governance 支線；不單獨寫完整 sharding owner |
| 6 | `kafka-job-foundation-platform-mock-rollback` | Kafka job foundation / platform-mock notification / rollback 嘗試 | 中 | 2024 `kafka JOB` direct commits 很多，但 current production flow 分散 | 中 | 作背景 evidence；不優先成第一條 Step 3 |

## 5. Candidate Flow Detail

### 5.1 `proxy-user-data-report-projection`

白話定位: `settled_bets` 事件進來後，job repo 依 agent / player / day / currency 聚合玩家投注、派彩與 profit，寫入代理玩家報表；Quartz job 再把三天前資料做 summary、backup、delete。

主要 code path:

- `ProxyUserDataConsumerService#consumeProxyUserDataService`
- `ProxyUserDataConsumerService#consumeProxyUserDataServiceInternal`
- `ReportAgentPlayerService`
- `ReportAgentPlayerServiceImpl`
- `ReportAgentPlayerRepositoryCustomImpl`
- `ReportAgentPlayerRepositoryInternal`
- `ReportAgentPlayerJob#doExecute`
- `ReportAgentPlayer`

為什麼值得做:

- 最符合 `antplay-slot-game-job` 的 job / event processing 主軸。
- Nick / `10gt12nc` direct evidence 最集中，包含 `#386` 代理用戶數據、`#590` currency / daily schedule、`#702` key 重複與 `fix ag_report_player`。
- 可講 projection key design、currency 維度、batch insert / update、historical summary、backup / delete、job 重跑與資料重複副作用。

待 Step 2 / Step 3 確認:

- consumer 內 `reportMap` 是 in-memory accumulation，是否有跨 batch / restart / concurrency 風險。
- `agentId + playerId + dataDay + currency` key 是否完全覆蓋報表維度；`#702` key 重複修正要追 diff。
- Quartz summary / backup / delete 的 transaction boundary、失敗後重跑策略與 partial completion 風險。
- 多 agent / multi schema 查詢與 `@UseSchema` 後續修改對 report repository 的影響。

### 5.2 `activity-accumulated-bet-voucher`

白話定位: `settled_bets` event 依活動設定篩選 agent / player / currency / time，將投注額累積到 Redis，達到每日或活動期間門檻後發放 voucher / free spin，並用 Redis + DB 發放紀錄避免重複送。

主要 code path:

- `ActivityAccumateBetConsumerService#consumeSettledBets`
- `ActivityAccumateBetConsumerService#processDailyPlayerBet`
- `ActivityAccumateBetConsumerService#processPeriodPlayerBetCore`
- `BetVoucherUtils`
- `ActivityConfService`
- Redis keys: daily / period accumulate、daily / period zero spin count

為什麼值得做:

- 是 reward / voucher 類 money-adjacent flow，能講 Redis key design、threshold、activity window、currency config、voucher idempotency。
- `nick` 有 `feature/accumate_bet` merge 線索；雖然主要細節 commits 多為其他人，仍值得作 code-backed flow 深挖。
- 可補足「job repo 不只是報表，也會影響玩家可用 bonus / voucher」的面試廣度。

待 Step 2 / Step 3 確認:

- Nick / `10gt12nc` 對此 flow 的 direct path evidence 是否足夠；否則只作 code-backed / analysis 素材。
- Redis increment 與 DB voucher count 查詢之間的 race / duplicate award 風險。
- daily / period / period2 三種規則是否會互相重疊發放。
- `addVoucher` 下游是否有 idempotency key / unique constraint。

### 5.3 `big-win-notification`

白話定位: settled bet event 進來後，判斷玩家總贏分是否達投注額與 voucher bet 的 10 倍，組裝遮罩玩家名稱、遊戲翻譯名稱、幣別與金額，送到 push user topic 做中大獎通知。

主要 code path:

- `BigWinConsumerService#consumeBigWin`
- `BigWinConsumerService#maskPlayerName`
- `GameDataCache`
- `TransGames`
- `KafkaProducerService#sendMessage`
- push user topic config

為什麼值得做:

- Scope 比 report projection 小，適合快速做成 Step 3 / Step 4 面試 case。
- Nick / `10gt12nc` 有 `#303` direct commits，後續多人修正翻譯、currency、玩家名稱、detail issue，可練「我做過初版 / 後續有人接手」的保守 claim。
- 可講 derived event、通知不是交易真相、缺翻譯 fallback、玩家資訊遮罩、金額格式與 push topic failure。

待 Step 2 / Step 3 確認:

- `betIdPersistence.collectBetIdsByAgent` 與 big-win notification 的關係是否只是補助 id collection。
- 後續 Arnold / Gill commits 對 current behavior 的修改，哪些是 Nick direct，哪些是 context evidence。
- Kafka send failure 是否會重試、是否影響 consumer offset commit。

### 5.4 `settle-pool-monitor-darkpool-sync`

白話定位: settled bet event 進來後，依 normal spin / free spin / jackpot / activity / player control 分群，將 total bet / total win / player control 差額等資料累積到 settle pool / dark pool；若 Redis reset flag 出現，先從 Redis sync 現有 pool data 到 DB。

主要 code path:

- `SettlePoolMonitorConsumerService#consumeLatest`
- `SettlePoolMonitorConsumerService#mainExecute`
- `GroupSettleTypeRecord#groupBySettleType`
- `MainHandler`
- `MainMethod`
- `SyncDbFromRedis#syncData`
- `SettledPoolRepository`
- `RedisKeysUtil`

為什麼值得做:

- Senior Backend 價值高，牽涉 event grouping、Redis / DB state、risk / dark pool、activity / player-control / jackpot 分層。
- 可和 game-api 的 `runtime-rtp-darkpool-player-control` 串成「runtime decision -> job projection / monitor」上下游。

保守原因:

- 2026-01 到 2026-02 後續大量 commits 是 Arnold / Eliot，Nick direct evidence 不集中。
- 若做 Step 3，需要把 claim 標為 code-backed / analysis-first，不可寫成 Nick 完整 settle pool / risk / jackpot owner。

待 Step 2 / Step 3 確認:

- reset flag sync 時刪 key、讀 Redis、save DB 之間是否有並發 / partial save 風險。
- `busy` guard 目前註解掉，是否有重入風險。
- group key 是否足以分 normal / activity / player control / jackpot。
- downstream DB 表與 admin / monitoring 如何查這些資料，本輪未掃。

### 5.5 `db-partition-job-report-routing`

白話定位: job repo 內的 bet record、request log、report / jackpot balance repository 會受 schema route / 分表影響，table creation job 負責預建 daily / nine-day 表，避免高流量資料寫入或查詢打到錯表。

主要 code path:

- `UseSchema`
- `SchemaRouteAspect`
- `SchemaAwareDataSource`
- `CreateBetRecordDailyTable`
- `CreateBetRecordNineDayTable`
- `CreateRequestLogDailyTable`
- `CreateRequestLogNineDayTable`
- `BetRecordRepositoryNew`
- `JackpotBalanceRepositoryImpl`

為什麼值得做:

- 能補 high-traffic table governance / schema routing 面試素材。
- Nick / `10gt12nc` 有 `db_partition v2` direct commit，但後續 schema route / table path 也有多人修改，適合 Step 2 評估後當支線。

待 Step 2 / Step 3 確認:

- runtime repository 查詢是否真的走 current schema route。
- table creation job 的 schedule / failure alert / re-run 邏輯。
- 這條 flow 和 `antplay-slot-game-api bet-record-sharding-schema-route` 的重疊邊界。

### 5.6 `kafka-job-foundation-platform-mock-rollback`

白話定位: 早期 `kafka JOB` 系列 commits 建立 Kafka consumer / producer、設定檔、platform-mock notification 與 rollback 嘗試，是 job repo 的 event processing foundation。

主要 code path:

- `KafkaConfig`
- `KafkaProducerService`
- `KafkaController`
- early branch `origin/kafka_Nick` / `origin/kafka_Nick_dockerTest`
- application Kafka config

為什麼暫不優先:

- Nick direct commits 很多，但 current production flow 分散到 report、notification、activity、settle pool。
- Step 3 若只做 foundation，容易變成技術整理而不是 production flow。
- 適合作為其他候選 flow 的背景 evidence，不作第一條代表 flow。

## 6. 暫不優先 Flow

| Flow | 原因 |
| --- | --- |
| `free-spins-earliest-latest-consumer` | 有 consumer code，但目前 Step 1 evidence 不如 report / activity / big-win 集中 |
| `play-time-consumer` | 價值偏統計 / activity supporting，先不優先 |
| `push-user-job` | 可作 notification compensation 支線，但 big-win notification 更容易形成完整 flow |
| `bonus-pool-job` | 可能牽涉 bonus pool / free spin data，但 Nick direct evidence 與 production impact 尚未定位 |
| `auth-admin-api-copied-domain` | repo 內有 admin / API / auth 支援類，但不是本 Step 主要 job production flow |

## 7. 本輪未掃 / 待確認

- 未做 Level 3 逐檔逐行 / 每個 commit diff 深掃。
- 未確認最新遠端 refs；remote fetch 失敗一次後依 KB 停止重試。
- 未掃 live DB schema、Quartz DB schedule config、Kafka topic / consumer group runtime config。
- 未掃 `antplay-slot-game-api` producer 端 `settled_bets` event payload final contract；Step 2 / Step 3 需要補 upstream。
- 未掃 admin / monitoring 下游如何查 report / settle pool / notification。
- 未將任何單條 candidate flow 升級為正式履歷 claim。
- 未更新 `05 / 08 / 17`；本 Step 只作 Flow Track 選題。

## 8. Step 1 結論

`antplay-slot-game-job` 已完成 Flow Track Step 1。最值得進 Step 2 比較的是:

1. `proxy-user-data-report-projection`
2. `activity-accumulated-bet-voucher`
3. `big-win-notification`
4. `settle-pool-monitor-darkpool-sync`
5. `db-partition-job-report-routing`
6. `kafka-job-foundation-platform-mock-rollback`

目前不應直接跳 Step 3。依 KB，下一步必須先做 Step 2，比較 candidate flows 的 Senior Backend 價值、Nick evidence、upstream / downstream 邊界、失敗風險與第一條代表 flow。

```text
antplay antplay-slot-game-job Step 2
```
