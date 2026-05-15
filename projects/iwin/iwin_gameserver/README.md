# iwin iwin_gameserver

本資料夾整理 `/Users/nick/Git/iwin/iwin_gameserver` 的專案知識。

`iwin_gameserver` 是 Java 遊戲伺服器 repo，包含玩家入口、center 大廳 / 錢包、各遊戲服務、dbproxy、log server、social server 與共用 network / Redis / Zookeeper / Protobuf 基礎設施。它比 `app_bi` 更接近 runtime source of truth，適合作為 Senior Java Backend / Platform Backend / System Owner 的 production flow 素材。

## 讀檔順序

1. [architecture-map.md](architecture-map.md)：最小專案地圖。
2. [step1-candidate-flows.md](step1-candidate-flows.md)：Step 1 候選 flow 盤點。
3. [step2-flow-comparison.md](step2-flow-comparison.md)：Step 2 候選 flow 技術點與風險比較。
4. [flows/third-party-transfer-in-out/flow.md](flows/third-party-transfer-in-out/flow.md)：Step 3 單條 flow 主研究報告。
5. [flows/third-party-transfer-in-out/career-interview.md](flows/third-party-transfer-in-out/career-interview.md)：該 flow 的保守面試 / 履歷素材。
6. [flows/third-party-transfer-in-out/materials/](flows/third-party-transfer-in-out/materials/)：證據、技術決策、詳細面試稿與 claim 邊界附錄。
7. [career-interview.md](career-interview.md)：project-level 履歷 / 面試邊界索引。

## 目前狀態

| 文件 / flow | 狀態 | 說明 |
| --- | --- | --- |
| `README.md` | 已建立 | 專案入口與履歷邊界 |
| `architecture-map.md` | 已補強 | Level 1 最小架構地圖，已補 root module、game modules、service instance 邊界 |
| `step1-candidate-flows.md` | 已建立 | Level 1 Flow 掃描，列出 Top 5 候選 |
| `step2-flow-comparison.md` | 已建立 | 候選 flow 技術點、子模組範圍與風險比較 |
| `flows/third-party-transfer-in-out/flow.md` | Step 3 已建立 | 第三方遊戲投派整合 / 投注派彩退款，Level 2 深掃 |
| `flows/third-party-transfer-in-out/career-interview.md` | Step 5 已完成 | 保守面試案例，含履歷 / 自傳邊界 |
| `career-interview.md` | Step 5 已建立 | project-level career / interview boundary；正式履歷暫不更新 |

## KB 更新後深度檢查

更新時間：2026-05-15
狀態：已依最新 KB 重新檢查，可沿用但已補 repo 最新性與 Step 狀態邊界

已確認：

- 本 project 已有 Step 1、Step 2、單條 flow Step 3、Step 4、Step 5；沒有跳 Step。
- `third-party-transfer-in-out` flow folder 已具備 `flow.md`、`career-interview.md`、`materials/evidence.md`、`materials/decision-notes.md`、`materials/interview.md`、`materials/claim-boundary.md`。
- `flow.md` 已有白話導讀、Code 分層對照、最小架構圖、正常流程圖、逐步說明與 Senior / Owner 分析。
- 依最新 KB 的 multi-session / staging 防污染規則，本輪檢查開始時 `nick-vault` working tree 與 staging area 皆乾淨；後續改檔需精準 stage 本 project 檔案，不使用 `git add .`。
- `/Users/nick/Git/iwin/iwin_gameserver` 已重新 fetch，`main` 與 `origin/main` 一致。
- `/Users/nick/Git/iwin/third_games_api` 已重新 fetch，`beta` 與 `origin/beta` 一致。

仍待確認：

- Nick 本人是否實際參與 `iwin_gameserver` 或此 flow。
- `third-party-transfer-in-out` Step 5 結論：暫不形成正式履歷 / 自傳 claim；只保留為面試分析素材。
- 若要把此 flow 升級成強 evidence 或履歷 claim，仍需 Level 3 path-specific commit diff、MR / ticket / production issue 或 Nick 本人確認。

## 專案定位

已確認：

- Java 8 / Maven multi-module 遊戲伺服器。
- 核心 module 包含 `slots-gate`、`slots-center`、`slots-dbproxy`、`slots-game-log`、`slots-social`、`slots-common`、`slots-games`。
- `slots-center` 提供 center_http 指令入口，處理 `DEPOSIT`、`WITHDRAW`、`SET_BET_TARGET`、`QUERY_BET_TARGET`、`NOTICE`、第三方遊戲投注 / 派彩 / 退款 / 投派整合等 flow。
- `slots-games/slots-game-common` 提供遊戲共同 runtime、玩家資料、房間、機率 / 賠付、機器人、與 center 錢包同步資料結構。
- `slots-dbproxy` 是跨 module 的 MySQL / Redis 查寫代理。
- `slots-game-log` 負責遊戲與投注相關 log writer / cron 類工作。

待確認：

- Nick 本人是否實際開發過 `iwin_gameserver` 的哪條 flow。
- 每條候選 flow 的完整 transaction boundary、idempotency、retry / compensation、reconciliation。
- 第三方遊戲整合 flow 的上游 repo 是否為 `third_games_api`、`game_api` 或其他服務。

## 履歷邊界

目前只能說：

- `iwin_gameserver` 可作為分析 iwin 遊戲 runtime、錢包、投注 / 派彩 / 退款與資料一致性的 code-backed 素材。
- 可用來準備 Senior / Owner 面試案例，但仍需補 Nick 本人 evidence。

目前不能說：

- Nick 主導 `iwin_gameserver`。
- Nick 是遊戲伺服器 owner。
- Nick 獨立完成第三方遊戲整合、錢包、投注結算或 dbproxy。
- 任何改善百分比、正式架構師責任或全權 owner claim。

Step 5 結論：

- `third-party-transfer-in-out` 已完成履歷 / 自傳邊界整理。
- 不更新 `senior-owner-playbook/05-resume-master-zh.md`。
- 不更新 `senior-owner-playbook/08-application-autobiography-zh.md`。
- 若 Nick 後續補本人 MR / ticket / commit / production issue / 本人確認，再重新評估是否升級。

## 下一步建議

只推薦一件事：

```text
iwin_gameserver center-http-deposit-withdraw Step 3
```

原因：

- `third-party-transfer-in-out` 已完成 Step 5，正式履歷 / 自傳暫不更新。
- 同 project 下一條最高價值候選是 `center-http-deposit-withdraw`，會補單條 flow 主報告。
- 下一步不會更新履歷；仍以 code-backed flow 分析與 evidence 邊界為主。
