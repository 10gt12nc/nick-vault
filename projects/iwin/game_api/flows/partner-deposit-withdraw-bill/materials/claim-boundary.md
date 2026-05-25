# partner-deposit-withdraw-bill claim boundary

更新時間：2026-05-19
Step：5
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## Step 5 判定

結論：可作 code-backed 面試素材，不更新正式履歷 / 自傳。

原因：

- Flow 本身已由 Controller、validation、sign、PartnerService、Mongo order、GM command 與查單 code 確認。
- Step 4 已整理成面試講法、Q&A、STAR 與 Lead / Architect 追問。
- Step 5 已重新 fetch source repo 並重跑 partner path-specific history。
- path-specific history 未看到 Nick / `10gt12nc` 直接修改 PartnerController、PartnerServiceImpl、PartnerLogServiceImpl、ValidatedServiceImpl、PartnerLoginDto 或 BillInfo。
- Nick 尚未本人確認這條 partner flow 是他做過或維護過。

## 可以主張

- `game_api` 有 partner 上分 / 下分 / 查單 flow。
- Flow 涉及 partner request validation、sign、Mongo order、GM command、wallet side effect 與分日查單。
- `origin/k3s` 對 sign replay / secret log 風險有修正方向。
- 可以從 idempotency、transaction boundary、failure window、reconciliation、observability 角度分析。
- 可以在面試中提出 partner order id、本地 unique key、下游 billNos 去重、unknown state、aging pending monitor 與查單 partial failure contract。

## 可以在面試中保守說

- 「我分析過一條 partner 上下分 / 查單 flow，重點是外部 money API 到內部 wallet side effect 的一致性。」
- 「我會先看 partner order id / idempotency，再看本地訂單狀態與下游 GM command 是否能對帳。」
- 「如果 GM 成功但 Mongo update 失敗，查單狀態可能與玩家餘額不一致，所以需要 unknown 狀態與 reconciliation。」
- 「這條目前是 code-backed 分析案例，不會把它包裝成我主導開發。」

## 目前不能主張

- Nick 開發此 partner flow。
- Nick 主導 partner API / 上下分 / 查單。
- Nick 修復 sign replay、重複上下分、錯帳或 production incident。
- Nick 設計 `origin/k3s` 的 nonce / replay prevention。
- Nick 設計完整 reconciliation。
- 此 flow 已可寫入正式履歷。

## 若面試官問是不是你做的

保守回答：

> 這條我目前會把它放在 code-backed 分析案例，而不是直接說我主導開發。我的重點是我能讀懂 partner money API 的交易邊界，並提出 idempotency、reconciliation 與 observability 的改造方向。若要作為實際開發經驗，我會另外拿有 commit / ticket / 本人確認的 flow 來講。

## 待補 evidence

- Nick 本人確認是否做過此 flow。
- `10gt12nc` 是否在其他 branch / ticket / MR 修改過 partner API。
- production issue / incident / support ticket。
- `coin_change_log` schema / index。
- gameserver 對 `billNos` 的 duplicate handling。
- 若未來 Nick 本人確認此 flow 參與經驗，需另標為「本人確認，待 commit / ticket 補強」，不能直接覆蓋本次 Step 5 結論。

## 下一步

```text
- 歷史下一步已完成：iwin_gameserver center-http-deposit-withdraw 已完成到 Step 5；目前沒有預設下一步，請以 senior-owner-playbook/01-senior-owner-flow-inventory.md 與 senior-owner-playbook/06-todo.md 為準。
```
