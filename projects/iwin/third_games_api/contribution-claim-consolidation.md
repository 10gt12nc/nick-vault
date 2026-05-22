# iwin third_games_api contribution claim consolidation

日期：2026-05-20
掃描等級：Level 3 取向 rolling / scoped contribution claim 掃描
狀態：已完成 rolling / scoped consolidation；不是 final consolidation
證據層級：`專案存在 / code-backed + 局部測試線索 + 下游 iwin_gameserver direct evidence`

## 結論

`third_games_api` 目前不新增正式履歷主成果。

本輪掃到的 Nick / `10gt12nc` direct evidence，在 `third_games_api` 本 repo 內主要是：

- `origin/Test001-Nick` 上兩筆 `10gt12nc` 對 `AntplayController.java` 的測試用 commit。
- `nick` merge `beta2` 到 `main` 的 merge commit。

這些只能支撐「局部測試 / 排查 / branch merge 線索」，不能寫成 Nick 主導 `third_games_api`、主導 GSC / OneAPI / Antplay provider 串接、完整 seamless wallet owner 或 production incident owner。

但本輪也確認：`third_games_api` 是很好的 code-backed 面試素材，因為它把 provider callback、Redis routing、gameserver wallet mutation、Mongo audit / transaction evidence 串在一起。Nick 較強的第三方遊戲實作 evidence 目前主要在下游 `/Users/nick/Git/iwin/iwin_gameserver` 的 Antplay / GSC / PG command、log、投派整合相關 commits；這些可放進 `iwin_gameserver` project consolidation，不應反向包裝成 `third_games_api` 主成果。

## 自動重讀

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`

已重讀 vault：

- `projects/iwin/third_games_api/README.md`
- `projects/iwin/third_games_api/step1-candidate-flows.md`
- `projects/iwin/third_games_api/step2-flow-comparison.md`
- `projects/iwin/third_games_api/flows/gsc-transfer-bet-settle-rollback/flow.md`
- `projects/iwin/third_games_api/flows/gsc-transfer-bet-settle-rollback/career-interview.md`
- `projects/iwin/third_games_api/flows/gsc-transfer-bet-settle-rollback/materials/evidence.md`
- `projects/iwin/third_games_api/flows/gsc-transfer-bet-settle-rollback/materials/claim-boundary.md`
- `projects/iwin/third_games_api/flows/gsc-transfer-bet-settle-rollback/materials/interview.md`
- `projects/iwin/third_games_api/flows/gsc-transfer-bet-settle-rollback/materials/decision-notes.md`
- `projects/iwin/README.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`
- `senior-owner-playbook/06-todo.md`

## source repo 狀態

### `/Users/nick/Git/iwin/third_games_api`

- 已執行 `git fetch --all --prune` 更新 remote refs。
- 目前分支：`beta`
- local HEAD：`4915ea5a5000d61eb36717203ea4c6afc45322fa`
- `origin/beta` HEAD：`4915ea5a5000d61eb36717203ea4c6afc45322fa`
- ahead / behind：`0 / 0`
- 本機工作樹：乾淨。
- 本輪只讀 source repo；未 pull、未 checkout、未 merge、未 rebase、未改公司 repo。

遠端 refs：

- `origin/main`
- `origin/beta`
- `origin/beta2`
- `origin/k3s`
- `origin/Test001-Nick`
- `origin/checkPerformance`

### `/Users/nick/Git/iwin/iwin_gameserver`

- 已執行 `git fetch --all --prune` 更新 remote refs。
- 目前分支：`main`
- local HEAD：`30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- `origin/main` HEAD：`30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- ahead / behind：`0 / 0`
- 本機工作樹：乾淨。
- 本輪只讀 source repo；未 pull、未 checkout、未 merge、未 rebase、未改公司 repo。

關聯遠端 refs：

- `origin/Nick-GSC_PG`
- `origin/Nick-review`
- `origin/k3s`
- `origin/main`

## 掃描範圍

已掃：

- `third_games_api` 全分支 Nick / `10gt12nc` author log。
- `third_games_api` 近期 log。
- `third_games_api` path-specific history：
  - `src/main/java/com/slots/web/controller/GscController.java`
  - `src/main/java/com/slots/web/controller/OneApiController.java`
  - `src/main/java/com/slots/web/controller/AntplayController.java`
  - `src/main/java/com/slots/web/common/utils/GetGameRedis.java`
  - `src/main/java/com/slots/web/schedule/ScheduleServer.java`
  - `src/main/java/com/slots/web/common/filter/WhiteListFilter.java`
- `third_games_api` `origin/Test001-Nick` 與 `origin/checkPerformance` log。
- `third_games_api` `10gt12nc` commits `ec9d812`、`63e88f2` 的 path diff 摘要；不搬運其中硬編測試值、測試 URL 或環境資訊。
- 下游 `iwin_gameserver` GSC / PG / Antplay 相關 path-specific Nick / `10gt12nc` history。
- 既有 GSC flow KB 與 claim boundary。

未掃 / 不宣稱：

- 未 checkout 每個遠端分支逐檔逐行。
- 未掃 provider 官方規格。
- 未掃 production route / deploy / incident / ticket 系統。
- 未掃 Mongo index / schema。
- 未確認 `third_games_api` production 實際走 GSC `transfer` 還是 split endpoints。
- 未把 `iwin_gameserver` direct commits 直接歸因為 `third_games_api` 開發成果。

## contribution evidence 摘要

| 類型 | Evidence | 判斷 |
| --- | --- | --- |
| `third_games_api` 測試 / 排查線索 | `ec9d812` / `63e88f2`：`10gt12nc` 在 `origin/Test001-Nick` 修改 `AntplayController.java`，commit message 為測試用，內容偏硬編測試帳號 / GameServer URL / 暫時關閉部分 validation / sign / Redis routing | 局部測試線索；不能放正式履歷主成果，不能寫 production provider 開發 |
| `third_games_api` branch merge | `b16e606`：`nick` merge `beta2` into `main`，merge message 指向 Antplay content-type 相關 MR | branch / merge 線索；可作參與環境或分支流轉背景，不足以宣稱功能 owner |
| GSC transfer flow | `gsc-transfer-bet-settle-rollback` Step 5 已確認 GSC transfer callback、gameserver `PGTRANSFERINOUT`、Mongo audit / transaction evidence | code-backed 面試素材；仍不升級正式履歷 |
| OneAPI / Antplay provider flow | OneAPI `oneapi-wallet-bet-result` 已完成 Step 5；Antplay `antplay-bet-settle-rollback` 已完成 Step 5；GSC split endpoint 與 Redis config flow 仍是 Step 2 candidate | code-backed 面試素材；OneAPI 與 Antplay 可面試講；不可寫成 Nick 成果 |
| 下游 `iwin_gameserver` direct evidence | `10gt12nc` 在 `iwin_gameserver` 有大量 Antplay / GSC / PG commits，例如 `feat(#144): iwin gameserver 對接antplay遊戲` 系列、`feat(#PG): GSC+ PG`、`feat(#PG): bet_result 投派整合`、GSC / Antplay log reel 優化與投派整合 commits | 較強的真實開發 evidence，但 project attribution 屬於 `iwin_gameserver`；只能作 `third_games_api` 下游關聯，不直接包成 `third_games_api` owner |

## 可放履歷：正式主成果

目前不新增 `third_games_api` standalone 正式履歷 bullet。

原因：

- `third_games_api` 本 repo 內 Nick / `10gt12nc` direct commits 偏測試用與 branch merge。
- GSC / OneAPI / Antplay provider adapter 主體 commit 多為 Derek / arnold。
- 已完成的 GSC transfer flow 目前是 code-backed analysis，不是 Nick 真實開發 evidence。
- 下游 `iwin_gameserver` 的強 evidence 已由 `iwin_gameserver contribution claim consolidation` 統一收口，不反向包裝成 `third_games_api` 成果。

若需要在履歷大段落中保留第三方遊戲經驗，建議仍用既有保守口徑：

```text
參與或分析第三方遊戲 provider integration 與遊戲結算相關流程，涵蓋登入、轉入轉出、下注 / 派彩、rollback、交易同步、紀錄保存與報表鏈路，聚焦玩家餘額、provider transaction 與 round log 的一致性。
```

這句不能解讀成 Nick 主導 `third_games_api`。

## 可面試講：code-backed / 分析過

可以講：

- `third_games_api` 是 provider-facing adapter，不是 wallet source of truth。
- GSC `transfer` flow 會經過 sign / currency / account / Redis game mapping / center routing，再呼叫 gameserver `PGTRANSFERINOUT`。
- Mongo `third_log_gsc` / `third_transaction_gsc` 是 callback / transaction evidence，不等於最終帳本。
- Senior / Owner 追問點：provider retry、gameserver 成功但 Mongo 失敗、ROLLBACK 不呼叫 gameserver、多筆 transactions 只處理最後一筆、Redis config freshness。
- 下游 gameserver 的 Antplay / GSC / PG command 與 log projection 才是 wallet mutation / transaction boundary 的主戰場。

建議面試講法：

```text
我分析過 third_games_api 這類第三方遊戲 adapter flow。它不是錢包 source of truth，而是把 provider callback 驗簽、轉換、路由到 gameserver，再用 Mongo 留 audit evidence。真正要問的是 gameserver wallet mutation 是否冪等、provider retry 如何收斂，以及 Mongo / log projection 和錢包狀態怎麼對帳。
```

## 不可誇大

不得寫：

- Nick 主導 `third_games_api`。
- Nick 負責 GSC / OneAPI / Antplay 任一 provider adapter 全流程。
- Nick 設計第三方遊戲 seamless wallet 架構。
- Nick 建立 provider callback exactly-once / idempotency / reconciliation。
- Nick 修復 GSC rollback 或 production 錯帳。
- `third_games_api` 本 repo 的測試用 commit 等同 production provider 開發。
- 下游 `iwin_gameserver` direct commits 等同 `third_games_api` direct contribution。

## 各 flow claim 更新判斷

| Flow | 目前狀態 | consolidation 後判斷 |
| --- | --- | --- |
| `gsc-transfer-bet-settle-rollback` | Step 5 已完成 | 不更新正式履歷；保留為 code-backed 面試素材 |
| `oneapi-wallet-bet-result` | Step 5 已完成 | code-backed 面試素材；不更新正式履歷；下游 PGTransferInOut direct evidence 歸屬 `iwin_gameserver` |
| `antplay-bet-settle-rollback` | Step 5 已完成 | `third_games_api` 本 repo 有 10gt12nc 測試線索，但正式開發主體不足；可做三段式 money flow / retry failure window 面試對照，不寫正式成果 |
| `gsc-seamless-withdraw-deposit-cancel` | Step 2 candidate | 待確認 production 使用狀態；不更新正式履歷 |
| `third-platform-redis-config-refresh` | Step 2 candidate | 支援性 flow；目前不作履歷主成果 |

## 履歷 / 自傳更新結論

本輪應更新：

- `projects/iwin/third_games_api/README.md`
- `projects/iwin/README.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/06-todo.md`
- `senior-owner-playbook/05-resume-master-zh.md` / `08-application-autobiography-zh.md` 的 claim boundary note：只補「third_games_api 不新增 standalone 主成果」，不新增正式成果 bullet。

不應新增：

- 新的 flow Step。
- 新的流水帳。
- 公司 repo 文件。
- `third_games_api` standalone 履歷主成果。

## 下一步建議

只推薦一件事：

```text
iwin third_games_api gsc-seamless-withdraw-deposit-cancel Step 3
```

原因：

- `gsc-transfer-bet-settle-rollback Step 5` 已完成，結論仍是 interview-only / no standalone resume bullet。
- 真正較強的第三方遊戲 direct evidence 在 `iwin_gameserver`，已由該 project consolidation 正確歸位。
- `oneapi-wallet-bet-result Step 5` 與 `antplay-bet-settle-rollback Step 5` 都已完成；下一步回 Step 2 ranking 第四候選，確認 GSC 分離式 endpoint 是否仍值得深挖。
