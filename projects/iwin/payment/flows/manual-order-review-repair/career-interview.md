# manual-order-review-repair career-interview

完成狀態：Step 5 已完成
證據層級：專案存在 / code-backed；Nick 貢獻未確認到可放正式履歷的直接 evidence

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

## 3 分鐘講法

我會把這個案例講成「金流人工補償邊界」。正常人工審核不是後台直接改 DB；app_bi 會呼叫 payment 的 `/payment/public/oderView`，payment 先確認訂單仍是 `WAIT`，再依充值或提現處理副作用。充值通過會上分，提現退回會退款，接著才更新訂單與玩家統計。

比較危險的是直接修狀態。app_bi 有 `repairOrderService` 能直接更新 `payment_order_yyyy_M.status`，但這個 method 內沒有處理 provider 查單、wallet 上分 / 退款或統計更新，所以我會把它視為 break-glass。真正安全的 SOP 應該先查 provider 狀態、wallet log、payment order，再決定是補成功、退款、保持 unknown，或只修顯示狀態。

這條 flow 能展示 owner 思維：自動流程和人工補償不是互斥，而是同一個狀態機的不同入口。人工權限越大，就越要有狀態 guard、audit、before-after、callback 晚到的 no-op / conflict handling，不能靠營運猜測改 money state。

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
| callback 晚到怎麼辦？ | 晚到 callback 要先看訂單是否已終態。若人工已補成功或退回，callback 不應覆蓋；理想上要有 raw callback / query evidence 和人工衝突處理。 |
| 這條能放履歷嗎？ | 目前不放。它是 code-backed 面試分析素材，但沒有 Nick 直接修改人工審核 / 修單主線的 path-specific evidence。 |

## Step 5 claim gate

最終判斷：不更新正式履歷 / 自傳。

理由：

- 未找到 Nick 直接修改 `oderView`、`gameRecharge`、app_bi `bill_check` 或 `repairOrderService` 的 path-specific evidence。
- 已找到的 `10gt12nc` commit 只支撐共享建單 / 提款 insert consistency 題材，不支撐人工審核 / 補單 / 修單 owner claim。
- 本 flow 可作面試分析素材，但不得寫成 Nick 主導人工審核、補單、修單、reconciliation 或 break-glass repair 設計。

## 下一步

```text
- 歷史下一步已完成：iwin_gameserver center-http-deposit-withdraw 已完成到 Step 5；目前沒有預設下一步，請以 senior-owner-playbook/01-senior-owner-flow-inventory.md 與 senior-owner-playbook/06-todo.md 為準。
```

## 履歷 claim 分層（2026-05-18 KB 對齊）

- 可放履歷：目前不單獨升級成本 flow 的真實開發成果；project-level payment contribution consolidation 已完成，payment 履歷只保守寫 provider 對接 / 維護與 order consistency。
- 可面試講：code-backed / 分析過。可用本 flow 說明 money correctness、狀態轉移、冪等、retry、補償、人工修復或 runtime config consistency。
- 不可誇大：不得把本 flow 寫成 Nick 主導完整 payment / wallet owner、設計整套金流架構、解決全部對帳或 production incident，除非後續補到本人 MR / ticket / production issue / 本人確認與重要 diff。
