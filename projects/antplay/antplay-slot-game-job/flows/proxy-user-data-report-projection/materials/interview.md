# Interview Notes - proxy-user-data-report-projection

日期: 2026-05-22

## Step 5 狀態

本檔是 `proxy-user-data-report-projection` 的正式面試稿。Step 5 已補 claim gate，可直接作 flow-level Kafka report projection / Quartz summary 面試 case；正式履歷仍以 project-level consolidation 與 rolling resume package 為準。

## 面試主軸

這條 flow 最適合講成「Kafka event projection + Quartz report lifecycle」。

核心不是吹成完整平台，而是展示:

- 能讀懂 event-driven derived data。
- 能把報表 identity 與 business dimension 對齊。
- 能看出 batch update、summary、backup、delete 的 consistency 風險。
- 能保守區分 source of truth / projection / reconciliation。

## 30 秒版本

我可以分享一個 AntPlay slot job 的報表 projection case。上游結算完成後會發 `settled_bets` Kafka event，job service 會把 bet records 依代理、玩家、日期、幣別聚合到 `ag_report_player`，再透過每天 02:00 的 Quartz job 把三天前以前的日資料彙總、備份、清掉。這裡我主要會看 projection key、currency 維度、batch insert / update，以及 summary 重跑時會不會重複加總。

## 90 秒版本

這條 flow 是結算後的 derived report。上游發出 `settled_bets` Kafka event 後，consumer 解析 `SettleBetsVo`，把 bet records 依 `agentId + playerId + dataDay + currency` 聚合成代理玩家日報表，寫到 `ag_report_player`。這裡 key design 很重要，因為同一玩家同一天可能跨幣別，不同代理也不能混在一起。對應 evidence 是 `#590` currency 拆分，以及 `#702` 把 agentId 納入 oldMap / key 判斷，避免 key 重複。

後段是 Quartz lifecycle。每天 02:00 job 會把三天前以前的日資料彙總到 `dataDay = 0` 的 summary row，再 backup / delete 舊資料。這段我會特別看 partial failure，因為 summary 成功但 backup/delete 失敗，下次重跑可能重複加總。我的設計口徑會是: report table 是 derived projection，不是交易 source of truth；要補 job execution log、處理筆數、唯一鍵 / upsert、backup 去重與 rebuild / reconciliation runbook。

## 3 分鐘版本

我可以分享 AntPlay slot job 裡一條代理玩家報表 projection。這條 flow 的背景是，遊戲結算後上游會把一批 bet records 包成 `SettleBetsVo`，送到 Kafka `settled_bets` topic。job service 端的 `ProxyUserDataConsumerService` 消費後，會抽出每筆 bet 的 agent、player、日期、幣別、投注額與總贏分，聚合成 `ReportAgentPlayer` 寫進 `ag_report_player`。

第一個重點是 report identity。這張報表不能只用玩家或日期當 key，因為業務上要區分代理、玩家、日期與幣別。後續維護裡有兩個很典型的 evidence: `#590` 把 ReportAgentPlayer 拆 currency，代表 query、update、summary 都要跟著把 currency 納入；`#702` key 重複修正則把 agentId 放進 oldMap / key 判斷，避免不同代理資料 collision。這類 bug 的本質不是欄位漏加，而是 projection identity 沒有完整對齊 business dimension。

第二個重點是這是 derived report，不是交易 source of truth。consumer 目前會用 service-level map 做累積，再查 DB old row，沒有就 insert，有且新的 betCount 較大才 update。這種做法比直接加 diff 更不容易在某些重送情境 double add，但不能宣稱 exactly-once，因為還要看 Kafka ack、JVM restart、DB unique key、source event 是否可 replay。面試時我會明確說: 真正校正要回到 source bet record 或 settlement event。

第三個重點是 Quartz summary lifecycle。每天 02:00 job 會把三天前以前的 daily rows 彙總到 `dataDay = 0`，然後 backup 到 `ag_report_player_bak`，再 delete 主表舊資料。這裡如果 summary 成功但 backup 或 delete 失敗，下次重跑就可能重複加總；如果某個 player 失敗但 job 繼續，整體 job end 也不代表全成功。所以 owner-grade 的補強會是 job execution id、agent/player/day/currency 維度 log、summary/backup/delete count、backup 去重與重跑前檢查。

我會把這個 case 總結成 event-driven report correctness。重點不是只會接 Kafka，而是能把 event projection、DB identity、歷史資料 lifecycle 和 reconciliation 一起看。

## Senior 追問與回答

### Q1: 這張 report table 是 source of truth 嗎？

不是。它是 derived projection。下注結算正確性應該回到 bet record / settlement flow；`ag_report_player` 服務的是查詢與營運報表。這樣定位後，owner 要補的是 replay / rebuild / reconciliation，而不是把 report table 當交易主表。

### Q2: 為什麼 key 要有 currency？

因為同一代理、同一玩家、同一天可能有不同幣別，如果 key 沒有 currency，bet amount / total win / profit 會混算。`#590` 的維護 evidence 就是把 `ReportAgentPlayer` 拆 currency，query / update / summary 都要跟著加上 currency。Step 5 補掃後，仍只確認這是 code-level report identity；DB unique key / migration evidence 未在 source scan 中找到。

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

### Q7: `reportMap` 放在 service field 有什麼風險？

它是長生命週期 in-memory state。好處是 process 內可以累積後覆寫 DB，但風險是 map 會長大、consumer concurrency 共享同一份 state、restart 後狀態消失，DB partial failure 後 map 和 DB 也可能不一致。所以我不會把它講成完整 idempotency，只會講成現有實作的累積策略，owner 改善方向是 unique key / upsert、event id 去重或可重建的 source data。

### Q8: 為什麼 summary 用 `dataDay = 0`？

這是一種把歷史 daily rows 折疊成 summary row 的做法，讓主表不要一直累積舊日資料，查詢歷史總量也比較快。但它的代價是語意要非常清楚: `dataDay > 0` 是日資料，`dataDay = 0` 是 summary；重跑時不能把同一批 daily rows 重複加進 summary。

### Q9: 如果面試官問實際結果，你怎麼保守回答？

我會說這條 flow 有 direct commit evidence，包含代理用戶數據、currency 拆分與 key 重複修正；但我不會說完整 BI platform 或 exactly-once 是我主導落地。Step 5 補掃有看到基本 retry / dead-letter-topic 設定，但 replay / DLQ 消費治理與補數 runbook 未確認。這個 case 能證明我做過 Kafka / Quartz job 報表 projection 維護，也能從 owner 角度分析 failure window。

## 面試常見陷阱

| 陷阱 | 不好的回答 | 建議回答 |
| --- | --- | --- |
| 把報表講成交易主流程 | 這張表保證下注金額正確 | 這是 derived report，交易正確性要回 source bet / settlement |
| 過度宣稱 Kafka | 我們做到 exactly-once | current evidence 未確認 exactly-once，只能說有 projection 與待補 retry / replay |
| 只講欄位 | 我加了 currency 欄位 | currency 是 report identity 的一部分，query / update / summary 都要一致 |
| 忽略 job partial | 每天 job 跑完就好了 | summary / backup / delete 有 partial failure，要能重跑與對帳 |
| 把他人後續修正當自己的 | current code 都是我做的 | direct commits 是 Nick / `10gt12nc` 的部分，後續他人修正只作 current behavior context |

## 可反問面試官

- 你們的報表 projection 是從 DB binlog、message queue，還是交易流程同步寫？
- derived report 若落後或錯誤，是否有 replay / rebuild tooling？
- 你們對 report cleanup 是用 partition drop、summary table，還是 cold storage？
- 報表正確性在你們公司是由業務報表團隊、交易團隊，還是 platform team owner？

## 面試邊界

可說:

- 參與 Kafka / Quartz 報表 projection 維護。
- 處理過 currency / key design 類報表一致性問題。
- 能分析 summary / backup / delete failure window。

避免說:

- 不得寫成主導完整 event platform。
- 保證 exactly-once。
- 不得寫成主導完整 BI / report platform。
- 不得寫成主導完整交易結算或 wallet correctness。

## Step 5 結論

這條 flow 已經可以作為面試案例使用。Step 5 已追 current code / blame / important diff，結論是可回填 project-level Kafka report projection / Quartz summary evidence；但 replay / DLQ 消費治理、DB unique key 與後續 Arnold / Eliot current fixes 仍要保守標界線。
