# antplay-slot-admin-api

`antplay-slot-admin-api` 是 AntPlay slot 系列的後台 API / control plane repo，使用 Java / Spring Boot，涵蓋 admin / merchant login、JWT / RBAC、商戶設定、白名單、玩家 / 投注 / request log 查詢、每日 / 每小時報表、RTP / 暗池 / 活動風控監控、Quartz job 與 RabbitMQ listener。

## Status

| 項目 | 狀態 |
| --- | --- |
| contribution claim consolidation | 已完成 / rolling / 2026-05-20 |
| Flow Track | 未開始 |
| 履歷判斷 | 真實開發過 + code-backed，可保守放後台 API / risk ops / async data processing |
| 下一步 | `antplay antplay-slot-admin-api Step 1` |

## Claim Boundary

可保守說:

- 參與 `antplay-slot-admin-api` 後台 API / control plane 開發與維護。
- 處理 admin / merchant auth、JWT / refresh / 401、商戶白名單、Game API 白名單同步、超級代理、商戶玩家 / 投注 / request log / 報表查詢。
- 參與 RTP / 暗池 / 活動風控監控、玩家單點控制、RabbitMQ request log / 風控通知與 Quartz job 類非同步流程。

不可誇大:

- 不寫主導完整 AntPlay slot platform。
- 不寫完整遊戲下注 / 派彩 / rollback runtime owner。
- 不寫完整 wallet / ledger / reconciliation owner。
- 不寫完整 RabbitMQ architecture / exactly-once / outbox owner。
- 不寫量化改善或完整事故 owner，除非後續補 ticket / MR / production issue。

## Files

- [contribution-claim-consolidation.md](/Users/nick/Git/nick/nick-vault/projects/antplay/antplay-slot-admin-api/contribution-claim-consolidation.md)
