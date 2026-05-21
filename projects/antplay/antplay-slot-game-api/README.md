# antplay-slot-game-api

`antplay-slot-game-api` 是 AntPlay slot 系列的 Java / Spring Boot 遊戲 API / runtime repo，涵蓋 game init、bet、voucher bet、agent API callback、transfer wallet、bet record、dark pool / RTP、player control、RabbitMQ request log、Quartz 補通知與分表。

## Status

| 項目 | 狀態 |
| --- | --- |
| contribution claim consolidation | 已完成 / rolling / 2026-05-20 |
| Flow Track | `slot-bet-settle-rollback` Step 5 已完成；`transfer-wallet-money-in-out` Step 5 已完成；`request-log-rabbitmq-async` Step 5 已完成；`bet-record-sharding-schema-route` Step 5 已完成 / 2026-05-21 |
| 履歷判斷 | 真實開發過 + code-backed，可保守放遊戲 API runtime / betting-settlement / transfer wallet / async log / high-traffic table governance |
| 下一步 | `antplay antplay-slot-game-api runtime-rtp-darkpool-player-control Step 3` |

## Claim Boundary

可保守說:

- 參與 `antplay-slot-game-api` 遊戲 API / slot runtime 開發維護。
- 處理 game init、bet / settle / rollback、bet record、transfer wallet、分表、request log MQ、白名單與 auth token 類 runtime 流程。
- 參與轉帳錢包 deadlock 補償、bet record 分表 / 查詢、RabbitMQ request log 非同步化、RTP / dark pool / player control 關聯修正。
- `slot-bet-settle-rollback` Step 5 已確認可作 project-level claim 的強化 evidence；但不單獨寫成完整下注結算 / wallet owner。
- `transfer-wallet-money-in-out` Step 5 已完成；可回填 project-level transfer wallet / DB + Redis consistency / transaction lookup / 分表 evidence，但不單獨寫成完整 transfer wallet owner。
- `request-log-rabbitmq-async` Step 5 已完成；已追到 game-api producer、admin-api consumer 與 Nick / `10gt12nc` #774 direct commits，可作 async audit / observability project-level claim，但不單獨寫成完整 RabbitMQ / event platform owner。
- `bet-record-sharding-schema-route` Step 5 已完成；可回填 project-level high-traffic data governance / schema route / partition key evidence，但不單獨寫成完整 sharding owner。

不可誇大:

- 不寫主導完整 AntPlay slot platform。
- 不寫完整遊戲數學 / RTP 策略 owner。
- 不寫完整 wallet / ledger / reconciliation owner。
- 不寫完整 RabbitMQ / Kafka architecture、exactly-once 或 outbox owner。
- 不寫完整 sharding architecture 或 production automatic table creation owner；current code 顯示 table creator service 存在，但 create-table jobs 的實際 agent loop 已停用。
- 不寫量化改善或完整事故 owner，除非後續補 ticket / production incident。

## Files

- [flows/slot-bet-settle-rollback/flow.md](/Users/nick/Git/nick/nick-vault/projects/antplay/antplay-slot-game-api/flows/slot-bet-settle-rollback/flow.md)
- [flows/slot-bet-settle-rollback/career-interview.md](/Users/nick/Git/nick/nick-vault/projects/antplay/antplay-slot-game-api/flows/slot-bet-settle-rollback/career-interview.md)
- [flows/transfer-wallet-money-in-out/flow.md](/Users/nick/Git/nick/nick-vault/projects/antplay/antplay-slot-game-api/flows/transfer-wallet-money-in-out/flow.md)
- [flows/transfer-wallet-money-in-out/career-interview.md](/Users/nick/Git/nick/nick-vault/projects/antplay/antplay-slot-game-api/flows/transfer-wallet-money-in-out/career-interview.md)
- [flows/request-log-rabbitmq-async/flow.md](/Users/nick/Git/nick/nick-vault/projects/antplay/antplay-slot-game-api/flows/request-log-rabbitmq-async/flow.md)
- [flows/request-log-rabbitmq-async/career-interview.md](/Users/nick/Git/nick/nick-vault/projects/antplay/antplay-slot-game-api/flows/request-log-rabbitmq-async/career-interview.md)
- [flows/bet-record-sharding-schema-route/flow.md](/Users/nick/Git/nick/nick-vault/projects/antplay/antplay-slot-game-api/flows/bet-record-sharding-schema-route/flow.md)
- [flows/bet-record-sharding-schema-route/career-interview.md](/Users/nick/Git/nick/nick-vault/projects/antplay/antplay-slot-game-api/flows/bet-record-sharding-schema-route/career-interview.md)
- [step2-flow-comparison.md](/Users/nick/Git/nick/nick-vault/projects/antplay/antplay-slot-game-api/step2-flow-comparison.md)
- [step1-candidate-flows.md](/Users/nick/Git/nick/nick-vault/projects/antplay/antplay-slot-game-api/step1-candidate-flows.md)
- [contribution-claim-consolidation.md](/Users/nick/Git/nick/nick-vault/projects/antplay/antplay-slot-game-api/contribution-claim-consolidation.md)
