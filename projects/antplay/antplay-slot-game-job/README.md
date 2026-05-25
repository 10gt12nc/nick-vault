# antplay-slot-game-job

`antplay-slot-game-job` 是 AntPlay slot 系列的 Java / Spring Boot job / event processing repo，涵蓋 Kafka consumer、Quartz job、代理玩家報表聚合、活動累積投注、big-win notification、分表 / job config 與部分 settle pool / risk 相關後續維護。

## Status

| 項目 | 狀態 |
| --- | --- |
| contribution claim consolidation | 已完成 / rolling / 2026-05-20 |
| Flow Track | `settle-pool-monitor-darkpool-sync` Step 4 已完成 / 2026-05-25 |
| 履歷判斷 | 真實開發過 + code-backed，可保守放 Kafka / Quartz job、代理玩家報表、活動累積投注、big-win notification、分表 / job config |
| 下一步 | `antplay antplay-slot-game-job settle-pool-monitor-darkpool-sync Step 5` |

## Claim Boundary

可保守說:

- 參與 `antplay-slot-game-job` job / event processing 開發維護。
- 處理 Kafka `settled_bets` consumer、Quartz report job、代理玩家報表聚合、活動累積投注、big-win notification 與 db partition / job config 類維護。
- 參與報表 key / currency / daily summary 類資料一致性修正與 job schedule 防呆。
- `proxy-user-data-report-projection Step 5` 已確認可回填 project-level claim，但單條 flow 不直接改 `05 / 08`。
- `activity-accumulated-bet-voucher Step 5` 已確認只作 reward correctness 面試素材與 project-level supporting evidence；不單獨放正式履歷。
- `big-win-notification Step 5` 已完成 claim gate；可回填 project-level supporting evidence，但不單獨更新 `05 / 08`。
- `settle-pool-monitor-darkpool-sync Step 4` 已完成正式面試 case；目前只作 code-backed / analysis-first 面試素材，不回填正式履歷。

不可誇大:

- 不寫主導完整 AntPlay slot platform。
- 不寫完整 Kafka event platform、exactly-once、outbox 或完整 replay architecture owner。
- 不寫 big-win notification guaranteed delivery；`_id` / `BetIdPersistence` / downstream push bridge 未證明去重，`fullPlayerName` 也未證明已被下游過濾。
- 不寫完整 settle pool / risk / jackpot owner；後續 commit 顯示其他人有大量接續開發。
- 不把 `settle-pool-monitor-darkpool-sync` 寫成 Nick 真實開發；目前未找到 Nick / `10gt12nc` direct path-specific evidence。
- 不寫完整 BI / report platform owner。
- 不寫完整遊戲數學 / RTP 策略 owner。

## Files

- [contribution-claim-consolidation.md](/Users/nick/Git/nick/nick-vault/projects/antplay/antplay-slot-game-job/contribution-claim-consolidation.md)
- [step1-candidate-flows.md](/Users/nick/Git/nick/nick-vault/projects/antplay/antplay-slot-game-job/step1-candidate-flows.md)
- [step2-flow-comparison.md](/Users/nick/Git/nick/nick-vault/projects/antplay/antplay-slot-game-job/step2-flow-comparison.md)
- [flows/proxy-user-data-report-projection/flow.md](/Users/nick/Git/nick/nick-vault/projects/antplay/antplay-slot-game-job/flows/proxy-user-data-report-projection/flow.md)
- [flows/proxy-user-data-report-projection/career-interview.md](/Users/nick/Git/nick/nick-vault/projects/antplay/antplay-slot-game-job/flows/proxy-user-data-report-projection/career-interview.md)
- [flows/activity-accumulated-bet-voucher/flow.md](/Users/nick/Git/nick/nick-vault/projects/antplay/antplay-slot-game-job/flows/activity-accumulated-bet-voucher/flow.md)
- [flows/activity-accumulated-bet-voucher/career-interview.md](/Users/nick/Git/nick/nick-vault/projects/antplay/antplay-slot-game-job/flows/activity-accumulated-bet-voucher/career-interview.md)
- [flows/big-win-notification/flow.md](/Users/nick/Git/nick/nick-vault/projects/antplay/antplay-slot-game-job/flows/big-win-notification/flow.md)
- [flows/big-win-notification/career-interview.md](/Users/nick/Git/nick/nick-vault/projects/antplay/antplay-slot-game-job/flows/big-win-notification/career-interview.md)
- [flows/settle-pool-monitor-darkpool-sync/flow.md](/Users/nick/Git/nick/nick-vault/projects/antplay/antplay-slot-game-job/flows/settle-pool-monitor-darkpool-sync/flow.md)
- [flows/settle-pool-monitor-darkpool-sync/career-interview.md](/Users/nick/Git/nick/nick-vault/projects/antplay/antplay-slot-game-job/flows/settle-pool-monitor-darkpool-sync/career-interview.md)
