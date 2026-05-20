# ugsoft-connector-api

`ugsoft-connector-api` 是 UGSoft 的 provider connector / gateway repo。它比 `ugsoft-admin-api` 更接近第三方 provider integration，包含商戶端 connector API、AntPlay / DerPlay adapter、callback、transfer wallet、request log / bet record MQ、熔斷、白名單、分表與補償 job 等線索。

## 目前整理狀態

| 項目 | 狀態 |
| --- | --- |
| contribution claim consolidation | 已完成 / rolling |
| Step 1 / Step 2 | 尚未正式建立 |
| Flow packages | 尚未建立 |
| 正式履歷 | 可保守補入 provider connector / transfer wallet / MQ 素材 |

## 先讀

- [contribution-claim-consolidation.md](contribution-claim-consolidation.md)

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
