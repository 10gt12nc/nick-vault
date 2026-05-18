# iwin game_api

本資料夾整理 `/Users/nick/Git/iwin/game_api` 的專案知識。

`game_api` 是 iwin 玩家端 / partner API 聚合層，主要價值是理解玩家登入註冊、遊戲入口、戰績查詢、優惠券兌換、partner 上下分 / 查單、代理分潤領取與活動獎勵流程。它比 `app_bi` 更接近 production API 與 money / state transition，但目前仍只標為 `專案存在 / code-backed`；沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認前，不寫成 Nick 真實開發成果。

## 讀檔順序

1. [step1-candidate-flows.md](step1-candidate-flows.md)：Step 1 候選 flow 盤點。
2. [step2-flow-comparison.md](step2-flow-comparison.md)：Step 2 候選 flow 風險與價值比較。
3. [flows/coupon-redeem-credit-grant/flow.md](flows/coupon-redeem-credit-grant/flow.md)：Step 3 第一條 flow 主研究報告。
4. [flows/coupon-redeem-credit-grant/career-interview.md](flows/coupon-redeem-credit-grant/career-interview.md)：該 flow 的保守面試素材。
5. [flows/coupon-redeem-credit-grant/materials/](flows/coupon-redeem-credit-grant/materials/)：證據、技術決策、詳細面試稿與 claim 邊界附錄。

## 目前狀態

| 文件 / flow | 狀態 | 說明 |
| --- | --- | --- |
| `step1-candidate-flows.md` | 已建立 | Level 1 掃描，找出 Top 5 production flow 候選 |
| `step2-flow-comparison.md` | 已建立 | 已比較 coupon、partner 上下分、代理分潤、戰績查詢、登入註冊；建議先深挖 coupon |
| `flows/coupon-redeem-credit-grant/` | Step 4 已建立 / 已依最新 KB 覆核 | 已完成優惠券兌換上分 / 打碼要求 Level 2 深掃，並轉成保守面試案例；KB 更新後判定可沿用，暫不升 Level 3；只作 code-backed 面試學習素材，不更新正式履歷 |

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

待確認：

- Nick 實際參與過哪些 `game_api` 功能。
- 哪些功能有 Nick 本人 MR / ticket / commit / production issue evidence。
- GM command handler 的下游 repo、wallet source of truth、重試 / 補償 / 對帳機制。
- `k3s` branch 與 main branch 在 runtime / deployment 上的差異是否影響 flow 分析。

## 履歷邊界

目前只能說：

- 可用來理解 / 分析 iwin 玩家端 API、partner API 與 money-related orchestration。
- 可作為 Senior Backend 面試素材的 evidence base，但需先完成單條 flow Level 2 深掃。

目前不能說：

- Nick 主導 `game_api`。
- Nick 負責完整玩家端 API 或 partner API。
- Nick 是 coupon、上下分、代理分潤或登入系統 owner。
- 單靠 Step 1 寫入正式履歷 / 自傳。

## 下一步建議

只推薦一件事：

```text
iwin payment contribution claim consolidation
```

原因：

- `coupon-redeem-credit-grant` 目前仍缺 Nick 本人 MR / ticket / commit / production issue / 本人確認，先不搶寫正式履歷。
- 目前履歷主線應先修正 `payment`：Nick 已確認 payment 實際開發很多，且已有 `10gt12nc` provider evidence。
- 等 payment contribution consolidation 完成後，再回來判斷 game_api 是否需要 Step 5 claim gate。

## 履歷 claim 分層（2026-05-18 KB 對齊）

- 可放履歷：目前不放正式履歷；尚未補到 Nick 本人 coupon flow 的 MR / ticket / commit / production issue / 本人確認。
- 可面試講：code-backed / 分析過。可用 coupon redeem credit grant 說明跨系統 money side effect、transaction boundary、idempotency、partial success 與 reconciliation。
- 不可誇大：不得寫成 Nick 主導 coupon 系統、修復雙領 production bug、設計 Redis lock 或負責完整玩家端 API owner。
