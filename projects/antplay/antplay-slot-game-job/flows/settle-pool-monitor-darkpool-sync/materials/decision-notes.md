# Decision Notes - settle-pool-monitor-darkpool-sync

日期: 2026-05-25
Step 4 補充日期: 2026-05-25
Step 5 補充日期: 2026-05-25

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

- Step 4 已可作正式面試 analysis case。
- 不回填正式履歷主 bullet。
- 不說主導 complete risk / dark pool / jackpot platform。

## 6. Step 4 面試用 Owner Decision

如果面試官追問「你會怎麼收斂這條 flow」，回答順序建議是:

1. 先補 projection idempotency：用 `batchId + betId + poolType` 或 processed-event table 防 replay double count。
2. 再補 reset sync 安全性：sync state、distributed lock、staging table、row count / checksum、restore / retry runbook。
3. 補 observability：skipped additional_info、group count、increment count、reset rows、sync failure、alert count。
4. 補 unit governance：每個 key / threshold / log / dashboard 標清 cent 或 hao。
5. 最後補 reconciliation：從 bet record 重算 expected pool，和 `settled_pool` / Redis snapshot 比對。

這個順序能表現 Senior / Owner 判斷：先保 correctness，再保 recoverability，最後補 visibility 與 runbook。

## 7. Step 5 Claim Decision

Step 5 不改 technical recommendation，但收斂 career claim:

- 這條 flow 的技術價值高，適合面試追問。
- Nick direct evidence 不足，不能轉成正式履歷成果。
- 若面試官問「你做過嗎」，回答要改成「這條是我 code-backed 分析過的 flow；我會用它說明我如何拆 projection consistency，不會說這是我主導開發」。
- 若要補成可履歷 claim，需要 Nick 本人確認或 ticket / MR / production issue evidence。
