# settle-pool-monitor-darkpool-sync

日期: 2026-05-25

## 0. 閱讀定位

- Flow 中文名稱: settle pool monitor / dark pool Redis -> DB sync。
- Flow slug: `settle-pool-monitor-darkpool-sync`。
- 完成狀態: Step 3 Level 2 flow 深掃完成。
- 證據層級: 專案存在 / code-backed / analysis-first。current code 技術價值高，但主路徑 current blame 幾乎是 Arnold，早期基礎有 Eliot；目前沒有 Nick / `10gt12nc` direct path evidence，不能寫成 Nick 真實開發或主導 settle pool / risk / jackpot owner。
- 本 flow 類型: Kafka settlement event -> risk / dark pool monitor projection -> DB / alert。
- 是否只確認到入口: 否，已確認 consumer、分群、handler、DB repository、reset flag sync 與 path-specific history；未掃 admin / monitoring 查詢畫面與 game-api producer 最新 contract。

Source repo 遠端狀態: 先前同 repo `git fetch --all --prune` 已因內網不可達失敗；依 KB 不反覆重試。local `master` / local `origin/master` 都在 `d847357`，ahead / behind `0 / 0`，但未確認最新遠端，本文件依本地 refs / 本地 working tree 保守分析。

## 1. 白話導讀

這條 flow 是把每一批已結算投注 event，整理成「目前各類池子的投注量、派彩金額、玩家控制差額」。

上游 `settled_bets` event 裡有多筆 `BetRecord`。Job 會先把它們依類型分群：一般 spin、free spin、jackpot、活動 spin、活動 free spin、單點玩家控制。每一群會算出 batch total bet / total win，再寫進 `settled_pool`。另外，如果 Redis 出現 reset flag，job 會先從 Redis 讀目前 dark pool key，把 snapshot 同步到 DB。

它不是下注交易 source of truth，也不是完整風控平台。它更像 runtime 後面的 monitor / projection：用結算後資料和 dark pool snapshot 做比對，發現池子差異或 max win 超過預期時送 alert。

壞掉時，最直覺會有幾種風險:

- Kafka event 重送造成 `settled_pool` 重複 increment。
- reset flag 被刪掉後，sync DB 中途失敗，後續不再自動補。
- `settled_pool` 被 truncate / rebuild 時有 partial save 風險。
- `additional_info` 缺失時，該筆 bet 不會納入計算。
- jackpot 單位是 hao，一般池是 cent，單位錯會讓 alert 和 max win 判斷失真。
- player control 差額用 win - bet 累加，若 compare type / success 判斷錯，可能誤判控制完成狀態。

## 2. 初中階 Code 分層對照

| 分層 | Code / 資料 | 作用 | 本輪判斷 |
| --- | --- | --- | --- |
| Event source | Kafka `settled_bets` | 已結算下注批次 | consumer 已確認；producer 最新 contract 未掃 |
| Payload VO | `SettleBetsVo` | 包 `batch_id` 與 bet list | 已確認 |
| Domain model | `BetRecord` + `AdditionalInfoVO` | bet / win / type / jackpot / dark pool before value / player control config | 已確認 |
| Consumer | `SettlePoolMonitorConsumerService#consumeLatest` | 讀 event、先 sync reset flag、再 mainExecute | 已確認 |
| Grouping | `GroupSettleTypeRecord#groupBySettleType` | 依 bet type / buy free / jackpot / activity / player control 分群 | 已確認 |
| Handler | `MainHandler` | 計 batch total bet / win、寫 `settled_pool`、送 alert | 已確認 |
| Key builder | `MainMethod` + `RedisKeysUtil` | 產生 normal / free / jackpot / activity / player-control Redis key | 已確認 |
| Persistence | `SettledPoolRepositoryImpl` | 查 key、insert / increment、reset snapshot saveAll | 已確認 |
| Reset sync | `SyncDbFromRedis#syncData` | Redis reset flag 存在時，從 Redis 讀 snapshot 並重建 DB | 已確認 |
| Alert | `MonitorAlertUtil` | maxWin / darkpool diff alert | 已確認 wrapper call；下游通知未掃 |
| Admin / monitoring | admin 查詢 settled pool | 未掃 | 不作完整後台 / 風控平台 claim |

## 3. 最小架構圖

```text
antplay-slot-game-api / settlement producer (待確認最新)
  -> Kafka topic: settled_bets
  -> SettlePoolMonitorConsumerService
  -> SyncDbFromRedis if antplay:settlePool:reset exists
  -> GroupSettleTypeRecord
  -> MainHandler
  -> SettledPoolRepository
  -> settled_pool / settled_pool_bak
  -> MonitorAlertUtil
```

## 4. 正常流程圖

```text
settled_bets event
  -> parse SettleBetsVo
  -> syncIfResetFlagPresent
  -> read jackpot games
  -> group records by settle type
  -> normal spin/free/jackpot groups
  -> activity spin/free groups
  -> player control groups
  -> calculate batch total bet / win
  -> save increment into settled_pool
  -> compare maxWin / before dark pool
  -> send alert if threshold exceeded
```

## 5. 正常流程逐步說明

1. `SettlePoolMonitorConsumerService` 只有在 `spring.kafka.consumer.task.monitor=true` 時啟用。
2. Listener 消費 `settled_bets`，groupId 是 `monitor`，concurrency 讀 topic4 consumer。
3. Consumer 先把 Kafka value 反序列化成 `SettleBetsVo`。
4. 每次處理前呼叫 `syncIfResetFlagPresent()`；如果 Redis 有 `antplay:settlePool:reset`，`SyncDbFromRedis#syncData` 會啟動 DB 重建流程。
5. 讀 jackpot game set，交給 `GroupSettleTypeRecord` 分群。
6. `GroupSettleTypeRecord` 先 parse `BetRecord#additionalInfo`；如果 `additional_info` 是 null，production 會 log error 並跳過該筆。
7. `BET_TYPE_SINGLE_POINT_CONTROL` 會依 `agentId + currency + account + playerControlConfigId` 分到 player control。
8. `BET_TYPE_ACTIVITY` 會依 `agentId + activityId + game + currency` 分到 activity spin 或 activity free。
9. `BET_TYPE_NORMAL` 會依 `agentId + game + currency` 分到 normal spin / free；如果是 jackpot game，還會額外分到 normal jackpot。
10. `MainHandler` 對每個 group 計算 batch total bet / total win。
11. 一般池會將 total bet / total win increment 到 `settled_pool`；jackpot 會用 jackpot 比例與 hao 單位。
12. Activity 池用 voucher bet 作 total bet，依 activity id / game / currency 建 key。
13. Player control 只寫 diff amount，計算方式是 `batchTotalWin - batchTotalBet`。
14. 寫入後，handler 用 latest DB value、batch value、RTP cache 與 `additional_info` 的 before dark pool value 做 maxWin / diff alert 判斷。
15. 若超過 threshold，呼叫 `MonitorAlertUtil#sendAlert`。

## 6. Reset Flag Sync 流程

```text
Redis key antplay:settlePool:reset exists
  -> delete reset flag first
  -> load enabled agents and games
  -> read normal spin/free/jackpot dark pool keys from Redis
  -> read active activity dark pool keys from Redis
  -> read active player control diff keys from Redis
  -> backup current settled_pool into settled_pool_bak
  -> truncate settled_pool
  -> insert snapshot rows
```

這段是本 flow 的高風險點。它目前先刪 reset flag，再讀 Redis / DB / activity / player control，最後 `saveAll`。`saveAll` 會先 backup、truncate，再逐筆 insert。若中途 exception 或 process crash，需要靠人工重設 reset flag 或外部 runbook 補救；本 repo 未看到 durable sync job state。

## 7. 業務問題

這條 flow 解的是「結算後，營運 / 風控要看到各類池子的累積狀態與異常差異」。

它的價值在於:

- 把 runtime dark pool / activity / jackpot / player control 的資料投影到可查詢 DB。
- 對 max win 可能超出池可承受範圍的情境發 alert。
- 對 settlement pool 與 dark pool before value 差異過大的情境發 alert。
- 讓後續 admin / monitoring 可以看 pool 狀態。

它不是:

- 下注派彩 source of truth。
- 完整 RTP 策略。
- 完整 jackpot owner。
- 完整即時風控平台。

## 8. DB / Redis / MQ / 外部 API

| 類型 | 已確認 | 說明 |
| --- | --- | --- |
| MQ in | Kafka `settled_bets` | monitor consumer 消費 settlement event |
| Redis | normal / free / jackpot / activity / player-control dark pool keys | reset sync 會讀 Redis snapshot |
| Redis | `antplay:settlePool:reset` | reset flag；存在就觸發 sync |
| DB | `settled_pool` | projection / snapshot table |
| DB | `settled_pool_bak` | `saveAll` 會先 backup current table |
| DB | `ag_player_control` | player control active config / success check |
| DB | activity config / agent / game | reset sync enumerates active data |
| Alert | `MonitorAlertUtil` | max win / diff alert |
| External API | 無直接 HTTP | 本 flow 是 Kafka + DB / Redis / alert |

## 9. 資料狀態與 State Transition

```text
BetRecord settled
  -> grouped into settle pool bucket
  -> batch total bet / win calculated
  -> DB row incremented
  -> alert comparison evaluated
```

Reset sync:

```text
Redis dark pool state
  -> reset flag detected
  -> reset flag deleted
  -> current DB backed up
  -> current DB truncated
  -> snapshot rows inserted
```

重要 source of truth 邊界:

- `BetRecord` 是已結算事件輸入。
- Redis dark pool 是 runtime 池子狀態。
- `settled_pool` 是 monitor / projection / snapshot，不是下注交易真相。
- `additional_info.beforeDp*` 是投注前 snapshot，用來做差異檢查；若 upstream 沒填，monitor 無法完整比對。

## 10. Failure Window / Consistency

| 風險 | 現況 | Senior / Owner 解讀 |
| --- | --- | --- |
| Kafka event 重送 | `saveOneIncrement` 以 DB current value 加 batch value，未見 event id 去重 | 重送可能重複累加；需要 batch id / bet id idempotency 或 reconciliation |
| reset flag 先刪 | `syncData` 一開始刪 `SETTLE_POOL_RESET` | 後續失敗可能不再自動 sync；需要 durable sync state 或失敗後重設 flag |
| `saveAll` truncate rebuild | 先 backup、truncate，再 insert | partial insert / crash window 高；需要 transaction 驗證與 restore / retry runbook |
| busy guard 註解 | `AtomicBoolean busy` 存在但 compareAndSet 被註解 | 多 consumer / 多 instance 同時遇到 reset flag 時，依 Redis delete 競態避免部分重入，但不是明確 distributed lock |
| `additional_info` null | production log error 後 return | 該筆不進 pool 計算；需要 upstream contract / missing metric |
| jackpot 單位 | jackpot total bet / win 使用 hao，其他多為 cent | alert / maxWin 比較要很小心，面試不能混用單位 |
| player control success | 超過門檻後只檢查 `isSuccess`，失敗 log error | 未見自動關閉或補償；只能說 monitor / diagnosis，不說完整控制治理 |
| `getOneValue` ignore type | `findByKey` 只依 agentId + key 查，未用 type filter | 若 key 唯一性足夠可行；若 key collision，type 不參與查詢會有風險 |

## 11. Retry / Compensation / Reconciliation

已確認:

- Consumer catch exception 後 log error，不 rethrow。
- `saveOneIncrement` 是 DB insert or update increment，沒有 event-level idempotency。
- Reset sync 有 backup table，但本 repo 未看到 restore flow。
- `SyncDbFromRedis` 對 activity / player control 子段 catch 後繼續，但整體 `saveAll` exception 會被 consumer catch。
- Alert 有 `SETTLED_POOL_MAXWIN` 與 `SETTLED_POOL_DIFF`。

待確認:

- Kafka listener ack / error handler 對此 consumer 的實際 offset commit 行為。
- `settled_pool` 是否有 DB unique key / DDL 和 backup restore runbook。
- Admin / monitoring 如何查 `settled_pool` 與 alert。
- 是否有 reconciliation job 以 bet record 重建 settle pool。
- 上游 `additional_info` 是否保證 before dark pool / jackpot / player control 欄位存在。

Owner 建議:

- 對 event projection 補 deterministic idempotency key，例如 batch id + bet id + pool type。
- Reset sync 不應只靠 delete flag；可增加 sync job id、狀態表、running lock、failure retry。
- `saveAll` truncate 前後要有 row count / checksum / restore path。
- Alert 應補 metric：generated groups、skipped additional_info、increment success、sync rows、sync failure。
- 單位 cent / hao 要在 code 與文件上明確標示。

## 12. Senior / Owner 設計取捨

| 決策點 | 現況 | 好處 | 風險 |
| --- | --- | --- | --- |
| 結算後做 projection | `settled_bets` -> monitor consumer | 不阻塞 runtime bet path | event replay 會影響 projection 正確性 |
| DB increment | `saveOneIncrement` | 寫法直接、可累加 batch | 缺 event idempotency |
| Reset from Redis | reset flag 觸發 snapshot | 可用 runtime Redis 修復 DB projection | sync 本身需要 lock / retry / restore |
| Jackpot 另拆 | jackpot group 額外計算 | 可分 normal pool 與 jackpot pool | 同一 normal bet 可能同時進 normal / jackpot bucket，面試要說清楚 |
| Activity 用 voucher bet | activity pool 用 `voucherBet` | 符合活動投放成本語意 | 若 voucherBet 單位或空值不一致會造成池差 |
| Player control diff | `totalWin - totalBet` | 可看單玩家控制差額 | success / status governance 不在本 flow |

## 13. 面試 / 履歷邊界摘要

可面試講:

- 這是一條 code-backed 的 Kafka settlement projection / dark pool monitor flow。
- 我能讀懂它如何把 normal / free / jackpot / activity / player control 分群，如何累加 `settled_pool`，以及 reset flag 如何從 Redis rebuild DB。
- Senior 重點是 event replay idempotency、reset sync partial failure、Redis / DB consistency、cent / hao 單位、alert 與 runbook。

履歷要保守:

- 本 flow 不建議單獨寫入正式履歷。
- 可作 `antplay-slot-game-job` project-level 「settle pool / dark pool monitor code-backed analysis」補充素材。
- 目前不能說 Nick 真實開發此 flow。

不能誇大:

- 不說主導完整 settle pool / dark pool / risk platform。
- 不說主導完整 jackpot owner。
- 不說已建立可靠 replay / idempotency / reconciliation。
- 不把 Arnold / Eliot 的 commits 說成 Nick direct contribution。
- 不直接更新 `05 / 08`。

## 14. 本次實際掃描範圍

Vault:

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `projects/antplay/antplay-slot-game-job/README.md`
- `projects/antplay/antplay-slot-game-job/step1-candidate-flows.md`
- `projects/antplay/antplay-slot-game-job/step2-flow-comparison.md`
- `projects/antplay/antplay-slot-game-job/contribution-claim-consolidation.md`
- `projects/antplay/antplay-slot-game-api/flows/runtime-rtp-darkpool-player-control/*` 作上下游背景參考

Source code:

- `SettlePoolMonitorConsumerService`
- `GroupSettleTypeRecord`
- `MainHandler`
- `MainMethod`
- `SyncDbFromRedis`
- `SettledPoolRepository` / `SettledPoolRepositoryImpl`
- `SettledPool`
- `RedisKeysUtil` / `RedisKey`
- `SettlePoolContainer` / `SettlePoolKeyType`
- `AdditionalInfoVO`
- `PlayerControlServiceImpl` / `PlayerControlRepository`
- `application.yml` monitor toggle / topic config only

Git history:

- Path-specific log for settle pool package, `SettledPoolRepositoryImpl`, `SettledPool`, Redis key utilities, `AdditionalInfoVO`。
- Selected important commits: `909170d`, `9929cf2`, `5aceb24`, `854c00d`, `54b2a52`, `d026f37`, `76e5b76`, `fa6d9a0`, `266d730`, `e5b5d83`, `a6fec13`, `a25367b`。
- Current blame summary: core settle pool files are Arnold-heavy; early base includes Eliot; no Nick / `10gt12nc` direct path-specific evidence found.

未掃 / 待確認:

- 上游 `settled_bets` producer 最新 contract。
- Admin / monitoring 查詢 `settled_pool` 的下游。
- live Redis / DB schema / DB unique key / production alert config。
- Kafka listener ack / DLT / retry runtime behavior。
- Level 3 逐檔逐 commit diff。

## 15. Step 3 結論

這條 flow 已完成 Step 3 學習包。它是 AntPlay job repo 裡技術含量高的 code-backed flow，可用來練 Senior Backend 對 event projection、Redis / DB consistency、reset sync、alert、risk monitor 的分析能力。

但 claim boundary 必須非常保守：current implementation 主要是 Arnold / Eliot，不是 Nick direct evidence。它可以當面試中的「我分析過 / 能講清楚」素材，不能當履歷主成果或主導經驗。

下一步應做 Step 4，把這條整理成正式面試 case，並持續維持 analysis-first 邊界。

```text
antplay antplay-slot-game-job settle-pool-monitor-darkpool-sync Step 4
```
