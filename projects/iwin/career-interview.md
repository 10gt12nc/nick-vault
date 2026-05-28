# iwin domain career / interview map

更新日期：2026-05-28

本檔把 iwin domain-level 大地圖轉成履歷與面試口徑。正式履歷仍以 `senior-owner-playbook/05-resume-master-zh.md` 與 `08-application-autobiography-zh.md` 為準；本檔只提供 iwin 系統視角與 claim boundary。

## 可以怎麼定位 iwin 經驗

保守主軸：

```text
參與 iwin 平台多個後端交易與遊戲整合相關模組維護，範圍包含第三方金流 provider request / callback / query / withdraw 對接、玩家 / partner API orchestration、遊戲伺服器第三方 provider 投派整合、批次報表 projection 與後台 / BI control plane 分析。能從 API、provider adapter、runtime wallet、batch projection、後台入口與部署拓撲拆解 source of truth、狀態轉移、idempotency、failure window 與人工修復邊界。
```

這句是 domain-level 面試定位，不一定原封不動放履歷。正式履歷仍要拆成已證實的 project-level claims。

## 可放履歷的 iwin 素材

| Project | 可放履歷口徑 | 證據層級 | 不可擴張 |
| --- | --- | --- | --- |
| `payment` | 參與第三方金流 provider request / callback / query / withdraw 對接與維護，處理 payment / withdraw order consistency、provider sign / response parsing 類問題 | 真實開發過 + 本人確認 + code-backed | 不寫完整金流、完整 wallet、完整 ledger / reconciliation owner |
| `game_api` | 參與 coupon / reward 類玩家 API orchestration，包含 API、service、DB record、GM command 與下游 bet target handler 對接 | 真實開發過 + code-backed | 不寫完整 game_api、完整 partner API、完整 reward system owner |
| `game_job` | 參與每日遊戲資料彙總 batch / BI projection、GSC 第三方遊戲紀錄 Mongo 備份 job 分批處理維護 | 真實開發過 / 局部真實開發過 + code-backed | 不寫完整 BI pipeline、完整 game_job owner、完整第三方紀錄備份 owner |
| `iwin_gameserver` | 參與第三方遊戲 provider 投派整合與 gameserver 錢包 / 投注流水串接，限 Antplay / GSC / PG 類 bet / settle / refund / transfer-in-out 與 log projection | 真實開發過 + code-backed | 不寫完整 gameserver、完整 wallet、完整 provider integration owner |

## 可面試講但不放主履歷的素材

| Project | 面試價值 | 語氣 |
| --- | --- | --- |
| `third_games_api` | provider adapter、seamless wallet callback、Mongo audit、gameserver transaction boundary | code-backed / 分析過 |
| `app_bi` | 後台入口、BI 查詢、Redis sync、人工修復入口如何追到真正後端 source of truth | code-backed / 分析過 |
| `bi_share` | legacy Laravel 分享 / 佣金 / BI 報表、repair / rebuild 風險 | 分析過，不主打 |
| `k3s-deploy` | gameserver phased rollout、Kustomize topology、ConfigMap / Secret、observability gate | interview-only |
| `iwin-workspace` | 文件不足下的跨 repo KB、source map、schema / config / history 梳理 | supporting evidence |
| `payment-thirdparty-simulator` | GoldenPay / NimTestPay provider contract、callback / query 測試 supporting | supporting evidence |

## 面試時的 iwin 大圖回答

如果面試官問「你怎麼理解原公司的大系統？」可以回答：

```text
我不會把它看成一個單體，而是分成 API orchestration、provider adapter、runtime source of truth、batch projection、control plane 跟 deployment topology。以 iwin 來說，payment 處理第三方金流 request / callback / withdraw order；game_api 處理玩家和 partner API；third_games_api 接第三方遊戲 provider callback，但真正錢包 mutation 和投注流水通常要追到 iwin_gameserver；game_job 會把 payment、遊戲 log、Mongo 或分表資料投影成報表；app_bi 是營運查詢和人工修復入口，不一定是 source of truth。我的習慣是先確認資料落點和狀態機，再看 idempotency、重試補償、人工修復跟觀測點。
```

## 追問防線

| 追問 | 回答方向 |
| --- | --- |
| 你是不是整個 iwin owner？ | 不是。我掌握的是代表性 production flows 與跨系統邊界，能對特定 flow 說清楚風險與交接，不是單人 owner 全平台。 |
| payment 是否就是完整錢包？ | 不是。payment 是 provider orchestration 與 payment / withdraw order 狀態核心之一，錢包落點與最終餘額異動要追 gameserver / game lobby。 |
| third_games_api 是否是錢包 source of truth？ | 不是。它偏 adapter / audit / callback orchestration，真正 wallet mutation 通常在 gameserver。 |
| app_bi 是否能代表後端成果？ | 不能直接代表。app_bi 多數是後台入口 / BI / control plane，要回追下游 service 才能判斷交易邏輯。 |
| 你能不能從 0 架完整系統？ | 我可以用這些 flows 抽象出交易型 backend 的設計骨架：source of truth、idempotency、state transition、projection、observability、manual repair。但完整平台仍需要產品、前端、DBA、SRE、QA、資安與多個後端合作。 |

## 推薦讀法

1. 先讀 [architecture-map.md](architecture-map.md)，建立整體分層。
2. 再讀 [integration-map.md](integration-map.md)，理解三條主線和查問題順序。
3. 想補履歷 claim，回到 `payment`、`game_api`、`game_job`、`iwin_gameserver` 的 `contribution-claim-consolidation.md`。
4. 想練面試 case，回到單條 flow 的 `flow.md` 與 `career-interview.md`。
