# proxy-user-data-report-projection Career / Interview

日期: 2026-05-22

## Evidence Level

- 證據層級: 真實開發過 + code-backed。
- Nick / `10gt12nc` direct evidence: `#386` 代理用戶數據、`#590` currency 拆分、`#702` key 重複、`fix ag_report_player`。
- 完成狀態: Step 4 面試 case 已完成。
- 本文件是 flow-level 面試素材，不是 project-level final consolidation。正式履歷仍以 `contribution-claim-consolidation.md` 與後續 Step 5 claim gate 為準。

## 履歷保守 Bullet

可放入 project-level 素材池的保守版本:

- 參與 AntPlay slot job / event processing 維護，處理 Kafka `settled_bets` 事件投影、代理玩家日報表聚合、currency 維度修正與 Quartz summary / backup / cleanup 類報表資料治理。

更短版本:

- 參與 Kafka / Quartz job 報表 projection 維護，處理代理玩家報表聚合、幣別維度與歷史資料彙總清理。

## 面試定位

這條 flow 面試時不要講成「我做了一個報表功能」而已，應該講成:

```text
結算事件 -> Kafka projection -> 報表 identity -> DB upsert-like write -> Quartz summary / backup / delete -> derived data reconciliation
```

主軸是報表 projection 的 correctness，不是 Kafka 名詞堆疊。最能打的點有三個:

1. report identity: `agentId + playerId + dataDay + currency`。
2. derived table: report table 不是交易 source of truth，要能重建 / 對帳。
3. lifecycle job: summary / backup / delete 有 partial failure window。

## 30 秒說法

我有參與 AntPlay slot job 裡代理玩家報表的 event projection。這條 flow 會消費 `settled_bets` Kafka event，把 bet record 依代理、玩家、日期、幣別聚合到 `ag_report_player`，再由每天 02:00 的 Quartz job 把三天前以前的日資料彙總、備份、刪除。這裡我會特別注意 report key、currency、batch insert / update、以及 summary 重跑時可能重複加總的 failure window。

## 90 秒壓縮版

我可以講一個 AntPlay slot job 的 Kafka 報表 projection。上游遊戲結算後會發 `settled_bets` event，job service 消費後把 bet records 依 `agentId + playerId + dataDay + currency` 聚合成代理玩家日報表，寫到 `ag_report_player`。後續每天 02:00 的 Quartz job 會把三天前以前的日資料彙總成 `dataDay = 0` 的 summary row，然後 backup / delete 舊資料。

這個 case 的重點不是單純消費 Kafka，而是 report correctness。像 currency 拆分如果沒有一起改 query、update、summary，報表就會把不同幣別混算；`#702` key 重複修正則是把 agentId 納入 oldMap / key 判斷，避免不同代理資料 collision。另外，這張表是 derived report，不是下注或 wallet 的 source of truth，所以 owner 要思考 replay / rebuild / reconciliation。尤其 summary -> backup -> delete 若中途失敗，下次重跑可能重複加總或漏備份，這就是我會在設計上補 job execution log、處理筆數、唯一鍵 / upsert 與重跑 runbook 的地方。

## 90 秒說法

這條 flow 不是下注主交易，而是結算後的 derived report。上游送出 `settled_bets` event 後，`ProxyUserDataConsumerService` 會解析 `SettleBetsVo`，逐筆取 `agentId / playerId / account / bet / totalWin / currency`，再用 `agentId + playerId + dataDay + currency` 當 key 累加下注筆數、下注額、總贏分與 profit。DB 寫入前會先查舊 row，沒有就 insert，有且目前累計筆數較大才 update。

後面還有 `ReportAgentPlayerJob` 每天 02:00 跑 summary，把三天前以前的日報表按玩家與幣別彙總到 `dataDay = 0`，然後 backup / delete 舊日資料。這個 case 可以講出很多 Senior 關注點，例如 currency 加入 key 避免混算、agentId key 修正避免跨代理 collision、report table 是 derived projection 不是 source of truth，以及 summary / backup / delete 如果不是同一 transaction，要怎麼設計重跑與 reconciliation。

## 3 分鐘說法

我參與過 AntPlay slot job 裡代理玩家報表 projection 的維護。這條 flow 的背景是，下注結算完成後，上游會把一批 `BetRecord` 包成 `SettleBetsVo` 丟到 Kafka `settled_bets` topic。job service 端的 `ProxyUserDataConsumerService` 會消費這個 event，依 `agentId + playerId + dataDay + currency` 把下注筆數、下注額、total win、profit 聚合到報表 table。

這裡比較容易出問題的是 key design 與 consistency。早期如果 key 只有 player / day，或沒有把 currency、agentId 一起納入，比較容易在多幣別或不同代理同玩家資料時混算。後續我這邊的維護 evidence 包含 `#590` currency 維度拆分，以及 `#702` key 重複修正，把 agentId 納入 oldMap key 判斷。這類改動的重點不是只是多一個欄位，而是報表 projection 的 identity 要和業務維度一致。

寫入 DB 時，consumer 不是每筆直接加總，而是 process 內用 map 累積後查舊 row，沒有就 insert，有且新的 betCount 比 DB 大才 update。這能降低部分重送造成的 double add，但也帶來長生命週期 map、consumer concurrency、restart 後狀態如何恢復等問題，所以我會把這張表定位成 derived report，而不是交易 source of truth。

另一段是 Quartz summary job。它每天 02:00 把三天前以前的日報表彙總到 `dataDay = 0` 的 summary row，然後 backup 再 delete 舊資料。這裡的 owner 問題是 partial failure: 如果 summary update 成功但 backup 或 delete 失敗，下次重跑可能重複加總；如果 delete 成功但備份有問題，也會造成查帳壓力。所以實務上我會建議補 job execution id、處理筆數、agent / player / currency 維度 log、backup 去重或重跑 runbook，並保留用 source bet record 或 Kafka replay 重建報表的能力。

這個 case 我會保守描述為「參與 Kafka / Quartz 報表 projection 與資料治理維護」，不會說自己主導完整 Kafka platform 或完整 BI 系統。

## STAR

| 面向 | 內容 |
| --- | --- |
| Situation | AntPlay slot 需要把結算後 bet records 轉成代理 / 玩家報表，支援營運與代理查詢。 |
| Task | 維護 Kafka event projection、currency 維度、key collision 修正與歷史報表 summary / cleanup。 |
| Action | 追 `settled_bets` consumer、report key、`ag_report_player` insert / update、Quartz summary / backup / delete，修正或理解 currency / agent key 邊界。 |
| Result | 報表 projection 維度更貼近業務 identity，可作 Kafka / Quartz / derived report consistency 的面試 case。 |

## Failure Scenarios

| 情境 | 面試回答重點 |
| --- | --- |
| Kafka event 重送 | current code 用累計值覆寫，不是直接加 diff；但不能宣稱 exactly-once，仍要看 ack / retry / unique key / replay。 |
| consumer process restart | `reportMap` 會歸零，derived report 需要能從 source event / bet record 重建或對帳。 |
| `reportMap` 長期累積 | 有 memory growth 與 concurrency sharing 風險，應評估清理策略、window aggregation 或 DB-level idempotency。 |
| currency 缺漏 | null currency 被轉空字串；要確認舊資料 migration 與查詢語意。 |
| summary 成功但 backup/delete 失敗 | 下次重跑可能重複加總，應加 job execution marker、processed range 或 idempotent summary。 |
| backup 成功但 delete 失敗 | 舊日資料留在主表，下一次 summary 可能重複處理；需要 backup 去重與重跑檢查。 |
| 某玩家失敗但 job 繼續 | job 結束不代表全成功，log / metrics 要能定位 agent / player / day / currency。 |

## Senior 追問短答

### Q1: 這條 flow 的 source of truth 是什麼？

報表 table 不是 source of truth，它是 derived projection。真正要校正時應回到上游 settlement event 或 bet record。這樣講可以避免把報表 table 誇大成交易正確性的核心。

### Q2: 為什麼 key 要有 agentId？

如果只用 player / day / currency，不同代理下同玩家 id 或資料維度可能 collision。`#702` 就是 key 重複修正 evidence，把 agentId 放回 oldMap / report key 判斷，讓 code key 與業務 identity 對齊。

### Q3: 為什麼 key 要有 currency？

多幣別場景下，同一玩家同一天可能有不同 currency。`#590` 把 ReportAgentPlayer 拆 currency，代表 query、update、summary、backup 都要跟著帶 currency，不然會混算 bet amount / win / profit。

### Q4: 這種 projection 怎麼避免重複計算？

current code 是查舊 row 後用累計值覆寫，且只有新 betCount 大於 DB old betCount 才 update。這可以降低部分重送 double add，但不是完整 exactly-once。更完整的做法會是 DB unique key / upsert、event id 去重、可重建的 source data 與 reconciliation。

### Q5: Summary job 怎麼設計才安全？

我會把 summary / backup / delete 視為一個 lifecycle，不只是一個 SQL job。至少要記 job execution id、處理範圍、summary count、backup count、delete count；backup table 要能去重；重跑前要能知道哪些 range 已處理，避免 summary row 被重複加總。

### Q6: 如果 production 報表數字不準，你怎麼查？

先定位維度: agentId、playerId、dataDay、currency。再比對 source bet record / Kafka event、`ag_report_player` daily row、`dataDay=0` summary row、backup row。若只有 summary 錯，要看 backup/delete 是否 partial；若 daily row 就錯，要回 consumer key、old row lookup、insert/update 條件與 event 是否重送。

## Lead / Architect 追問

| 追問 | 回答方向 |
| --- | --- |
| 如果讓你重做，你會改哪裡？ | 先補 DB unique key / upsert 與 job execution log，再補 rebuild / reconciliation runbook；不一定一開始就上 heavy architecture。 |
| 為什麼不用同步寫報表？ | 報表不是交易主流程，非同步 projection 可以降低下注 latency；代價是 eventual consistency 與重建機制。 |
| 如果 Kafka lag 很大怎麼辦？ | 先看 consumer lag、partition / concurrency、DB write batch、reportMap size，再決定擴 consumer、調 batch 或改寫入策略。 |
| 這和 outbox / CDC 有什麼差？ | 這裡目前是上游 event projection；outbox / CDC 更偏事件可靠發送或資料變更捕捉。不能硬說已有 outbox。 |
| 怎麼向非技術主管解釋？ | 這是結算後的報表加工線，核心是避免不同代理 / 玩家 / 日期 / 幣別混算，並確保舊資料彙總清理可以重跑與查帳。 |

## 面試收尾句

這個 case 我會把它定位成「event-driven derived report 的 correctness」。我做法不是只看 Kafka consumer 有沒有收到訊息，而是把 report key、DB identity、summary lifecycle、partial failure 與 reconciliation 放在一起看，避免報表長期累積後變成難查的資料債。

## 可說

- 我參與過這條 flow 的開發維護，有 direct commit evidence。
- 我能解釋 Kafka event 如何投影到 DB report table。
- 我能講 currency 維度與 key collision 的一致性問題。
- 我能分析 Quartz summary / backup / delete 的 partial failure window。

## 不可誇大

- 不說我設計完整 Kafka event platform。
- 不說這條 flow 有 exactly-once。
- 不說我主導完整 BI / report platform。
- 不說我主導完整下注結算 source of truth。

## 待 Step 5 補強

- 追 current behavior 中哪些是 Nick direct contribution，哪些是後續他人 context。
- 確認 DB unique key / index、Kafka ack / retry、rebuild tooling 是否有 evidence。
- 判斷這條 flow 的 claim 是否可以回填 project-level consolidation。
