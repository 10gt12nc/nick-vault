# payment-channel-config-selection career-interview

完成狀態：Step 3 已完成
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 保守定位

這條 flow 目前只作面試分析素材，不放入正式履歷。

可說：

- 我分析過 iwin payment 的支付方式 / 商戶 / 提現設定選擇 flow，重點是 DB 設定如何透過 Redis projection 被 payment runtime 消費。
- 這條 flow 不是直接 money movement，但它決定玩家能否看到正確的充值 / 提現入口。
- 面試時可討論玩家層級、channel、device、商戶 status、Redis partial sync、cold-cache fallback 與 config versioning。

不可說：

- Nick 主導 payment config selection。
- Nick 設計了完整設定中心。
- Nick 修過 payment list/detail production issue。
- 已確認完整 config version / atomic rollout。

## 30 秒講法

payment 的支付列表不是硬編碼，也不是把所有商戶都丟給前端。玩家呼叫 `/payment/list` 或 `/payment/detail` 時，後端會先查玩家層級，再從 Redis `settings` 讀支付類型、商戶、快捷支付、VIP 充值與提現設定，最後依 channel、device、玩家層級與商戶狀態過濾。這條 flow 的風險是 DB、Redis projection 和 runtime filter 不一致，會讓玩家看不到支付方式或看到錯商戶。

## 3 分鐘講法

我會把這條 flow 定位成 payment 的 runtime config selection。app_bi 後台維護 MySQL 設定，像 payment type、merchant、merchant account、convenient pay、VIP pay、pay setting、user layer，然後透過 RedisSynchronize 寫到 Redis `settings` hash。payment runtime 在 `/payment/list`、`/payment/detail`、`/public/withdrawConfig` 讀這些 projection，再結合玩家的 `log_user.user_layer`、channel、device 來決定前端看到什麼。

這裡的 owner 風險不是「能不能查到設定」而已，而是多個 key 要一致。舉例來說，`payTypeList` 出現某支付類型，但 `merchantList` 還沒同步到對應商戶，前端會看到支付方式卻沒有可用 detail；或者 `merchantList` 和 `merchantAccountList` 不一致，provider request 時就可能缺 merchant id。再來，cold cache fallback 也要測，因為 runtime 如果 Redis key 空了，fallback DB 後第一個 request 不能拿到空結果。

我會補的 owner guard 是 config version、每個 channel / key 的同步結果、schema validation、readiness check，並讓 payment runtime fail closed：不確定的商戶不要曝光，而不是讓玩家進入錯誤 money flow。

## 可用問答

| 問題 | 保守答法 |
| --- | --- |
| 這條 flow 跟金流 correctness 有關嗎？ | 有，但它是前置條件。它不直接上分 / 扣款，但會決定玩家能否進入正確支付 / 提現 flow。 |
| payment list 怎麼決定顯示哪些支付方式？ | 讀玩家層級，讀 Redis 裡的 `payTypeList`、`merchantList`、`convenientPay`、`vipPay`，再依 channel、device、layers 過濾。 |
| payment detail 跟 list 差在哪？ | list 決定有哪些支付類型；detail 決定該 pay code 下有哪些商戶、快捷支付或 VIP 充值 detail。 |
| 最大風險是什麼？ | DB / Redis / runtime filter 不一致，尤其是多 key partial sync，會造成支付入口空白、錯商戶、錯檔位或提現限制錯誤。 |
| 會怎麼改善？ | 加 config version、同步結果表、schema validation、cold-cache 測試、channel/key readiness check，runtime 對不完整 config fail closed。 |
| 這條能放履歷嗎？ | 目前不放。它是 code-backed 分析素材，但沒有 Nick 直接 path-specific evidence。 |

## 下一步

```text
iwin payment payment-channel-config-selection Step 4
```
