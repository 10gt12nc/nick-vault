# Claim Boundary：center-http-deposit-withdraw

## 結論

目前本 flow 屬於：

```text
專案存在 / code-backed
```

尚未升級為：

```text
真實開發過
```

原因：

- 已深掃 `iwin_gameserver` center_http 上分 / 下分主要 code path。
- 已掃上游 `payment` / `game_api` 主要呼叫入口。
- 但尚未看到 Nick / `10gt12nc` 對本 flow 的直接 MR / ticket / production issue 或明確 path-specific commit。
- `10gt12nc` 在 `game_api coupon` 與 `iwin_gameserver coupon` 有 evidence，但不能自動擴張成完整 center_http deposit / withdraw owner。
- 2026-05-20 `iwin_gameserver` contribution consolidation 已確認 Nick / `10gt12nc` 有第三方 provider 投派整合 direct commits；這能支撐 third-party transfer in/out claim，但仍不能自動擴張成本 flow 的 `DEPOSIT/WITHDRAW` direct development claim。

## 可放履歷

目前不建議新增正式履歷 bullet。

若未來補到本人確認或 direct evidence，可考慮保守寫：

- 參與 iwin 遊戲伺服器與上游金流 / API 的上分、下分流程分析與維護，關注 wallet mutation、冪等、timeout retry 與帳變對帳。

但目前 Step 3 不寫。

## 可面試講

可以講：

- code-backed 分析過 `iwin_gameserver` center_http `DEPOSIT/WITHDRAW` flow。
- 理解 `HttpService -> HttpNewBill -> NewBillJob -> PlayerData` 的錢包變更路徑。
- 能指出上游 order state 與 gameserver wallet mutation 不在同一 transaction。
- 能指出 `billNos` 在 gameserver 主要路徑中目前只看到傳入 log，未看到明確 duplicate guard。
- 能提出 owner 級改善：processed bill record、query-by-billNo、mutation audit、log replay、side effect compensation。

## 不可誇大

不可寫：

- Nick 主導 `iwin_gameserver`。
- Nick 是 gameserver wallet owner。
- Nick 獨立完成上分 / 下分系統。
- Nick 主導完整金流交易一致性。
- Nick 解決過本 flow 的 production duplicate deposit / withdraw incident。
- 本 flow 已證明 Nick 開發完整 payment + gameserver money pipeline。

## Evidence 升級條件

若要升級成 `真實開發過`，至少需要其中之一：

- Nick 本人明確確認他做過此 flow 的某段，並標示「本人確認，待 commit / ticket 補強」。
- 找到 Nick / `10gt12nc` 對 `HttpService.onDeposit/onWithdraw`、`HttpNewBill`、`NewBillJob`、`PlayerData.modifyAndGetCoinFromBill()` 或上游 direct caller 的直接 commit。
- 找到對應 MR / ticket / bug / production issue，能連到 Nick 的實際修正。
- 找到重要 diff 能證明 Nick 改過上分 / 下分 idempotency、timeout、order consistency、wallet mutation 或對帳。

## 和既有履歷素材的關係

已可放履歷的主線仍是：

- `payment` project-level contribution consolidation：Nick 本人確認 + 多 provider / order consistency direct commits。
- `game_api coupon-redeem-credit-grant`：`10gt12nc` 有 coupon direct commits。
- `game_job daily-game-data-summary` / `third-party-record-mongo-backup`：有局部 direct commits。

本 flow 目前是補強「面試能講清楚 runtime wallet / idempotency」的素材，不是新增正式履歷 claim。
