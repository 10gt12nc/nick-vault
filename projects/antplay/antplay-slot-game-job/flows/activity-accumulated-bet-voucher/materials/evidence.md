# Evidence - activity-accumulated-bet-voucher

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

注意: source remote 是內網環境，vault 不記錄內網 URL / IP / sensitive remote detail。

## 2. 本輪掃描範圍

Vault:

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/antplay/antplay-slot-game-job/README.md`
- `projects/antplay/antplay-slot-game-job/step1-candidate-flows.md`
- `projects/antplay/antplay-slot-game-job/step2-flow-comparison.md`
- `projects/antplay/antplay-slot-game-job/contribution-claim-consolidation.md`

Source code:

- `ActivityAccumateBetConsumerService`
- `BetVoucherUtils`
- `BetVoucherUtilsExt`
- `SettleBetsVo`
- `BetRecord`
- `JobOptions`
- `application.yml` toggle / topic key only；未記錄環境或敏感設定

Git history:

- path-specific log for `ActivityAccumateBetConsumerService` / voucher utils。
- selected important commits for initial feature, Redis key / DB count fixes, Nick merge, config enable, schema routing and current toggle context。

未掃 / 待確認:

- 上游 `settled_bets` producer 端完整 code path。
- 外部 lib `ActivityConfService` / `BetVoucherService` implementation。
- `BetVoucher` table schema / unique key / transaction boundary。
- 活動設定 params schema / 單位 / timezone spec。
- production runbook / backoffice 補發或回收工具。

## 3. Code Evidence

| Area | File | Evidence |
| --- | --- | --- |
| Kafka consumer | `ActivityAccumateBetConsumerService` | `@KafkaListener(topics = {"settled_bets"}, groupId = "activity", concurrency = ...)` |
| Feature toggle | `ActivityAccumateBetConsumerService` / `application.yml` | `spring.kafka.consumer.task.activity-accumate-bet` 控制啟用；current config 為 true |
| Activity lookup | `ActivityAccumateBetConsumerService` | `activityConfService.findActiveByTypeAndStatus(1, 1)` |
| Event grouping | `ActivityAccumateBetConsumerService` | 以 `playerId + currency + agentId` 聚合同一 Kafka message 的 bet amount |
| Activity filter | `ActivityAccumateBetConsumerService` | 檢查 agentId 與 bet time 是否在 activity start / end |
| Daily Redis | `ActivityAccumateBetConsumerService` | Daily accumulate key / Daily awarded key，TTL 到明天 0 點 |
| Period Redis | `ActivityAccumateBetConsumerService` | Period / Period2 accumulate key / awarded key，TTL 到 activity end |
| Voucher issue | `BetVoucherUtils` / `BetVoucherUtilsExt` | 呼叫 `BetVoucherService#addVoucher` |
| DB awarded count | `BetVoucherUtilsExt` | 呼叫 `countByPlayerIdAndActivityIdAndCurrencyAndGiftType` |
| Schema route | `BetVoucherUtilsExt` | `@UseSchema` on add / count methods |

## 4. Commit Evidence

| Commit | Author | Date | Evidence |
| --- | --- | --- | --- |
| `26e76b0` | Gill | 2025-08-01 | 新增 `ActivityAccumateBetConsumerService`，處理 settled bets 與 free spins 發放 |
| `a4555e6` | Gill | 2025-08-01 | Redis key prefix refactor |
| `656e836` | Gill | 2025-08-05 | activity validation / Redis key management 強化 |
| `c831979` | Gill | 2025-08-07 | Redis key 加入 agentId，改用 active type/status，加入 DB awarded count guard |
| `977318d` | Gill | 2025-08-07 | Daily 達標後仍繼續累計，但不重複發獎 |
| `83729c6` | Gill | 2025-08-07 | 依 player / currency / agent 聚合同一批下注，改善批次內累計邏輯 |
| `62fa93f` | nick | 2025-08-08 | merge `feature/accumate_bet` into `master`，累計下注送零元旋轉活動修改 |
| `73fdb57` | Gill | 2025-08-08 | enable `activity-accumate-bet` config |
| `94e0c9d` | Arnold | 2025-10-07 | 暫時移除 listener concurrency context |
| `e2d6210` | Arnold | 2025-10-07 | 恢復 listener concurrency context |
| `15803ad` | Eliot | 2025-12-04 | `@UseSchema` 相關調整，將 voucher utils 路由抽出 |
| `07267ed` | Arnold | 2025-12-04 | partition / schema routing context，新增 `BetVoucherUtilsExt` |
| `cb2265c` | Arnold | 2026-01-15 | timezone / betrecord context，current file 有後續調整 |

## 5. Blame Summary

| File | Blame 摘要 | Claim 判斷 |
| --- | --- | --- |
| `ActivityAccumateBetConsumerService.java` | Gill 佔絕大多數，少量 Arnold / Eliot | code-backed 強；Nick 只能主張 merge / 接觸 / 分析，不主張主開發 |
| `BetVoucherUtils.java` | Arnold / Eliot | 下游 wrapper / schema route context，不是 Nick direct contribution |
| `BetVoucherUtilsExt.java` | Arnold | `@UseSchema` wrapper context，不是 Nick direct contribution |

## 6. 已確認

- 這條 flow 是 Kafka `settled_bets` event -> Redis accumulated bet -> BetVoucher issuing。
- current config 啟用 `activity-accumate-bet` consumer。
- Daily、Period、Period2 三種 reward path 都在同一 consumer 中處理。
- Redis key 已包含 agentId、activityId、playerId、currency，Daily 另含 date。
- Period reward 有 Redis awarded count 與 DB voucher count 兩層防重檢查。
- Nick 有 merge evidence，但主要 implementation commits 不是 Nick。

## 7. 合理推論

- 這條 flow 屬於 reward / bonus projection，不是下注或 wallet source of truth。
- Redis 是 projection state，BetVoucher DB 比 Redis 更接近發獎 evidence，但下游 service 實作未掃。
- 如果 Kafka 重送或 consumer retry，Redis increment 可能重複累加，需要靠 awarded count / DB count / idempotency guard 防止重複發。
- DB count 查詢失敗回 0 是 fail-open 風險。

## 8. 待確認

- `BetVoucherService#addVoucher` 是否有 unique key / idempotency key。
- `refId` 是否只是追蹤 id，或會參與防重。
- activity params 的 `total_bet` 單位、currency schema 與 validation。
- Daily key 用 consumer current date 是否符合 bet time / timezone spec。
- group key 沒有 activityId 是否在 mixed activity set 情境下安全。
- 是否有補發 / 回收 / reconciliation tooling。
