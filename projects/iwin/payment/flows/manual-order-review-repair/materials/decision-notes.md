# decision-notes

## Step 4 判斷

`manual-order-review-repair` 是 recovery flow，不是單純後台 CRUD。它的核心決策是：人工介入時，哪些操作必須走 payment API 觸發 money side effect，哪些直接修 status 只能作為 break-glass。

| Decision | 目前 code 形狀 | 風險 | Step 4 收斂 |
| --- | --- | --- | --- |
| 正常人工審核走 `/oderView` | `app_bi bill_check` 呼叫 payment API | API 失敗時後台只拿到失敗，仍要查 payment / provider / wallet | 面試說清楚人工審核不是直接改 DB |
| 只處理 `WAIT` | app_bi 與 payment 都 guard `status=1` / `WAIT` | `PROCESSING` 卡住無法用同一條審核路徑處理 | unknown 狀態走 provider / wallet 查證 SOP |
| 退回必須退款 | `oderView` 提現退回會呼叫 `upperDeposit` | 退款成功但訂單更新失敗會半完成 | 需要 wallet idempotency / reconciliation |
| 補單 / GM 上分走 `gameRecharge` | 建單後 `upperDeposit` / `gmDownScore` | 可能與 provider callback 晚到衝突 | 補單前先查 provider / wallet / payment 三邊 |
| 直接修 status 是 break-glass | `repairOrderService` 只 update status / remark / opLog | 可能沒有 wallet / 統計 side effect | 權限、audit、before-after、raw evidence 必須更嚴格 |

## Owner 口徑

最保守、最準確的講法：

> 人工修復不能只設計成「後台能改狀態」。money 系統裡，人工動作要分成會觸發副作用的審核 API、和只修 metadata/status 的 break-glass 工具。前者要有狀態 guard，後者要有更嚴格的 evidence、audit 與補償 SOP。

## Step 4 owner decisions

1. **正常審核與 break-glass 分權**：一般客服 / 營運只應走 `/oderView` 或指定出款 API；direct repair 要更高權限。
2. **unknown 不做 money decision**：`PROCESSING` 或 provider timeout 要先查證，不直接退款或成功。
3. **修復必須帶 evidence**：provider query result、wallet log、payment order snapshot 需要跟 repair action 綁定。
4. **callback 晚到要有衝突處理**：人工修復後的 provider callback 不能直接覆蓋終態。
5. **統計與顯示分離**：修 payment order status 不代表 `log_user`、`user_behaviour`、wallet log 都已一致。

## Step 5 claim decision

本 flow 不更新正式履歷 / 自傳。人工審核、補單、修單主線目前沒有 Nick 直接 path-specific evidence，只保留為面試 owner decision 素材。

## 下一步

```text
iwin game_api partner-deposit-withdraw-bill Step 5
```
