# Decision Notes - proxy-user-data-report-projection

日期: 2026-05-22

## 1. Derived Projection vs Source of Truth

這條 flow 應定位成 derived projection。它從 `settled_bets` event 產生代理玩家報表，不應被包裝成下注結算或錢包 source of truth。

Owner 判斷:

- report 可以 eventual consistent。
- report 錯誤時要能重建或對帳。
- 面試要講清楚 source data、projection table、summary table 的分工。

## 2. Projection Key

current code 的 key 是:

```text
agentId + "_" + playerId + "_" + dataDay + "_" + currency
```

這代表報表 identity 至少有四個維度:

- agent: 避免跨代理 collision。
- player: 玩家維度。
- day: 報表日。
- currency: 避免多幣別混算。

`#590` 與 `#702` 都顯示 key / currency 不是小修；它是 report correctness 的核心。

## 3. In-memory Aggregation

`reportMap` 是 consumer service field。這個設計讓 process 內累積值可以覆寫 DB row，但 owner 要注意:

- memory growth。
- consumer concurrency 共享 state。
- JVM restart 後 state loss。
- DB partial failure 後 map 與 DB 的狀態差。

如果重做，較 owner-grade 的方向可能是:

- DB-level unique key + idempotent upsert。
- event id / bet id 去重表。
- 用 source bet record batch rebuild report。
- 定期清 map 或改成 per-message / per-window aggregation。

## 4. Batch Lookup

current `findOldPlayers` 用固定 800 batch 的 `(agent_id, player_id, data_day, currency) IN (...)`，不足時 padding。

可能目的:

- 避免 SQL placeholder 動態長度過大。
- 控制單次查詢大小。

待確認:

- DB index 是否支援這組 composite lookup。
- padding 是否影響 query plan。
- 大量 reportMap values 時是否造成不必要查詢。

## 5. Summary / Backup / Delete

Quartz job 把舊日資料彙總到 `dataDay = 0`，再 backup / delete。

Owner 風險:

- summary update 是加總，不是覆寫。
- backup / delete 與 summary 之間有 partial failure window。
- per-player catch 會讓 job 局部成功、局部失敗。

Owner 改善:

- summary job execution id。
- processed / summary / backup / deleted count。
- backup table unique key 或 dedupe。
- rerun 前檢查已 summary 範圍。
- rebuild report runbook。

## 6. Observability

目前看到的是一般 log 與 exception log，未確認 metric / alert。

建議觀測維度:

- topic / partition / offset。
- batch id。
- agentId / playerId / dataDay / currency。
- insert count / update count。
- old row count。
- summary count / backup count / delete count。
- exception count by job execution。
