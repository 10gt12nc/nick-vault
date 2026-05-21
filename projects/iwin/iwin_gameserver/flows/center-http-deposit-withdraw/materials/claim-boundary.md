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

## Step 4 更新

日期：2026-05-21

本 flow 已完成 Step 4，正式轉成面試 case；但 evidence 層級不升級。

可用方式：

- 面試中可講「code-backed 分析過 gameserver center_http 上分 / 下分」。
- 可用來展示 Senior / Owner 思考：上游 order state、runtime wallet mutation、per-account queue、`billNos` idempotency、currency log、side effect compensation、reconciliation。

仍不可用方式：

- 不寫進正式履歷主 bullet。
- 不寫 Nick 主導上分 / 下分。
- 不寫 Nick 建立 processed bill / query-by-billNo / reconciliation，因為這些是 owner 建議，不是已確認落地成果。

Step 5 claim gate 時要重新檢查：

- 是否有 Nick 本人確認。
- 是否有 `10gt12nc` direct commit 命中 `DEPOSIT/WITHDRAW`、`HttpNewBill`、`NewBillJob` 或上游 direct caller。
- 是否有 MR / ticket / incident 可證明 Nick 參與過本 flow 的維護或修復。

## Step 5 claim gate

日期：2026-05-21

結論：

```text
本 flow 完成 Step 5，但不升級為正式履歷 claim。
```

判定：

- 證據層級：`專案存在 / code-backed`
- 可面試講：是
- 可放正式履歷：否
- 是否更新 `05` / `08`：否
- 是否回填 project-level consolidation：只回填「本 flow Step 5 已完成但維持 interview-only」；不擴張 project 履歷 claim

關鍵原因：

- `DEPOSIT/WITHDRAW` dispatch、`onWithdraw()`、`HttpNewBill`、`NewBillJob`、一般 `modifyAndGetCoinFromBill()` / `modifyAndGetBankCoin()` 主路徑仍主要是初始 commit author。
- `10gt12nc` 有在 Antplay / provider 投派整合時觸碰 `HttpService.onDeposit()` 周邊與 `PlayerData` provider wallet mutation hook，但 diff context 主要支援第三方 provider flow，不足以證明 Nick 負責完整 center_http 上分 / 下分。
- `payment` 與 `game_api` 上游 direct evidence 可以支援 Nick 的其他 project-level claim，但不能反向包裝成 gameserver center_http 上下分 owner。

可說：

- 「我 code-backed 深掃過 iwin gameserver center_http 上分 / 下分，能說明上游 order、gameserver wallet mutation、per-account queue、timeout retry、`billNos` idempotency 與 log / side effect failure window。」

不可說：

- 「我主導 / 負責 center_http 上分 / 下分。」
- 「我建立 gameserver `billNos` 防重或對帳架構。」
- 「我解決過本 flow 重複加扣或錯帳 incident。」
- 「我負責完整 gameserver wallet owner。」
