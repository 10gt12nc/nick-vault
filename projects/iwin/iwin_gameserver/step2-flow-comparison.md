# iwin_gameserver Step 2：候選 Flow 技術點與風險比較

更新時間：2026-05-15
掃描等級：Level 1 Flow 掃描 / 候選 flow 比較
狀態：初版
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 本次結論

`iwin_gameserver` 不是單一服務，而是 root multi-module 加上多個 game modules 與 service instance config。Step 2 的重點不是急著建立 `flows/{flow}`，而是先把候選 flow 橫跨哪些 module、風險在哪、哪條最值得進 Step 3 比清楚。

比較後，最值得進 Step 3 的候選是：

```text
third-party-transfer-in-out
```

原因是它橫跨 `slots-center`、`slots-games/slots-game-common`、`slots-game-log` 與可能的上游 adapter，直接牽涉玩家餘額、投注 / 有效投注、派彩 / 退款、log 與 idempotency。這比先做純 module map 或 dbproxy class summary 更接近 Senior / Owner 的 production flow。

本輪不更新履歷。沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認前，本文件所有 flow 都只作 `專案存在 / code-backed` 或 `分析素材 / learning-only`。

## 自動重讀紀錄

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/CONVENTIONS.md`

已重讀 vault：

- `projects/iwin/iwin_gameserver/README.md`
- `projects/iwin/iwin_gameserver/architecture-map.md`
- `projects/iwin/iwin_gameserver/step1-candidate-flows.md`

已重讀 code：

- `/Users/nick/Git/iwin/iwin_gameserver/pom.xml`
- `/Users/nick/Git/iwin/iwin_gameserver/slots-games/pom.xml`
- `/Users/nick/Git/iwin/iwin_gameserver/README.md`
- `slots-center/src/main/java/com/slots/center/service/HttpService.java`
- 第三方 transfer / settle job 相關 path
- dbproxy controller / mapper 相關 path
- `slots-games/slots-game-common` 共同遊戲 runtime path 清單

未重讀 / 未完成：

- 未 checkout 每個遠端分支。
- 未逐 commit diff。
- 未深掃 `third_games_api`、`game_api`、`payment`。
- 未逐一深掃 27 個 active game modules。
- 未確認 Nick 個人 MR / ticket / production issue。

## 舊文件狀態判斷

| 文件 | 狀態 | 判斷 |
| --- | --- | --- |
| `README.md` | 已補 | 已加入 Step 2 讀檔順序與狀態 |
| `architecture-map.md` | 已補 | 原本只列 root module，已補 game modules / service instance 邊界 |
| `step1-candidate-flows.md` | 已修正 | 下一步改回 `iwin_gameserver Step 2`，避免看起來跳過 Step 2 |
| `step2-flow-comparison.md` | 本次新增 | 補齊 Step 2 |

## 多子模組邊界

### Root module 分層

| 層級 | modules | flow 意義 |
| --- | --- | --- |
| 玩家入口 | `slots-gate` | player connection / packet routing，不是每條 money flow 都從這裡開始 |
| 大廳 / 錢包 / HTTP 指令 | `slots-center` | `DEPOSIT`、`WITHDRAW`、打碼、第三方投注 / 派彩 / 退款的主要入口 |
| 遊戲 runtime | `slots-games`、`slots-games/slots-game-common`、各 `slots-gameXX-*` | spin / settle / RTP / game state / coin sync |
| 資料代理 | `slots-dbproxy` | Redis / MySQL cache-db write path |
| log writer | `slots-game-log` | 投注 / 遊戲 / refund / RTP 類 log 與後續報表資料來源 |
| social | `slots-social` | mail / social，暫非高優先 |
| common infra | `slots-common` | Protobuf、Netty、Redis、Zookeeper、dao annotation、controller |

### Game module 分層

`slots-games` 不是單一 game。已確認 active game modules 有 27 個，另有多個在 `slots-games/pom.xml` 被註解。Step 2 不逐一做每款遊戲，原因是這會變成平均整理 module；後續若選 `game-spin-settlement-log-reel`，必須先挑一款代表 game 再進 Step 3。

### Service instance 邊界

`service/**` 底下有 center、gate、dbproxy、log、social 與多個 game instance config。這些是部署 / instance 邊界線索，不等於新的 flow。單條 flow 深掃時只引用相關 instance，不把所有 service config 平均整理。

## Flow 比較表

| 排名 | Flow | 中文名稱 | 涉及 module | Senior / Owner 價值 | 最大缺口 | 建議 |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | `third-party-transfer-in-out` | 第三方遊戲投派整合 / 投注派彩退款 | `slots-center`、`slots-games/slots-game-common`、`slots-game-log`、上游 adapter 待確認 | 高 | 上游 repo、idempotency、log / coin 寫入順序未完整確認 | 進 Step 3 |
| 2 | `center-http-deposit-withdraw` | center_http 玩家上分 / 下分 | `slots-center`、`slots-dbproxy`、`slots-common`、上游 `payment` / `game_api` | 高 | 上游 state machine 未掃，gameserver 防重待確認 | 先列候選，之後可跨 repo 深掃 |
| 3 | `game-spin-settlement-log-reel` | 遊戲 spin / 結算 / 投注流水 | `slots-gate`、`slots-center`、`slots-games`、單一 game module、`slots-game-log` | 高 | 必須先選代表 game；不能一次掃 27 個 game | 暫不跳，等選定遊戲 |
| 4 | `bet-target-set-query` | 打碼目標設定與查詢 | `slots-center`、`slots-dbproxy`、`slots-common`、上游 `payment` / `app_bi` 待確認 | 中高 | 打碼資料落點與投注扣減邏輯未追完 | 可作後續 money rule flow |
| 5 | `dbproxy-cache-db-write-path` | DB proxy Redis / MySQL 查寫路徑 | `slots-common`、`slots-dbproxy` | 中高 | 容易變 class summary；需綁定具體業務 flow | 作輔助深挖，不先單獨做 |

## 候選詳述

### 1. `third-party-transfer-in-out`

建議狀態：已進 Step 4，主報告見 `flows/third-party-transfer-in-out/flow.md`，面試素材見 `flows/third-party-transfer-in-out/career-interview.md`
證據層級：專案存在 / code-backed；Nick 貢獻待確認

已確認：

- `HttpService` 有 `ANTPLAY_BET`、`ANTPLAY_SETTLE`、`ANTPLAY_REFUND`、`ANTPLAYTRANSFERINOUT`、`PGTRANSFERINOUT`、`GSC_BET`、`GSC_SETTLE`、`GSC_REFUND`、`GSC_OTHER`。
- HTTP wrapper job 會先查 `SqlPlayerData`，再把實際 job 放進 center game pool。
- 實際 job 使用 `addMoney`、`betCoin`、`validBetCoin`、`spinCurrency`、`transactionId`、`betId` 等欄位。
- 近期 history 有 Antplay 投派整合、PG refund、GSC 相關 commit。

為什麼優先：

- 它不是單純 HTTP API，也不是單一 module。
- 扣款 / 派彩 / 退款錯誤會直接造成玩家餘額與投注流水不一致。
- `transactionId` / `betId` 很適合追 idempotency、replay、對帳與重試語意。
- 可以明確產出 Step 3 的主報告，不會變成「看完很多 class 但沒有 flow」。

Step 3 必須補：

- 上游 repo / adapter 是誰。
- `modifyAndGetCoin*` 的 atomicity / dataVersion / 防重。
- coin update、log_coins、log_reel、player data 的順序。
- 扣款成功但 response 失敗、派彩晚到、退款重送、玩家餘額不足時的行為。

### 2. `center-http-deposit-withdraw`

建議狀態：高價值候選，但需跨 `payment` / `game_api`
證據層級：專案存在 / code-backed；Nick 貢獻待確認

已確認：

- `HttpService` 有 `DEPOSIT` / `WITHDRAW`。
- `doCommand()` 以 `accountId` 找玩家 cache，不在 cache 時查 DB。
- workspace 關聯文件顯示 `payment` 與 `game_api` 都可能呼叫 center_http。

為什麼暫排第二：

- 它很重要，但單看 gameserver 會缺上游 order state machine。
- 要做得像 Senior / Owner 案例，必須同步看 `payment` 或 `game_api` 的 request id、訂單狀態與重試策略。

### 3. `game-spin-settlement-log-reel`

建議狀態：高價值，但要先選代表 game
證據層級：專案存在 / code-backed；Nick 貢獻待確認

已確認：

- `slots-games` 下有 27 個 active game modules。
- `slots-game-common` 有共同 game runtime、room、player、ruler、config、robot、center coin sync 類 code。
- `SqlLogReelData` 對應投注流水欄位。

為什麼不先做：

- 如果不先選一款 game，很容易變成 27 個 module 平均摘要。
- 正確做法是挑一款 production game，追 spin request、扣款 / 派彩、center sync、log writer 與 app_bi 查詢關聯。

### 4. `bet-target-set-query`

建議狀態：中高價值候選
證據層級：專案存在 / code-backed；Nick 貢獻待確認

已確認：

- `HttpService` 有 `SET_BET_TARGET`、`SET_BET_TARGET_COUPON`、`QUERY_BET_TARGET`。
- flow 與 `SpinBetTargetConst` reason、player 打碼目標方法相關。

為什麼暫排第四：

- 它是重要 money rule，但 Step 3 前要先知道上游是 payment 出款檢查、app_bi 人工操作，還是活動 / coupon。
- 若沒有投注流水扣減邏輯，會只剩設定 / 查詢，不夠完整。

### 5. `dbproxy-cache-db-write-path`

建議狀態：作為其他 flow 的輔助深挖，不先單獨做
證據層級：專案存在 / code-backed；Nick 貢獻待確認

已確認：

- `DbproxyController` 封裝 query / insert / update / execute SQL request。
- `DbController` 處理 Redis cache 與 MySQL session。
- `DbproxyMapper` 是 dynamic SQL 類 mapper。

為什麼不先做：

- 它有平台價值，但單獨做容易變 class summary。
- 更好的方式是在 `third-party-transfer-in-out` 或 `center-http-deposit-withdraw` 裡追到 DB 寫入時，再把 dbproxy 當 transaction / consistency 附屬層深挖。

## 下一步建議

只推薦一件事：

```text
iwin_gameserver third-party-transfer-in-out Step 5
```

原因：

- Step 4 已把 `flow.md` 收斂成可練習的面試 case。
- 下一步應整理履歷 / 自傳邊界，明確判斷哪些只能當分析素材、哪些需要 Nick 本人 evidence。
- 不建議跳新 flow，因為同一條 flow 還沒完成 Step 5。
