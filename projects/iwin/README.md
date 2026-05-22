# iwin projects

本資料夾整理 iwin 相關 repo 的 Senior / Owner 學習資料。長期主軸是 production flow，不做 class summary，也不把後台入口包裝成完整後端成果。

## 讀檔順序

1. [game_api](game_api/README.md)：玩家端 / partner API orchestration，三條代表 flow 與 project-level contribution consolidation 已完成，2026-05-20 已重新覆核；正式履歷只採 coupon 保守 claim，partner / agent bonus 只作 code-backed 面試素材。
2. [payment](payment/README.md)：金流 / 充值 / 提現 orchestration，Top 5 flow 與 project-level contribution consolidation 已完成，2026-05-20 已重新覆核並補入 GoldenPay direct evidence；不因新規則重做。
3. [app_bi](app_bi/README.md)：PHP / ThinkPHP 後台與 BI / control plane，4 條主要 flow 已到 Step 5，定位為後台入口與面試分析素材。
4. [game_job](game_job/README.md)：批次任務與 BI projection，Top 5 代表 flows 與 project-level contribution consolidation 已完成，2026-05-20 已重新覆核；不因新規則重做。
5. [third_games_api](third_games_api/README.md)：第三方遊戲 provider adapter，`gsc-transfer-bet-settle-rollback` 已到 Step 5，`oneapi-wallet-bet-result` 已到 Step 3，rolling / scoped contribution consolidation 已完成；不新增 standalone 正式履歷主成果。
6. [iwin_gameserver](iwin_gameserver/README.md)：Java 遊戲伺服器，`contribution-claim-consolidation` 已完成 rolling / scoped 收口；第三方 provider 投派整合可保守放履歷，本批代表 flows 已完成 Step 5。
7. [k3s-deploy](k3s-deploy/README.md)：K3s / Kustomize deploy manifests，`gameserver-phased-rollout` 已到 Step 4；project-local 下一步是 Step 5 claim gate。
8. [bi_share](bi_share/README.md)：PHP / Laravel 分享、佣金與 BI 報表 legacy repo；rolling / scoped negative contribution consolidation 已完成，不放正式履歷主成果。
9. [iwin-workspace](iwin-workspace/README.md)：workspace / KB / docs / environment index；contribution consolidation 已完成，不新增 standalone 正式履歷主成果，只作工作方法與 cross-repo reconstruction supporting evidence。

## 目前內容

| Project | 目前內容 | 下一步 |
| --- | --- | --- |
| `game_api` | 三條代表 flow 已完成 Step 5；project-level contribution consolidation 已完成並於 2026-05-20 重新覆核；正式履歷只採 coupon 保守 claim，partner / agent bonus 只作面試素材 | 已收斂，不因新規則重做 |
| `payment` | Top 5 flow 與 project-level contribution consolidation 已完成並於 2026-05-20 重新覆核；`payment-order-provider-request` 已有 Nick / `10gt12nc` path-specific evidence，GoldenPay direct commits 已補入 claim | 不因新規則重做；後續只在 Nick 指定新 payment flow 時追加 |
| `app_bi` | Step 1 / Step 2 / architecture / career；4 條主要 flow 已完成 Step 5 | 已收斂；不搶履歷 claim |
| `game_job` | Step 1 / Step 2；Top 5 代表 flows 與 project-level contribution consolidation 已完成並於 2026-05-20 重新覆核，其中 daily summary / GSC backup 有 direct evidence | 已收斂，不因新規則重做 |
| `third_games_api` | Step 1 / Step 2；`gsc-transfer-bet-settle-rollback` 已完成 Step 5；`oneapi-wallet-bet-result` 已完成 Step 3；rolling / scoped contribution consolidation 已完成，不新增 standalone 正式履歷主成果 | `iwin third_games_api oneapi-wallet-bet-result Step 4` |
| `iwin_gameserver` | architecture / Step 1 / Step 2；本批代表 flows 已完成 Step 5；project consolidation 升級為部分真實開發過 | 已收斂；第三方 provider 投派整合可保守放履歷 |
| `k3s-deploy` | architecture / Step 1 / Step 2；`gameserver-phased-rollout` 已完成 Step 4 面試案例 | project-local 下一步：`iwin k3s-deploy gameserver-phased-rollout Step 5` |
| `bi_share` | rolling / scoped negative contribution consolidation 已完成；未見 Nick / `10gt12nc` direct production commits | 不放正式履歷主成果；若要深挖，先做 `iwin bi_share Step 1` |
| `iwin-workspace` | rolling / scoped contribution consolidation 已完成；有 Nick / `10gt12nc` KB / docs / environment index direct commits，但不是 production service | 不放 standalone 正式履歷主成果；作 cross-repo KB / system reconstruction supporting evidence |

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
- `bi_share` 偏 PHP / Laravel legacy 分享、佣金、BI 報表、匯出與 GM repair 入口。
- `iwin-workspace` 偏 workspace / KB / docs / environment index，不是業務 service source of truth。

待確認：

- `game_api` 已完成 project-level consolidation：coupon direct evidence 可放履歷；partner / agent bonus 只作面試素材；邀請好友轉盤有 direct commits 但尚未做完整 flow package，只作補充 evidence。
- `game_job` 已完成 project-level consolidation：daily summary / GSC backup 有 direct evidence，可保守放履歷；其他 flow 只作面試素材。
- 其他 iwin repo 若未補 Nick 本人 MR / ticket / commit / production issue / 本人確認，仍只作 code-backed 面試素材，不搶先放正式履歷。
- `bi_share` 已完成 rolling / scoped negative consolidation：目前未見 Nick direct production commits，僅作 legacy BI / 分享 / 佣金 / 報表分析素材。
- `third_games_api` 已完成 rolling / scoped contribution consolidation：本 repo 僅掃到局部測試 / merge 線索，不新增 standalone 正式履歷主成果；下游 `iwin_gameserver` direct evidence 已在 `iwin_gameserver` consolidation 正確歸位。
- `iwin_gameserver` 已完成 rolling / scoped contribution consolidation：Nick / `10gt12nc` 在 Antplay / GSC / PG 第三方 provider 投派整合、gameserver money job、log projection 有 direct commits；正式履歷可保守寫「參與第三方遊戲 provider 投派整合與 gameserver 錢包 / 投注流水串接」。
- `iwin-workspace` 已完成 rolling / scoped contribution consolidation：有大量 KB / docs / environment index direct commits，但只支撐工作方法與系統理解能力，不新增 standalone 正式履歷主成果。

履歷邊界：

- `payment` 不再用單條 flow 的「未確認」去否定整個 repo 經驗；已確認的 provider request / callback / query evidence 可以保守支撐「參與第三方金流 provider 對接與維護」，且 project-level consolidation 已完成並於 2026-05-20 重新覆核。
- `game_api` 目前只有 coupon 這條正向 direct evidence 可放正式履歷；partner / agent bonus 只作 code-backed 面試素材，不直接升級完整 project owner。`game_job` 已完成完整 contribution consolidation。
- 其他 iwin flow 目前預設標為 `專案存在 / code-backed` 或 `分析素材 / learning-only`。
- `bi_share` 不新增正式履歷主成果；若面試提到，只能說分析過 legacy BI / 分享 / 佣金報表系統與 projection / repair 風險。
- `third_games_api` 不新增 standalone 正式履歷主成果；若面試提到，只能說分析過第三方遊戲 provider adapter / seamless wallet callback / Mongo audit / gameserver transaction boundary。
- `iwin_gameserver` 可新增保守第三方 provider 投派整合履歷 claim，但不可寫成完整 gameserver owner、完整 wallet owner、完整上分 / 下分 owner 或完整 provider integration owner。
- `iwin-workspace` 不新增 standalone 正式履歷主成果；若提到，只能作接手文件不足、跨 repo KB、schema / config / history 梳理的 supporting evidence。
- 不寫「主導完整金流 / 遊戲錢包 / DevOps owner」，除非後續補到足夠 evidence。

## 履歷 claim 分層（2026-05-18 KB 對齊）

- 可放履歷：`payment`、`game_api`、`game_job` 已完成 project-level consolidation；各自只採已確認的保守 claim，不擴張成完整 project owner。
- 可面試講：所有 iwin 已整理 flow 都可作 code-backed / 分析過的面試素材，但要明確說「我分析過」或「我參與的範圍是...」。
- 不可誇大：不得寫成主導完整金流、完整 gameserver wallet owner、完整 BI pipeline owner、K3s 遷移 owner、獨立完成或量化改善。
