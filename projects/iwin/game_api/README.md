# iwin game_api

本資料夾整理 `/Users/nick/Git/iwin/game_api` 的專案知識。

`game_api` 是 iwin 玩家端 / partner API 聚合層，主要價值是理解玩家登入註冊、遊戲入口、戰績查詢、優惠券兌換、partner 上下分 / 查單、代理分潤領取與活動獎勵流程。它比 `app_bi` 更接近 production API 與 money / state transition。`coupon-redeem-credit-grant` 已完成 Step 5，Nick / `10gt12nc` 在 `game_api` 與 `iwin_gameserver` coupon 相關 path 有直接 commit evidence，已在 project-level contribution consolidation 中收斂為可放履歷的保守 claim。`partner-deposit-withdraw-bill` 與 `agent-bonus-receive-transfer` 都已完成 Step 5，但目前未見 Nick direct path evidence，只作 code-backed 面試素材，不更新正式履歷。仍不得寫成主導完整玩家端 API、完整 partner API 或 reward system owner。

## 讀檔順序

1. [step1-candidate-flows.md](step1-candidate-flows.md)：Step 1 候選 flow 盤點。
2. [step2-flow-comparison.md](step2-flow-comparison.md)：Step 2 候選 flow 風險與價值比較。
3. [flows/coupon-redeem-credit-grant/flow.md](flows/coupon-redeem-credit-grant/flow.md)：第一條完整 flow 主研究報告，已到 Step 5。
4. [flows/coupon-redeem-credit-grant/career-interview.md](flows/coupon-redeem-credit-grant/career-interview.md)：該 flow 的保守面試素材。
5. [flows/coupon-redeem-credit-grant/materials/](flows/coupon-redeem-credit-grant/materials/)：證據、技術決策、詳細面試稿與 claim 邊界附錄。
6. [flows/partner-deposit-withdraw-bill/flow.md](flows/partner-deposit-withdraw-bill/flow.md)：第二條代表 money API flow，Partner API 上分 / 下分 / 查單，已完成 Step 5。
7. [flows/partner-deposit-withdraw-bill/career-interview.md](flows/partner-deposit-withdraw-bill/career-interview.md)：該 flow 的 Step 5 code-backed 面試素材與 claim gate。
8. [flows/partner-deposit-withdraw-bill/materials/](flows/partner-deposit-withdraw-bill/materials/)：Step 5 掃描範圍、evidence、decision、interview、claim boundary。
9. [flows/agent-bonus-receive-transfer/flow.md](flows/agent-bonus-receive-transfer/flow.md)：第三條代表 money-like flow，代理佣金領取 / 轉帳，已完成 Step 5。
10. [flows/agent-bonus-receive-transfer/career-interview.md](flows/agent-bonus-receive-transfer/career-interview.md)：該 flow 的 Step 5 code-backed 面試素材。
11. [flows/agent-bonus-receive-transfer/materials/](flows/agent-bonus-receive-transfer/materials/)：Step 5 掃描範圍、evidence、decision、interview、claim boundary。
12. [contribution-claim-consolidation.md](contribution-claim-consolidation.md)：project-level contribution claim consolidation，已完成；正式履歷只採 coupon 保守 claim。

## 目前狀態

| 文件 / flow | 狀態 | 說明 |
| --- | --- | --- |
| `step1-candidate-flows.md` | 已建立 | Level 1 掃描，找出 Top 5 production flow 候選 |
| `step2-flow-comparison.md` | 已建立 | 已比較 coupon、partner 上下分、代理分潤、戰績查詢、登入註冊；建議先深挖 coupon |
| `flows/coupon-redeem-credit-grant/` | Step 5 已完成 | 已完成優惠券兌換上分 / 打碼要求 Level 2+ claim gate；`10gt12nc` 在 coupon Controller / Service / DAO / mapper / entity 與 gameserver bet target handler 有 path-specific commits，可作 project contribution consolidation evidence |
| `flows/partner-deposit-withdraw-bill/` | Step 5 已完成 | 已完成 Partner API 上分 / 下分 / 查單 Level 2 code-backed flow 深掃、面試素材與單條 flow claim gate；目前未看到 Nick / `10gt12nc` direct path evidence，不更新正式履歷 |
| `flows/agent-bonus-receive-transfer/` | Step 5 已完成 | 已完成代理佣金領取 / 轉帳 Level 2 flow 深掃、面試收斂與單條 flow claim gate；確認 `game_api` API、Mongo `agent_money`、Redis projection、GM 上分與 `game_job` 佣金結算關聯；目前未看到 Nick / `10gt12nc` direct path evidence，不更新正式履歷 |
| `contribution-claim-consolidation.md` | 已完成 / 2026-05-20 已重新覆核 | 已掃 repo-wide Nick / `10gt12nc` commits、branches、重要 diff、三條代表 flow evidence 與履歷素材；正式履歷只採 coupon 保守 claim，partner / agent bonus 只作面試素材 |

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
- 已完成 `game_api` project-level contribution consolidation；coupon 可放履歷，partner / agent bonus 目前只作面試素材。

待確認：

- Nick 是否參與過其他 `game_api` 功能。
- 哪些功能有 Nick 本人 MR / ticket / commit / production issue evidence。
- GM command handler 的下游 repo、wallet source of truth、重試 / 補償 / 對帳機制。
- `k3s` branch 與 main branch 在 runtime / deployment 上的差異是否影響 flow 分析。

## 履歷邊界

目前可以說：

- 可用來理解 / 分析 iwin 玩家端 API、partner API 與 money-related orchestration。
- `coupon-redeem-credit-grant` 可作 Nick 參與玩家優惠券兌換上分 / 打碼要求 flow 開發的候選 evidence，包含 API、service、DB record、GM command orchestration 與下游 bet target handler 對接。
- `partner-deposit-withdraw-bill` 已完成 Step 5，可作 code-backed 面試素材，用來說明 partner 上分 / 下分 / 查單、Mongo 訂單狀態、GM command、idempotency 與 reconciliation 風險；目前不作正式履歷 claim。
- `agent-bonus-receive-transfer` 已完成 Step 5，可作代理佣金、Mongo / Redis projection、GM command consistency 的 code-backed 面試素材；目前不作正式履歷 claim。
- repo-wide 掃描看到 Nick / `10gt12nc` 有邀請好友轉盤活動 direct commits，但該活動尚未整理成完整 flow package；目前只作補充 evidence，不放正式履歷主成果。
- 其他 `game_api` flow 仍需各自完成 claim gate，不能因 coupon evidence 擴大成整個 `game_api` owner。

目前不能說：

- Nick 主導 `game_api`。
- Nick 負責完整玩家端 API 或 partner API。
- Nick 是 coupon、上下分、代理分潤或登入系統 owner。
- 單靠 Step 1 寫入正式履歷 / 自傳。

## 歷史下一步狀態

原本的下一步已完成：

```text
- 歷史下一步已完成：iwin_gameserver center-http-deposit-withdraw 已完成到 Step 5；目前沒有預設下一步，請以 senior-owner-playbook/01-senior-owner-flow-inventory.md 與 senior-owner-playbook/06-todo.md 為準。
```

原因：

- `game_api` 本批代表 flow 與 project-level contribution consolidation 已完成。
- 歷史下一步已完成：iwin_gameserver center-http-deposit-withdraw 已完成到 Step 5；目前沒有預設下一步，請以 senior-owner-playbook/01-senior-owner-flow-inventory.md 與 senior-owner-playbook/06-todo.md 為準。
- 2026-05-20 已重新 fetch / 重讀 / 覆核，結論仍維持：正式履歷只採 coupon 保守 claim。
- `payment`、`game_job`、`app_bi`、`bi_share` 的 contribution consolidation 也已收斂。
- 歷史下一步已完成：iwin_gameserver center-http-deposit-withdraw 已完成到 Step 5；目前沒有預設下一步，請以 senior-owner-playbook/01-senior-owner-flow-inventory.md 與 senior-owner-playbook/06-todo.md 為準。

## 履歷 claim 分層（2026-05-18 KB 對齊）

- 可放履歷：coupon flow 已通過 project-level consolidation，可用「參與 / 開發」口徑保守寫入。
- 可面試講：code-backed / 分析過。可用 coupon redeem credit grant 說明跨系統 money side effect、transaction boundary、idempotency、partial success 與 reconciliation。
- 不可誇大：不得寫成 Nick 主導完整 coupon / reward 系統、修復雙領 production bug、設計 Redis lock、負責完整玩家端 API owner、Partner API owner、代理佣金 owner 或完整 wallet / reconciliation owner。
