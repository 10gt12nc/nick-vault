# antplay-slot-game-api Contribution Claim Consolidation

日期: 2026-05-20

## 結論

`antplay-slot-game-api` 可以列為 Nick / `10gt12nc` 真實開發過的遊戲 API / slot runtime repo。這份 evidence 比 admin-api 更接近交易主線，因為 direct commits 觸及 `/game/init`、`/game/bet`、transfer wallet、bet record、settle / rollback、request log、RTP / dark pool、player control、分表與 Quartz retry / notify 類流程。

2026-05-21 補充：`slot-bet-settle-rollback` 已完成 Step 5。單條 flow claim gate 確認 Nick / `10gt12nc` 在 `GameFacade` / `GameFlowFacade` / `AgentApiFacade` / `CompensationService` 有 path-specific direct commits，可作本 project-level 履歷 claim 的強化 evidence。限制是：目前本地 `develop` 中 deadlock compensation 的實際 refund / fail 標記呼叫被註解，所以不能把它寫成完整 production compensation / wallet ledger owner。

2026-05-21 補充：`transfer-wallet-money-in-out` 已完成 Step 5。單條 flow claim gate 確認 Nick / `10gt12nc` 在 transfer wallet 分表 / schema route / transaction lookup / `updatePlayerWallet` rows check 有 direct evidence，可作 project-level transfer wallet 維護與 DB / Redis consistency 素材。限制是：transfer wallet API 初版主要不是 Nick，不能寫成主導完整 transfer wallet / ledger / reconciliation owner。

履歷可以保守寫:

> 參與 AntPlay slot 遊戲 API / runtime 開發維護，處理 game init、bet / settle / rollback、轉帳錢包、bet record 分表、RabbitMQ request log、Quartz 補通知與 RTP / dark pool / player control 關聯修正。

不要寫:

- 主導完整 AntPlay slot platform。
- 主導完整遊戲數學 / RTP 策略。
- 主導完整 wallet / ledger / reconciliation。
- 建立完整 RabbitMQ / Kafka architecture、exactly-once 或 outbox。
- 完整風控平台 owner、完整報表平台 owner 或量化改善。

## Evidence Summary

| 類別 | 判斷 | Evidence |
| --- | --- | --- |
| 直接開發 | 真實開發過 | `git log --all --author='10gt12nc|Nick|nick'` 有大量 direct commits；`shortlog --all` 顯示 `10gt12nc` 約 151 筆、`nick` 約 3 筆 |
| game runtime | 真實開發過 + code-backed | `GameController`、`GameFacade`、`GameFlowFacade` 有 game init、bet、voucher bet、bet result、settle / rollback、dark pool / RTP、player control |
| transfer wallet | 真實開發過 + code-backed | `TransferBalanceController`、`TransferBalanceFacade`、`TransferBalanceService`、`TransferPlayerWallet` / transaction / order lookup；`transfer-wallet-money-in-out` Step 5 已完成，確認 `54078fe` / `718a207` / transfer table path direct evidence；初版 API 不是 Nick，不寫主導完整 wallet owner |
| compensation / deadlock | 真實開發過 | `54078fe`、`31d7a46`：轉帳錢包 deadlock 補償，涉及 `GameFacade`、`GameFlowFacade`、`CompensationService`、`WalletFlag`、`BetRecordManageService` |
| bet record / sharding | 真實開發過 | `#167` 系列、`Nick-bet_record_daily`、`db_partition` / `db-switch`：bet record 日表 / agent table / request log / transfer table / `@UseSchema` |
| RabbitMQ request log | 真實開發過 | `d3e0002` / `71fff7b`：RequestLog 改丟 RabbitMQ 非同步執行，涉及 `AgentApiFacade`、`RabbitMQConfig`、`RabbitMqService`、`RequestLogMessageDto` |
| slot bet / settle / rollback flow | 真實開發過 + code-backed | `slot-bet-settle-rollback` Step 5 已完成；`a2b2af5`、`54078fe`、`31d7a46`、`d3e0002` 等 direct commits 支撐下注、settle、transfer wallet failure window 與 request log async audit |
| whitelist / auth token | 真實開發過 | `#684` 白名單功能、`auth_token 擋 game_code` 系列，涉及 `WhiteIpFilter`、`PublicAuthFacade`、`AuthTokenService`、`GameFacade` |
| Kafka | 只作歷史嘗試，不作現行 claim | `feat(#kafka): kafka JOB` 後有 `Revert "feat(#kafka): kafka JOB"`，不得寫成現行 Kafka production owner |
| final 全量 flow | 部分完成 / 待補 | Step 1 / Step 2 已完成，第一條代表 flow `slot-bet-settle-rollback` 已完成 Step 5；其餘 candidate flows 尚未深掃，本檔仍是 rolling consolidation |

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
- vault index: source repo inventory、flow inventory、05 / 08 / todo、既有 antplay admin-api claim 邊界
- source git: `git log --all`、`shortlog --all`、remote branch list、branch-specific log、key commit stats
- key branches: `origin/develop`、`origin/master`、`origin/transfer_wallet`、`origin/deadlock`、`origin/requestLogUseRabbitmq`、`origin/Nick-bet_record_daily`、`origin/feature/rabbitMq_maintain`、`origin/feature/rabbitMq_maintenance_rtp`
- key commits: `#167` bet record / request log 分表、`#374` money-in-out 失敗回調 job、`#684` 白名單、`#774` request log RabbitMQ、`auth_token`、`db_partition` / `db-switch`、`transfer wallet deadlock compensation`
- source code: `GameController`、`GameFacade`、`GameFlowFacade`、`AgentApiFacade`、`TransferBalanceController`、`TransferBalanceFacade`、`TransferBalanceService`、`BetRecordManageService`、`CompensationService`、Quartz notify / table creator 類路徑

未完成 / 待回填:

- Step 1 / Step 2 已完成，`slot-bet-settle-rollback Step 5` 與 `transfer-wallet-money-in-out Step 5` 已完成；其餘 candidate flows 尚未逐條建立 `flow.md` / `career-interview.md`。
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

Step 5 後更精準的單條 flow 口徑:

- 參與 AntPlay slot game API 下注 / 結算主線維護，處理 `/game/bet`、bet record state、single / transfer wallet、provider settle / rollback、request log MQ 與補通知相關一致性議題。
- 參與 transfer wallet 相關維護，處理 `transferReferenceId` 防重、transaction / order lookup、DB wallet / Redis balance、分表與 wallet update failure-window 的 code-backed 分析與改造。

可面試講:

- Slot runtime 的一局下注如何從 `/game/bet` 走到 bet record、math result、settle / rollback。
- Transfer wallet 為什麼要同時考慮 Redis balance、DB wallet、transaction row、order lookup、request log。
- Deadlock / DB 更新失敗時，哪些錢已扣、哪些 log 已寫、哪些 callback 已送，補償要怎麼保守處理。
- Bet record / request log 分表如何影響查詢、job、schema routing 與 AOP self-call。
- Request log MQ 化後，延遲落庫 / 重複訊息 / consumer failure 如何界定 owner boundary。

不可誇大:

- 不說主導完整 AntPlay slot platform。
- 不說完整遊戲數學 / RTP 策略 owner。
- 不說完整 wallet / ledger / reconciliation owner。
- 不說 deadlock compensation 已完整落地；本地 `develop` 中實際 refund / fail 標記呼叫目前被註解。
- 不說完整 RabbitMQ / Kafka architecture、exactly-once、outbox 或完整 event-driven platform。
- 不說量化改善或事故修復，除非後續補 production issue / metric。

## 05 / 08 更新口徑

建議補入履歷 / 自傳的保守句:

> 參與 AntPlay slot 遊戲 API / runtime 開發維護，處理 game init、bet / settle / rollback、轉帳錢包、bet record 分表、RabbitMQ request log、Quartz 補通知與 RTP / dark pool / player control 關聯修正。

若篇幅有限，可併入現職的「遊戲 API runtime / betting settlement / transfer wallet / async data processing」一段，不需要單獨寫成完整平台 owner。

## Suggested Next

`antplay-slot-game-api` 的 Career Track 已能保守補履歷；Flow Track 已完成 Step 1 / Step 2、`slot-bet-settle-rollback Step 5` 與 `transfer-wallet-money-in-out Step 5`。下一步若繼續本 repo，應依 Step 2 ranking 做 `request-log-rabbitmq-async Step 3`，補 async audit / observability case。

```text
antplay antplay-slot-game-api request-log-rabbitmq-async Step 3
```
