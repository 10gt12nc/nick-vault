# AntPlay integration map v1

更新日期：2026-05-28

本檔聚焦 AntPlay 跨 project 的資料流與整合邊界，搭配 [architecture-map.md](architecture-map.md) 閱讀。

## Integration Inventory

| 整合面 | 上游 | 下游 | 已整理代表 flow | 主要風險 | Claim 邊界 |
| --- | --- | --- | --- | --- | --- |
| 下注 / 派彩 / rollback runtime | 玩家 / merchant -> `antplay-slot-game-api` | wallet state、bet record、request log、math result | `slot-bet-settle-rollback`、`transfer-wallet-money-in-out` | transaction boundary、deadlock / compensation、duplicate request、provider position | 可保守寫 runtime / betting-settlement / transfer wallet 維護；不可寫完整 wallet owner |
| Request log / async audit | `antplay-slot-game-api` | RabbitMQ -> `antplay-slot-admin-api` / reports | `request-log-rabbitmq-async`、`request-log-rabbitmq-admin-consumer` | async loss / duplicate、consumer idempotency、audit not source of truth | 可寫非同步資料處理；不可寫完整 RabbitMQ architecture |
| Bet record / high-traffic data | `antplay-slot-game-api` | sharding schema、report job、admin query | `bet-record-sharding-schema-route`、`db-partition-job-report-routing` | schema route、partition drift、query correctness | 可寫 high-traffic table governance supporting；不可寫完整 sharding owner |
| Runtime decision / risk | game runtime / admin control | RTP / dark pool / player control / risk monitor | `runtime-rtp-darkpool-player-control`、`settle-pool-monitor-darkpool-sync` | math contract vs runtime acceptance、failure window、risk monitor lag | 可面試講 owner decision；不可寫完整 RTP / risk platform owner |
| Job / projection / notification | Kafka / Quartz / runtime events | report projection、big-win notification、activity voucher | `proxy-user-data-report-projection`、`activity-accumulated-bet-voucher`、`big-win-notification` | replay、duplicate notification、activity correctness、report key / currency | 可寫 job / event processing；不可寫 complete event platform |
| Admin / whitelist / merchant control | admin / merchant ops | Game API whitelist / runtime cache / request log views | `game-api-whitelist-sync` | DB / Redis / runtime fanout、cache reload、fail closed | 可寫後台控制面；不可寫完整 security platform owner |
| Math contract | math-core / `*-math` | Game runtime result handling | five `star-math` representative flows | result contract mismatch、RTP / reel strip drift、feature state transform | 可寫 math module 維護與驗證；不可寫完整遊戲數學 owner |

## Trace Map

### 查下注 / 派彩問題

1. 先定位是 runtime request、transfer wallet、math result 還是 async audit 問題。
2. 查 `antplay-slot-game-api` 的 bet / settle / rollback state、wallet mutation 與 bet record。
3. 若 request log 或報表不同步，追 RabbitMQ、admin consumer、job projection，不先假設 runtime state 錯。
4. 若 RTP / result 被追問，分清 math module result contract 與 game-api runtime decision。

### 查後台 / 風控問題

1. 先定位是 `antplay-slot-admin-api` 的查詢 / control plane，還是 `antplay-slot-game-api` runtime 行為。
2. 白名單 / merchant config 類要追 DB、Redis / cache reload、runtime fanout。
3. RequestLog / BetRecord 類要追 producer、MQ、consumer、dedup / update 行為。
4. 風控 / RTP / 暗池監控要標明監控 lag 與 runtime source of truth。

### 查 math / feature 問題

1. 先分清 `math-core` contract、單一 `*-math` module、game-api runtime result acceptance。
2. RTP / reel strip / buy free / jackpot / special feature 只用代表 flows 講，不平均聲稱全部 game module。
3. 若要講 production claim，回到 direct commits 與 grouped consolidation，不用單一 feature 擴張成完整 math owner。

## 面試用大圖講法

```text
AntPlay 我會分成 runtime、job、admin control plane 和 math contract 四層。game-api 負責玩家遊戲流程、下注派彩、轉帳錢包、bet record 和 request log；game-job 負責 Kafka / Quartz 的報表 projection、activity supporting flow、big-win notification 和分表；admin-api 是後台控制面，處理商戶、白名單、request log / bet record consumer、風控監控；math-core 和各個 *-math repo 提供 slot math result contract、RTP / reel strip、buy free、jackpot 和特殊 feature 邏輯。面試時我會先分清 runtime source of truth、async audit、report projection 和 control plane，避免把後台查詢或 MQ log 當成交易帳本。
```

## 不可誇大

- 不說主導完整 AntPlay slot platform。
- 不說完整 wallet / ledger / reconciliation owner。
- 不說完整 RTP / 遊戲數學 / certification owner。
- 不說完整 Kafka / RabbitMQ exactly-once architecture owner。
- 不把 admin control plane 擴張成 runtime owner。
- 不把 `*-math` grouped evidence 擴張成全部 math repo 全量掌握。
