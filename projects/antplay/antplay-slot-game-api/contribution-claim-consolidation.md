# antplay-slot-game-api Contribution Claim Consolidation

日期: 2026-05-20；refresh: 2026-05-21

## 結論

`antplay-slot-game-api` 可以列為 Nick / `10gt12nc` 真實開發過的遊戲 API / slot runtime repo。這份 evidence 比 admin-api 更接近交易主線，因為 direct commits 觸及 `/game/init`、`/game/bet`、transfer wallet、bet record、settle / rollback、request log、RTP / dark pool、player control、分表與 Quartz retry / notify 類流程。

2026-05-21 refresh：本批五條代表 flows 已全部完成 Step 5，包含 `slot-bet-settle-rollback`、`transfer-wallet-money-in-out`、`request-log-rabbitmq-async`、`bet-record-sharding-schema-route`、`runtime-rtp-darkpool-player-control`。因此本檔從 2026-05-20 rolling consolidation 更新為「目前代表 flows 已收斂的 project-level consolidation」。限制是：這仍不是整個 repo Level 3 逐檔逐 commit final，且 source remote refs 因內網 GitLab 連線限制未確認最新，只能依本地 refs / current working tree 保守判斷。

2026-05-21 補充：`slot-bet-settle-rollback` 已完成 Step 5。單條 flow claim gate 確認 Nick / `10gt12nc` 在 `GameFacade` / `GameFlowFacade` / `AgentApiFacade` / `CompensationService` 有 path-specific direct commits，可作本 project-level 履歷 claim 的強化 evidence。限制是：目前本地 `develop` 中 deadlock compensation 的實際 refund / fail 標記呼叫被註解，所以不能把它寫成完整 production compensation / wallet ledger owner。

2026-05-21 補充：`transfer-wallet-money-in-out` 已完成 Step 5。單條 flow claim gate 確認 Nick / `10gt12nc` 在 transfer wallet 分表 / schema route / transaction lookup / `updatePlayerWallet` rows check 有 direct evidence，可作 project-level transfer wallet 維護與 DB / Redis consistency 素材。限制是：transfer wallet API 初版主要不是 Nick，不能寫成主導完整 transfer wallet / ledger / reconciliation owner。

2026-05-21 補充：`request-log-rabbitmq-async` 已完成 Step 5。已追到 game-api `AgentApiFacade#sendRequestLogMq` producer、RabbitMQ exchange / queue / routing key、admin-api `RequestLogListener` consumer 與落庫 SQL，並確認 Nick / `10gt12nc` 在 #774 producer / consumer 兩側都有 direct commits。這可以回填 project-level async audit / observability claim；但不單獨寫成完整 RabbitMQ architecture、exactly-once、outbox、DLQ / retry / alert 或 event platform owner，且 routing key / queue key 最終格式修正屬他人 context evidence。

2026-05-21 補充：`bet-record-sharding-schema-route` 已完成 Step 5。Nick / `10gt12nc` 對 `@UseSchema` / schema route、#167 bet record 分表、db_partition v2、table creator service、bet record query fix 有 direct evidence；可把 high-traffic table governance / partition key 查寫 / schema route 補進 project-level 面試與履歷素材。限制是：不能寫完整 sharding platform owner、production automatic table creation owner，或已完整確認 logical-to-physical live route。

2026-05-21 補充：`runtime-rtp-darkpool-player-control` 已完成 Step 5。已確認 Nick / `10gt12nc` 在 `GameFacade#bet` respin loop、2 分鐘 timeout / transfer wallet refund branch、dark pool `maxWin` / `singleBetDp` / `singleWinDp`、player control setData caller、deadlock / afterBet failure window 有 direct evidence，可回填 project-level game API runtime decision / result acceptance / dark pool failure window 素材。限制是：current `RtpCache` 主體、player control 初版與 jackpot service 主體多為他人，不寫完整 RTP 策略、遊戲數學、dark pool / player control / jackpot platform owner；current develop 中部分 deadlock compensation 呼叫也已被後續註解。

履歷可以保守寫:

> 參與 AntPlay slot 遊戲 API / runtime 開發維護，處理 game init、bet / settle / rollback、轉帳錢包、bet record / request log / transfer transaction 分表、RabbitMQ request log、Quartz 補通知與 RTP / dark pool / player control 關聯修正。

不要寫:

- 主導完整 AntPlay slot platform。
- 主導完整遊戲數學 / RTP 策略。
- 主導完整 wallet / ledger / reconciliation。
- 建立完整 RabbitMQ / Kafka architecture、exactly-once 或 outbox。
- 建立完整 sharding platform 或 production automatic table creation owner。
- 主導完整 dark pool / player control / jackpot platform。
- 完整風控平台 owner、完整報表平台 owner 或量化改善。

## Evidence Summary

| 類別 | 判斷 | Evidence |
| --- | --- | --- |
| 直接開發 | 真實開發過 | `git log --all --author='10gt12nc|Nick|nick'` 有大量 direct commits；`shortlog --all` 顯示 `10gt12nc` 約 151 筆、`nick` 約 3 筆 |
| game runtime | 真實開發過 + code-backed | `GameController`、`GameFacade`、`GameFlowFacade` 有 game init、bet、voucher bet、bet result、settle / rollback、dark pool / RTP、player control |
| transfer wallet | 真實開發過 + code-backed | `TransferBalanceController`、`TransferBalanceFacade`、`TransferBalanceService`、`TransferPlayerWallet` / transaction / order lookup；`transfer-wallet-money-in-out` Step 5 已完成，確認 `54078fe` / `718a207` / transfer table path direct evidence；初版 API 不是 Nick，不寫主導完整 wallet owner |
| compensation / deadlock | 真實開發過 | `54078fe`、`31d7a46`：轉帳錢包 deadlock 補償，涉及 `GameFacade`、`GameFlowFacade`、`CompensationService`、`WalletFlag`、`BetRecordManageService` |
| bet record / sharding | 真實開發過 | `#167` 系列、`Nick-bet_record_daily`、`db_partition` / `db-switch`：bet record 日表 / agent table / request log / transfer table / `@UseSchema` |
| RabbitMQ request log | 真實開發過 + code-backed | `request-log-rabbitmq-async` Step 5 已完成；`d3e0002` / `71fff7b`：RequestLog 改丟 RabbitMQ 非同步執行，涉及 `AgentApiFacade`、`RabbitMQConfig`、`RabbitMqService`、`RequestLogMessageDto`；admin-api consumer 也有 #774 direct evidence；key 格式後續修正屬他人 context |
| slot bet / settle / rollback flow | 真實開發過 + code-backed | `slot-bet-settle-rollback` Step 5 已完成；`a2b2af5`、`54078fe`、`31d7a46`、`d3e0002` 等 direct commits 支撐下注、settle、transfer wallet failure window 與 request log async audit |
| whitelist / auth token | 真實開發過 | `#684` 白名單功能、`auth_token 擋 game_code` 系列，涉及 `WhiteIpFilter`、`PublicAuthFacade`、`AuthTokenService`、`GameFacade` |
| Kafka | 只作歷史嘗試，不作現行 claim | `feat(#kafka): kafka JOB` 後有 `Revert "feat(#kafka): kafka JOB"`，不得寫成現行 Kafka production owner |
| representative flow consolidation | 已 refresh / 非 Level 3 全量 final | Step 1 / Step 2 已完成；本批代表 flows `slot-bet-settle-rollback`、`transfer-wallet-money-in-out`、`request-log-rabbitmq-async`、`bet-record-sharding-schema-route`、`runtime-rtp-darkpool-player-control` 均已 Step 5；本檔已回填五條 flow 的 project-level claim，但仍不是整個 repo Level 3 逐檔逐 commit final |

## Source Scan Record

來源 repo:

```text
/Users/nick/Git/antplay/antplay-slot-game-api
```

遠端狀態:

- 已嘗試執行 `git fetch --all --prune`，但 remote 為內網 Git，當前連線失敗；remote refs 未能更新。
- local branch: `develop`
- local HEAD: `079aa6603b50db3c185e383295ca5966bbe272fb`
- remote HEAD: `origin/master`
- 本機既有 `origin/develop`: `079aa6603b50db3c185e383295ca5966bbe272fb`
- local vs 本機既有 `origin/develop`: `0 / 0`
- 本機既有 `origin/master`: `602421ea702252a8b23759e7e78dc6b62f2fb4b3`
- local vs 本機既有 `origin/master`: `483 / 1`
- source repo 工作樹乾淨。
- 重要限制：因 fetch 失敗，本次只能宣稱「本機既有 refs / code-backed」，不能宣稱已看最新 remote。

本次掃描範圍:

- vault KB: `AGENTS.md`、`00-operating-rules.md`、`09-ai-prompt-manual.md`、`03-flow-learning-package-template.md`
- vault index: project README、domain README、source repo inventory、flow inventory、interview casebook、todo、既有 antplay admin-api claim 邊界
- source git: `git status`、`git rev-parse HEAD`、`git rev-parse origin/develop`、`git rev-list --left-right --count HEAD...origin/develop`、`git shortlog --all`；依 KB 規則，先前內網 fetch 失敗後本輪不反覆重試
- key branches: `origin/develop`、`origin/master`、`origin/transfer_wallet`、`origin/deadlock`、`origin/requestLogUseRabbitmq`、`origin/Nick-bet_record_daily`、`origin/feature/rabbitMq_maintain`、`origin/feature/rabbitMq_maintenance_rtp`
- key commits: `#167` bet record / request log 分表、`#374` money-in-out 失敗回調 job、`#684` 白名單、`#774` request log RabbitMQ、`auth_token`、`db_partition` / `db-switch`、`transfer wallet deadlock compensation`
- source code: `GameController`、`GameFacade`、`GameFlowFacade`、`AgentApiFacade`、`TransferBalanceController`、`TransferBalanceFacade`、`TransferBalanceService`、`BetRecordManageService`、`CompensationService`、Quartz notify / table creator 類路徑

未完成 / 待回填:

- Step 1 / Step 2 已完成，`slot-bet-settle-rollback Step 5`、`transfer-wallet-money-in-out Step 5`、`request-log-rabbitmq-async Step 5`、`bet-record-sharding-schema-route Step 5` 與 `runtime-rtp-darkpool-player-control Step 5` 已完成並已回填本檔。後續若新 flow 補出更強或相反 evidence，要再回填本檔與 05 / 08。
- 未掃 `antplay-slot-game-job`，不能把 game-api claim 擴張成批次 / projection / backup owner。
- 未逐檔逐行 Level 3。

## Important Commit Evidence

### Game init / bet / settle / rollback runtime

已確認 code path:

- `GameController`: `/game/init`、`/game/bet`、`/game/voucher-bet`。
- `GameFacade`: init、main activity、free spin activity、bet orchestration、dark pool / RTP / player control、voucher / bought free spin。
- `GameFlowFacade`: beforeBet、afterBet、settle、cancel、transfer wallet balance mutation、bet record state update。
- `AgentApiFacade`: balance、bet、betSettle、betRollback、第三方 agent callback request log。

可支撐:

- 參與 slot runtime 的 game init、下注、開獎、結算與 rollback 相關維護。
- 面試可講下注前扣款、下注紀錄狀態、開獎後 settle、失敗時 rollback / compensation 的 failure window。

不可誇大:

- 不說完整 slot runtime owner；目前是 rolling claim，尚未做每條 flow Step 3~5。
- 不說完整遊戲數學 owner；math-core / `*-math` 未深掃。

### Transfer wallet / money in-out / compensation

重要 commits:

- `2aeab77` / `0a33ca0` / `0056404` / `06a6871`: `feat(#374): money_inout 失败回调job`。
- `54078fe` / `a2b2af5`: `轉帳錢包 deadlock 補償`。
- `31d7a46`: `deadlock 補償`。
- `99c63ff`、`aaddfdc`、`3531f42`、`eb2573a`: transfer wallet / order lookup / transaction / request log table work。

已確認 code path:

- `TransferBalanceController`: `/public/transfer/balance`、`transfer-in`、`transfer-out`、`transfer-out-all`。
- `TransferBalanceService`: Redis balance、player wallet、transaction、order lookup、DB + Redis balance update。
- `GameFlowFacade#getBeforeBetMoney`、`AgentApiFacade#betSettle`、`CompensationService#refundTMOnDeadlock`。

可支撐:

- 參與轉帳錢包、下注前扣款、結算加錢、deadlock 補償與交易紀錄維護。
- 面試可講 DB / Redis 雙寫、transaction status、reference id 防重、failure window、補償的保守設計。

不可誇大:

- 不說完整 wallet / ledger / reconciliation owner。
- 不說已建立完整對帳系統。

### Bet record / request log / daily table / schema switching

重要 commits:

- `#167` 系列：bet 寫日表、request log 拆表、查詢七天內、agent table / SQL 修正、notify count、timezone / locale。
- `718a207`: `db_partition v2`，大量涉及 bet record、request log、transfer wallet transaction、jackpot、voucher、player control 與 schedule job。
- `5f7edc9` / `69ea70f` / `be07c72` / `fce625c`: `db-switch` / `UseSchema` / `@UseSchema` / job 外部調用。
- `9d14f91` / `18d63b8`: 多路徑 `@UseSchema` 補齊。

可支撐:

- 參與高流量 runtime table partition / schema routing / request log table 的維護。
- 面試可講為什麼 bet record / request log / transfer transaction 需要按日期或 agent 分表，以及 AOP / self-call 導致 schema route 不生效的風險。

不可誇大:

- 不說主導完整 DB sharding architecture；目前 evidence 是多次修正 / 接入 / 維護。

### RabbitMQ request log

重要 commits:

- `d3e0002` / `71fff7b`: `feat(#774): RequestLog改丢rabbitmq非同步执行`。

已確認 code path:

- `RabbitMQConfig`、`RabbitMq`、`RequestLogMessageDto`、`RabbitMqService`、`AgentApiFacade#sendRequestLogMq`。

可支撐:

- 參與第三方 agent API request log 從同步 DB 寫入改成 RabbitMQ 非同步投遞。
- 面試可講 log 非同步化的延遲、失敗、message duplication 與落庫 consumer 邊界。

不可誇大:

- 不說完整 RabbitMQ architecture owner、exactly-once 或 outbox。
- 舊 `KafkaJob` 有 revert，Kafka 不作現行 production claim。

### White IP / auth token / game code guard

重要 commits:

- `670fb30` / `85eb457` / `e9bb03b` / `02559b8`: `#684` 白名單功能、POST body / request wrapper、IP 取得與 filter。
- `e32f8a7` / `1323978` / `0cde389` / `b63c276`: `auth_token 擋 game_code`、跳轉與欄位順序修正。

可支撐:

- 參與 runtime API access control，包含白名單 filter、auth token game code guard 與跳轉防呆。
- 面試可講遊戲啟動 token 不應跨 game 使用、POST body filter 只能讀一次的坑。

不可誇大:

- 不說完整 IAM / gateway security owner。

### RTP / dark pool / player control

重要 commits:

- `948429d` / `e0921e7` / `168f951` / `6d0665c` / `f382d73` / `d2eff9f`: `getTargetRTP` / target RTP 修正。
- `3922cc0` / `def5073`: dark pool 設定讀取相關 revert / 修正。
- `2708045`: `fix ag_player_control`。

已確認 code path:

- `GameFacade#bet`: target RTP、normal / free / activity RTP、jackpot RTP、dark pool force respin、player control、voucher / bought free spin 分支。
- `RtpCache`、`PlayerControlService`、`DarkpoolService`、`JackpotService`。

可支撐:

- 參與 RTP / dark pool / player control 的 runtime 修正與風險處理。
- 面試可講這是高風險計算區，但目前只能以「參與修正 / 維護」口徑使用。

不可誇大:

- 不說完整 RTP 策略、遊戲數學模型或風控 owner。

## Resume Claim

可放履歷:

- 參與 AntPlay slot 遊戲 API / runtime 開發維護，處理 game init、bet / settle / rollback、轉帳錢包、bet record、request log 與 runtime access control。
- 參與轉帳錢包與下注結算的一致性維護，包含 reference id 防重、DB / Redis balance update、transaction / order lookup、deadlock 補償與 Quartz 補通知。
- 參與 bet record / request log / transfer wallet 分表與 schema routing 維護，處理 `@UseSchema`、日表 / agent table、job processor 與查詢窗口。
- 參與 request log RabbitMQ 非同步化與白名單 / auth token / game code guard 相關維護。
- 參與 slot runtime decision flow 相關維護，釐清 RTP / dark pool / player control / jackpot 與 math result contract 在 `/game/bet` 的整合邊界；履歷只寫 game API runtime decision / result acceptance，不寫完整 RTP / math owner。

Step 5 後更精準的單條 flow 口徑:

- 參與 AntPlay slot game API 下注 / 結算主線維護，處理 `/game/bet`、bet record state、single / transfer wallet、provider settle / rollback、request log MQ 與補通知相關一致性議題。
- 參與 transfer wallet 相關維護，處理 `transferReferenceId` 防重、transaction / order lookup、DB wallet / Redis balance、分表與 wallet update failure-window 的 code-backed 分析與改造。
- 參與 provider API request log RabbitMQ 非同步化，將 request / response / error / elapsed time audit 從 game-api 主流程同步落庫改為 MQ producer + admin-api consumer 落庫；可講 audit side effect 解耦、at-least-once + consumer dedupe、routing contract 與 observability loss 邊界。
- 參與 AntPlay slot game API runtime decision flow 維護，處理 `/game/bet` 中 RTP / dark pool / player control / jackpot 與 math result contract 的整合邊界；可講 respin loop、dark pool `maxWin`、timeout refund branch、result acceptance 與 side effect failure window。

可面試講:

- Slot runtime 的一局下注如何從 `/game/bet` 走到 bet record、math result、settle / rollback。
- Transfer wallet 為什麼要同時考慮 Redis balance、DB wallet、transaction row、order lookup、request log。
- Deadlock / DB 更新失敗時，哪些錢已扣、哪些 log 已寫、哪些 callback 已送，補償要怎麼保守處理。
- Bet record / request log 分表如何影響查詢、job、schema routing 與 AOP self-call。
- Request log MQ 化後，延遲落庫 / 重複訊息 / consumer failure 如何界定 owner boundary。
- Runtime decision 如何在 game-api、math module、營運策略與 money correctness 間切責任：RTP cache / dark pool / player control / jackpot 只是 result acceptance guardrail，不等於完整遊戲數學 owner。

不可誇大:

- 不說主導完整 AntPlay slot platform。
- 不說完整遊戲數學 / RTP 策略 owner。
- 不說完整 dark pool / player control / jackpot platform owner。
- 不說完整 wallet / ledger / reconciliation owner。
- 不說 deadlock compensation 已完整落地；本地 `develop` 中實際 refund / fail 標記呼叫目前被註解。
- 不說完整 RabbitMQ / Kafka architecture、exactly-once、outbox 或完整 event-driven platform。
- 不說量化改善或事故修復，除非後續補 production issue / metric。

## 05 / 08 更新口徑

2026-05-21 `rolling resume package` 已回填 05 / 08。建議補入履歷 / 自傳的保守句:

> 參與 AntPlay slot 遊戲 API / runtime 開發維護，處理 game init、bet / settle / rollback、轉帳錢包、bet record / request log / transfer transaction 分表、RabbitMQ request log、Quartz 補通知與 RTP / dark pool / player control 關聯修正。

可作面試 / 自傳延伸句:

> 針對高流量遊戲交易流程，整理並維護 bet record state、wallet consistency、async audit、schema routing 與 runtime decision failure window，支援下注結算與營運稽核的穩定性。

若篇幅有限，可併入現職的「遊戲 API runtime / betting settlement / transfer wallet / async data processing」一段，不需要單獨寫成完整平台 owner。

## Historical Next-Step Status

`antplay-slot-game-api` Career Track 已 refresh；Flow Track 本批代表 flows 已全部 Step 5；05 / 08 rolling 履歷 / 自傳投遞稿已回填。下一步回全域 todo 的 iwin Flow Track。

```text
- 歷史下一步已完成：iwin_gameserver center-http-deposit-withdraw 已完成到 Step 5；目前沒有預設下一步，請以 senior-owner-playbook/01-senior-owner-flow-inventory.md 與 senior-owner-playbook/06-todo.md 為準。
```
