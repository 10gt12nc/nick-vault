# interview

## Step 4 面試稿

## 3 分鐘講法

這條 flow 我會從「人工修復不是 CRUD」開始講。payment 的人工審核有正常 API 路徑，也有 direct repair 工具。正常路徑是 app_bi 呼叫 payment `/payment/public/oderView`，payment 會檢查訂單還在 `WAIT`，再依充值或提現處理副作用。充值通過會上分、更新訂單與充值統計；提現退回會把 trade type 改成提現退回，呼叫 `upperDeposit` 把錢退回玩家，再更新訂單與失敗統計。

direct repair 則是另一回事。app_bi 的 `repairOrderService` 可以直接修 `payment_order_yyyy_M.status` 和 remark，這可以解決營運顯示或狀態卡住，但它沒有在 method 內做 provider 查單、wallet 退款 / 上分或統計修正。所以我會把它定義成 break-glass 工具：只能在 provider、payment order、wallet log 三邊查完後使用。

如果遇到 `PROCESSING` 卡住，我不會直接退款或改成功，因為 provider 可能已經接單。要先查 provider query / 商戶後台、payment order、wallet currency log。success 就補終態，failed 才退款，unknown 就維持人工待查並保留 evidence。這是 owner 要定義的人工補償邊界。

## 高頻追問

| 追問 | 回答方向 |
| --- | --- |
| 為什麼人工審核不能直接改 status？ | 因為充值成功、提現退回都會觸發 wallet side effect；只改 status 會讓 payment order 與玩家餘額不一致。 |
| payment 怎麼避免同一筆訂單被審兩次？ | `oderView` 讀單後要求狀態仍是 `WAIT`；app_bi `bill_check` 也只查 `status=1`。但這只是第一層 guard，不代表完整 exactly-once。 |
| 卡在 `PROCESSING` 怎麼辦？ | 先查 provider / 商戶後台與 wallet log，unknown 不退款、不手動成功。app_bi UI 也提示自動提現異常要先代付快查。 |
| 退回提現的風險是什麼？ | 退回會呼叫 `upperDeposit` 把錢加回玩家，若退款成功後本地訂單或統計更新失敗，就需要 repair / reconcile。 |
| 直接修復狀態應該怎麼管？ | 要有權限、原因、before-after、provider 查單結果、wallet log evidence；必要時另補 wallet / 統計 side effect。 |
| 人工補單和 callback 晚到會不會衝突？ | 會，所以人工補單前要確認 provider / wallet 狀態；補完後 callback 應被終態 guard 擋住或進衝突處理，不可覆蓋人工終態。 |
| 這條 flow 的 source of truth 是什麼？ | 至少有三個：payment order、provider status、game lobby / center wallet log。direct repair 不能單獨決定真相。 |
| 會怎麼設計更完整？ | aging order dashboard、provider query job、repair case table、before-after audit、operator reason、raw evidence link、wallet idempotency check。 |

## 人工 repair SOP checklist

| 步驟 | 要確認什麼 | 不確認的風險 |
| --- | --- | --- |
| 1 | `billNo` 與月表 | 修錯月份或查不到訂單 |
| 2 | payment order 現況 | 覆蓋終態或重複處理 |
| 3 | provider 查單 / 商戶後台 | unknown 被誤判 success / failed |
| 4 | game lobby / center wallet log | status 與玩家餘額不一致 |
| 5 | 是否已有 callback 晚到紀錄 | 人工修復後又被 callback 覆蓋 |
| 6 | 選擇 `/oderView`、`gameRecharge` 或 direct repair | 用錯入口造成少做 side effect |
| 7 | 寫 operator / reason / before-after | 事故後無法追責或對帳 |
| 8 | 事後抽樣對帳 | 修完仍有三邊不一致 |

## 可用 owner 句子

> 我不會把人工修復做成一個萬用改狀態按鈕。money 系統裡，正常審核要走會觸發副作用的 API；direct repair 只能作為 break-glass，且必須附 provider、wallet、payment 三邊 evidence。

> `PROCESSING` 的重點是 unknown，不是 failed。unknown 狀態直接退款或成功，都是用猜的處理 money state。

## Step 5 使用邊界

這份面試稿可以拿來練 Senior / Owner 題，但不放正式履歷 / 自傳。原因是目前沒有 Nick 直接修改人工審核 / 補單 / 修單主線的 path-specific evidence。

## 下一步

```text
iwin game_api partner-deposit-withdraw-bill Step 4
```
