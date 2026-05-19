# partner-deposit-withdraw-bill career interview

更新時間：2026-05-19
Step：3
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 一句話版本

我分析過 `game_api` 的 partner 上分 / 下分 / 查單 flow，它會把 partner request 驗簽後轉成內部玩家錢包 GM command，並用 Mongo 每日分表記錄訂單狀態；這條 flow 的重點是 idempotency、GM side effect 與查單 reconciliation。

## 30 秒版本

這條 flow 是 partner money API。`NewPay` 和 `Withdraw` 會先驗參數、驗 sign、找玩家，再寫 Mongo 訂單 `status=1`，接著呼叫 gameserver GM command 做上分或下分，成功後把訂單改成 `status=0`。`BillInfo` 和 `BillList` 則依 billNos 或時間區間查 Mongo 每日分表。

我會特別看三個風險：第一，partner 重送時是否有 idempotency key；第二，GM 成功但 Mongo 更新失敗時怎麼對帳；第三，查單是否可能跨日或 partial failure 導致 partner 看到錯誤狀態。

## 2 分鐘版本

這條 partner flow 有四個入口：`NewPay`、`Withdraw`、`BillInfo`、`BillList`。`NewPay` 是上分，`Withdraw` 是下分，兩者共用 `PartnerLoginDto`、`ValidatedServiceImpl` 和 `PartnerLogServiceImpl#checkSign`。真正的錢包異動不是在 `game_api` 本地做，而是 `PartnerServiceImpl` 組 `GmIntfcComponent.send(...)`，送 `GM_CMD_DEPOSIT` 或 `GM_CMD_WITHDRAW` 到下游 gameserver。

本地會先用 `saveCoin` 寫 Mongo `bi_log.coin_change_log_{yyyyMMdd}`，狀態是 `1`，GM 成功後再用 `updCoin` 改成 `0`。如果是 `FmsServiceException`，會改成 `4`；但 generic exception 沒有改狀態，所以可能留下 pending。另一個風險是 `updCoin` 用當天日期找 collection，如果跨日或補償時不是原單日期，就可能找錯 collection。

Senior 角度我會把它拆成 source of truth 和 transaction boundary：partner request、Mongo order、GM wallet side effect 和查單結果不是同一個 transaction。要讓它變可靠，應該補 partner order id / idempotency key、明確狀態機、unknown reconciliation、aging pending monitor，以及下游 billNos 去重。

## 目前可講

- Partner money API 的驗參數、驗簽、上下分、查單流程。
- Mongo 分日 collection 的訂單狀態與查詢方式。
- GM command 與 local order 不在同一 transaction 的風險。
- `origin/k3s` 對 sign replay 風險的強化方向。
- 如何設計 idempotency、reconciliation 與 observability。

## 目前不可講

- 我開發了這條 partner flow。
- 我主導 partner API 或錢包上下分。
- 我修復了重複上下分 production incident。
- 我建立完整 reconciliation 平台。

## Step 4 待整理

- 補正式 Q&A。
- 補 STAR 版本。
- 補面試官追問：partner 重送、GM timeout、Mongo pending、跨日查單、sign replay。
- 補 claim boundary 與 Step 5 待補 evidence。

## 下一步建議

```text
iwin game_api partner-deposit-withdraw-bill Step 4
```
