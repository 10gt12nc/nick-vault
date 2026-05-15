# iwin_gameserver Step 1：候選 Flow 盤點

更新時間：2026-05-15
掃描等級：Level 1 Flow 掃描
狀態：初版
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 本次結論

`iwin_gameserver` 是 iwin 裡目前比 `app_bi` 更值得深挖的核心 runtime repo。它的高價值 flow 集中在：

1. 第三方遊戲投派整合 / 投注派彩退款。
2. payment / game_api 透過 center_http 對玩家上分 / 下分。
3. 打碼目標設定與查詢。
4. 遊戲 spin / 結算 / log_reel 投注流水。
5. dbproxy 的 MySQL / Redis 查寫代理。

本輪不更新履歷。沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認前，本文件所有候選 flow 都只作 `專案存在 / code-backed` 或 `分析素材 / learning-only`。

## 自動重讀紀錄

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/README.md`
- `projects/source-repo-inventory.md`
- `projects/CONVENTIONS.md`

已重讀 vault：

- `projects/iwin/app_bi/README.md`
- `projects/iwin/app_bi/step1-candidate-flows.md`
- `projects/iwin/app_bi/step2-flow-comparison.md`
- 既有 `projects/iwin/app_bi/flows/**` 清單，確認沒有 `iwin_gameserver` flow folder。

已重讀 code / workspace：

- `/Users/nick/Git/iwin/iwin_gameserver`
- `/Users/nick/Git/iwin/iwin-workspace/docs/專案分析/iwin_gameserver.md`
- `/Users/nick/Git/iwin/iwin-workspace/docs/跨專案關聯/game_api-gameserver.md`
- `/Users/nick/Git/iwin/iwin-workspace/docs/跨專案關聯/payment-gameserver.md`

未重讀：

- 未 checkout 每個遠端分支。
- 未逐 commit diff。
- 未深掃 `payment`、`game_api`、`third_games_api`、`app_bi` 的對應上游 code。
- 未確認 Nick 個人 MR / ticket / production issue。

## 舊文件狀態判斷

| 文件 | 狀態 | 判斷 |
| --- | --- | --- |
| `projects/iwin/iwin_gameserver/README.md` | 本次新增 | 新 project 入口 |
| `projects/iwin/iwin_gameserver/architecture-map.md` | 本次新增 | 最小定位圖，不是單條 flow 報告 |
| `projects/iwin/iwin_gameserver/step1-candidate-flows.md` | 本次新增 | Step 1 主文件 |
| workspace `docs/專案分析/iwin_gameserver.md` | 可參考 / 不搬運 | 有 module 地圖，但含過舊路徑與不適合進 vault 的環境資訊，本次只取結構理解 |
| workspace 跨專案關聯文件 | 可參考 / 需 flow 內再驗證 | 提供 `payment`、`game_api` 與 center_http 關係，但不當成單條 flow evidence 終點 |

## 掃描等級判斷

本次使用 Level 1。

原因：

- Nick 只指定 `iwin iwin_gameserver`，目前 vault 尚無此 project，第一步應先找 candidate flows。
- 尚未選定單條 flow，不適合直接 Level 2 深挖。
- 若要寫成履歷或強 evidence，才需要 Level 3 逐 commit diff 與 Nick 本人 evidence。

本次做：

- 建立 project 入口。
- 建立最小 architecture map。
- 找 Top 5 Senior / Owner candidate flows。
- 標清楚已確認、推測、待確認與履歷邊界。

本次不做：

- 不建立單條 flow folder。
- 不更新正式履歷 / 自傳。
- 不把 code-backed 功能寫成 Nick 真實做過。

## 本次掃描範圍

已看 repo：

- `/Users/nick/Git/iwin/iwin_gameserver`
- `/Users/nick/Git/nick/nick-vault`

source repo 狀態：

- `/Users/nick/Git/iwin/iwin_gameserver` 目前分支：`main`
- 近期主線 log 包含 `兑换码功能`、`antplay 支援投派整合`、`GSC`、`PG 退款`、`PG bet_result 投派整合`、`iwin gameserver 對接antplay遊戲` 等線索。

已看分支：

- 本地：`main`
- 遠端清單包含：`origin/main`、`origin/AntPlay-transferInOut`、`origin/beta`、`origin/nick-LocalDevTest`、`origin/Nick-GSC_PG`、`origin/Nick-review`、`origin/rate-script`、`origin/feature/RD-188`、`origin/feature/RD-162`。

已看 git log：

- 近期主線 log。
- path-specific log：`HttpService.java`、第三方 transfer / settle job、`GamePlayer.java`、`DbController.java`、`AbstractGameBehaviorJob.java`。
- commit stat：`4843791`、`571b6fa`、`6da60bd` 等第三方遊戲整合相關 commit。

已看 code path：

- `README.md`
- `pom.xml`
- `slots-center/src/main/java/com/slots/center/service/HttpService.java`
- `slots-center/src/main/java/com/slots/sql/job/HttpAntplayTransferInOut.java`
- `slots-center/src/main/java/com/slots/sql/job/HttpPGTransferInOut.java`
- `slots-center/src/main/java/com/slots/sql/job/HttpGSCTransferInOut.java`
- `slots-center/src/main/java/com/slots/center/job/http/AntplayTransferInOutJob.java`
- `slots-center/src/main/java/com/slots/center/job/http/PGTransferInOutJob.java`
- `slots-center/src/main/java/com/slots/center/job/http/GSCTransferInOutJob.java`
- `slots-games/slots-game-common/src/main/java/com/slots/game/common/data/GamePlayer.java`
- `slots-games/slots-game-common/src/main/java/com/slots/game/common/data/AddCenterCoin*.java`
- `slots-common/src/main/java/com/slots/common/controller/DbproxyController.java`
- `slots-dbproxy/src/main/java/com/slots/dbproxy/controller/DbController.java`
- `slots-center/src/main/java/com/slots/sql/data/SqlPlayerData.java`
- `slots-center/src/main/java/com/slots/sql/data/SqlLogReelData.java`

## 專案定位

已確認：

- `iwin_gameserver` 是 Java / Maven multi-module 遊戲伺服器。
- 不是 Spring Boot CRUD 專案，而是自建 Netty / Protobuf / Zookeeper / Redis / MyBatis runtime。
- `slots-center` 是多個高價值 flow 的入口：center_http、玩家 cache、錢包、打碼、第三方遊戲投注 / 派彩 / 退款。
- `slots-games` 與 `slots-game-common` 承載遊戲 runtime、spin、結算、玩家遊戲狀態與中心錢包同步資料。
- `slots-dbproxy` 透過共用 DAO annotation / dynamic SQL 對 MySQL 與 Redis 做查寫。

推測：

- 部分第三方遊戲 flow 的上游 adapter 可能在 `third_games_api` 或其他 gateway repo。
- 真正 production 對帳需要把 gameserver log、第三方 betId / transactionId、payment / game_api 訂單資料串起來。

待確認：

- Nick 是否實際參與過 Antplay / PG / GSC / 投派整合。
- 每條 flow 的 idempotency key 是否足夠。
- fail after coin update / before log write 的補償或對帳。

## Top Candidate Flows

### 1. `third-party-transfer-in-out`

中文名稱：第三方遊戲投派整合 / 投注派彩退款
證據層級：專案存在 / code-backed；Nick 貢獻待確認
建議：Step 2 比較後，優先進 Step 3

為什麼重要：

- 同時牽涉扣款、加款、退款、投注額、有效投注、實際投注、玩家餘額與遊戲 log。
- failure window 很多：重複 callback、扣款成功但 log 失敗、派彩延遲、退款與派彩順序、玩家餘額不足、玩家不存在 / 被封禁。
- path-specific history 顯示 Antplay、PG、GSC 多次演進，適合做 owner decision 與 bug history 題。

已確認 evidence：

- `HttpService` 有 `ANTPLAY_BET`、`ANTPLAY_SETTLE`、`ANTPLAY_REFUND`、`ANTPLAYTRANSFERINOUT`、`PGTRANSFERINOUT`、`GSC_BET`、`GSC_SETTLE`、`GSC_REFUND`、`GSC_OTHER`。
- `HttpAntplayTransferInOut`、`HttpPGTransferInOut`、`HttpGSCTransferInOut` 先查玩家資料，再把 job 丟進 center game pool。
- `AntplayTransferInOutJob`、`PGTransferInOutJob`、`GSCTransferInOutJob` 會處理 `addMoney`、`betCoin`、`validBetCoin`、`spinCurrency`、`transactionId`、`betId`。
- commit stat 顯示 `4843791` 新增 Antplay 投派整合大量 code，`571b6fa` 新增 PG 退款，主線有 GSC 相關 commit。

推測：

- `transactionId` / `betId` 可能是 idempotency 與對帳關鍵，但目前未完整確認唯一性與防重。
- 第三方 adapter 可能在其他 repo；gameserver 可能只接收轉換後 center_http command。

待確認：

- 上游 repo 與 API contract。
- `modifyAndGetCoin*` 是否有防重、version、atomicity。
- log_reel / log_coins / player data 寫入順序。
- callback timeout 後重送是否安全。

履歷邊界：

- 目前不可寫 Nick 主導第三方遊戲整合。
- 可作高價值面試分析素材，等 Step 3 / Step 4 / Nick 本人 evidence 後再討論履歷。

### 2. `center-http-deposit-withdraw`

中文名稱：center_http 玩家上分 / 下分
證據層級：專案存在 / code-backed；Nick 貢獻待確認

為什麼重要：

- 直接影響玩家餘額，是 money correctness 主線。
- 連到 `payment` 與 `game_api`，可切出不同入口：玩家自助充值 / 提現、合作夥伴上分 / 下分。
- 面試價值在於跨 repo boundary、timeout、重試、交易狀態與遊戲錢包一致性。

已確認 evidence：

- `HttpService` 解析 `DEPOSIT` 與 `WITHDRAW` command。
- `doCommand()` 會依 `accountId` 找 player cache，沒有 cache 時查 `SqlPlayerData`。
- workspace 跨專案關聯文件指出 `payment` 與 `game_api` 會呼叫 center_http 上分 / 下分。

推測：

- payment / game_api 端有各自訂單與請求 idempotency，gameserver 端是否再防重待確認。

待確認：

- `onDeposit()` / `onWithdraw()` 完整資料流。
- payment / game_api 上游 state machine。
- 失敗後是否人工補單、重送或對帳。

履歷邊界：

- 不可只憑 gameserver code 寫 Nick 負責金流上下分。
- 若後續選它，必須同步掃 `payment` / `game_api`。

### 3. `bet-target-set-query`

中文名稱：打碼目標設定與查詢
證據層級：專案存在 / code-backed；Nick 貢獻待確認

為什麼重要：

- 出款前常需要判斷有效投注 / 打碼目標是否達成。
- 牽涉入款、出款、活動、人工調整與玩家累積投注資料。
- 適合練 source of truth、projection、查詢一致性與跨系統規則邊界。

已確認 evidence：

- `HttpService` 有 `SET_BET_TARGET`、`SET_BET_TARGET_COUPON`、`QUERY_BET_TARGET`。
- `setPlayerBetTarget()` 會呼叫 player 的打碼目標累加方法。
- `queryPlayerBetTarget()` 會讀 player 的未完成打碼目標。
- `SpinBetTargetConst` 定義多種 reason。

推測：

- `payment` 出款檢查與 `app_bi` 人工操作可能共用這條能力。

待確認：

- 打碼資料落點與資料結構。
- 投注流水如何累積扣減目標。
- 多 center / cache 不一致時的行為。

履歷邊界：

- 目前只可作 analysis / learning。

### 4. `game-spin-settlement-log-reel`

中文名稱：遊戲 spin / 結算 / 投注流水寫入
證據層級：專案存在 / code-backed；Nick 貢獻待確認

為什麼重要：

- 遊戲核心 runtime：下注、轉輪、派彩、玩家餘額、投注流水。
- 錯誤會影響玩家錢、RTP、報表、對帳與客服排查。
- 可連到 `app_bi game-round-record-query` 的 log writer 來源。

已確認 evidence：

- `slots-games/slots-game-common` 有 `GamePlayer`、`AddCenterCoin`、`SlotsGame*`、`PointToWin*`、`ruler`、`def` 等共同遊戲 runtime。
- `SqlLogReelData` 對應 `log_reel`，含 `spin_currency`、`spin_bet`、`spin_rate` 等欄位。
- `slots-game-log` 有 RTP / reel / refund 類 log 線索。

推測：

- 不同遊戲 module 可能各自覆寫部分 spin / settle 行為。

待確認：

- 選哪一款遊戲作代表 flow。
- spin request 到 center coin sync 到 log writer 的完整順序。
- failure after game result / before wallet update 的處理。

履歷邊界：

- 不可泛稱 Nick 實作所有遊戲結算。
- 若要深挖，需先選一款具代表性的 production game。

### 5. `dbproxy-cache-db-write-path`

中文名稱：DB proxy 的 Redis / MySQL 查寫路徑
證據層級：專案存在 / code-backed；Nick 貢獻待確認

為什麼重要：

- 這是多個 runtime flow 的資料落點共用能力。
- 牽涉 cache-aside、dynamic SQL、RPC callback、partial failure、Redis / MySQL consistency。
- 對 Platform Backend 面試很有價值，但要避免變成 class summary。

已確認 evidence：

- `DbproxyController` 透過 `QueryDataRq`、`InsertDataRq`、`UpdateDataNtc`、`ExecuteSqlRq` 對 `slots-dbproxy` 發訊息。
- `DbController` 讀寫 Redis cache 與 MySQL session。
- `DbproxyMapper` 使用 dynamic SQL 類方法查寫不同 table。
- `DbproxyWorld` 有 save task / stop 時 flush 類線索。

推測：

- 部分更新可能先改 Redis 或先改 MySQL，需看各 method 完整邏輯。

待確認：

- insert / update / query 的 cache 與 DB 順序。
- 寫失敗後是否 retry / compensate。
- dynamic SQL 的欄位白名單 / 注入風險邊界。

履歷邊界：

- 目前只能作 platform learning。
- 若 Nick 沒有實際開發 evidence，不寫成平台資料層設計成果。

## 建議第一條深挖 flow

建議先做：

```text
third-party-transfer-in-out
```

理由：

- 它比純 dbproxy 更貼近業務與 money correctness。
- 它比一般 deposit / withdraw 更能展開 idempotency、refund、settle、valid bet、log 與對帳。
- 主線 git history 有多個相關 commit，後續若 Nick 要 Level 3 可追 bug / feature evolution。
- 它有明確 code path，可進 Step 2 比較後再 Step 3 建單條 flow folder。

## 下一步要讀的 code path

下一步 Step 2 / Step 3 應優先讀：

- `slots-center/src/main/java/com/slots/center/service/HttpService.java`
- `slots-center/src/main/java/com/slots/sql/job/HttpAntplayTransferInOut.java`
- `slots-center/src/main/java/com/slots/sql/job/HttpPGTransferInOut.java`
- `slots-center/src/main/java/com/slots/sql/job/HttpGSCTransferInOut.java`
- `slots-center/src/main/java/com/slots/center/job/http/AntplayTransferInOutJob.java`
- `slots-center/src/main/java/com/slots/center/job/http/PGTransferInOutJob.java`
- `slots-center/src/main/java/com/slots/center/job/http/GSCTransferInOutJob.java`
- `slots-games/slots-game-common/src/main/java/com/slots/game/common/data/AddCenterCoinAP.java`
- `slots-games/slots-game-common/src/main/java/com/slots/game/common/data/AddCenterCoinPG.java`
- `slots-games/slots-game-common/src/main/java/com/slots/game/common/data/AddCenterCoinGSC.java`
- `slots-games/slots-game-common/src/main/java/com/slots/game/common/data/GamePlayer.java`
- `slots-game-log/src/main/java/**` 的 PG / GSC / Antplay log writer。
- 相關 upstream repo：`third_games_api`、`game_api` 或其他 third-party adapter，待 Step 2 定位。

## 下一步建議

只推薦一件事：

```text
iwin_gameserver Step 2
```

原因：

- Step 1 只負責找候選 flow，不能跳過 Step 2 直接做單條 flow。
- `iwin_gameserver` 是多 module repo，Step 2 必須先把候選 flow 橫跨的 root module、game module、service instance 與上游 repo 邊界比清楚。
- 不更新正式履歷；此階段只會產出候選 flow 技術 / 風險比較與後續 code path。
