# iwin game_api

本資料夾整理 `/Users/nick/Git/iwin/game_api` 的專案知識。

`game_api` 是 iwin 玩家端 / partner API 聚合層，主要價值是理解玩家登入註冊、遊戲入口、戰績查詢、優惠券兌換、partner 上下分 / 查單、代理分潤領取與活動獎勵流程。它比 `app_bi` 更接近 production API 與 money / state transition。`coupon-redeem-credit-grant` 已完成 Step 5，Nick / `10gt12nc` 在 `game_api` 與 `iwin_gameserver` coupon 相關 path 有直接 commit evidence，可作未來 project-level contribution consolidation 的 strong evidence；但目前 Step 2 本批代表 flows 未完成，仍不得寫成主導完整玩家端 API、完整 reward system owner 或已解決 production 雙領事故。

## 讀檔順序

1. [step1-candidate-flows.md](step1-candidate-flows.md)：Step 1 候選 flow 盤點。
2. [step2-flow-comparison.md](step2-flow-comparison.md)：Step 2 候選 flow 風險與價值比較。
3. [flows/coupon-redeem-credit-grant/flow.md](flows/coupon-redeem-credit-grant/flow.md)：第一條完整 flow 主研究報告，已到 Step 5。
4. [flows/coupon-redeem-credit-grant/career-interview.md](flows/coupon-redeem-credit-grant/career-interview.md)：該 flow 的保守面試素材。
5. [flows/coupon-redeem-credit-grant/materials/](flows/coupon-redeem-credit-grant/materials/)：證據、技術決策、詳細面試稿與 claim 邊界附錄。
6. [flows/partner-deposit-withdraw-bill/flow.md](flows/partner-deposit-withdraw-bill/flow.md)：第二條代表 money API flow，Partner API 上分 / 下分 / 查單，已完成 Step 3。
7. [flows/partner-deposit-withdraw-bill/career-interview.md](flows/partner-deposit-withdraw-bill/career-interview.md)：該 flow 的 Step 3 面試草稿。
8. [flows/partner-deposit-withdraw-bill/materials/](flows/partner-deposit-withdraw-bill/materials/)：Step 3 掃描範圍、evidence、decision、interview、claim boundary。

## 目前狀態

| 文件 / flow | 狀態 | 說明 |
| --- | --- | --- |
| `step1-candidate-flows.md` | 已建立 | Level 1 掃描，找出 Top 5 production flow 候選 |
| `step2-flow-comparison.md` | 已建立 | 已比較 coupon、partner 上下分、代理分潤、戰績查詢、登入註冊；建議先深挖 coupon |
| `flows/coupon-redeem-credit-grant/` | Step 5 已完成 | 已完成優惠券兌換上分 / 打碼要求 Level 2+ claim gate；`10gt12nc` 在 coupon Controller / Service / DAO / mapper / entity 與 gameserver bet target handler 有 path-specific commits，可作 project contribution consolidation evidence |
| `flows/partner-deposit-withdraw-bill/` | Step 3 已完成 | 已完成 Partner API 上分 / 下分 / 查單 Level 2 code-backed flow 深掃；目前未看到 Nick / `10gt12nc` direct path evidence，不更新正式履歷 |
| `contribution-claim-consolidation.md` | 暫緩 | 依最新 KB，單條 coupon Step 5 不能直接代表整個 `game_api` project；先補第二條代表 flow |

## 專案定位

已確認：

- Java / Spring Boot 專案。
- 入口包含 `controller/*`、`service/*`、`service/impl/*`、`service/partner/*`、`service/share/*`、`data/dao/*`、`resources/mapper/*`。
- 常見能力：玩家登入註冊、token / Redis cache、第三方 OAuth、SMS、遊戲清單與設定、遊戲戰績查詢、partner API、GM command 對接、Mongo / MySQL / Redis 混合讀寫。
- 高價值 flow 集中在 money correctness、state transition、idempotency、動態分表查詢與 Redis / Mongo / GM command 一致性。

推測：

- `game_api` 是 API / orchestration 層，不一定是所有交易的 source of truth。
- coupon 兌換、partner 上下分、代理佣金領取會呼叫 GM command；實際錢包落點與遊戲帳本可能在 `iwin_gameserver` 或其他下游。
- 部分營運設定與後台資料來源可能來自 `app_bi` 或 PHP 共用 Redis。

已確認：

- Nick / `10gt12nc` 實際參與 `coupon-redeem-credit-grant` flow 的 `game_api` 入口、service、DAO、mapper、entity 與 `iwin_gameserver` 打碼 handler；詳見 flow evidence / claim boundary。

待確認：

- Nick 是否參與過其他 `game_api` 功能。
- 哪些功能有 Nick 本人 MR / ticket / commit / production issue evidence。
- GM command handler 的下游 repo、wallet source of truth、重試 / 補償 / 對帳機制。
- `k3s` branch 與 main branch 在 runtime / deployment 上的差異是否影響 flow 分析。

## 履歷邊界

目前可以說：

- 可用來理解 / 分析 iwin 玩家端 API、partner API 與 money-related orchestration。
- `coupon-redeem-credit-grant` 可作 Nick 參與玩家優惠券兌換上分 / 打碼要求 flow 開發的候選 evidence，包含 API、service、DB record、GM command orchestration 與下游 bet target handler 對接。
- `partner-deposit-withdraw-bill` 可作 code-backed 面試素材，用來說明 partner 上分 / 下分 / 查單、Mongo 訂單狀態、GM command、idempotency 與 reconciliation 風險；目前不作正式履歷 claim。
- 其他 `game_api` flow 仍需各自完成 claim gate，不能因 coupon evidence 擴大成整個 `game_api` owner。

目前不能說：

- Nick 主導 `game_api`。
- Nick 負責完整玩家端 API 或 partner API。
- Nick 是 coupon、上下分、代理分潤或登入系統 owner。
- 單靠 Step 1 寫入正式履歷 / 自傳。

## 下一步建議

只推薦一件事：

```text
iwin game_api partner-deposit-withdraw-bill Step 4
```

原因：

- `partner-deposit-withdraw-bill` Step 3 已完成，入口、上下分、查單、狀態與風險窗口已讀清楚。
- 下一步應把這條 flow 收斂成 Step 4 面試素材。
- 仍不做 `game_api contribution claim consolidation`，因為本批代表 flows 尚未完成到 Step 5。

## 履歷 claim 分層（2026-05-18 KB 對齊）

- 可放履歷：coupon flow 可作單條 flow evidence。完整 project-level consolidation 暫緩，等 `game_api` 補足更多代表 flow 後再做。
- 可面試講：code-backed / 分析過。可用 coupon redeem credit grant 說明跨系統 money side effect、transaction boundary、idempotency、partial success 與 reconciliation。
- 不可誇大：不得寫成 Nick 主導完整 coupon / reward 系統、修復雙領 production bug、設計 Redis lock、負責完整玩家端 API owner 或完整 wallet / reconciliation owner。
