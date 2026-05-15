# iwin third_games_api Step 1：候選 Flow 盤點

更新時間：2026-05-15
掃描等級：Level 1 Flow 掃描
狀態：已建立
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 本次結論

`third_games_api` 是 iwin 第三方遊戲 provider 整合層。它比純查詢 / 後台類 flow 更值得做 Senior / Owner 分析，因為 active endpoint 直接接 provider callback，並透過 gameserver `center_http` 讀寫玩家餘額、下注、派彩、有效投注與 rollback / cancel。

目前最值得延伸的候選 flow：

1. `gsc-transfer-bet-settle-rollback`：GSC `/api/seamless/transfer` 投派整合 callback，單一 API 處理 bet / win / rollback 類 action，直接碰錢與有效投注。
2. `oneapi-wallet-bet-result`：OneAPI `/wallet/bet_result`，HMAC 驗簽、transaction id 冪等、PGTRANSFERINOUT、Mongo transaction，適合分析 provider idempotency。
3. `antplay-bet-settle-rollback`：Antplay 舊版 bet / settle / rollback 三段式流程，Mongo 先記 bet、settle 再查 betTime / bet，與 gameserver command 串接明顯。
4. `gsc-seamless-withdraw-deposit-cancel`：GSC 分離式 withdraw / deposit / rollback / cancel callback，能分析 provider 狀態機與 betId / action 去重。
5. `third-platform-redis-config-refresh`：Redis `Game:List:*`、`Game:ThirdPlatform:*` 快取與白名單 / center_http 選路，屬於 runtime config / routing 風險。

不更新履歷。沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認前，所有候選 flow 都只當 `專案存在 / code-backed` 或 `分析素材 / learning-only`。

## 自動重讀紀錄

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/CONVENTIONS.md`
- `projects/source-repo-inventory.md`

已重讀 vault：

- `projects/README.md`
- `projects/iwin/game_api/README.md`
- `projects/iwin/game_api/step1-candidate-flows.md`
- `projects/**/flows/` 與 `senior-owner-playbook/01-senior-owner-flow-inventory.md` 的 third-game / GSC / OneAPI / Antplay 線索。
- 確認 `projects/iwin/third_games_api/` 原本不存在，這輪新建 README 與 Step 1。

已重讀參考文件：

- `/Users/nick/Git/iwin/iwin-workspace/docs/專案分析/third_games_api.md`
- `/Users/nick/Git/iwin/iwin-workspace/docs/專案分析/third_games_api/kb/INDEX.md`
- `/Users/nick/Git/iwin/iwin-workspace/docs/專案分析/third_games_api/kb/catalog/third-game-integration.md`

注意：

- 舊分析文件含環境值、URL、IP、密碼等敏感資訊，本 vault 文件只引用整理後的結構與 code path，不搬運敏感內容。

已重讀 code repo：

- `/Users/nick/Git/iwin/third_games_api`
- 目前分支：`beta`
- 遠端分支清單：已看到 `origin/main`、`origin/beta`、`origin/k3s`、`origin/Test001-Nick`、`origin/checkPerformance`、`origin/beta2`。
- 近期 log：已看到 GSC、OneAPI、Antplay、currency in sign、createdTime for backup、Redis / performance 等 commit 線索。
- path-specific log：已看 `GscController.java`、`AntplayController.java`、`OneApiController.java`、`GetGameRedis.java` 相關 log。

已看主要 code path：

- `src/main/java/com/slots/web/controller/GscController.java`
- `src/main/java/com/slots/web/controller/OneApiController.java`
- `src/main/java/com/slots/web/controller/AntplayController.java`
- `src/main/java/com/slots/web/controller/CashController.java`
- `src/main/java/com/slots/web/controller/RootController.java`
- `src/main/java/com/slots/web/common/filter/WhiteListFilter.java`
- `src/main/java/com/slots/web/common/utils/GetGameRedis.java`
- `src/main/java/com/slots/web/common/constant/PGRedis.java`
- `src/main/java/com/slots/web/common/constant/OneapiRedis.java`
- `src/main/java/com/slots/web/common/constant/AntplayRedis.java`
- `src/main/java/com/slots/web/common/constant/GscRedis.java`

未重讀：

- 未 checkout 每個遠端分支。
- 未逐 commit diff。
- 未掃完整 `iwin_gameserver` command handler。
- 未掃 `game_job` 對 third transaction / log 的對帳或報表 job。
- 未確認 Mongo index / schema。
- 未確認 Nick 個人 MR / ticket / production issue。

## 舊文件狀態判斷

| 文件 | 狀態 | 判斷 |
| --- | --- | --- |
| `projects/iwin/third_games_api/README.md` | 本輪新建 | 專案入口，保守標註履歷邊界 |
| `projects/iwin/third_games_api/step1-candidate-flows.md` | 本輪新建 | Level 1 candidate flow 盤點 |
| workspace 舊分析 `docs/專案分析/third_games_api.md` | 可參考 / 不搬運 | 有專案概覽、endpoint、DB / Redis / Mongo 線索，但含敏感配置，不能直接複製進 vault |
| workspace catalog `third-game-integration.md` | 可參考 / 不搬運 | 有與 gameserver / payment 的關聯定位，可作 Step 1 導航 |
| `app_bi/game-round-record-query` 相關 flow | 可沿用 / 不重整 | 已提到第三方 provider serial id 與 log writer，但主題是後台查詢，不取代本 project Step 1 |

## 掃描等級判斷

本次使用 Level 1。

原因：

- Nick 只指定 `iwin third_games_api`，尚未指定單條 flow。
- `third_games_api` 同時有 GSC、OneAPI、Antplay、多個 callback 形態，直接 Level 2 容易選錯。
- Step 1 目標是找 Top 3-5 candidate flows，不是完整拆單一 provider。
- Level 3 目前不值得，因為還沒選定 flow，也還沒確認 Nick 本人 evidence。

本次做：

- 建立 `third_games_api` 專案入口。
- 找出 Top 5 production flow 候選。
- 標清楚已確認、推測、待確認。
- 標清楚不可更新正式履歷。

本次不做：

- 不建立單條 flow folder。
- 不逐檔逐行掃完整 repo。
- 不逐 commit diff。
- 不更新履歷 / 自傳。
- 不把 `third_games_api` 包裝成 Nick 主開發成果。

## 本次掃描範圍

已看 repo：

- `/Users/nick/Git/iwin/third_games_api`
- `/Users/nick/Git/iwin/iwin-workspace` 的舊分析文件
- `/Users/nick/Git/nick/nick-vault`

source repo 狀態：

- `/Users/nick/Git/iwin/third_games_api`
- 目前分支：`beta`

已看分支：

- 本地：`beta`、`main`
- 遠端清單包含：`origin/main`、`origin/beta`、`origin/k3s`、`origin/Test001-Nick`、`origin/checkPerformance`、`origin/beta2`。

已看 git log：

- 近期主線 log，包含 GSC、OneAPI、Antplay、currency in sign、createdTime for backup、Redis / performance 線索。
- path-specific log：`GscController.java`、`AntplayController.java`、`OneApiController.java`、`GetGameRedis.java`。

已看 code path：

- controllers：`GscController`、`OneApiController`、`AntplayController`、`CashController`、`RootController`。
- runtime config：`GetGameRedis`、`WhiteListFilter`、`PGRedis`、`OneapiRedis`、`AntplayRedis`、`GscRedis`。
- inactive / commented controller 線索：`PGController`、`PartnerController`、`LoginController`、`SmsController`、`ConfigurationController` 等只作遺留線索，不列入 active production flow。

## 專案定位

已確認：

- `third_games_api` 是 Java / Spring Boot API 專案。
- active endpoint 集中在第三方遊戲 provider launch、balance、bet、settle、rollback / cancel / transfer / pushbet。
- `GscController`、`OneApiController`、`AntplayController` 都會使用 Redis cached platform config 與 game id mapping。
- provider callback 成功時會呼叫 gameserver command，例如 `GSC_BET`、`PGTRANSFERINOUT`、`ANTPLAY_BET`、`ANTPLAY_SETTLE`。
- 多條 flow 會寫 Mongo `bi_log` 下的 `third_log_*` / `third_transaction_*` collection 作為 callback / transaction evidence。

推測：

- gameserver 是 wallet mutation 與遊戲狀態真正 commit boundary。
- `third_games_api` 的 Mongo log 是 audit / reconciliation evidence，不等於唯一帳本。
- Redis `Game:ThirdPlatform:*` 的 `center_http` 選路會影響所有 provider callback 的 gameserver target。

待確認：

- gameserver handler 是否以 transaction id / bet id 保證冪等。
- Mongo collection 是否有 unique index，或只是 code 查詢防重。
- provider timeout / retry 是否會造成「gameserver 已改錢、Mongo 未寫」或「Mongo 已寫、provider 回應失敗」。
- `checkPerformance`、`k3s`、`Test001-Nick` 分支是否有與 Nick 有關的 evidence。

## Top Candidate Flows

### 1. `gsc-transfer-bet-settle-rollback`

中文名稱：GSC transfer 投派整合 / rollback
證據層級：專案存在 / code-backed；Nick 貢獻待確認
建議：第一條深挖 flow

為什麼重要：

- `/api/seamless/transfer` 是 GSC 投派整合式 callback，單一 API 裡依 `action` 計算扣款、派彩、rollback 類語意。
- 直接呼叫 gameserver `PGTRANSFERINOUT`，同時傳 `addMoney`、`BetCoin`、`validBetCoin`、`spinCurrency`、`transactionId`、`gameId`、`betId`。
- commit log 顯示近期與 GSC transfer、rollback 判斷、currency / sign 參數、log 欄位有關，適合追演進。

production 風險：

- 同一 `betId` / `transactionId` 在多個 action 下如何去重，需要 gameserver 與 Mongo 一起確認。
- `ROLLBACK` 分支目前看起來不呼叫 gameserver，只用入參金額推算回傳餘額並寫 Mongo；這個分支要確認是否只是配合 provider 驗證，還是存在錢包狀態不同步風險。
- 金額單位由 `exchangeRate` 轉換，和舊固定 `100000` 邏輯並存，需要確認幣別與精度。
- gameserver 成功但 Mongo 寫入失敗、或 Mongo 寫入成功但 provider 沒收到 response，都會影響重送與對帳。

已確認 evidence：

- `GscController#transfer`：`POST /api/seamless/transfer`。
- 驗簽：`operatorCode + requestTime + "transfer" + gscKey` 的 MD5。
- 幣別檢查：`checkCurrency(currency)`。
- 玩家檢查：`getAccountVo(memberAccount)`。
- game id：從 Redis `Game:List:ThirdIdList` 的 `PG` mapping 取得。
- platform routing：從 Redis `Game:ThirdPlatform:PG` 的 `center_http` 依 `centerId` 選路。
- 成功後寫 `third_log_gsc` 與 `third_transaction_gsc`。

推測：

- `PGTRANSFERINOUT` 在 gameserver 端才是真正 wallet / bet log commit。
- transfer flow 比舊 withdraw / deposit 拆分式 API 更貼近目前 GSC 對接主線。

待確認：

- gameserver `PGTRANSFERINOUT` handler 的冪等 key 與 transaction boundary。
- `ROLLBACK` 不呼叫 gameserver 的語意是否合理。
- `exchangeRate` 設定來源與不同 currency 的精度策略。
- Mongo `third_transaction_gsc` 是否有 unique index 或只靠 code 查詢。

履歷邊界：

- 潛力高，但目前不可寫進正式履歷。
- 可先作為 `專案存在 / code-backed` 的第三方遊戲無縫錢包分析素材。

### 2. `oneapi-wallet-bet-result`

中文名稱：OneAPI / PG bet_result 投派 callback
證據層級：專案存在 / code-backed；Nick 貢獻待確認
建議：高價值候選，Step 2 和 GSC transfer 比較後再決定是否深挖

為什麼重要：

- `/wallet/bet_result` 是 provider 投派結果 callback，包含 `traceId`、`transactionId`、`betId`、`externalTransactionId`、`roundId`。
- 使用 HMAC-SHA256 驗簽，比 MD5 類簽章更適合討論 provider contract 與 request body canonicalization。
- 已有 `hasTransactionId(transactionId)` 重送處理，會查 Mongo balance 後回 `SC_OK`。

production 風險：

- `resultType = END` 且 `betId` 不存在會回 transaction not exists；需確認 provider 重送順序與 round lifecycle。
- `transactionId` 已存在時回 Mongo 查出的 balance，若 Mongo 落後 gameserver 可能回舊值。
- `betAmount`、`winAmount`、`jackpotAmount`、`effectiveTurnover` 都乘固定倍率，需確認單位與精度。
- 呼叫 gameserver 成功後再寫 Mongo 的順序與 failure window 要深挖。

已確認 evidence：

- `OneApiController#settle`：`POST /wallet/bet_result`。
- 驗簽：以 `oneApiSecretKey` 對 JSON body fields 做 HMAC-SHA256，比對 request header `X-Signature`。
- 幣別：目前檢查固定 currency。
- 冪等：`hasTransactionId(transactionId)` 命中時回 `SC_OK` 與已記錄 balance。
- gameserver command：`PGTRANSFERINOUT`。
- Mongo collection：`third_transaction_oneapi`。

推測：

- `traceId` 是 provider request correlation，`transactionId` 是更適合做冪等的 key。
- gameserver 端仍需確認是否也用 `transactionId` 去重。

待確認：

- HMAC JSON 組字串方式是否與 provider 完全一致，欄位順序是否固定。
- `END` / `WIN` / `BET_WIN` / `BET_LOSE` / `LOSE` 的完整狀態機。
- Mongo `queryBalance(betId, ...)` 查詢 key 是否足以區分多 transaction。
- gameserver handler 與 `third_transaction_oneapi` 的一致性。

履歷邊界：

- 高價值，但目前不可寫成 Nick 成果。
- 若後續補到 Nick evidence，可能轉成 provider callback idempotency / wallet mutation 案例。

### 3. `antplay-bet-settle-rollback`

中文名稱：Antplay 投注 / 結算 / rollback 三段式流程
證據層級：專案存在 / code-backed；Nick 貢獻待確認
建議：適合做 state transition 與 rollback 對照案例

為什麼重要：

- Antplay 舊版 flow 清楚分成 `/antplay/bet`、`/antplay/settle`、`/antplay/rollback`。
- `bet` 成功後寫 Mongo；`settle` 會先確認 `betId` 存在，再從 Mongo 查 bet / betTime 送 gameserver。
- 這是典型「下注先扣款、結算後派彩、rollback 要回到前狀態」的遊戲交易 flow。

production 風險：

- bet 呼叫 gameserver 成功但 Mongo 寫入失敗時，settle 可能因查不到 `betId` 被拒。
- settle 從 Mongo 查 bet / betTime，若 Mongo 資料不完整會影響 gameserver 指令。
- rollback 是否依原 bet / settle 狀態判斷，需深掃 rollback method 與 gameserver handler。
- 多 provider 都用類似 mapping / center_http，設定錯會導致整條 provider flow 失效。

已確認 evidence：

- `AntplayController#bet`：`POST /antplay/bet`，驗簽、金額乘倍率、查 Redis game mapping、呼叫 `ANTPLAY_BET`、寫 `third_log_antplay` 與 `third_transaction_antplay` step=1。
- `AntplayController#settle`：`POST /antplay/settle`，驗簽、`hasBetId(betId)`、`queryBet`、`queryBetTime`、呼叫 `ANTPLAY_SETTLE`。
- path-specific log 含 `feat(#Antplay): iwin串接Antplay API`、`結算/回滾從MongoDB拿betTime`、`新增gameserver入參參數; 從MongoDB取得bet`。

推測：

- Antplay 三段式 flow 的 state transition 比 GSC transfer 更容易畫清楚，但目前可能不是最新 provider 主線。
- gameserver 端才決定真正 wallet / round log 是否成功。

待確認：

- `rollback` method 的完整 code path。
- `third_transaction_antplay` 的 step 定義與查詢條件。
- gameserver `ANTPLAY_BET`、`ANTPLAY_SETTLE`、rollback handler 的 idempotency。
- Antplay 新版 `bet_settle-new` 是否已取代舊版三段式流程。

履歷邊界：

- 可作為下注 / 結算 / rollback 學習素材。
- 目前不可說 Nick 設計或負責 Antplay 串接。

### 4. `gsc-seamless-withdraw-deposit-cancel`

中文名稱：GSC seamless withdraw / deposit / rollback / cancel
證據層級：專案存在 / code-backed；Nick 貢獻待確認
建議：中高價值，適合和 transfer flow 比較 provider 狀態機

為什麼重要：

- GSC 既有拆分式 endpoint：`/api/seamless/withdraw`、`/deposit`、`/rollback`、`/cancel`。
- 每個 endpoint 有不同 action name、sign string、error code 與 Mongo step。
- 可用來比較 provider API 演進：分離式狀態機 vs `transfer` 整合式 callback。

production 風險：

- `withdraw` / `deposit` / `rollback` / `cancel` 對同一 betId 的順序與重送處理若不一致，會造成 provider 與 gameserver 狀態不同步。
- `hasBetId(betId, action, step)` 類判斷要確認查詢條件是否足夠。
- 拆分式 endpoint 更容易遇到 deposit 先到、withdraw 未落、rollback 重送等順序問題。

已確認 evidence：

- `GscController#bet`：`POST /api/seamless/withdraw`，呼叫 `GSC_BET`，寫 `third_log_gsc` / `third_transaction_gsc`。
- `GscController#settle`：`POST /api/seamless/deposit`，處理派彩。
- `GscController#rollback`：`POST /api/seamless/rollback`。
- `GscController#cancel`：`POST /api/seamless/cancel`。
- commit log 含 `新增pushbet API; 新增/transfer rollback判斷`、`修改回調路徑,串接/transfer API`、`串接GSC_PG遊戲`。

推測：

- 這條 flow 可能是 GSC transfer 前後相容或不同 provider mode 的歷史。
- 深挖時要先確認 production 實際使用哪組 endpoint。

待確認：

- GSC 目前 production 使用 transfer 還是分離式 endpoint。
- rollback / cancel 對 gameserver command 與 Mongo step 的差異。
- provider contract 中 action / wager_status 的合法狀態。

履歷邊界：

- 適合面試討論 provider 狀態機，但目前不進正式履歷。

### 5. `third-platform-redis-config-refresh`

中文名稱：第三方平台 Redis 設定快取 / center_http 選路
證據層級：專案存在 / code-backed；Nick 貢獻待確認
建議：作為支援性 flow，若 Step 2 發現交易 flow 太分散，可先深挖此 routing / config 風險

為什麼重要：

- 所有 provider flow 都依賴 Redis `Game:List:ThirdIdList`、`Game:List:ThirdIdMapping`、`Game:ThirdPlatform:PG / Antplay / Gsc`。
- `center_http` 依玩家 `centerId` 選 gameserver target，是交易請求能否打到正確 server 的關鍵。
- `WhiteListFilter` 與 `GetGameRedis` 分別影響 provider access control 與 runtime routing。

production 風險：

- game id mapping 快取錯或缺資料，provider callback 會找不到內部 game id。
- `center_http` 錯誤會把玩家交易打到錯誤 gameserver 或直接失敗。
- `GetGameRedis` 由 schedule 定期 refresh，仍需確認 refresh 間隔、失敗時是否保留舊值、告警與灰度策略。
- 白名單 / platform status 和 cached routing 的一致性要確認。

已確認 evidence：

- `GetGameRedis#init` 讀 `Game:List:ThirdIdList`、`Game:List:ThirdIdMapping`、`Game:ThirdPlatform:PG`、`Game:ThirdPlatform:Antplay`、`Game:ThirdPlatform:Gsc`。
- `ScheduleServer` 每隔固定秒數呼叫 `GetGameRedis.getInstance().init()`。
- `WhiteListFilter` 以 `Game:ThirdPlatform:{Platform}` 讀白名單與 status。
- `GscController`、`OneApiController`、`AntplayController` 都讀 cached static object。

推測：

- 這是跨 provider 的共用 routing / config surface，對 incident blast radius 很大。
- 若 Nick 參與過後台 Redis config 或 K3s runtime，可能能和 `app_bi admin-config-redis-sync` 串成更完整故事，但目前 evidence 不足。

待確認：

- `ScheduleServer` 是否在 production 啟用。
- refresh 失敗時是否保留舊 config 或清成 null。
- Redis config 修改來源與審批 / audit 流程。
- `Game:ThirdPlatform:Gsc` 與 `PG` 的實際使用差異。

履歷邊界：

- 可作為平台 routing / config consistency 分析素材。
- 目前不可說 Nick 負責第三方平台設定中心。

## 不建議優先深挖的項目

| 項目 | 原因 |
| --- | --- |
| `CashController` / `CashServiceImpl` | active endpoint 存在，但更像早期內部錢包 / PG DTO 沿用；需要先確認 production 使用情境 |
| `PGController` 直連 PG | 整檔 annotation 被註解，不是目前 active endpoint |
| `LoginController` / `SmsController` / `PartnerController` | 多數 annotation 被註解，屬於遺留搬遷線索，不適合作為本 project 第一條 production flow |
| `gameList` / `login launch` | 有 provider launch 價值，但 money correctness 低於 bet / settle / rollback callback |

## Step 2 建議比較維度

下一步若做 Step 2，建議比較：

- money correctness：是否直接改玩家餘額 / 有效投注。
- provider idempotency：重送、timeout、duplicate transaction 的處理是否清楚。
- gameserver evidence：是否容易追到下游 command handler。
- commit history：是否有近期演進或 bug / performance context。
- 面試可轉換性：是否能講清楚 state transition、failure window、owner decision。
- 履歷邊界：是否有機會補到 Nick 本人 evidence；沒有 evidence 前不寫履歷。

## 下一步建議

只推薦一件事：

```text
iwin third_games_api Step 2
```

原因：

- Top 5 flow 已初步定位，但 GSC transfer、OneAPI bet_result、Antplay bet / settle 的價值很接近。
- Step 2 可以先比較哪條最值得 Level 2 深掃，避免直接跳進大型 controller 逐行整理。
- Step 2 預計建立 `step2-flow-comparison.md`；不更新履歷 / 自傳；完成後依規則自動 commit。
