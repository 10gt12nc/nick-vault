# partner-deposit-withdraw-bill claim boundary

更新時間：2026-05-19
Step：3
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## Step 3 判定

結論：目前只能作 code-backed 面試素材，不更新正式履歷 / 自傳。

原因：

- 本輪已確認 flow 存在，且 code evidence 足以支撐上下分 / 查單分析。
- path-specific history 未看到 Nick / `10gt12nc` 直接修改 PartnerController、PartnerServiceImpl、PartnerLogServiceImpl、ValidatedServiceImpl、PartnerLoginDto 或 BillInfo。
- Nick 尚未本人確認這條 partner flow 是他做過或維護過。

## 可以主張

- `game_api` 有 partner 上分 / 下分 / 查單 flow。
- Flow 涉及 partner request validation、sign、Mongo order、GM command、wallet side effect 與分日查單。
- `origin/k3s` 對 sign replay / secret log 風險有修正方向。
- 可以從 idempotency、transaction boundary、failure window、reconciliation、observability 角度分析。

## 可以在面試中保守說

- 「我分析過一條 partner 上下分 / 查單 flow，重點是外部 money API 到內部 wallet side effect 的一致性。」
- 「我會先看 partner order id / idempotency，再看本地訂單狀態與下游 GM command 是否能對帳。」
- 「如果 GM 成功但 Mongo update 失敗，查單狀態可能與玩家餘額不一致，所以需要 unknown 狀態與 reconciliation。」

## 目前不能主張

- Nick 開發此 partner flow。
- Nick 主導 partner API / 上下分 / 查單。
- Nick 修復 sign replay、重複上下分、錯帳或 production incident。
- Nick 設計完整 reconciliation。
- 此 flow 已可寫入正式履歷。

## 待補 evidence

- Nick 本人確認是否做過此 flow。
- `10gt12nc` 是否在其他 branch / ticket / MR 修改過 partner API。
- production issue / incident / support ticket。
- `coin_change_log` schema / index。
- gameserver 對 `billNos` 的 duplicate handling。

## 下一步

```text
iwin game_api partner-deposit-withdraw-bill Step 4
```
