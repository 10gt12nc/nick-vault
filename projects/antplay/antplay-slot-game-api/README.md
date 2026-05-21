# antplay-slot-game-api

`antplay-slot-game-api` 是 AntPlay slot 系列的 Java / Spring Boot 遊戲 API / runtime repo，涵蓋 game init、bet、voucher bet、agent API callback、transfer wallet、bet record、dark pool / RTP、player control、RabbitMQ request log、Quartz 補通知與分表。

## Status

| 項目 | 狀態 |
| --- | --- |
| contribution claim consolidation | 已完成 / rolling / 2026-05-20 |
| Flow Track | Step 1 已完成 / 2026-05-21 |
| 履歷判斷 | 真實開發過 + code-backed，可保守放遊戲 API runtime / betting-settlement / transfer wallet / async log |
| 下一步 | `antplay antplay-slot-game-api Step 2` |

## Claim Boundary

可保守說:

- 參與 `antplay-slot-game-api` 遊戲 API / slot runtime 開發維護。
- 處理 game init、bet / settle / rollback、bet record、transfer wallet、分表、request log MQ、白名單與 auth token 類 runtime 流程。
- 參與轉帳錢包 deadlock 補償、bet record 分表 / 查詢、RabbitMQ request log 非同步化、RTP / dark pool / player control 關聯修正。

不可誇大:

- 不寫主導完整 AntPlay slot platform。
- 不寫完整遊戲數學 / RTP 策略 owner。
- 不寫完整 wallet / ledger / reconciliation owner。
- 不寫完整 RabbitMQ / Kafka architecture、exactly-once 或 outbox owner。
- 不寫量化改善或完整事故 owner，除非後續補 ticket / production incident。

## Files

- [step1-candidate-flows.md](/Users/nick/Git/nick/nick-vault/projects/antplay/antplay-slot-game-api/step1-candidate-flows.md)
- [contribution-claim-consolidation.md](/Users/nick/Git/nick/nick-vault/projects/antplay/antplay-slot-game-api/contribution-claim-consolidation.md)
