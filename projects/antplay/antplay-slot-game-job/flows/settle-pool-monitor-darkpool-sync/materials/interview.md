# Interview Notes - settle-pool-monitor-darkpool-sync

日期: 2026-05-25

## Step 3 狀態

本檔是 `settle-pool-monitor-darkpool-sync` 的 Step 3 初版面試素材。正式 30 秒 / 90 秒 / 3 分鐘稿會在 Step 4 收斂。

## 面試主軸

```text
settled_bets -> group by pool type -> settled_pool increment -> reset sync from Redis -> alert
```

這條 flow 要講的是 projection consistency，不是交易正確性本身。

## 可講問題

### Q1: 為什麼這條不是 source of truth？

因為下注 / 派彩結果要回到 bet record / wallet / settlement。`settled_pool` 是 settlement event 後的 monitor projection，用來看 pool 狀態和 alert。

### Q2: Kafka event 重送會怎樣？

current `saveOneIncrement` 以 DB current value 加 batch value，未見 batch id / bet id 去重。若同一 event 重送，可能 double count。

### Q3: Reset sync 最大風險是什麼？

`SyncDbFromRedis` 先刪 reset flag，再從 Redis 讀 snapshot，最後 `saveAll` backup / truncate / insert。如果中途失敗，flag 已消失，DB 可能是 partial state，需要 retry / restore runbook。

### Q4: 為什麼單位很重要？

normal / activity 多用 cent，jackpot 轉 hao。maxWin / diff alert 如果混用單位，就會誤報或漏報。

### Q5: 為什麼這條不能放履歷主成果？

因為本輪 path-specific history 與 blame 顯示 core current implementation 主要是 Arnold / Eliot，沒有 Nick direct commit。可作 code-backed analysis，不可說真實開發。

## Failure Scenarios

| 情境 | 面試回答重點 |
| --- | --- |
| event replay | `settled_pool` increment 缺 event idempotency，可能 double count |
| reset sync crash | flag 先刪、DB truncate / insert 中斷，需要 runbook |
| busy guard disabled | `AtomicBoolean` 存在但註解，缺明確 distributed lock |
| additional_info null | production log error and skip，projection 可能漏算 |
| jackpot unit mismatch | jackpot hao vs normal cent，alert 可能失真 |
| admin / alert 未掃 | 只能說 producer side / repository side，不能宣稱完整 monitoring |

## Lead / Architect 追問方向

| 追問 | 回答方向 |
| --- | --- |
| 如何讓 projection replay-safe？ | batch id / bet id / pool type deterministic key，或 event processed table |
| reset sync 怎麼設計更安全？ | 狀態表、distributed lock、idempotent rebuild、checksum、restore runbook |
| 需要 outbox 嗎？ | 這是 consumer projection，重點是 processed-event idempotency 與 reconciliation，不一定是 outbox |
| 怎麼做 reconciliation？ | 從 bet record 按同樣 grouping 重算，和 `settled_pool` 比對差異 |
| 怎麼觀測？ | skipped additional_info、group count、increment count、reset rows、alert count、sync failure metric |

## Step 4 待收斂

- 30 秒 / 90 秒 / 3 分鐘版本。
- STAR。
- Senior 追問短答。
- 不能誇大說法。
