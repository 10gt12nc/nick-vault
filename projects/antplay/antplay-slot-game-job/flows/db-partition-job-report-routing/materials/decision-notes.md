# db-partition-job-report-routing Decision Notes

日期: 2026-05-25

## 技術取捨 1: 動態表名 vs 固定表 + route

動態表名:

- 優點: 直覺，table name 直接表達日期 / agent。
- 缺點: repository 到處拼 SQL，migration / DDL / index / query 條件分散，容易漏表或漏 agent。

固定表 + route:

- 優點: SQL pattern 集中，table name 穩定，route metadata 可統一治理。
- 缺點: `agentId` / `pt_day` / schema context 變成強 contract；一漏就是查錯庫或跨 agent 污染。

本 flow current direction:

- `pt_bet_record` / `pt_request_log` / `ag_report_player` 固定表名。
- `@UseSchema` 負責 schema route。
- SQL 仍保留 `agent_id` / `pt_day` 等條件。

## 技術取捨 2: AOP route vs explicit route

AOP route:

- 優點: 呼叫方簡潔，只要 method 參數能被解析出 `agentId`。
- 缺點: hidden contract 強，review 時不容易看出是否會切 schema。

Explicit route:

- 優點: 呼叫點清楚，容易測試。
- 缺點: boilerplate 多，nested route / restore 容易由開發者手動漏掉。

Owner 判斷:

- 若使用 AOP，必須 fail fast、log method signature、建立 annotation usage checklist。
- 對 migration / critical table query，建議加 integration test 或 route assertion。

## 技術取捨 3: Cache agent dbGroupNum

好處:

- 減少每次 repository call 都查 agent metadata。

風險:

- dbGroupNum 變更時 cache stale。
- route metadata 是控制面資料，錯誤會造成資料面錯 schema。

Owner 建議:

- cache TTL / invalidation。
- migration 期間 freeze agent dbGroupNum。
- route miss / stale reload metric。

## 技術取捨 4: report summary backup delete

現況:

- summary older than three days。
- update or insert `data_day = 0` summary。
- backup old rows。
- delete old rows。

風險:

- backup 成功 delete 失敗，重跑可能重複 backup。
- summary update 成功 backup / delete 失敗，可能重複加總。
- route 錯 schema 時會造成 silent data mismatch。

Owner 建議:

- summary job state。
- backup unique key 或 idempotent insert。
- before / after count + checksum。
- failure alert 與人工 repair runbook。
