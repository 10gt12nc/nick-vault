# Interview Notes - proxy-user-data-report-projection

日期: 2026-05-22

## 面試主軸

這條 flow 最適合講成「Kafka event projection + Quartz report lifecycle」。

核心不是吹成完整平台，而是展示:

- 能讀懂 event-driven derived data。
- 能把報表 identity 與 business dimension 對齊。
- 能看出 batch update、summary、backup、delete 的 consistency 風險。
- 能保守區分 source of truth / projection / reconciliation。

## 開場 30 秒

我可以分享一個 AntPlay slot job 的報表 projection case。上游結算完成後會發 `settled_bets` Kafka event，job service 會把 bet records 依代理、玩家、日期、幣別聚合到 `ag_report_player`，再透過每天 02:00 的 Quartz job 把三天前以前的日資料彙總、備份、清掉。這裡我主要會看 projection key、currency 維度、batch insert / update，以及 summary 重跑時會不會重複加總。

## Senior 追問與回答

### Q1: 這張 report table 是 source of truth 嗎？

不是。它是 derived projection。下注結算正確性應該回到 bet record / settlement flow；`ag_report_player` 服務的是查詢與營運報表。這樣定位後，owner 要補的是 replay / rebuild / reconciliation，而不是把 report table 當交易主表。

### Q2: 為什麼 key 要有 currency？

因為同一代理、同一玩家、同一天可能有不同幣別，如果 key 沒有 currency，bet amount / total win / profit 會混算。`#590` 的維護 evidence 就是把 `ReportAgentPlayer` 拆 currency，query / update / summary 都要跟著加上 currency。

### Q3: `#702 key 重複` 你怎麼理解？

從 diff 看，oldMap 與 report key 後來把 `agentId` 納入，避免只用 player / day / currency 時跨代理 collision。這是典型 projection identity 和 DB query condition 要一致的問題。

### Q4: consumer 重送會不會 double count？

不能直接宣稱完全不會。current design 是用 process 內 map 累加，查 DB old row 後只有新的 betCount 大於 old betCount 才 update，update 是覆寫累計值，不是直接加 diff。這能降低部分重送造成的 double add，但仍要看 Kafka ack、map restart、DB unique key、上游 event 是否可 replay。

### Q5: summary job 最大 failure window 是什麼？

summary -> backup -> delete 不是在 job 層看到一個完整 transaction。若 summary row 已加總，但 backup 或 delete 失敗，下次重跑可能再次把同一批 daily row 加進 summary。這需要 idempotent marker、backup 去重、job execution log，或以 source data 重建 summary 的 runbook。

### Q6: 如果你是 owner，會補什麼？

我會先補觀測性與重跑邊界:

- 每次 consumer / job 記 agentId、playerId、dataDay、currency、處理筆數。
- summary job 記 processed / backed up / deleted counts。
- `agent_id + player_id + data_day + currency` 補 unique key 或 upsert strategy。
- backup table 要有去重策略。
- 建一個 rebuild / reconciliation runbook，從 source bet record 或 Kafka replay 重建某天某代理報表。

## 可反問面試官

- 你們的報表 projection 是從 DB binlog、message queue，還是交易流程同步寫？
- derived report 若落後或錯誤，是否有 replay / rebuild tooling？
- 你們對 report cleanup 是用 partition drop、summary table，還是 cold storage？

## 面試邊界

可說:

- 參與 Kafka / Quartz 報表 projection 維護。
- 處理過 currency / key design 類報表一致性問題。
- 能分析 summary / backup / delete failure window。

避免說:

- 主導完整 event platform。
- 保證 exactly-once。
- 主導完整 BI / report platform。
- 主導完整交易結算或 wallet correctness。
