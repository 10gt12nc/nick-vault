# Evidence - settle-pool-monitor-darkpool-sync

日期: 2026-05-25

## 1. Source Repo 狀態

| 項目 | 狀態 |
| --- | --- |
| source path | `/Users/nick/Git/antplay/antplay-slot-game-job` |
| local branch | `master` |
| local HEAD | `d847357` |
| local `origin/master` | `d847357` |
| ahead / behind | `0 / 0` |
| source working tree | clean |
| remote refs | 先前同 repo fetch 已因內網不可達失敗；依 KB 不反覆重試 |
| 最新性判斷 | 未確認最新遠端；本 Step 依本地 refs / 本地 working tree 保守分析 |

注意: source remote 是內網環境，vault 不記錄內網 URL / IP / sensitive remote detail。`application.yml` 只作 toggle / topic key evidence，不記錄環境敏感值。

## 2. 本輪掃描範圍

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
- `SettledPoolRepository`
- `SettledPoolRepositoryImpl`
- `SettledPool`
- `RedisKeysUtil`
- `RedisKey`
- `SettlePoolContainer`
- `SettlePoolKeyType`
- `AdditionalInfoVO`
- `PlayerControlServiceImpl`
- `PlayerControlRepository`
- `application.yml` monitor toggle / topic config only

Git history:

- path-specific log for settle pool package / repository / entity / Redis key / `AdditionalInfoVO`。
- selected commit stats for `909170d`, `9929cf2`, `5aceb24`, `854c00d`, `54b2a52`, `d026f37`, `76e5b76`, `fa6d9a0`, `266d730`, `e5b5d83`, `a6fec13`, `a25367b`。
- current blame summary for `SettlePoolMonitorConsumerService`、`MainHandler`、`SyncDbFromRedis`、`GroupSettleTypeRecord`。

未掃 / 待確認:

- 上游 `settled_bets` producer 最新 contract。
- Admin / monitoring 下游。
- live Redis / DB schema / DB unique key / production alert config。
- Kafka listener ack / DLT / retry runtime behavior。
- Level 3 逐檔逐 commit diff。

## 3. Code Evidence

| Area | File / Method | Evidence |
| --- | --- | --- |
| Kafka consumer | `SettlePoolMonitorConsumerService#consumeLatest` | `@KafkaListener(topics = {"settled_bets"}, groupId = "monitor")` |
| Feature toggle | `application.yml` | `spring.kafka.consumer.task.monitor` current config is true |
| Reset sync entry | `syncIfResetFlagPresent` -> `SyncDbFromRedis#syncData` | 每次 event 前檢查 reset flag |
| Grouping | `GroupSettleTypeRecord#groupBySettleType` | normal / activity / player control 分群 |
| Jackpot grouping | `GroupSettleTypeRecord` | normal bet 若是 jackpot game，額外放入 `normalJackpotBatches` |
| Batch calc | `MainHandler#buildBatchDataForBatch` | 累加 batch total bet / total win，並取最早 time 的 `AdditionalInfoVO` |
| Normal / activity write | `MainHandler#handleBatchCommon` | `saveOneIncrement` total bet / win |
| Player control write | `MainHandler#handleBatchPlayerControl` | 寫 diff amount，計算 `totalWin - totalBet` |
| Reset snapshot | `SyncDbFromRedis#getAgentDarkPoolData` 等 | 從 Redis 讀 normal / activity / player control snapshot |
| DB rebuild | `SettledPoolRepositoryImpl#saveAll` | backup current table -> truncate -> insert snapshot |
| DB increment | `SettledPoolRepositoryImpl#saveOneIncrement` | find by key, insert or update value + delta |
| Alert | `MonitorAlertUtil#sendAlert` | `SETTLED_POOL_MAXWIN` / `SETTLED_POOL_DIFF` |

## 4. Commit Evidence

| Commit | Author | Date | Evidence |
| --- | --- | --- | --- |
| `909170d` | Eliot | 2026-01-09 | add settled pool，新增 `SettledPool` / repository / Redis reset key |
| `9929cf2` | Eliot | 2026-01-14 | additional_info from game-api，補 `AdditionalInfoVO` |
| `5aceb24` | Eliot | 2026-01-19 | settle pool func2，補 repository / additional info context |
| `854c00d` | Eliot | 2026-01-20 | fix settled pool monitor err + rabbitmq |
| `54b2a52` | Arnold | 2026-01-22 | 整合 Redis key / function |
| `d026f37` | Arnold | 2026-01-23 | new version un test，加入 container / key type / repository rework |
| `76e5b76` | Arnold | 2026-01-26 | 將程式碼切開，新增 consumer / MainMethod / handler / sync / group key |
| `fa6d9a0` | Arnold | 2026-01-26 | 暫時完成 normal bet |
| `266d730` | Arnold | 2026-01-26 | 修正單點控制 |
| `e5b5d83` | Arnold | 2026-02-03 | 修正同步 issue，影響 `SyncDbFromRedis` |
| `a6fec13` | Arnold | 2026-02-02 | 修正 maxWin notification threshold |
| `a25367b` | Arnold | 2026-02-11 | 即時風控開啟 / cache optimization context |

Nick / `10gt12nc` path-specific result:

- `git log --all --author='10gt12nc|Nick|nick' -- <settle pool paths>` 本輪沒有找到 direct path-specific commit。
- 因此本 flow 不能標成 `真實開發過`，只能標 `專案存在 / code-backed / analysis-first`。

## 5. Blame Summary

| File | Blame 摘要 | Claim 判斷 |
| --- | --- | --- |
| `SettlePoolMonitorConsumerService.java` | Arnold 約 94 lines | current implementation 不是 Nick direct |
| `MainHandler.java` | Arnold 約 400 lines | current implementation 不是 Nick direct |
| `SyncDbFromRedis.java` | Arnold 約 301 lines | current implementation 不是 Nick direct |
| `GroupSettleTypeRecord.java` | Arnold 約 101 lines | current implementation 不是 Nick direct |

## 6. 已確認

- 此 flow 消費 `settled_bets`，groupId 是 `monitor`。
- `monitor` consumer current config 為 true。
- 分群包含 normal spin、normal free、normal jackpot、activity spin、activity free、player control。
- `additional_info` 為 null 時，production 會 log error 並跳過該筆。
- `saveOneIncrement` 沒有 event id / bet id 去重 evidence。
- reset sync 會先刪 reset flag，再 backup / truncate / insert `settled_pool`。
- `AtomicBoolean busy` 存在但 guard 被註解。
- jackpot pool 使用 hao 單位，normal / activity 多為 cent。
- current core code 主要是 Arnold / Eliot。

## 7. 合理推論

- 此 flow 是 monitor / projection，不是交易 source of truth。
- Kafka replay 可能造成 `settled_pool` double count。
- Reset sync 中途失敗時，需要人工或外部 runbook 重設 flag / restore；本 repo 未看到 durable state。
- 若 upstream `additional_info` 缺少 before dark pool snapshot，diff alert 不可靠。
- 因 Nick direct evidence 不足，只能作面試 analysis 素材。

## 8. 待確認

- 上游 `settled_bets` producer 是否保證 `additional_info` 欄位完整。
- `settled_pool` DDL / unique key / backup restore strategy。
- Admin / monitoring 查詢與 alert 消費方式。
- Kafka error handler 對此 consumer exception 的 offset commit behavior。
- 是否有 reconciliation job 從 bet record 重建 settle pool。

## 9. Step 3 Evidence 結論

此 flow 技術價值高，但不是 Nick direct claim。Step 3 應維持:

- 可面試講: code-backed analysis / event projection / consistency。
- 可回填 project-level: 暫不回填正式履歷，只能作 supporting study evidence。
- 不可誇大: 不說真實開發、主導風控、主導 jackpot / player control / dark pool。
