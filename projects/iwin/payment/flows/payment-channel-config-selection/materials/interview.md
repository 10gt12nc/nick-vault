# interview

## Step 4 面試稿

## 3 分鐘講法

這條 flow 我會講成 payment runtime config selection。玩家進充值頁時，payment 不會直接回傳所有商戶，而是透過 `/payment/list` 和 `/payment/detail` 先判斷這個玩家可用哪些支付類型和商戶。判斷資料來自 app_bi 後台維護的 MySQL 設定，再同步到 Redis `settings`，payment runtime 讀 Redis projection。

主流程是：先用 uid 查玩家層級，依 uid 算 channel Redis DB，再讀 `payTypeList`、`merchantList`、`merchantAccountList`、`convenientPay`、`vipPay`、`paySetting`。`/payment/list` 決定支付類型是否可見；`/payment/detail` 決定這個 pay code 下有哪些商戶 / 檔位 / VIP 聯絡方式；`withdrawConfig` 則決定提現方式、上下限和已綁定帳號遮罩。

Senior 風險在 consistency。這裡不是單 key cache，而是多個 key 必須同一版本。如果 `payTypeList` 同步了，但 `merchantList` 沒同步，前端可能看到支付類型但沒有 detail；如果 `merchantList` 和 `merchantAccountList` 不一致，後續 provider request 可能缺 merchant id；如果 Redis miss fallback DB 邏輯沒處理好，第一個 request 可能還是拿到空列表。

## 高頻追問

| 追問 | 回答方向 |
| --- | --- |
| 這條為什麼有 Senior 價值？ | 它是金流入口前置條件，錯了會造成玩家不能充值 / 提現或進錯商戶。 |
| DB 和 Redis 誰是 source of truth？ | MySQL 設定是 source of truth，Redis 是 runtime projection，payment API 是 eligibility decision。 |
| list 和 detail 為什麼分開？ | list 先決定可見支付類型；detail 再依 pay code 回傳商戶 / VIP / 快捷支付細節。 |
| partial sync 怎麼處理？ | 需要 config version、batch validation、per-channel sync result；runtime 讀到不一致要 fail closed。 |
| Redis key 空了怎麼辦？ | 可以 fallback DB，但要確保回傳值也更新，不只是寫 Redis；還要有告警和 cold-cache test。 |
| 怎麼排查玩家看不到支付方式？ | 查玩家層級、channel Redis DB、payTypeList、merchantList、device、layers、商戶 status、merchantAccountList 對應。 |

## 可用 owner 句子

> 支付方式選擇不是 UI 清單問題，而是 runtime eligibility 問題；錯誤設定會直接影響玩家能不能進入 money flow。

> 我會要求 config sync 有版本與驗證，不然多 key projection 只要同步一半，就會讓 payment list 和 detail 分裂。

## Production 排查順序

玩家看不到支付方式：

1. 先確認 uid 對應 channel、Redis DB、玩家層級。
2. 查 `/payment/list` 是否有空列表 warning。
3. 查 `settings/payTypeList` 是否有該 channel 的支付類型。
4. 查 `merchantList`、`convenientPay`、`vipPay` 是否符合 channel、device、layers。
5. 查 app_bi sync 是否只同步部分 key，或 waitSync / opLog 有失敗。
6. 查 Redis key miss fallback DB 後，本次 response 是否仍是空資料。

玩家看到支付類型但 detail 空：

1. 查 pay code 是否存在於 `payTypeList`。
2. 查 `merchantList` 是否有同 pay type 且 status=1 的商戶。
3. 查 `merchantAccountList` 是否能用 merchant name + channel 對上 merchant id。
4. 查 device / layers 是否在 detail 階段再被過濾。
5. 查 amount gears / activity 是否造成前端無法顯示。

## Lead / Architect 追問

| 追問 | 回答方向 |
| --- | --- |
| 為什麼不用直接查 DB？ | payment runtime 需要低延遲與跨 channel 設定 projection；但 Redis projection 必須可驗證，不然只是把一致性問題藏起來。 |
| 怎麼避免多 key partial sync？ | 用 batch version / config hash，把 `payTypeList`、`merchantList`、`merchantAccountList` 等 key 綁成同一版，sync 完做 validation，再切 runtime readable version。 |
| fail closed 會不會影響營收？ | 會短期少曝光，但錯商戶或錯提現設定會造成更大的 money flow 風險。可以搭配 readiness alert 讓營運快速修設定。 |
| 需要哪些 observability？ | 每 channel / key 的 sync result、config version、runtime key miss / schema mismatch alert、以及「某玩家為什麼看不到某支付方式」的診斷輸出。 |

## 下一步

```text
iwin iwin_gameserver contribution claim consolidation
```
