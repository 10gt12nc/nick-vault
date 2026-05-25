# Decision Notes - settle-pool-monitor-darkpool-sync

日期: 2026-05-25

## 1. Projection vs Source Of Truth

`settled_pool` 是 projection，不是下注交易 source of truth。

交易真相應回到:

- bet record。
- wallet / settlement result。
- upstream runtime dark pool Redis state。

Projection 的 owner decision 是「如何讓衍生資料可重建、可比對、可觀測」，不是把它包裝成交易主狀態。

## 2. Increment Projection 的 Idempotency

`saveOneIncrement` 很直覺，但 replay sensitive。

如果要 replay-safe，常見做法:

- processed event table: `batchId + betId + poolType`。
- deterministic projection key + source event version。
- 先聚合到 staging table，再用 replace / recompute。
- 提供 reconciliation job 從 bet record 重算。

本 repo 目前未看到這些完整 evidence，因此面試要說「current code 需要補」。

## 3. Reset Sync 的風險

Current sync:

```text
delete reset flag
-> read Redis
-> backup DB
-> truncate DB
-> insert snapshot
```

風險:

- flag 先刪造成 failure 後不自動重跑。
- truncate 後 insert 失敗造成 DB partial。
- 多 instance 缺 distributed lock。
- backup table 可用，但 restore path 未確認。

更安全設計:

- sync job state: pending / running / failed / done。
- lock key with TTL。
- staging table rebuild，驗證 row count / checksum 後 swap。
- failure alert + manual retry command。

## 4. 單位與 Key Design

Normal / activity 使用 cent，jackpot 使用 hao。這種設計能讓 jackpot 精度更高，但會增加 owner 認知負擔。

面試要主動講:

- 不能把 cent / hao 混在同一個 alert。
- key name / type / unit 應清楚。
- log 裡要用對 `centToYuan` / `haoToYuan`。

## 5. Claim Decision

這條 flow 技術含量足，但不是 Nick direct implementation。

Career decision:

- Step 3 / Step 4 可作面試 analysis case。
- 不回填正式履歷主 bullet。
- 不說主導 complete risk / dark pool / jackpot platform。
