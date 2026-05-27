# ugsoft-connector-api

`ugsoft-connector-api` 是 UGSoft 的 provider connector / gateway repo。它比 `ugsoft-admin-api` 更接近第三方 provider integration，包含商戶端 connector API、AntPlay / DerPlay adapter、callback、transfer wallet、request log / bet record MQ、熔斷、白名單、分表與補償 job 等線索。

## 目前整理狀態

| 項目 | 狀態 |
| --- | --- |
| contribution claim consolidation | 已完成 / refreshed / 2026-05-27 |
| Step 1 | 已完成：[`step1-candidate-flows.md`](step1-candidate-flows.md) |
| Step 2 | 已完成：[`step2-flow-comparison.md`](step2-flow-comparison.md) |
| Flow packages | 本批三條代表 flow `transfer-wallet-in-out-query`、`provider-callback-bet-settle-to-mq`、`request-bet-record-mq-sync` 均已完成 Step 5；不得宣稱全 project flow 完整 |
| 正式履歷 | 可保守補入 provider connector / transfer wallet / callback / request-bet-record MQ / job sync 素材 |

## 先讀

- [contribution-claim-consolidation.md](contribution-claim-consolidation.md)
- [step1-candidate-flows.md](step1-candidate-flows.md)
- [step2-flow-comparison.md](step2-flow-comparison.md)

## Flow Track

Step 1 已篩出 6 條候選，Step 2 已完成比較與排序：

1. `transfer-wallet-in-out-query`
2. `provider-callback-bet-settle-to-mq`
3. `request-bet-record-mq-sync`
4. `provider-client-login-launch-game`
5. `provider-circuit-breaker-fast-fail`
6. `schema-route-partition-transfer-record`

本批代表 flows：

1. `transfer-wallet-in-out-query`
2. `provider-callback-bet-settle-to-mq`
3. `request-bet-record-mq-sync`

第一順位單條 flow Step 3 / Step 4 / Step 5 已完成：

```text
projects/ugsoft/ugsoft-connector-api/flows/transfer-wallet-in-out-query/flow.md
```

第二順位單條 flow Step 3 / Step 4 / Step 5 已完成：

```text
projects/ugsoft/ugsoft-connector-api/flows/provider-callback-bet-settle-to-mq/flow.md
```

第三順位單條 flow Step 3 / Step 4 / Step 5 已完成：

```text
projects/ugsoft/ugsoft-connector-api/flows/request-bet-record-mq-sync/flow.md
```

`ugsoft-connector-api contribution claim consolidation refresh` 已完成。三條代表 flow 的 Step 5 claim gate 已回填 project-level claim，並保留不可誇大的邊界。這是非 iwin 廣度補強；目前沒有預設下一步。

## 履歷邊界

可用:

- 參與 UGSoft provider connector / gateway 開發與維護。
- 參與 AntPlay / DerPlay adapter、login / balance / transfer in-out / bet-settle / transaction lookup、callback 與 request / bet record MQ。
- 可面試延伸：provider contract mapping、sign / amount / currency / language conversion、transfer wallet provider position、callback idempotency、MQ eventual consistency、Circuit Breaker fast-fail、分表與 compensation job。

不可誇大:

- 不寫成主導完整 UGSoft 平台。
- 不寫成全部 provider owner 或完整 connector architecture owner。
- 不寫完整 wallet source of truth owner。
- 不寫已建立完整 reconciliation / exactly-once / outbox。
- 不寫量化改善或完整事故 owner。
