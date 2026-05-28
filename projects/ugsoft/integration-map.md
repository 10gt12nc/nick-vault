# UGSoft integration map v1

更新日期：2026-05-28

本檔聚焦 UGSoft 跨 project 的資料流與整合邊界，搭配 [architecture-map.md](architecture-map.md) 閱讀。

## Integration Inventory

| 整合面 | 上游 | 下游 | 已整理代表 flow | 主要風險 | Claim 邊界 |
| --- | --- | --- | --- | --- | --- |
| Transfer wallet / transaction query | merchant client -> `ugsoft-connector-api` | provider adapter、provider transaction state | `transfer-wallet-in-out-query` | provider position、amount / currency / language mapping、duplicate transfer、transaction lookup | 可寫 provider connector / transfer wallet；不可寫完整 wallet owner |
| Provider callback -> MQ | provider -> `ugsoft-connector-api` | RabbitMQ / admin consumer / report | `provider-callback-bet-settle-to-mq` | callback idempotency、eventual consistency、MQ duplicate / loss | 可寫 callback / MQ supporting；不可寫 exactly-once / outbox owner |
| Request / bet record sync | connector / MQ / job | admin-api / report / risk views | `request-bet-record-mq-sync`、`connect-bet-record-mq-ingestion` | duplicate check、quota update、report lag、consumer replay | 可寫非同步資料處理；不可寫完整 event platform |
| RequestLog admin consumer | connector / game-api logs -> MQ | `ugsoft-admin-api` request log DB / query | `request-log-rabbitmq-admin-consumer` | async audit not source of truth、consumer idempotency、查詢時間窗 | 可寫 admin consumer / observability；不可寫完整 RabbitMQ architecture |
| White IP control plane | admin ops -> `ugsoft-admin-api` | provider / game API runtime cache | `game-api-provider-white-ip-control-plane` | DB / Redis / fanout cache reload、fail closed / stale cache | 可寫 control plane / whitelist；不可寫完整 security platform |

## Trace Map

### 查 transfer / provider 問題

1. 先定位是 merchant request、connector mapping、provider response 還是 callback 事件問題。
2. 查 connector API 的 trace id / transfer reference / provider transaction id。
3. 若 callback 已到但 admin 查詢異常，追 MQ / admin consumer / report projection，不先假設 provider state 錯。
4. 若重送或查單，分清 provider transaction lookup 與本地狀態 source。

### 查 request / bet record 報表問題

1. 先確認資料來源是 connector callback、request log MQ 還是 bet record MQ。
2. 查 admin consumer 的 duplicate guard、quota update 與寫入狀態。
3. 若報表 lag，先看 consumer / job / query time window，不把後台查詢直接當 source of truth。

### 查白名單 / access control 問題

1. 先定位是 admin API 更新、DB / Redis cache、fanout reload，還是 runtime service 沒套用。
2. 白名單是 control plane，不等於完整 security platform。
3. 缺 evidence 時不說所有 provider / Game API 都已一致 fail closed。

## 面試用大圖講法

```text
UGSoft 我會分成 connector 和 admin 兩層。connector-api 是 provider gateway，處理商戶端 login、balance、transfer in-out、transaction query、provider callback，以及 request / bet record MQ；admin-api 是後台控制面，處理 auth / RBAC、商戶與 provider 白名單、RequestLog / BetRecord consumer、報表和風控監控。面試時我會先分清 provider contract、connector local state、MQ async audit 和 admin report view，不會把後台查詢當交易 source of truth，也不會把 MQ 寫成 exactly-once，除非有證據。
```

## 不可誇大

- 不說主導完整 UGSoft 平台。
- 不說完整 provider gateway / connector architecture owner。
- 不說完整 wallet / reconciliation owner。
- 不說完整 RabbitMQ / exactly-once / outbox owner。
- 不把 workspace docs 反向包裝成 service runtime direct development。
- 不把 admin control plane 擴張成完整 security platform owner。
