# manual-order-review-repair career-interview

完成狀態：Step 3 已建立
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 保守定位

這條 flow 目前只作面試分析素材，不放入正式履歷。

可說：

- 我分析過 iwin payment 的人工審核、補單與訂單修復 flow，重點是後台操作如何連到 payment 訂單狀態、玩家上分 / 退款、統計更新與跨月月表。
- 人工審核不能只看後台按鈕；要分清楚 `oderView` 這類會觸發副作用的 payment API，和 `repairOrderService` 這類直接修 status 的 break-glass 工具。
- 面試時可討論 `WAIT` guard、`PROCESSING` unknown、provider 查單、callback 晚到、直接修狀態的風險。

不可說：

- Nick 主導人工審核 / 補單系統。
- Nick 設計了 repair workflow。
- Nick 修過 manual-order-review-repair production issue。
- 這條 flow 已確認完整 reconciliation。

## 30 秒講法

我會把 payment 的人工修復拆成兩層：一層是正常人工審核 API，例如 `/payment/public/oderView`，它會檢查訂單還是待審，然後依充值或提現分支去做上分、退款、更新訂單與玩家統計；另一層是後台 break-glass repair，例如直接把某筆 `payment_order_yyyy_M` 狀態改掉。後者不能當成完整 reconciliation，因為它不一定有 wallet 副作用，也不一定綁定 provider 查單 evidence。

## 面試主軸

這條 flow 的 owner 重點是：

- 哪些狀態可以人工改，哪些只能查證後處理。
- 人工退回或補單是否真的觸發上分 / 退款。
- 直接修狀態時如何避免和 provider callback 晚到衝突。
- 如何保留 operator、reason、before-after、provider query result。
- unknown 狀態不應直接退款或成功。

## 可用問答

| 問題 | 保守答法 |
| --- | --- |
| 人工審核是直接改 DB 嗎？ | 正常審核不是。app_bi 會呼叫 payment `/oderView`，payment 會做狀態 guard、上分 / 退款與統計更新。但另有 `repairOrderService` 可直接修 status，應視為 break-glass repair。 |
| 為什麼只允許待審單？ | 因為終態訂單可能已經完成上分、退款或 provider 出款。`oderView` 與 app_bi `bill_check` 都以 `WAIT=1` 作主要 guard，避免人工覆蓋終態。 |
| 提現退回最危險在哪？ | 退回不是改 status，而是要把錢退回玩家。若 `upperDeposit` 成功但訂單更新失敗，或人工修狀態但 wallet 沒動，都會造成 money state 不一致。 |
| `PROCESSING` 卡住怎麼辦？ | 不能直接退款或手動出款。要先用 provider 查單 / 商戶後台 / wallet log 確認終態。app_bi UI 也有提示：自動提現提交異常要先代付快查，不要盲目改手動出款。 |
| 直接修復訂單狀態可以嗎？ | 可以作為最後手段，但要降級為 break-glass repair：必須有原因、operator、before-after、provider 查單結果，並確認是否需要補做 wallet / 統計 side effect。 |

## 下一步

```text
iwin payment manual-order-review-repair Step 4
```
