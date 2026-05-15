# iwin projects

本資料夾整理 iwin 相關 repo 的 Senior / Owner 學習資料。長期主軸是 production flow，不做 class summary，也不把後台入口包裝成完整後端成果。

## 讀檔順序

1. [payment](payment/README.md)：金流 / 充值 / 提現 orchestration，目前 `payment-provider-callback` 已到 Step 4，下一步 Step 5。
2. [game_api](game_api/README.md)：玩家端 / partner API orchestration，目前 `coupon-redeem-credit-grant` 已到 Step 4，下一步 Step 5。
3. [game_job](game_job/README.md)：批次任務與 BI projection，目前 `daily-game-data-summary` 已到 Step 4，下一步 Step 5。
4. [third_games_api](third_games_api/README.md)：第三方遊戲 provider adapter，目前 `gsc-transfer-bet-settle-rollback` 已到 Step 4，下一步 Step 5。
5. [iwin_gameserver](iwin_gameserver/README.md)：Java 遊戲伺服器，`third-party-transfer-in-out` 已到 Step 5，下一條候選是 `center-http-deposit-withdraw` Step 3。
6. [k3s-deploy](k3s-deploy/README.md)：K3s / Kustomize deploy manifests，目前 `gameserver-phased-rollout` 已到 Step 4，下一步 Step 5。
7. [app_bi](app_bi/README.md)：PHP / ThinkPHP 後台與 BI / control plane，4 條主要 flow 已到 Step 5，定位為後台入口與面試分析素材。

## 目前內容

| Project | 目前內容 | 下一步 |
| --- | --- | --- |
| `payment` | Step 1 / Step 2；`payment-provider-callback` 已完成 Step 4 evidence 補強 | `iwin payment payment-provider-callback Step 5` |
| `game_api` | Step 1 / Step 2；`coupon-redeem-credit-grant` 已完成 Step 4 面試案例 | `iwin game_api coupon-redeem-credit-grant Step 5` |
| `game_job` | Step 1 / Step 2；`daily-game-data-summary` 已完成 Step 4 面試案例 | `game_job daily-game-data-summary Step 5` |
| `third_games_api` | Step 1 / Step 2；`gsc-transfer-bet-settle-rollback` 已完成 Step 4 面試案例 | `iwin third_games_api gsc-transfer-bet-settle-rollback Step 5` |
| `iwin_gameserver` | architecture / Step 1 / Step 2；`third-party-transfer-in-out` 已完成 Step 5 | `iwin_gameserver center-http-deposit-withdraw Step 3` |
| `k3s-deploy` | architecture / Step 1 / Step 2；`gameserver-phased-rollout` 已完成 Step 4 面試案例 | `iwin k3s-deploy gameserver-phased-rollout Step 5` |
| `app_bi` | Step 1 / Step 2 / architecture / career；4 條主要 flow 已完成 Step 5 | app_bi 已收斂；下一步轉 `iwin payment payment-provider-callback Step 5` |

## 共用邊界

已確認：

- iwin 來源 repo 位置以 [projects/source-repo-inventory.md](../source-repo-inventory.md) 為索引。
- `app_bi` 偏後台 / BI / control plane。
- `game_api` 偏玩家端 / partner API orchestration。
- `game_job` 偏批次任務、BI projection 與資料清洗。
- `iwin_gameserver` 偏遊戲 runtime、center_http、玩家錢包、投注 / 派彩 / 退款、DB proxy 與 log writer。
- `payment` 偏金流 provider request / callback、訂單狀態轉移、MQ retry 與人工修復邊界。
- `third_games_api` 偏第三方遊戲 provider callback / seamless wallet adapter。
- `k3s-deploy` 偏 dev-k3s deployment topology、rollout / rollback、config / secret 與 observability。

待確認：

- Nick 本人實際參與過哪些 repo / flow。
- 哪些 flow 有 Nick 本人 MR / ticket / commit / production issue / 本人確認。

履歷邊界：

- 沒有 Nick 本人 evidence 前，所有 iwin flow 預設只能標為 `專案存在 / code-backed` 或 `分析素材 / learning-only`。
- 不更新正式履歷 / 自傳，除非單條 flow 已完成足夠深掃且 Nick 明確要求。
