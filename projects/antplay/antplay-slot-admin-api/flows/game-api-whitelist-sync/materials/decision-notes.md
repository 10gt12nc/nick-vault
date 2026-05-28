# game-api-whitelist-sync Decision Notes

日期: 2026-05-28

## DB + Redis 雙層設計

| 選項 | 優點 | 代價 |
| --- | --- | --- |
| runtime 每次查 DB | 設定來源單一 | API latency 受 DB 影響，DB 壓力高 |
| runtime 查 Redis | 快、適合高頻 filter | 需要處理 DB / Redis 不一致 |
| 只放 Redis 不放 DB | 簡單快 | 後台查詢、審計、重建困難 |

本 flow 採 DB + Redis：DB 保存設定，Redis 支撐 runtime check。這是合理方向，但 owner 要補 reconciliation。

## 需要注意的 production 判斷

- Redis update 不在 DB transaction 裡。
- `queryExistIp` 不能取代 DB unique key。
- `WhitelistSync` 先動 Redis 再動 DB，rollback 不會還原 Redis。
- hard-coded merchant / prefix 是特定需求，不宜包裝成通用平台能力。
- runtime filter 的 agentId extraction、body wrapper、filter order 都會影響實際放行判斷。

## Owner 改善方向

- DB unique key: `(agent_id, ip)`。
- Cache rebuild: 從 DB 重建 `antplay:GameApiWhiteIp:{agentId}`。
- Diff check: 定期比對 DB vs Redis。
- Sync result: 記錄本次 sync 影響 agent 數、成功 / 失敗清單。
- Metrics: runtime reject count、Redis operation failure、sync duration。
- Alert: Redis update fail、filter reject spike、agentId missing spike。
