# Claim Boundary - proxy-user-data-report-projection

日期: 2026-05-22

## Claim 分層

| 層級 | 判斷 |
| --- | --- |
| 真實開發過 | 可成立。`10gt12nc` 有 `#386`、`#590`、`#702`、`fix ag_report_player` direct commits。 |
| code-backed | 可成立。current code path 可完整追到 consumer、repository、Quartz job。 |
| 可面試講 | 可成立。Step 4 已整理成正式 Kafka projection / Quartz lifecycle / derived report correctness 面試 case。 |
| 可直接履歷 | 可保守放 project-level bullet，但單條 flow Step 4 不直接更新 05 / 08。 |
| 不可誇大 | 不能說完整 Kafka platform / BI platform / exactly-once / replay architecture owner。 |

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
- 不說 exactly-once / outbox / DLQ / replay architecture 已完整落地。
- 不說完整 BI / report platform owner。
- 不說這條 report flow 直接保證下注或錢包金額正確。
- 不說後續 Arnold / Eliot 修正都是 Nick 做的。

## 待 Step 5 補強

- 追更完整 blame / important diff，確認哪些 current behavior 是 Nick direct contribution，哪些是後續他人 context。
- 確認是否有 DB unique key / index evidence。
- 確認 Kafka retry / DLQ / ack mode。
- 確認是否有 rebuild / replay command 或人工補數流程。
- 決定這條 flow 可回填 project-level consolidation 的最終 wording。
