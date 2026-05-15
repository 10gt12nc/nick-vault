# iwin third_games_api

本資料夾整理 `/Users/nick/Git/iwin/third_games_api` 的專案知識。

`third_games_api` 是 iwin 第三方遊戲整合服務，主要接收 Antplay、OneAPI / PG、GSC 等 provider 的 launch、balance、bet、settle、rollback / cancel / transfer callback，並透過 Redis 內的平台設定找到 gameserver `center_http`，再呼叫 gameserver 修改或查詢玩家餘額、投注、派彩與有效投注資料。

目前所有內容只標為 `專案存在 / code-backed`；沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認前，不寫成 Nick 真實開發成果。

## 讀檔順序

1. [step1-candidate-flows.md](step1-candidate-flows.md)：Step 1 候選 flow 盤點。
2. [step2-flow-comparison.md](step2-flow-comparison.md)：Step 2 候選 flow 風險與價值比較。
3. 待建立：`flows/{flow-name}/flow.md`：單條 flow 的主研究報告。
4. 待建立：`flows/{flow-name}/career-interview.md`：該 flow 的保守面試 / 履歷素材。
5. 待建立：`flows/{flow-name}/materials/`：證據、技術決策、詳細面試稿與 claim 邊界附錄。

## 目前狀態

| 文件 / flow | 狀態 | 說明 |
| --- | --- | --- |
| `step1-candidate-flows.md` | 已建立 | Level 1 掃描，找出 Top 5 third-party game integration flow 候選 |
| `step2-flow-comparison.md` | 已建立 | 比較候選 flow，建議第一條深挖 `gsc-transfer-bet-settle-rollback` |
| `flows/` | 尚未建立 | Step 1 不建立 flow folder，等 Nick 選定單條 flow 後再建 |

## 專案定位

已確認：

- Java / Spring Boot 專案。
- active controller 主要是 `AntplayController`、`OneApiController`、`GscController`、`CashController`、`RootController`。
- 多數玩家帳號、SMS、partner 類 controller 仍保留檔案，但目前 annotation 被註解，不能當 active endpoint。
- 核心資料流是 provider request / callback -> 驗簽與幣別檢查 -> 玩家與 game id 對照 -> gameserver `center_http` -> Mongo third log / transaction。
- 主要風險集中在 money correctness、provider idempotency、rollback / cancel 語意、Redis platform config freshness、Mongo log 與 gameserver wallet mutation 的一致性。

推測：

- `third_games_api` 是 third-party game orchestration / adapter 層，不是錢包最終 source of truth。
- 真正餘額與投注狀態落點在 `iwin_gameserver`，本 project 需要搭配 gameserver handler 才能完整判斷 transaction boundary。
- Mongo `third_log_*` / `third_transaction_*` 較像 callback audit / reconciliation evidence，不一定是最終帳本。

待確認：

- Nick 實際參與過哪些 `third_games_api` 功能。
- GSC / OneAPI / Antplay 對應 gameserver command handler 的去重、交易狀態與 rollback 邊界。
- Mongo collection 是否有 unique index 或其他冪等保護。
- provider 重送、timeout、gameserver 成功但 Mongo 寫失敗時的補償 / 對帳流程。

## 履歷邊界

目前只能說：

- 可用來理解 / 分析第三方遊戲 provider callback、seamless wallet、下注派彩與 rollback 的後端整合風險。
- 可作為 Senior Backend 面試素材的 evidence base，但需先完成單條 flow Level 2 深掃。

目前不能說：

- Nick 主導 `third_games_api`。
- Nick 負責 Antplay / OneAPI / GSC 任一 provider 串接。
- Nick 是第三方遊戲錢包、下注派彩、rollback 或對帳 owner。
- 單靠 Step 1 寫入正式履歷 / 自傳。

## 下一步建議

只推薦一件事：

```text
iwin third_games_api gsc-transfer-bet-settle-rollback Step 3
```

原因：

- Step 2 已比較 Top 5 candidate flows。
- `gsc-transfer-bet-settle-rollback` 同時具備 money correctness、provider idempotency、rollback / transfer 語意、gameserver downstream evidence 與近期 commit 線索。
- Step 3 會建立單條 flow 學習包；不更新正式履歷，完成後依規則自動 commit。
