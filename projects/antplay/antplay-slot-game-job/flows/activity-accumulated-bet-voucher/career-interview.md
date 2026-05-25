# activity-accumulated-bet-voucher Career / Interview

日期: 2026-05-25

## Evidence Level

- 證據層級: 專案存在 / code-backed + Nick merge evidence。
- Nick evidence: `62fa93f` merge `feature/accumate_bet` into `master`，merge message 指向累計下注送零元旋轉活動修改。
- Direct implementation evidence: current `ActivityAccumateBetConsumerService` 主要 blame 為 Gill，後續有 Arnold / Eliot 修改；Nick 不宜主張主導開發。
- 完成狀態: Step 4 面試 case 已完成。
- 本文件是 flow-level 素材，不是 project-level final consolidation。正式履歷仍以 `contribution-claim-consolidation.md` 與 rolling resume package 為準。

## 履歷保守 Bullet

不建議單獨放成正式主 bullet。

若要放進 project-level 素材池，只能保守寫:

- 接觸 / 分析 AntPlay slot job 的活動累計投注 reward flow，理解 Kafka settlement event、Redis 累計投注、BetVoucher 發放與防重邊界。

更短版本:

- 以 code-backed 方式梳理活動累計投注 voucher flow，聚焦 Redis / DB consistency 與 reward idempotency。

## 面試定位

這條 flow 面試時不要講成「我做活動發獎」。比較安全的定位是:

```text
settlement event -> activity eligibility -> Redis accumulate -> voucher issuing -> duplicate reward guard
```

主軸是 reward correctness，不是完整活動平台。

## 30 秒說法

我看過 AntPlay job 裡一條活動累計投注送 voucher 的 flow。它會消費 `settled_bets`，依活動設定過濾 agent、活動時間與幣別，把同玩家的投注額累加到 Redis；達到 daily 或 period 門檻後，透過 `BetVoucherService` 發 free spin / voucher。這條 case 我會用來分析 Kafka 重送、Redis awarded count、DB voucher count 與重複發放的邊界，但我會保守說這是 code-backed 分析素材，不說是我主導開發。

## 90 秒說法

我可以講一條 AntPlay job 的活動累計投注 reward flow。它不是下注主交易，而是 settlement event 後的 reward projection。上游結算後會把 `BetRecord` 包在 `settled_bets` Kafka event 裡，job 端 consumer 會找 type=1、status=1 的活動設定，檢查 agent、活動期間與 currency，再把同玩家、同代理、同幣別的投注額累加到 Redis。

這條 flow 有 daily 與 period 兩種邏輯。Daily 是當日達標後發一次 voucher，靠 Redis daily awarded key 擋當日重複發；Period 是活動期間累積，依門檻倍數計算應發數，除了 Redis awarded count，還會查 DB voucher count 來避免重複發。它的重點不是「Redis 很快」，而是 Redis projection、DB voucher evidence、Kafka 重送之間的一致性邊界。

我會保守說這是 code-backed 分析素材，因為 Nick 的 evidence 主要是 merge，current implementation 主要是其他同事。但這個 case 很適合面試講 reward idempotency：例如 voucher 寫成功但 Redis awarded key 失敗、DB count 查詢失敗回 0、event 跨日 replay、或 activity config 單位錯誤，這些都可能造成漏發或重複發。

## 3 分鐘說法

這條 flow 是 AntPlay slot job 裡的活動累計投注送 voucher。它消費 `settled_bets` Kafka event，event 裡有一批下注結算後的 `BetRecord`。consumer 會先查 type=1、status=1 的活動設定，再對每筆 bet record 檢查 activity agent 是否匹配、bet time 是否在活動期間內，以及 currency 下是否有對應的 daily 或 accumulated bet 設定。

第一段是 event 內聚合。current code 會用 `playerId + currency + agentId` 當 group key，把同一個 Kafka message 裡同玩家、同代理、同幣別的 bet amount 先加總，再跑 daily / period reward logic。這能減少同批 event 中多筆 bet record 重複打 Redis / voucher service，但也有一個要追問的邊界：group key 沒包含 activityId，如果同批資料裡不同 bet record 對到不同 activity set，是否完全符合產品預期要再確認。

第二段是 daily reward。它用 Redis 記玩家當日累計投注額，key 裡包含 agent、activity、player、currency 與今天日期。每次 event 都先累計投注額，達到門檻且 daily awarded key 還不存在時，就呼叫 `BetVoucherService` 發 voucher，然後寫 awarded key，TTL 到隔天 0 點。這個設計簡單，但如果 voucher 已經寫入 DB、Redis awarded key 更新失敗，Daily path 沒看到 DB count guard，下一次 event 可能再發一次。

第三段是 period reward。Period / Period2 會用活動結束時間做 Redis TTL，並依 `periodAccumateBet / threshold` 算出應發倍數。這裡比 daily 多一層 DB guard：先用 Redis awarded count 算出還應發多少，再查 DB voucher count，扣掉 DB 已發數後才發新的 voucher。這可以降低 Redis 遺失或 event 重送造成的重複發，但 code 裡 DB count 查詢失敗會回 0，這是 fail-open，代表可用性與玩家體驗優先，但有重複發放風險。

如果我是 owner，我會補三件事。第一，確認 `BetVoucherService#addVoucher` 是否有 idempotency key，例如 agentId、activityId、playerId、currency、reward type、day 或 threshold multiple。第二，Daily 也補 DB awarded count guard，或建立 reward ledger / outbox，讓 voucher issue 與 awarded state 能 reconcile。第三，明確定義 daily 的 timezone 與 replay 語意，因為 eligibility 用 bet time，但 daily key 用 consumer now，event 延遲跨日時會有邊界問題。

這個 case 我會很保守地講，因為 direct implementation 主要不是 Nick。我會說我用這條 code-backed flow 分析 Kafka reward projection、Redis / DB consistency 與 duplicate reward guard，不會說我主導完整活動或 reward platform。

## STAR

| 面向 | 內容 |
| --- | --- |
| Situation | AntPlay slot job 有活動累計投注送 voucher / free spin flow，需要根據 settlement event 判斷玩家是否達到 reward 門檻。 |
| Task | 以 code-backed 方式梳理 Kafka event、活動設定、Redis 累計投注、BetVoucher 發放與防重邊界。 |
| Action | 追 `ActivityAccumateBetConsumerService`、Redis daily / period key、`BetVoucherUtils`、DB awarded count guard 與相關 commits / blame，拆出 failure windows。 |
| Result | 形成 reward correctness 面試案例，可回答 Kafka 重送、Redis / DB mismatch、Daily / Period 重複發與 idempotency 設計。 |

## Failure Scenarios

| 情境 | 面試回答重點 |
| --- | --- |
| Kafka event 重送 | Redis accumulate 可能重複增加；Daily 靠 awarded key 擋再發，Period 還要靠 Redis awarded count + DB count。完整做法應補 bet/event idempotency。 |
| addVoucher 成功但 Redis awarded key 失敗 | DB 已有 voucher，但 Redis guard 遺失；Daily 可能重複發，Period 依 DB count 可降低風險。 |
| DB awarded count 查詢失敗 | current wrapper 回 0，這是 fail-open；要明確決定 fail open / fail closed，並加告警與補償。 |
| 跨日 replay | activity eligibility 用 bet time，Daily key 用 consumer now；event 延遲或 replay 跨日時可能寫到錯誤日期 bucket。 |
| activity config 單位錯誤 | threshold / send count / gift type 設錯會造成大量錯發或漏發；需要 config validation / approval / dry run。 |
| 同批 mixed activity set | group key 沒 activityId，需確認同玩家同幣別同代理在同批事件中 activity set 是否可能不同。 |

## Senior 追問短答

### Q1: 這條 flow 的 source of truth 是什麼？

投注來源應回到 settlement event / bet record；reward 是否已發應以 BetVoucher DB 或 reward ledger 為 evidence。Redis 是累計與防重 projection，不應是唯一 source of truth。

### Q2: Redis key 已經有 awarded count，為什麼還要 DB count？

Redis 可能過期、被清、寫失敗或和 DB transaction 不一致。Period path 加 DB count 是為了用實際 voucher evidence 擋重複發。但 DB count 查詢失敗回 0 代表 fail-open，需要 owner 明確接受風險。

### Q3: Daily path 有什麼弱點？

Daily 主要靠 Redis awarded key，沒看到 DB count guard。如果 voucher 寫成功但 Redis awarded key 沒寫成功，下一次 event 可能重複發。可補 DB guard 或把 daily reward 寫入具 idempotency key 的 reward ledger。

### Q4: 你會怎麼設計 idempotency key？

我會用 agentId、activityId、playerId、currency、reward type、day 或 period、threshold multiple 組成 deterministic key。UUID refId 適合 trace，但每次 retry 都不同，不適合作為唯一防重 key。

### Q5: Kafka 重送要怎麼處理？

不能只靠「consumer 不出錯」。應該讓 reward issue idempotent，並能從 bet record / event id、Redis accumulate、BetVoucher DB 做 reconciliation。Kafka 重送時可以允許重算，但不能允許重複發 voucher。

### Q6: DB count 查詢失敗要回 0 嗎？

這要看 business decision。回 0 是 fail-open，玩家比較不會漏領，但可能多發；fail-closed 可以避免多發，但需要補發工具。至少要加告警與可追蹤的 failed check，不能安靜吞掉。

## Lead / Architect 追問

| 追問 | 回答方向 |
| --- | --- |
| 如果只能改一件事，你先改什麼？ | 先補 DB idempotency key / unique constraint，讓 duplicate voucher 在資料庫層擋住。 |
| Redis 和 DB 不一致怎麼對帳？ | 做 reconciliation: source bet amount、Redis accumulated、BetVoucher DB count、activity config snapshot。 |
| 怎麼處理活動設定錯誤？ | 活動設定 schema validation、金額單位檢查、approval、dry run / preview impact。 |
| 為什麼不是同步在下注主流程發？ | reward 是 settlement 後 projection，不應增加下注 latency；代價是要補 eventual consistency 和 reconciliation。 |
| 怎麼向產品解釋風險？ | 這不是錢包扣款錯，而是活動 reward 可能漏發或多發；要定義補發、回收與客服查詢流程。 |

## 面試收尾句

這個 case 我會定位成「reward projection correctness」。它的價值不是我背了多少 Redis key，而是能把 Kafka 重送、Redis projection、DB voucher evidence、idempotency key、fail-open / fail-closed decision 放在同一個 owner 視角下分析。

## 可說

- 這條 flow 是 Kafka settlement event 之後的 reward projection。
- Daily 使用 Redis accumulate + daily awarded key 控制每日一次。
- Period 使用 Redis awarded count，再補 DB voucher count 查詢。
- `addVoucher` 成功 / Redis awarded 更新失敗、DB count 查詢失敗回 0，是主要 failure window。
- Nick 有 merge evidence，但 implementation 主要是其他人 commits，因此要保守。

## 不可誇大

- 不說主導活動累計投注功能。
- 不說完整 reward platform owner。
- 不說已確認 `BetVoucherService` 有完整 idempotency。
- 不說 Kafka / Redis / DB 已有 exactly-once 或完整 reconciliation。
- 不把 Gill / Arnold / Eliot 的 current code 寫成 Nick direct contribution。

## Step 4 結論

Step 4 已完成正式面試說法。下一步應做 Step 5 claim gate，追 `BetVoucherService#addVoucher` 的 idempotency / unique key、Nick merge evidence 邊界、以及這條 flow 是否能回填 project-level consolidation；目前仍不直接更新 `05 / 08`。
