# decision-notes

## Step 3 判斷

`manual-order-review-repair` 是 recovery flow，不是單純後台 CRUD。它的核心決策是：人工介入時，哪些操作必須走 payment API 觸發 money side effect，哪些直接修 status 只能作為 break-glass。

| Decision | 目前 code 形狀 | 風險 | Step 4 要收斂 |
| --- | --- | --- | --- |
| 正常人工審核走 `/oderView` | `app_bi bill_check` 呼叫 payment API | API 失敗時後台只拿到失敗，仍要查 payment / provider / wallet | 面試說清楚人工審核不是直接改 DB |
| 只處理 `WAIT` | app_bi 與 payment 都 guard `status=1` / `WAIT` | `PROCESSING` 卡住無法用同一條審核路徑處理 | unknown 狀態 SOP |
| 退回必須退款 | `oderView` 提現退回會呼叫 `upperDeposit` | 退款成功但訂單更新失敗會半完成 | wallet idempotency / reconciliation |
| 補單 / GM 上分走 `gameRecharge` | 建單後 `upperDeposit` / `gmDownScore` | 可能與 provider callback 晚到衝突 | 補單前 evidence checklist |
| 直接修 status 是 break-glass | `repairOrderService` 只 update status / remark / opLog | 可能沒有 wallet / 統計 side effect | repair 權限與 audit boundary |

## Owner 口徑

最保守、最準確的講法：

> 人工修復不能只設計成「後台能改狀態」。money 系統裡，人工動作要分成會觸發副作用的審核 API、和只修 metadata/status 的 break-glass 工具。前者要有狀態 guard，後者要有更嚴格的 evidence、audit 與補償 SOP。

## 下一步

```text
iwin payment manual-order-review-repair Step 4
```
