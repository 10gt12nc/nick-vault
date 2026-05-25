# Claim Boundary - proxy-user-data-report-projection

日期: 2026-05-22

Step 5 補充日期: 2026-05-25

## Claim 分層

| 層級 | 判斷 |
| --- | --- |
| 真實開發過 | 可成立。`10gt12nc` 有 `#386`、`#590`、`#702`、`fix ag_report_player` direct commits。 |
| code-backed | 可成立。current code path 可完整追到 consumer、repository、Quartz job。 |
| 可面試講 | 可成立。Step 4 已整理成正式 Kafka projection / Quartz lifecycle / derived report correctness 面試 case，Step 5 已補 current blame / Kafka retry / DB constraint 邊界。 |
| 可直接履歷 | 可回填 project-level consolidation，作 AntPlay job / event processing、Kafka / Quartz、report projection evidence；單條 flow Step 5 不直接更新 05 / 08。 |
| 不可誇大 | 不能說完整 Kafka platform / BI platform / exactly-once / replay architecture owner；也不能把後續 Arnold / Eliot current fixes 說成 Nick 完成。 |

## 可放履歷

保守版本:

- 參與 AntPlay slot job / event processing 維護，處理 Kafka `settled_bets` 報表 projection、代理玩家日報表聚合、幣別維度修正與 Quartz summary / backup / cleanup。

可以搭配 project-level claim:

- Kafka consumer / Quartz job。
- 報表 projection / derived data。
- report key / currency / batch update。
- historical summary / backup / cleanup。

## 可面試講

- 為什麼 report table 是 derived projection，不是交易 source of truth。
- 為什麼 projection key 要包含 `agentId + playerId + dataDay + currency`。
- `#590` currency 拆分的資料一致性意義。
- `#702` key 重複修正的 identity 意義。
- `reportMap` 長生命週期的 memory / restart / concurrency 風險。
- summary / backup / delete partial failure 與重跑風險。

## 不可說

- 不說主導完整 AntPlay slot job platform。
- 不說完整 Kafka event platform owner。
- 不說 exactly-once / outbox / replay architecture 已完整落地；Kafka config 有基本 retry / dead-letter-topic evidence，但未確認 production DLQ 消費治理與補數 runbook。
- 不說完整 BI / report platform owner。
- 不說這條 report flow 直接保證下注或錢包金額正確。
- 不說後續 Arnold / Eliot 修正都是 Nick 做的。

## Step 5 Claim Gate 結論

- Step 5 結論: 可回填 project-level `antplay-slot-game-job` contribution consolidation，作為 Kafka report projection / Quartz summary / report key correctness 的直接 evidence。
- Direct evidence 強項: `#386` 初版 proxy report projection、`#590` currency 維度、`#702` key collision、`fix ag_report_player` table / agent filter。
- Current behavior 邊界: `ReportAgentPlayerRepositoryInternal` 的 2026 batch padding / safety context 多為 Arnold / Eliot 後續修正，只能作 current code-backed 補讀，不當作 Nick direct contribution。
- Kafka 邊界: source code 有 default listener retry / dead-letter-topic evidence，但 replay / DLQ 消費治理 / 補數流程未確認。
- DB 邊界: source scan 未找到 migration / DDL / unique key evidence；`agent_id + player_id + data_day + currency` 只能保守說是 code-level report identity。
