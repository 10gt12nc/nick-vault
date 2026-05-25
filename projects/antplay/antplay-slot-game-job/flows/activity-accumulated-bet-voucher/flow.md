# activity-accumulated-bet-voucher

日期: 2026-05-25

## 0. 閱讀定位

- Flow 中文名稱: 活動累計投注送 voucher / free spin。
- Flow slug: `activity-accumulated-bet-voucher`。
- 完成狀態: Step 5 claim gate 完成。
- 證據層級: 專案存在 / code-backed + Nick merge evidence；`nick` 有 `feature/accumate_bet` merge evidence，但 current code 主要由 Gill 開發、Arnold / Eliot 後續調整，不能標成 Nick 主開發。
- 本 flow 類型: Kafka event consumer + Redis accumulate + DB voucher issuing。
- 是否只確認到入口: 否，已確認 consumer、activity config、Redis key、voucher utility、Kafka toggle 與主要 git history；下游 `BetVoucherService` / `ActivityConfService` 來自外部 lib，本 repo 只確認呼叫邊界。

Source repo 遠端狀態: 先前同 repo `git fetch --all --prune` 已因內網不可達失敗；依 KB 不反覆重試。local `master` / local `origin/master` 都在 `d847357`，ahead / behind `0 / 0`，但未確認最新遠端，本文件依本地 refs / 本地 working tree 保守分析。

## 1. 白話導讀

這條 flow 的目的，是把玩家結算後的下注金額拿來計算活動資格。玩家在活動期間下注，系統會依代理、玩家、幣別、活動設定，把投注額累加到 Redis；如果達到活動設定的門檻，就透過 `BetVoucherService` 發 voucher / free spin。

它不是下注主交易，也不是錢包 source of truth。它是 settlement event 之後的 reward projection。成功後，玩家可能拿到每日一次的獎勵，或依活動期間累積投注倍數拿到多次獎勵。失敗時，最直覺會壞在:

- 活動獎勵沒發。
- Kafka 重送或 Redis / DB 狀態不同步造成重複發。
- 活動時間、代理、幣別或設定不匹配，導致玩家資格判斷錯。
- Redis awarded count 與 DB voucher count 不一致。

## 2. 初中階 Code 分層對照

| 分層 | Code / 資料 | 作用 | 本輪判斷 |
| --- | --- | --- | --- |
| Event source | Kafka `settled_bets` | 結算後下注批次事件 | producer 端未完整掃，沿用上游 event source |
| Payload VO | `SettleBetsVo` | 包 `batch_id` 與 `List<BetRecord>` | 已確認 |
| Domain model | `BetRecord` | 單筆下注結算資料，含 `agentId / playerId / account / bet / currency / time` | 已確認 |
| Consumer | `ActivityAccumateBetConsumerService` | 消費 settled bets，找活動設定，累加 Redis，達門檻發 voucher | 已確認 |
| Activity config | `ActivityConfService` / `ActivityConf` | 找 type=1 且 status=1 的活動，讀 `params` 判斷 daily / period 規則 | 外部 lib service，呼叫已確認 |
| Redis state | `StringRedisTemplate` | 存每日 / 期間累計投注與已發放次數 | 已確認 |
| Voucher utility | `BetVoucherUtils` / `BetVoucherUtilsExt` | 包一層 `BetVoucherService#addVoucher` 與 DB awarded count 查詢 | 已確認 wrapper；下游 service 實作未在本 repo |
| DB voucher | `BetVoucherService` | 發 voucher / 查已發放數 | 外部 lib，未掃 DB unique key / transaction |
| Kafka config | `application.yml` / `JobOptions` | `spring.kafka.consumer.task.activity-accumate-bet` 控制啟用 | 已確認 current config 為 true |

## 3. 最小架構圖

```text
antplay-slot-game-api / upstream settle
  -> Kafka topic: settled_bets
  -> ActivityAccumateBetConsumerService
  -> ActivityConfService.findActiveByTypeAndStatus(1, 1)
  -> Redis accumulate / awarded count keys
  -> BetVoucherUtils / BetVoucherService
  -> BetVoucher DB
```

## 4. 正常流程圖

```text
settled_bets event
  -> parse SettleBetsVo
  -> load active activity configs type=1 status=1
  -> filter each BetRecord by agent and activity time window
  -> group by playerId + currency + agentId
  -> sum bet amount within same message
  -> for each matched activity:
       daily_bet check
       accumulated_bet_1 check
       accumulated_bet_2 check
  -> increment Redis accumulated bet
  -> compare threshold
  -> add voucher when eligible
  -> update Redis awarded count
```

## 5. 正常流程逐步說明

### 5.1 Kafka consumer

`ActivityAccumateBetConsumerService` 只有在 `spring.kafka.consumer.task.activity-accumate-bet=true` 時啟用。listener 消費 `settled_bets`，groupId 是 `activity`，current code 使用設定中的 topic consumer concurrency。

consumer 會把 `record.value()` 反序列化成 `SettleBetsVo`，再透過 `ActivityConfService.findActiveByTypeAndStatus(1, 1)` 取得開啟中的累計投注活動。若沒有活動，就直接 return。

### 5.2 活動匹配與 event 內聚合

每筆 `BetRecord` 會先過兩個 filter:

- `isAgentIdMatched`: 若 activity 設了 agentId，必須等於 betRecord 的 agentId。
- `isBetTimeWithinActivityPeriod`: bet time 必須在 activity start / end 之間。

通過後，current code 用 `playerId + currency + agentId` 當 group key，把同一 Kafka message 內同玩家 / 同幣別 / 同代理的 bet amount 先累加成 `AccumulatedBetData`。這避免同一批 event 裡多筆 bet record 重複跑多次 Redis / voucher 邏輯。

注意: group key 沒包含 activityId。current code 把第一筆建立 group 時的 `matchedActivities` 存在 `AccumulatedBetData` 裡；若同一玩家同代理同幣別在同一 message 中不同 bet record 對應不同活動集合，是否完全符合預期要再確認。

### 5.3 Daily bet

`processPlayerBet` 處理 `daily_bet`:

1. 用系統當下日期 `yyyyMMdd` 當每日 key 的日期。
2. 從 `activityConf.params` 讀該 currency 的 `daily_bet`。
3. 檢查 `active`、`total_bet`、`send_counts`、`gift_type`。
4. Redis 累計 key: `antplay:Activity:Agent:{agentId}:Id:{activityId}:Daily:Accumulate:{playerId}:{currency}:{today}`。
5. Redis 已發 key: `antplay:Activity:Agent:{agentId}:Id:{activityId}:Daily:ZeroSpin:Count:{playerId}:{currency}:{today}`。
6. 每次都先 increment 累計投注額，再判斷是否已發。
7. 若今天已發，只累計不再發。
8. 若未發且累計投注達門檻，就呼叫 `betVoucherUtils.addVoucher(...)`，再寫 Redis 已發 key，TTL 到明天 0 點。

每日規則的 owner 重點是: 它不是停止累計，而是達標發過後仍繼續累計、但不重複發。這點有 `977318d` 的 commit message 佐證。

### 5.4 Period accumulated bet

`processPeriodPlayerBetCore` 處理 `accumulated_bet_1` 與 `accumulated_bet_2`:

1. 從 `activityConf.params` 讀 currency 下的 `accumulated_bet_1` 或 `accumulated_bet_2`。
2. Redis 累計 key 依 subActivityType 分成 Period / Period2。
3. 第一次建立 key 時設定 TTL 到 activity end。
4. 計算 `thresholdMultiple = periodAccumateBet / periodAccumateBetConf`。
5. Redis awarded count 先算應發次數。
6. 如果 Redis 算出還要發，會再查 DB `BetVoucherService.countByPlayerIdAndActivityIdAndCurrencyAndGiftType(...)`。
7. DB count 扣掉後仍應發，才呼叫 `betVoucherUtils.addVoucher(...)`。
8. 最後 Redis awarded count increment，TTL 到 activity end。

這裡有比 daily 更強的防重意圖: Redis count 之外又查 DB 已發數。但 DB 查詢失敗時 utility 會回 0，可能讓系統退回只靠 Redis 判斷，這是重要 failure window。

## 6. 業務問題

這條 flow 解的是「玩家累計投注達到活動門檻後，自動發 free spin / voucher」。

它的核心不是下注結算是否成功，而是 reward correctness:

- 不能漏發: 達門檻應該發。
- 不能重複發: Kafka 重送、consumer concurrency、Redis / DB mismatch 時不能多發。
- 不能錯發: agent、activity、currency、activity period 要匹配。
- 要可追: 發出的 voucher 要能回到 activity / player / currency / refId。

## 7. 系統位置與入口

主要入口:

- `src/main/java/com/ps/domain/kafka/service/ActivityAccumateBetConsumerService.java`

主要輔助:

- `src/main/java/com/ps/domain/kafka/service/utils/BetVoucherUtils.java`
- `src/main/java/com/ps/domain/kafka/service/utils/BetVoucherUtilsExt.java`
- `src/main/java/com/ps/quartz/log/vo/SettleBetsVo.java`
- `src/main/java/com/ps/domain/game/slot/data/entity/BetRecord.java`
- `src/main/resources/application.yml`
- `src/main/java/com/ps/quartz/job/JobOptions.java`

外部 lib / 下游待確認:

- `ActivityConfService`
- `BetVoucherService`
- `BetVoucher`

## 8. DB / Redis / MQ / 外部 API

| 類型 | 已確認 | 說明 |
| --- | --- | --- |
| MQ | Kafka `settled_bets` | consumer 已確認；producer 端未完整掃 |
| Redis | Daily accumulate / Daily awarded count | daily key 含 agent、activity、player、currency、today |
| Redis | Period / Period2 accumulate / awarded count | period key 含 agent、activity、player、currency；TTL 到 activity end |
| DB | BetVoucher table | 透過外部 `BetVoucherService` 發 voucher / count；table schema 未掃 |
| 外部 API | 無直接呼叫 | 主要是內部 event / Redis / service lib |

## 9. 資料狀態與 state transition

```text
BetRecord event
  -> matched / not matched activity
  -> accumulated bet amount in Redis
  -> threshold reached / not reached
  -> voucher issued by BetVoucherService
  -> Redis awarded count updated
  -> DB voucher count used as period guard
```

狀態語意:

- `BetRecord`: 上游結算事件中的下注資料。
- `ActivityConf`: 活動規則，包含 agent、start / end、currency params、daily / period threshold。
- Redis accumulate key: 投注額 projection。
- Redis awarded key: 已發放次數 projection。
- `BetVoucher`: 真正發給玩家的 reward evidence；本 repo 只確認 service call。

## 10. Failure Window / Consistency

### 10.1 Kafka 重送

consumer catch exception 只記 log，不重新 throw。Step 3 未確認這條 listener 的實際 ack 行為、DLQ 消費治理與補數 runbook。若 Kafka event 重送，Redis increment 可能重複累加，Daily 可能靠 awarded key 擋第二次發，但 Period 仍要看 Redis / DB count 是否一致。

### 10.2 Redis increment 成功，但 addVoucher 失敗

Daily flow 是先 increment Redis accumulate，再達標時呼叫 `addVoucher`，最後寫 awarded key。若 `addVoucher` 失敗且 exception 被外層 catch，可能出現累計已增加但 voucher 沒發、awarded key 沒寫的狀態。下一次 event 可能再次嘗試發，但是否補得準，要看 DB count 與 event 重送情境。

### 10.3 addVoucher 成功，但 awarded key 更新失敗

若 voucher 已經寫入 DB，但 Redis awarded key 沒更新，Daily 沒有額外查 DB count，下一次 event 可能重複發。Period 有 DB count guard，風險相對低一些，但仍依賴 `BetVoucherService` count 的準確性與 transaction visibility。

### 10.4 DB count 查詢失敗

`BetVoucherUtils#getDatabaseAwardedCount` catch exception 後回 0。這讓 Period 在 DB 查詢故障時可能低估已發次數，增加重複發放風險。這是面試可以講的 owner decision: DB guard fail open 還是 fail closed。

### 10.5 Activity period / daily boundary

活動匹配使用 betRecord 的 `time` 判斷 start / end，但 Daily key 使用 consumer 當下 `DateTime.now()` 產生今天日期。若 event 延遲、跨時區或 replay，可能發生 bet time 屬於昨天，但 Daily accumulate key 寫到今天的情境。這點需要 Step 4/5 再追是否有 timezone policy 或 activity spec。

### 10.6 group key 與 matchedActivities

同一 Kafka message 內的聚合 key 是 `playerId + currency + agentId`，沒有 activityId / game。若同一玩家同幣別同代理在同一批 event 裡，不同 bet record 對應不同 activity set，current code 可能只保留第一次 computeIfAbsent 時的 matchedActivities。這是值得追問的 correctness 邊界。

## 11. Retry / Compensation / Reconciliation

本輪已確認:

- Redis keys 有 TTL: daily 到明天 0 點，period 到 activity end。
- Period 發獎有 Redis awarded count + DB awarded count 雙層檢查。
- 每次 voucher issue 有 UUID refId。
- Kafka config 有 default retry / dead-letter-topic evidence，但本 flow 的 replay / 補數治理未確認。

本輪未確認:

- `BetVoucherService#addVoucher` 是否有 DB unique key / idempotency guard。
- refId 是否可用於防重，或只是 trace。
- DB transaction boundary: voucher 寫入、count 查詢、Redis awarded update 是否有一致性設計。
- 補發 / 回收 voucher 的後台工具。
- activity config params 的 schema / 單位 / timezone spec。

Owner 建議:

- Daily 也應考慮 DB awarded count guard，或將 Redis awarded update 與 voucher issue 的 failure 可補償化。
- `addVoucher` 應有 idempotency key，例如 activityId + playerId + currency + reward type + period/day + threshold multiple。
- Redis increment 與 DB voucher 發放要有 reconciliation 報表，能查出 Redis 已達標但 DB 未發、DB 已發但 Redis count 未更新。
- DB count 查詢失敗時要明確決定 fail open / fail closed，不要默默回 0。

## 12. Senior / Owner 設計取捨

| 決策點 | 現況 | 好處 | 風險 / 待補 |
| --- | --- | --- | --- |
| 用 Kafka settlement event 做 reward projection | `settled_bets` -> activity consumer | 不阻塞下注主流程 | Event 重送 / 延遲會影響 reward projection |
| Redis 做累計投注 | daily / period accumulate keys | 快、TTL 易控 | Redis 與 DB voucher 不一致時要補償 |
| Daily 只發一次 | awarded key 擋當日重複發 | 簡單 | Daily 沒看到 DB count guard |
| Period 依倍數發 | thresholdMultiple * sendCounts | 可支援多階發放 | DB count 查詢 fail open 可能重複發 |
| period1 / period2 分 key | Period / Period2 前綴 | 可支援兩組累積活動 | 活動設定錯誤可能重疊發 |
| `BetVoucherUtilsExt` 用 `@UseSchema` | 發 voucher / count 走 schema route | 支援分 schema | schema route 與 transaction 邊界未深掃 |

## 13. Lead / Architect 追問

1. Kafka event 重送時，Daily / Period 會不會重複發 voucher？
2. `addVoucher` 成功但 Redis awarded key 寫失敗，怎麼查、怎麼補？
3. DB count 查詢失敗為什麼回 0？這是 fail open 還是 bug？
4. Daily key 用 consumer now，activity period 用 bet time，跨日 replay 怎麼處理？
5. group key 沒有 activityId，是否可能漏掉某些 matched activity？
6. `BetVoucherService#addVoucher` 有沒有 unique key 或 idempotency key？
7. 如果活動設定 total_bet 單位錯誤，怎麼防止大量錯發？
8. 如何做 reward reconciliation: source bet、Redis accumulate、DB voucher 三者怎麼對？

## 14. 面試 / 履歷邊界摘要

可作面試:

- Kafka settlement event 驅動活動 reward projection。
- Redis accumulate + DB voucher count 的防重邊界。
- Daily / Period / Period2 活動規則與 TTL。
- Voucher 發放 failure window 與 reconciliation。

履歷要保守:

- 這條 flow 目前不建議單獨新增正式履歷 bullet。
- 可作 `antplay-slot-game-job` project-level 「activity accumulated bet / voucher reward flow code-backed 分析素材」。
- 若要講工作經驗，只能搭配 project-level claim 說「接觸 / 分析 / 維護 AntPlay job event processing，包含活動累計投注 reward flow」，不要說 Nick 主導開發這條 flow。

不能誇大:

- 不說 Nick 主導活動累計投注功能。
- 不說完整 reward platform owner。
- 不說已建立完整 idempotency / exactly-once / reconciliation。
- 不把 Gill / Arnold / Eliot 的 current code 說成 Nick 完成。

## 15. Step 5 Claim Gate 結論

這條 flow 有不錯的 Senior Backend 題材，但 Nick direct evidence 弱於上一條 `proxy-user-data-report-projection`。目前最保守的定位是 code-backed 面試素材，用來補 reward / voucher / Redis consistency 題，而不是主履歷 claim。

Step 5 已補查本 repo 內的 `BetVoucherService` 下游 evidence。結果是: `ActivityAccumateBetConsumerService` 只透過 `BetVoucherUtils` / `BetVoucherUtilsExt` 呼叫外部 `BetVoucherService#addVoucher` 與 `countByPlayerIdAndActivityIdAndCurrencyAndGiftType`；本 repo 沒有 `BetVoucherService` 實作、BetVoucher table schema、DB unique key 或 transaction boundary 可以證明發券具完整 idempotency。current code 每次發券使用新的 UUID `refId`，因此在本 repo evidence 下只能把它視為 trace id / request id 線索，不能當 deterministic idempotency key。

Nick evidence 仍維持保守: `62fa93f` 是 `nick` merge `feature/accumate_bet` into `master` 的 evidence，但 current implementation blame 主要是 Gill，後續 Arnold / Eliot 調整 schema route / current context。這足以支撐「接觸 / merge / code-backed 分析過」，不足以說 Nick 主導或主要開發活動累計投注 reward flow。

最終 claim gate:

- `真實開發過`: 本 flow 單獨不成立；只能在 project-level `antplay-slot-game-job` 真實開發經驗下作 supporting evidence。
- `code-backed / 可面試講`: 成立，可講 Kafka settlement event -> Redis accumulate -> DB voucher guard 的 reward correctness case。
- `可直接履歷`: 不單獨新增正式 bullet；只能回填 project-level consolidation，作活動累積投注 / voucher flow supporting evidence。
- `不可誇大`: 不說完整 reward platform owner、不說完整 idempotency / exactly-once、不說已確認 reconciliation，也不把 Gill / Arnold / Eliot 的 implementation 說成 Nick direct contribution。

本 Step 不直接更新 `05 / 08`。2026-05-25 後續進度: Step 2 排序第三順位 `big-win-notification Step 3` 已完成；下一步若延續 `antplay-slot-game-job` Flow Track，應做 `big-win-notification Step 4`。
