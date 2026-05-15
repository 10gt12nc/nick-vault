# iwin iwin_gameserver

本資料夾整理 `/Users/nick/Git/iwin/iwin_gameserver` 的專案知識。

`iwin_gameserver` 是 Java 遊戲伺服器 repo，包含玩家入口、center 大廳 / 錢包、各遊戲服務、dbproxy、log server、social server 與共用 network / Redis / Zookeeper / Protobuf 基礎設施。它比 `app_bi` 更接近 runtime source of truth，適合作為 Senior Java Backend / Platform Backend / System Owner 的 production flow 素材。

## 讀檔順序

1. [architecture-map.md](architecture-map.md)：最小專案地圖。
2. [step1-candidate-flows.md](step1-candidate-flows.md)：Step 1 候選 flow 盤點。
3. 未來 `flows/{flow-name}/flow.md`：單條 flow 的主研究報告。
4. 未來 `flows/{flow-name}/career-interview.md`：該 flow 的保守面試 / 履歷素材。
5. 未來 `flows/{flow-name}/materials/`：證據、技術決策、詳細面試稿與 claim 邊界附錄。

## 目前狀態

| 文件 / flow | 狀態 | 說明 |
| --- | --- | --- |
| `README.md` | 已建立 | 專案入口與履歷邊界 |
| `architecture-map.md` | 已建立 | Level 1 最小架構地圖，只放定位資訊 |
| `step1-candidate-flows.md` | 已建立 | Level 1 Flow 掃描，列出 Top 5 候選 |

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

## 下一步建議

只推薦一件事：

```text
iwin_gameserver third-party-transfer-in-out Step 2
```

原因：

- Step 1 已建立候選 ranking。
- `third-party-transfer-in-out` 同時牽涉第三方投注 / 派彩 / 退款、玩家錢包、投注流水、有效投注、log 與可能的 idempotency，是目前最值得比較後深挖的 flow。
- 下一步是 Step 2 候選 flow 技術點與風險比較，不更新正式履歷；完成 Step 2 後再進單條 flow Step 3。
