# iwin iwin_gameserver

本資料夾整理 `/Users/nick/Git/iwin/iwin_gameserver` 的專案知識。

`iwin_gameserver` 是 Java 遊戲伺服器 repo，包含玩家入口、center 大廳 / 錢包、各遊戲服務、dbproxy、log server、social server 與共用 network / Redis / Zookeeper / Protobuf 基礎設施。它比 `app_bi` 更接近 runtime source of truth，適合作為 Senior Java Backend / Platform Backend / System Owner 的 production flow 素材。

## 讀檔順序

1. [architecture-map.md](architecture-map.md)：最小專案地圖。
2. [step1-candidate-flows.md](step1-candidate-flows.md)：Step 1 候選 flow 盤點。
3. [step2-flow-comparison.md](step2-flow-comparison.md)：Step 2 候選 flow 技術點與風險比較。
4. [contribution-claim-consolidation.md](contribution-claim-consolidation.md)：project-level rolling / scoped 履歷 claim 收口。
5. [flows/third-party-transfer-in-out/flow.md](flows/third-party-transfer-in-out/flow.md)：第三方遊戲投派整合主研究報告。
6. [flows/third-party-transfer-in-out/career-interview.md](flows/third-party-transfer-in-out/career-interview.md)：該 flow 的面試 / 履歷素材。
7. [flows/third-party-transfer-in-out/materials/](flows/third-party-transfer-in-out/materials/)：證據、技術決策、詳細面試稿與 claim 邊界附錄。
8. [flows/center-http-deposit-withdraw/flow.md](flows/center-http-deposit-withdraw/flow.md)：center_http 上分 / 下分 Step 4 主學習包與面試 case 摘要。
9. [flows/center-http-deposit-withdraw/career-interview.md](flows/center-http-deposit-withdraw/career-interview.md)：center_http 上分 / 下分的正式面試素材。
10. [flows/center-http-deposit-withdraw/materials/](flows/center-http-deposit-withdraw/materials/)：證據、技術決策、詳細面試稿與 claim 邊界附錄。
11. [career-interview.md](career-interview.md)：project-level 履歷 / 面試邊界索引。

## 目前狀態

| 文件 / flow | 狀態 | 說明 |
| --- | --- | --- |
| `README.md` | 已建立 | 專案入口與履歷邊界 |
| `architecture-map.md` | 已補強 | Level 1 最小架構地圖，已補 root module、game modules、service instance 邊界 |
| `step1-candidate-flows.md` | 已建立 | Level 1 Flow 掃描，列出 Top 5 候選 |
| `step2-flow-comparison.md` | 已建立 | 候選 flow 技術點、子模組範圍與風險比較 |
| `flows/third-party-transfer-in-out/flow.md` | Step 3 已建立 | 第三方遊戲投派整合 / 投注派彩退款，Level 2 深掃 |
| `flows/third-party-transfer-in-out/career-interview.md` | Step 5 已完成；2026-05-20 已升級 claim | 有 Nick / `10gt12nc` direct commits，可併入 project-level 第三方 provider 投派整合履歷 claim |
| `flows/center-http-deposit-withdraw/flow.md` | Step 4 已完成 | center_http 玩家上分 / 下分，Level 2 深掃 + 面試 case 摘要 |
| `flows/center-http-deposit-withdraw/career-interview.md` | Step 4 已完成 | 正式面試素材；履歷 claim 留到 Step 5 判斷 |
| `contribution-claim-consolidation.md` | 已完成 / 2026-05-20 | rolling / scoped project-level claim 收口；第三方 provider 投派整合可保守放履歷，center-http 上下分仍 interview-only |
| `career-interview.md` | 已更新 | project-level career / interview boundary；正式履歷可保守補第三方 provider 投派整合 |

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

2026-05-20 更新：

- 已完成 [contribution-claim-consolidation.md](contribution-claim-consolidation.md)。
- `third-party-transfer-in-out` 不再只是分析素材；Nick / `10gt12nc` 在 Antplay / GSC / PG gameserver 投派整合、money job、`GamePlayer` log dispatch 與 log reel path 有直接 commits。
- `center-http-deposit-withdraw` 仍未看到 Nick / `10gt12nc` 對 `DEPOSIT/WITHDRAW` path 的 direct commits，維持 code-backed 面試素材。

仍待確認：

- 完整 project final consolidation 仍需等本批代表 flows 後續校正。
- `center-http-deposit-withdraw` Step 4 已完成，Step 5 尚未完成。
- 若要寫成更強 owner claim，仍需 MR / ticket / production issue、設計紀錄或 Nick 本人補充具體責任。

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

目前可以說：

- Nick / `10gt12nc` 在 `iwin_gameserver` 第三方遊戲 provider 投派整合、gameserver wallet mutation hook、log reel / bet log projection 有直接 code evidence。
- 正式履歷可保守寫「參與第三方遊戲 provider 投派整合與 gameserver 錢包 / 投注流水串接」，並限定在 Antplay / GSC / PG 類 bet / settle / refund / transfer-in-out flow 與 log projection。
- `center-http-deposit-withdraw` 可作為上分 / 下分 money flow 的 code-backed 面試素材，但目前不新增正式履歷 claim。

目前不能說：

- Nick 主導 `iwin_gameserver`。
- Nick 是遊戲伺服器 owner。
- Nick 獨立完成第三方遊戲整合、錢包、投注結算或 dbproxy。
- 任何改善百分比、正式架構師責任或全權 owner claim。

2026-05-20 consolidation 結論：

- 更新 `senior-owner-playbook/05-resume-master-zh.md` 與 `08-application-autobiography-zh.md`，但只採保守第三方 provider 投派整合 claim。
- 不把第三方投派 commits 擴張成完整 gameserver owner、完整遊戲錢包 owner 或完整上分 / 下分 owner。

## 下一步建議

只推薦一件事：

```text
iwin iwin_gameserver center-http-deposit-withdraw Step 5
```

原因：

- `center-http-deposit-withdraw` Step 4 已完成正式面試 case。
- Career Track 的 rolling / scoped contribution consolidation 已完成。
- 下一步先做 Step 5 claim gate；後續若新增 gameserver flow，再回填校正 project-level claim。

## 履歷 claim 分層（2026-05-18 KB 對齊）

- 可放履歷：第三方 provider 投派整合與 gameserver 錢包 / 投注流水串接，限 Antplay / GSC / PG 類 bet / settle / refund / transfer-in-out、money job 與 log projection。
- 可面試講：third-party transfer in/out 可用「部分真實開發過 + code-backed」語氣；center_http 上分 / 下分已完成 Step 4，仍用 code-backed / 分析過語氣。
- 不可誇大：不得寫成 Nick 主導 gameserver、完整 wallet owner、完整第三方遊戲整合 owner、完整上分 / 下分 owner 或解決 duplicate callback production incident。
