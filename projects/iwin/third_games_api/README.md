# iwin third_games_api

本資料夾整理 `/Users/nick/Git/iwin/third_games_api` 的專案知識。

`third_games_api` 是 iwin 第三方遊戲整合服務，主要接收 Antplay、OneAPI / PG、GSC 等 provider 的 launch、balance、bet、settle、rollback / cancel / transfer callback，並透過 Redis 內的平台設定找到 gameserver `center_http`，再呼叫 gameserver 修改或查詢玩家餘額、投注、派彩與有效投注資料。

目前已完成 rolling / scoped contribution consolidation。結論是：`third_games_api` 本 repo 不新增正式履歷主成果；既有 GSC flow 保留為 `專案存在 / code-backed` 的面試素材。Nick 較強的第三方遊戲 direct evidence 目前主要落在下游 `iwin_gameserver` 的 Antplay / GSC / PG command、log、投派整合 commits，不能反向包裝成 `third_games_api` owner。

## 讀檔順序

1. [step1-candidate-flows.md](step1-candidate-flows.md)：Step 1 候選 flow 盤點。
2. [step2-flow-comparison.md](step2-flow-comparison.md)：Step 2 候選 flow 風險與價值比較。
3. [contribution-claim-consolidation.md](contribution-claim-consolidation.md)：rolling / scoped project-level 貢獻收斂，確認不新增 `third_games_api` standalone 履歷主成果。
4. [flows/gsc-transfer-bet-settle-rollback/flow.md](flows/gsc-transfer-bet-settle-rollback/flow.md)：GSC transfer 投注 / 派彩 / rollback 主研究報告。
5. [flows/gsc-transfer-bet-settle-rollback/career-interview.md](flows/gsc-transfer-bet-settle-rollback/career-interview.md)：該 flow 的保守面試 / 履歷素材。
6. [flows/gsc-transfer-bet-settle-rollback/materials/](flows/gsc-transfer-bet-settle-rollback/materials/)：證據、技術決策、詳細面試稿與 claim 邊界附錄。

## 目前狀態

| 文件 / flow | 狀態 | 說明 |
| --- | --- | --- |
| `step1-candidate-flows.md` | 已建立 | Level 1 掃描，找出 Top 5 third-party game integration flow 候選 |
| `step2-flow-comparison.md` | 已建立 | 比較候選 flow，建議第一條深挖 `gsc-transfer-bet-settle-rollback` |
| `flows/gsc-transfer-bet-settle-rollback/` | 已建立 | Step 4 已轉成保守面試 case；保守標註為 `專案存在 / code-backed` 與 `分析素材 / learning-only`，尚未更新正式履歷 |
| `contribution-claim-consolidation.md` | 已完成 rolling / scoped | `third_games_api` 本 repo 只有局部測試 / merge 線索，不新增正式履歷主成果；下游 `iwin_gameserver` direct evidence 已由該 project consolidation 收口 |

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

- Nick 在 `third_games_api` 本 repo 目前只掃到局部測試 / merge 線索，未確認主導任一 production provider adapter。
- GSC / OneAPI / Antplay 對應 gameserver command handler 的去重、交易狀態與 rollback 邊界。
- Mongo collection 是否有 unique index 或其他冪等保護。
- provider 重送、timeout、gameserver 成功但 Mongo 寫失敗時的補償 / 對帳流程。

## 履歷邊界

目前只能說：

- 可用來理解 / 分析第三方遊戲 provider callback、seamless wallet、下注派彩與 rollback 的後端整合風險。
- 可作為 Senior Backend 面試素材的 evidence base；`gsc-transfer-bet-settle-rollback` 已完成 Step 4，可講 code-backed 分析，不更新正式履歷。
- 下游 `iwin_gameserver` 的 Antplay / GSC / PG direct commits 已於 `iwin_gameserver contribution claim consolidation` 正確歸位，不反向包裝成 `third_games_api` direct contribution。

目前不能說：

- Nick 主導 `third_games_api`。
- Nick 負責 Antplay / OneAPI / GSC 任一 provider 串接。
- Nick 是第三方遊戲錢包、下注派彩、rollback 或對帳 owner。
- 把下游 `iwin_gameserver` direct commits 包裝成 `third_games_api` direct contribution。

## 下一步建議

只推薦一件事：

```text
iwin iwin_gameserver center-http-deposit-withdraw Step 4
```

原因：

- `third_games_api` rolling consolidation 已完成，結論是不新增 standalone 正式履歷主成果。
- 較強的第三方遊戲 direct evidence 已歸到下游 `iwin_gameserver`，包含 Antplay / GSC / PG command、log、投派整合相關 `10gt12nc` commits。
- 下一步回到目前總 queue 的 `iwin_gameserver center-http-deposit-withdraw Step 4`；若 Nick 之後要 project-local flow 收斂，再回來做 `gsc-transfer-bet-settle-rollback Step 5`。

## 履歷 claim 分層（2026-05-18 KB 對齊）

- 可放履歷：目前不新增 `third_games_api` standalone 正式履歷主成果；尚未補到 Nick 本人對 GSC transfer callback 的 MR / ticket / commit / production issue / 本人確認。
- 可面試講：code-backed / 分析過。可用 GSC transfer bet / settle / rollback flow 說明第三方 seamless wallet callback、gameserver wallet mutation、Mongo audit、retry、idempotency 與 rollback 語意。
- 不可誇大：不得寫成 Nick 主導 GSC provider 串接、完整第三方遊戲錢包 owner、建立完整 idempotency / reconciliation 或解決 production 錯帳。
