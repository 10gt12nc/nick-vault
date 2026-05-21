# transfer-wallet-money-in-out Interview Notes

日期: 2026-05-21

## 1. Step 3 面試素材草稿

這條 flow 可以用來回答:

- 你怎麼看 wallet API 的 idempotency？
- DB 和 Redis balance 不一致怎麼辦？
- transfer-out 如何避免扣成負數？
- transaction record 先寫成功但 wallet 更新失敗怎麼處理？
- request log / lookup 的價值是什麼？

## 2. 90 秒草稿

Transfer wallet 的核心不是 API 本身，而是 money state 怎麼保持一致。這條 flow 入口會先驗簽，再用 `transferReferenceId` 做防重與查單 key。主流程會寫 transaction record、order lookup，再更新 wallet balance，最後補 request log response。比較大的風險是 transaction、lookup、DB wallet、Redis balance 不是同一個 atomic unit，所以如果 transaction 已寫 SUCCESS，但 wallet 或 Redis 更新失敗，就會出現查得到交易但餘額不一致的狀況。Owner 角度要補 DB unique key、conditional update、repair / reconciliation，以及明確定義 DB 是 source of truth、Redis 是 cache。

## 3. Senior 追問方向

| 問題 | 回答方向 |
| --- | --- |
| 3 秒 Redis lock 夠嗎？ | 不夠當完整 idempotency，只能擋短時間重送；長期仍要靠 DB unique key / transaction 查詢 |
| 為什麼有 `transferReferenceId` 又有 `transactionId`？ | reference 是外部 idempotency / 查詢 key，transactionId 是內部交易識別 |
| DB 和 Redis 誰是 source of truth？ | 應以 DB 為 source of truth，Redis 是 hot balance；需要 repair job 對齊 |
| transfer-out 如何防負數？ | 目前先讀 balance 再扣，理想上要用 DB conditional update 或 lock |
| request log 寫失敗要不要 rollback？ | request log 是 audit，不應阻塞 money flow，但要有補寫 / observability |

## 4. Step 4 待補

- 30 秒短版。
- 3 分鐘完整 STAR。
- failure scenarios 拆成重送、併發、DB / Redis dual-write、lookup / request log。
- owner 改善方案排序。
