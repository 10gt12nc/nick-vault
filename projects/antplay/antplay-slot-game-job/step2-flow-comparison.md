# antplay-slot-game-job Step 2 Flow Comparison

日期: 2026-05-22

## 0. 閱讀定位

本檔是 `antplay-slot-game-job` 的 Flow Track Step 2，用來比較 Step 1 找出的 candidate flows，決定哪一條最值得進 Step 3 深掃。這不是 class summary，也不是 project-level 履歷結論。

掃描深度: Level 1.5 comparison scan。沿用 Step 1 的 module map，補 path-specific history 與上下游邊界比較。

本 Step 不更新 `05 / 08 / 17`。單條 flow 仍要等 Step 3 / Step 4 / Step 5 後，才能判斷是否回填 project-level claim。

## 1. 本輪重讀範圍

Vault / KB:

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `projects/antplay/antplay-slot-game-job/README.md`
- `projects/antplay/antplay-slot-game-job/contribution-claim-consolidation.md`
- `projects/antplay/antplay-slot-game-job/step1-candidate-flows.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/06-todo.md`

Source repo:

- `/Users/nick/Git/antplay/antplay-slot-game-job`
- `ProxyUserDataConsumerService`
- `ReportAgentPlayerService`
- `ReportAgentPlayerServiceImpl`
- `ReportAgentPlayerRepositoryCustomImpl`
- `ReportAgentPlayerJob`
- `ActivityAccumateBetConsumerService`
- `BetVoucherUtils`
- `BigWinConsumerService`
- `GameDataCache`
- `SettlePoolMonitorConsumerService`
- `GroupSettleTypeRecord`
- `SyncDbFromRedis`
- path-specific git log for report projection / activity voucher / big-win

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

注意: source remote 是內網環境，vault 不記錄內網 URL / IP / sensitive remote detail。

## 3. Step 1 可沿用 / 需補項

| 文件 / 資料 | 判斷 | 說明 |
| --- | --- | --- |
| `README.md` | 可沿用 / 需同步 | Step 1 後狀態正確，本輪需把下一步改成第一條 flow Step 3 |
| `contribution-claim-consolidation.md` | 可沿用 | project-level claim 已完成 rolling；本輪只做 Flow Track 排序 |
| `step1-candidate-flows.md` | 可沿用 | 已列 module 邊界、source 狀態、候選 flow、未掃範圍；沒有跳 Step |
| source repo | 可沿用 / 遠端未確認 | fetch 仍失敗；本輪依本地 refs 保守比較 |
| 履歷素材 | 不更新 | Step 2 不直接升級履歷；只決定第一條 Step 3 |

## 4. Flow Ranking

| Rank | Flow | Senior Backend 價值 | Nick evidence | 上下游完整度 | 失敗 / consistency 題材 | Step 3 成本 | 結論 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | `proxy-user-data-report-projection` | 高 | 強：`#386`、`#590`、`#702`、`fix ag_report_player` direct commits | 高：Kafka `settled_bets` -> daily report -> Quartz summary / backup / delete | 高：projection key、currency、in-memory map、batch insert / update、summary 重跑、partial completion | 中高 | 第一條進 Step 3 |
| 2 | `activity-accumulated-bet-voucher` | 高 | 中：`nick` merge + code-backed，細節多為 Gill / Arnold | 中高：event -> activity config -> Redis accumulate -> DB voucher | 高：Redis increment、DB awarded count、duplicate voucher、daily / period overlap | 中高 | 第二順位；適合補 reward / voucher 邊界 |
| 3 | `big-win-notification` | 中高 | 中高：`#303` direct commits + 多人後續修改 | 中：event -> winner check -> push topic；下游 push 未掃 | 中：derived event、translation missing、send failure、offset / retry | 中 | 快速面試 case；第三順位 |
| 4 | `settle-pool-monitor-darkpool-sync` | 中高 | 弱到中：current code 高價值，但 2026 主要為 Arnold / Eliot | 中：event grouping -> Redis / DB dark pool；admin 查詢未掃 | 高：reset flag、Redis / DB sync、busy guard、partial save | 高 | code-backed analysis-first；不作 Nick 主 claim |
| 5 | `db-partition-job-report-routing` | 中 | 中：`db_partition v2` direct commit + 多人後續 schema route | 中：table job / schema route / repository；與 game-api flow 重疊 | 中：錯表、table missing、schema context leak | 中高 | 支線；可和 report projection 合併補讀 |
| 6 | `kafka-job-foundation-platform-mock-rollback` | 中 | 強但舊：2024 `kafka JOB` direct commits 多 | 低到中：foundation 分散，不是一條清楚 production flow | 中：config、consumer / producer、rollback attempt、memory issue | 中 | 作背景 evidence，不獨立進 Step 3 |

## 5. 第一順位：`proxy-user-data-report-projection`

### 為什麼先做它

這條 flow 最符合目前補強目標：

- Evidence 最乾淨：Nick / `10gt12nc` direct commits 集中在 `#386`、`#590`、`#702`、`fix ag_report_player`。
- Production flow 完整：Kafka event 進來、projection 到 DB、Quartz 做 summary、backup、delete。
- Senior Backend 題材夠硬：event projection、報表 key design、currency 維度、batch update、historical summary、重跑 / partial completion。
- 與履歷主軸一致：可強化 `Kafka / Quartz job、報表 projection、event processing`，但仍不直接在 Step 2 更新履歷。

### Step 3 應深掃的 code path

上游 event / payload:

- `settled_bets` topic
- `SettleBetsVo`
- `BetRecord`
- 上游 `antplay-slot-game-api` producer 端待補讀

Consumer:

- `ProxyUserDataConsumerService#consumeProxyUserDataService`
- `ProxyUserDataConsumerService#consumeProxyUserDataServiceInternal`
- in-memory `reportMap`
- `agentId / playerId / dataDay / currency` key

Persistence:

- `ReportAgentPlayer`
- `ReportAgentPlayerService`
- `ReportAgentPlayerServiceImpl`
- `ReportAgentPlayerRepositoryCustom`
- `ReportAgentPlayerRepositoryCustomImpl`
- `ReportAgentPlayerRepositoryInternal`

Quartz summary / cleanup:

- `ReportAgentPlayerJob#doExecute`
- `summaryReport`
- `findOldSummaryReport`
- `insertSummaryReport`
- `updateSummaryReport`
- `backupReport`
- `deleteReport`

History:

- `12df475` / `7aa06ed` / `7945101`: `#386` 代理用戶數據
- `4411d88` / `a9bb983` / `dbb5a66` / `806dc23`: `#590` currency / log / schedule
- `ef4f8f5`: `#702` key 重複
- `6866866`: `fix ag_report_player`
- `38b74bd`: report path fix context
- 後續 `arnold` / `eliot` commits 只作 current behavior context，不直接變成 Nick claim

### Step 3 必問問題

- `reportMap` 是長生命週期 in-memory map，consumer restart / concurrency / partition parallelism 時是否可能造成 missing / duplicate projection？
- daily projection key 是否必須包含 `agentId + playerId + dataDay + currency`？`#702` key 重複實際修了什麼？
- `findOldPlayers` / batch insert / batch update 的查詢條件與 update 條件是否一致？
- Quartz summary 對三天前資料做 summary、backup、delete，若中途失敗，重跑會不會重複 summary 或漏刪？
- currency 拆分後，舊資料與 summary row `dataDay=0` 的語意是否清楚？
- 是否有 schema / agent db group route 影響 report table？
- report projection 是否能作 reconciliation？還是只能作 derived report？正式說法要保守。

## 6. 第二順位：`activity-accumulated-bet-voucher`

這條 flow 很有 Senior Backend 價值，但先排第二：

- 優點：reward / voucher 類 money-adjacent flow，Redis + DB idempotency 題材強。
- 風險：direct evidence 比 report projection 弱，主要細節 commits 多為 Gill / Arnold；Nick 目前只有 `feature/accumate_bet` merge 線索。
- 適合時機：完成 report projection Step 5 後，若要補活動 / bonus / Redis 發放邊界，再做這條。

Step 3 前需補:

- `BetVoucherUtils#addVoucher` 下游唯一鍵 / idempotency。
- DB awarded count 與 Redis awarded count 的一致性。
- daily / period / period2 是否會互相重疊發放。
- 活動 config 的 currency / agent / time window 失配行為。

## 7. 第三順位：`big-win-notification`

這條 flow 適合做短中型面試 case：

- 優點：`#303` direct commits，code path 清楚，scope 較小，能快速講 derived notification。
- 限制：後續 current behavior 被多人修改，且通知不是交易 source of truth；履歷價值低於 report projection。
- 適合用途：補「Kafka consumer / async notification / data privacy」面試題，不作主履歷 bullet。

Step 3 前需補:

- push user topic send failure 是否影響 offset commit。
- 缺翻譯 / currency / player name mask 的 fallback。
- 後續 Arnold / Gill commits 改了哪些 current behavior。

## 8. 第四順位：`settle-pool-monitor-darkpool-sync`

這條 flow 技術價值高，但不適合先做 Nick 主 claim：

- 優點：Redis / DB sync、risk / dark pool、activity / player control / jackpot 分群，Senior 題材很硬。
- 限制：近期大量核心 commits 是 Arnold / Eliot；Nick direct evidence 不集中。
- 用法：可作 code-backed / analysis-first 面試素材，或之後和 `antplay-slot-game-api runtime-rtp-darkpool-player-control` 串上下游。

Step 3 前需補:

- `MainHandler` 各 handler 實際寫 Redis / DB 邏輯。
- `SyncDbFromRedis` reset flag 的並發與 partial save。
- admin / monitoring 查詢下游。
- Nick 是否有相關 commit / 本人確認。

## 9. 不直接進 Step 3 的 flow

| Flow | 不先做原因 |
| --- | --- |
| `db-partition-job-report-routing` | 和 game-api sharding flow 重疊，且更適合在 report projection Step 3 補讀 table / schema route |
| `kafka-job-foundation-platform-mock-rollback` | direct commits 多但太偏 foundation，不是一條清楚 production flow；作背景 evidence |
| `free-spins-earliest-latest-consumer` | Step 1 evidence 不如 activity voucher / report projection |
| `push-user-job` | 可併入 big-win notification 的下游補讀 |

## 10. Step 2 結論

第一條代表 flow 選定：

```text
proxy-user-data-report-projection
```

選它的原因:

- 最能代表 `antplay-slot-game-job` 的核心價值：Kafka event -> DB projection -> Quartz summary。
- Nick / `10gt12nc` direct evidence 最集中。
- 可形成 Senior Backend 面試中清楚的 state / consistency / retry / compensation 題。
- 與目前通用履歷主軸的 `MQ / batch / report projection / legacy flow` 對齊，但 Step 2 不直接更新履歷。

下一步:

```text
antplay antplay-slot-game-job proxy-user-data-report-projection Step 3
```

## 11. 2026-05-25 Progress Update

- `proxy-user-data-report-projection` 已完成 Step 5，已回填 project-level report projection / Quartz summary evidence。
- Rank 2 `activity-accumulated-bet-voucher` 已完成 Step 5，定位為 code-backed reward correctness 面試素材與 project-level supporting evidence；Nick 只有 merge evidence，current implementation 主要是 Gill / Arnold / Eliot context，不升級正式履歷 claim。
- Step 5 已補查 `BetVoucherService#addVoucher` 下游 idempotency / unique key；本 repo 沒有下游 implementation / DB unique key evidence，`refId` 每次 UUID 只能保守視為 trace id。
- Rank 3 `big-win-notification` 已完成 Step 5，確認 `#303` direct commits、current consumer / producer / translation path、後續多人修改與 derived notification failure windows，並補 `_id` / bet id 去重、`BetIdPersistence`、下游 `antplay-push` bridge / privacy 邊界。結論是可作 project-level supporting evidence，不單獨更新 `05 / 08`。
- Rank 4 `settle-pool-monitor-darkpool-sync` 已完成 Step 4，建立 Kafka `settled_bets` -> pool grouping -> `settled_pool` increment -> Redis reset snapshot -> alert 的正式面試 case。結論是 code-backed / analysis-first，未找到 Nick / `10gt12nc` direct path-specific evidence，不作 Nick 主導 settle pool / risk owner。
- 下一步延續同 flow Step 5，做單條 flow claim gate；不更新 `05 / 08`。

```text
antplay antplay-slot-game-job settle-pool-monitor-darkpool-sync Step 5
```
