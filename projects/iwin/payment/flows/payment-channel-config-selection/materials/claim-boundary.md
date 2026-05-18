# claim-boundary

## Step 3 claim 判斷

本 flow 目前是：

- `專案存在 / code-backed`
- `分析素材 / learning-only`
- Nick 個人貢獻：待確認

## 可以使用

- 面試時作為 runtime config consistency 案例。
- 說明 payment list/detail/withdrawConfig 如何消費 app_bi 同步到 Redis 的設定。
- 說明 DB / Redis / runtime filter 三層一致性的風險。
- 說明玩家層級、channel、device、merchant status、merchant account 對應的交叉條件。

## 不可使用

- 不放入正式履歷。
- 不寫 Nick 主導 payment config selection。
- 不寫 Nick 設計設定中心。
- 不寫 Nick 解決支付列表 production incident。
- 不寫已確認完整 config version / atomic rollout。

## Evidence 判斷

已確認：

- payment runtime code-backed 主線存在。
- app_bi Redis sync code-backed 上游存在。
- path history 中有 Derek / gill / arnold 的 config / Redis 相關修改。

未確認：

- `10gt12nc` 直接修改 payment list/detail/withdrawConfig。
- `10gt12nc` 直接修改 app_bi payment config sync。
- Nick 本人 ticket / MR / production issue。

## 下一步

```text
iwin payment payment-channel-config-selection Step 4
```
