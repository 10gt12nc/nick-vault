# iwin integration map v1

更新日期：2026-05-28

本檔聚焦跨 project 的資料流與整合邊界，搭配 [architecture-map.md](architecture-map.md) 閱讀。它回答「哪些系統互相呼叫、資料在哪裡轉換、出錯時該追哪一層」，不回答「每個 repo 全部 class 做什麼」。

## Integration Inventory

| 整合面 | 上游 | 下游 | 已整理代表 flow | 主要風險 | Claim 邊界 |
| --- | --- | --- | --- | --- | --- |
| 金流充值 | 玩家 / 商戶 -> `payment` | provider、gameserver / game lobby、`game_job` | `payment-order-provider-request`、`payment-provider-callback` | provider response parsing、callback duplicate、order state、下游餘額異動 | Nick 可保守寫參與 provider 對接與維護；不可寫完整金流 owner |
| 提現 / 自動出款 | 玩家 / 後台 -> `payment` | provider、人工修復入口 | `withdrawal-auto-review-refund`、`manual-order-review-repair` | 代付失敗退款、人工改狀態、重複副作用 | 面試可講；履歷只採已證實 provider / order consistency 範圍 |
| 玩家 / partner API | Partner / app -> `game_api` | gameserver GM command / Mongo / Redis | `coupon-redeem-credit-grant`、`partner-deposit-withdraw-bill`、`agent-bonus-receive-transfer` | API idempotency、GM command 成功但本地記錄失敗、查單一致性 | coupon 可放履歷；partner / agent bonus interview-only |
| 第三方遊戲 adapter | Antplay / GSC / OneAPI -> `third_games_api` | `iwin_gameserver` center_http / provider command | `gsc-transfer-bet-settle-rollback`、`oneapi-wallet-bet-result`、`antplay-bet-settle-rollback`、`gsc-seamless-withdraw-deposit-cancel` | provider contract 語意、rollback / cancel、Mongo audit vs wallet mutation | `third_games_api` 不新增 standalone 履歷 claim |
| Gameserver runtime | `game_api` / `third_games_api` / provider command | gameserver wallet、log reel、game log、dbproxy | `third-party-transfer-in-out`、`center-http-deposit-withdraw`、`game-spin-settlement-log-reel`、`bet-target-set-query` | 錢包 mutation、投注流水、log writer、打碼目標 | 可保守寫第三方 provider 投派整合與 gameserver 錢包 / 投注流水串接 |
| Batch / BI projection | `payment` / `iwin_gameserver` / Mongo / log tables | `game_job` -> app_bi / reports | `daily-game-data-summary`、`third-party-record-mongo-backup`、`coin-flow-batch-projection`、`online-payment-data-cleaning`、`partition-table-creation` | delete-insert replay、資料日 / 時區、backup delete、schema rollover | daily summary / GSC backup 可放履歷；其他 interview-only |
| 後台 / control plane | 營運 / 財務 / 客服 -> `app_bi` / `bi_share` | payment / reports / Redis / repair entry | `point-control-admin-operation`、`admin-config-redis-sync`、`daily-game-record-summary`、`game-round-record-query` | 後台只是入口、人工修復誤用、BI query 不等於 source of truth | 不放 app_bi / bi_share 正式主成果 |
| Deploy topology | `k3s-deploy` | iwin services / log pipeline | `gameserver-phased-rollout` | phase order、ZK registration、ConfigMap / Secret、Recreate / rollback | interview-only，不寫 K3s owner |

## Trace Map

### 查金流問題

1. 先定位是 `payment` 建單 / callback / query / withdraw，還是下游餘額異動。
2. 查 provider request / callback log 與 `payment_order` / withdraw order 狀態。
3. 若狀態已改但玩家餘額異常，追 gameserver / game lobby command 與 `billNo` / order id 是否具冪等保護。
4. 若報表不一致，再追 `game_job` payment cleaning / daily economic projection，而不是直接把報表當 source of truth。
5. 若人工修復介入，標清楚是 app_bi 後台入口，不代表修復邏輯 source of truth 在 app_bi。

### 查第三方遊戲下注 / 派彩問題

1. 先定位 provider 是走 `third_games_api` adapter 還是直接進 gameserver command。
2. 查 provider transaction id、Mongo third log / transaction、callback step。
3. 查 `iwin_gameserver` handler 是否完成 wallet mutation、log reel / bet log。
4. 若 app_bi 查不到資料，追 `game_job` / log writer / report projection，不先假設下注沒發生。
5. 若 rollback / cancel 出現，先分清 provider contract 語意，不把 rollback 一律當退款成功。

### 查 BI / 報表問題

1. 先確認報表是由 `game_job` projection、app_bi query 還是 bi_share legacy report 產生。
2. 查 task date、資料日、時區、partition table、delete + insert replay 是否一致。
3. 若來源資料缺失，回到 `payment` / `iwin_gameserver` / Mongo third log。
4. 若是 manual repair / rebuild，標記人工邊界與可能的 duplicate side effect。

## 面試用大圖講法

90 秒版本：

```text
iwin 不是單一服務，而是一組交易與遊戲 runtime 的跨 repo 系統。我的理解會先分層：payment 負責金流 provider request / callback / withdraw order，game_api 負責玩家與 partner API orchestration，third_games_api 是第三方遊戲 adapter，真正遊戲 runtime、center wallet、log writer 在 iwin_gameserver。game_job 再把 runtime 與 payment 資料投影成 BI / 報表，app_bi 只是後台入口和查詢 / 修復 control plane。面試時我不會把後台查詢當帳本，也不會把 adapter 當 wallet source of truth；我會先追 source of truth、idempotency key、狀態轉移、失敗窗口，再看補償、人工修復與報表 projection。
```

3 分鐘版本重點：

1. 先講分層：API / provider adapter / runtime wallet / batch projection / control plane / deploy topology。
2. 再講三條主線：金流 provider、第三方遊戲 provider、報表 projection。
3. 每條主線都說 source of truth 與 failure window。
4. 最後收 claim boundary：哪些是 Nick 可保守說參與，哪些只是 code-backed 分析。

## 不可誇大

- 不說自己完整掌握或主導整個 iwin 平台。
- 不把 `app_bi` / `bi_share` 後台入口說成交易 source of truth。
- 不把 `third_games_api` code-backed 分析說成 Nick direct provider owner。
- 不把 `k3s-deploy` interview-only 說成 Nick 主導 K3s migration。
- 不把 `game_job` projection 說成完整帳本或完整資料平台 owner。
- 不把 `payment` provider integration 擴張成完整 wallet / ledger / reconciliation owner。
