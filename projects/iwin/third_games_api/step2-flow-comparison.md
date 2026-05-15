# iwin third_games_api Step 2：候選 Flow 比較

更新時間：2026-05-15
掃描等級：Level 1.5 Flow 比較
狀態：已建立
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 本次結論

建議第一條進 Step 3：

```text
gsc-transfer-bet-settle-rollback
```

原因：

- 它是 GSC 投派整合式 callback，單一 endpoint 處理投注、派彩、rollback 類 action，比單純 launch / gameList 更有 Senior / Owner 價值。
- 它直接呼叫 gameserver `PGTRANSFERINOUT`，同時帶 `addMoney`、`BetCoin`、`validBetCoin`、`spinCurrency`、`transactionId`、`gameId`、`betId`，可以分析 money correctness、transaction boundary 與 wagering / spin currency。
- 下游 `iwin_gameserver` 已確認有 `PGTRANSFERINOUT` handler，並且有 GSC / Antplay bet-settle log pipeline 線索，Step 3 有足夠 code-backed evidence 可追。
- 近期 commit 有 GSC transfer、rollback 判斷、currency in sign、createdTime backup、log 強化等線索，適合做 path-specific history。
- 目前仍沒有 Nick 本人 MR / ticket / production issue / 本人確認，所以只作 `專案存在 / code-backed` 與 `分析素材 / learning-only`，不更新正式履歷。

本次不建議直接做 Level 3。原因是 Step 2 的目標是排序 candidate flows，還沒開始單條 flow 學習包；Level 3 應等 Step 3 確認主路徑後，再針對 gameserver handler、Mongo index、provider retry / timeout 逐 commit 深追。

## 自動重讀紀錄

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`

已重讀 project 文件：

- `projects/iwin/third_games_api/README.md`
- `projects/iwin/third_games_api/step1-candidate-flows.md`

已重讀 / 重新確認 source repo：

- `/Users/nick/Git/iwin/third_games_api`
- 已執行 `git fetch --all --prune`
- local branch：`beta`
- local HEAD：`4915ea5`
- `origin/beta`：`4915ea5`
- ahead / behind：`0 / 0`
- `origin/main`：`7f6bc3a`
- `origin/k3s`：`c3420c6`
- fetch 後看到 `origin/k3s` 有更新，但本次沒有 checkout / pull / merge / rebase。

已重讀 / 重新確認 downstream repo：

- `/Users/nick/Git/iwin/iwin_gameserver`
- 已執行 `git fetch --all --prune`
- local branch：`main`
- local HEAD：`30a9fcb`
- `origin/main`：`30a9fcb`
- ahead / behind：`0 / 0`
- 本次只讀 handler 線索，沒有改公司 repo。

已看 code path：

- `third_games_api`：
  - `GscController`
  - `OneApiController`
  - `AntplayController`
  - `GetGameRedis`
  - `WhiteListFilter`
  - `PGRedis` / `OneapiRedis` / `AntplayRedis` / `GscRedis`
- `iwin_gameserver`：
  - `slots-center/src/main/java/com/slots/center/service/HttpService.java`
  - `slots-games/slots-game-common/src/main/java/com/slots/game/common/data/GamePlayer.java`
  - `slots-common/src/main/java/com/slots/common/GameEnum.java`
  - `slots-game-log/src/main/java/com/slots/game/LogJobCrons.java`
  - `slots-game-log/src/main/java/com/slots/game/job/player/LogReelGSCBetJob.java`
  - `slots-game-log/src/main/java/com/slots/game/job/player/LogReelGSCSettleJob.java`
  - `slots-game-log/src/main/java/com/slots/game/job/player/LogReelAntplayBetJob.java`
  - `slots-game-log/src/main/java/com/slots/game/job/player/LogReelAntplaySettleJob.java`

未掃 / 待確認：

- 未 checkout `origin/k3s`、`origin/checkPerformance`、`origin/Test001-Nick`、`origin/beta2`。
- 未逐 commit diff。
- 未查 Mongo collection index / schema。
- 未掃 `game_job` 是否有 third transaction 對帳或報表補償 job。
- 未確認 production 實際使用 GSC `transfer` 或分離式 `withdraw / deposit / rollback / cancel` 哪一組。
- 未確認 Nick 本人 MR / ticket / production issue。

## 既有文件狀態

| 文件 | 狀態 | 判斷 |
| --- | --- | --- |
| `README.md` | 可沿用 / 本輪同步 | Step 1 後已乾淨；本輪更新 Step 2 狀態與下一步 |
| `step1-candidate-flows.md` | 可沿用 | 有掃描等級、已掃 / 未掃、候選 flow 與履歷邊界 |
| `step2-flow-comparison.md` | 本輪新建 | 比較 Top candidate，選出 Step 3 主線 |
| `flows/` | 尚未建立 | 符合規則；Step 2 前不建 flow folder |

## 專案 / module 邊界

### 已確認 runtime surface

| Layer | 角色 | 本次判斷 |
| --- | --- | --- |
| `third_games_api` controller | Provider-facing adapter | 接 provider launch / balance / bet / settle / rollback / transfer callback |
| Redis `Game:List:*` | Game id mapping | provider game code 轉 iwin game id |
| Redis `Game:ThirdPlatform:*` | Platform routing / whitelist / status | 依 platform 與 centerId 找 gameserver `center_http` |
| `iwin_gameserver` `HttpService` | Wallet / bet command receiver | 接 `PGTRANSFERINOUT`、`GSC_BET`、`GSC_SETTLE`、`GSC_REFUND`、`ANTPLAY_*` |
| `iwin_gameserver` `GamePlayer` | Wallet / log side effect | 產生 GSC / Antplay bet-settle log event |
| `slots-game-log` | Log projection writer | 將 GSC / Antplay bet / settle 類事件落到 log table |
| Mongo `third_log_*` / `third_transaction_*` | Callback / transaction evidence | 由 `third_games_api` 寫入，疑似 audit / replay / duplicate check evidence |

### 邊界判斷

已確認：

- `third_games_api` 不是 wallet 最終 source of truth。
- gameserver handler 是 Step 3 必須補看的下游 commit boundary。
- `third_games_api` 的 Mongo third log / transaction 更像 provider callback evidence 與 duplicate check projection，不應直接當帳本。

推測：

- `game_job` 或 BI 可能讀 GSC / Antplay log 做 daily report 或 reconciliation，但本次未掃。
- provider retry / timeout 的最終安全性，取決於 gameserver handler 是否也做 idempotency，而不是只看 `third_games_api` code 查 Mongo。

待確認：

- `PGTRANSFERINOUT` 與 `GSC_*` handler 是否用 `transactionId` / `betId` 去重。
- gameserver 成功但 `third_games_api` Mongo insert 失敗時，provider 重送會怎麼收斂。
- `transfer` flow 中 `ROLLBACK` 分支是否真的不呼叫 gameserver，或需對照 production provider contract。

## Flow 比較總表

| Ranking | Flow | Money correctness | Idempotency / retry | Downstream evidence | Commit history | 面試價值 | 履歷可用性 | 結論 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | `gsc-transfer-bet-settle-rollback` | 高 | 高 | 高 | 高 | 高 | 待確認 | 第一條 Step 3 |
| 2 | `oneapi-wallet-bet-result` | 高 | 高 | 中高 | 中 | 高 | 待確認 | 第二候選 |
| 3 | `antplay-bet-settle-rollback` | 高 | 中高 | 高 | 高 | 中高 | 待確認 | 適合後續做對照 |
| 4 | `gsc-seamless-withdraw-deposit-cancel` | 高 | 中 | 高 | 中高 | 中高 | 待確認 | 需先確認 production 使用哪組 endpoint |
| 5 | `third-platform-redis-config-refresh` | 中 | 中 | 中 | 中 | 中 | 待確認 | 支援性 flow，不優先於交易主線 |

## Flow 1：`gsc-transfer-bet-settle-rollback`

中文名稱：GSC transfer 投派整合 / rollback
建議狀態：第一條進 Step 3
證據層級：專案存在 / code-backed；Nick 貢獻待確認

### 為什麼排第一

- `POST /api/seamless/transfer` 是整合式 provider callback，一條 flow 內會處理 bet / settle / rollback 類 action。
- 直接碰玩家餘額、投注額、有效投注、spin currency 與 provider transaction。
- `third_games_api` 端能看到 provider 驗簽、currency、player lookup、game mapping、center_http 選路、gameserver request、Mongo log。
- `iwin_gameserver` 端能看到 `PGTRANSFERINOUT` command receiver，Step 3 有機會把 API adapter 和 wallet mutation / log projection 串起來。

### 已確認 code-backed evidence

- `GscController#transfer`：`POST /api/seamless/transfer`。
- 驗簽：`operatorCode + requestTime + "transfer" + gscKey` 的 MD5。
- 金額：`amount`、`bet_amount`、`valid_bet_amount`、`prize_amount` 轉成內部倍率。
- gameserver command：`PGTRANSFERINOUT`。
- Redis：`Game:List:ThirdIdList` 的 `PG` mapping、`Game:ThirdPlatform:PG` 的 `center_http`。
- Mongo：`third_log_gsc`、`third_transaction_gsc`。
- downstream：`HttpService` 有 `PGTRANSFERINOUT` handler。

### Step 3 必查問題

- `transactionId` / `betId` 哪個才是真正冪等 key。
- `ROLLBACK` action 分支的語意：是否只回應 provider 驗證，還是存在未打 gameserver 的狀態缺口。
- `exchangeRate` 與舊 `100000` 倍率並存時，是否可能造成精度或幣別錯誤。
- gameserver 成功、Mongo 失敗、provider timeout 三者任一失敗時，重送與對帳怎麼收斂。

### 履歷邊界

- 目前不可寫成 Nick 成果。
- Step 3 可先產出 Senior 面試素材：provider callback idempotency、seamless wallet transaction boundary、rollback semantics。

## Flow 2：`oneapi-wallet-bet-result`

中文名稱：OneAPI / PG bet_result 投派 callback
建議狀態：第二候選
證據層級：專案存在 / code-backed；Nick 貢獻待確認

### 為什麼排第二

- OneAPI flow 的 HMAC-SHA256 驗簽、`traceId` / `transactionId` / `betId` / `roundId` 欄位較完整，適合講 provider contract。
- `hasTransactionId(transactionId)` 命中時會回已記錄 balance，idempotency 線索比其他 flow 直接。
- 也呼叫 `PGTRANSFERINOUT`，可和 GSC transfer 對照。

### 為什麼不是第一

- 目前 Step 1 / Step 2 看到的近期 commit 與 downstream GSC / Antplay log evidence，比 OneAPI 更集中。
- OneAPI 的 `resultType` 狀態機需要額外確認 provider contract；單靠本輪掃描還不如 GSC transfer 立即可深挖。

### Step 3 候補查點

- HMAC JSON canonicalization 是否穩定。
- `resultType = END` 與 `hasBetId` 的狀態語意。
- `transactionId` 重送回 Mongo balance 是否可能回舊值。
- `queryBalance(betId, third_transaction_oneapi)` 是否足以區分多 transaction。

## Flow 3：`antplay-bet-settle-rollback`

中文名稱：Antplay 投注 / 結算 / rollback 三段式流程
建議狀態：第三候選
證據層級：專案存在 / code-backed；Nick 貢獻待確認

### 為什麼值得

- 舊版三段式 API 很適合畫 state transition：bet -> settle -> rollback。
- `settle` / `rollback` 會回查 Mongo 的 `bet` / `betTime`，failure window 很明顯。
- 下游 gameserver 有 `ANTPLAY_BET`、`ANTPLAY_SETTLE`、`ANTPLAY_REFUND` handler 與 log projection evidence。

### 為什麼不是第一

- `beta` branch 有 `Antplay停止对接商户API，改对接slot-game-api` 相關 commit，需先確認目前 production 是舊三段式、新 `bet_settle-new`，還是 slot-game-api 對接。
- 如果 production 主線已改，直接深挖舊 flow 的履歷 / 面試價值會低於 GSC transfer。

### Step 3 候補查點

- 舊 `/antplay/bet`、`/settle`、`/rollback` 是否仍 active / production 使用。
- 新版 `/antplay/bet_settle-new` 與舊版三段式差異。
- Mongo `third_transaction_antplay` step 1 / 2 / 3 是否有 unique index。
- gameserver `antplaySettled` 對 bet / settle / refund 的同一 betId 重入保護。

## Flow 4：`gsc-seamless-withdraw-deposit-cancel`

中文名稱：GSC seamless withdraw / deposit / rollback / cancel
建議狀態：第四候選
證據層級：專案存在 / code-backed；Nick 貢獻待確認

### 為什麼值得

- GSC 分離式 callback 可以分析 provider 狀態機：withdraw 扣款、deposit 派彩、rollback / cancel 補償。
- 下游 gameserver 有 `GSC_BET`、`GSC_SETTLE`、`GSC_REFUND` handler。
- log pipeline 有 GSC bet / settle projection。

### 為什麼不是第一

- 它和 `gsc-transfer-bet-settle-rollback` 可能是同 provider 的不同整合模式或歷史版本。
- 若 production 現在走 `/api/seamless/transfer`，先深挖分離式 endpoint 會偏掉主線。
- Step 3 應先釐清 `transfer` 是否取代或並存於分離式 endpoint。

### Step 3 候補查點

- production route / provider config 目前指向 transfer 還是 split endpoints。
- `hasBetId(betId, action, step)` 是否足以處理 duplicate / out-of-order。
- rollback / cancel 是否都對應 `GSC_REFUND`，或語意不同。

## Flow 5：`third-platform-redis-config-refresh`

中文名稱：第三方平台 Redis 設定快取 / center_http 選路
建議狀態：支援性候選，不優先於交易 flow
證據層級：專案存在 / code-backed；Nick 貢獻待確認

### 為什麼值得

- 所有 provider flow 都依賴 `Game:List:ThirdIdList`、`Game:List:ThirdIdMapping`、`Game:ThirdPlatform:*`。
- `center_http` 選錯會讓交易打到錯誤 gameserver 或直接失敗。
- `WhiteListFilter` 與 `GetGameRedis` 可討論 runtime config、白名單、平台狀態與 refresh。

### 為什麼不排第一

- 它本身不是交易狀態機，money correctness 是間接風險。
- Step 3 若先做 Redis config，會比較像平台 routing / config consistency case，不如交易 callback 對 Senior Backend 面試直接。
- `app_bi admin-config-redis-sync` 已有後台設定同步 Redis 的分析素材；本 flow 若要做，應先和那條 flow 去重。

### 候補查點

- `ScheduleServer` refresh 失敗時是否保留舊值。
- 白名單 / status 是每 request 即時讀，還是也受 static cache 影響。
- `Game:ThirdPlatform:Gsc` 與 `PG` key 在 GSC / OneAPI flow 的實際差異。

## 不建議本輪進 Step 3 的項目

| 項目 | 判斷 |
| --- | --- |
| `CashController` / `CashServiceImpl` | active 但像早期內部錢包 / PG DTO 沿用，production 使用情境不清 |
| `PGController` 直連 PG | annotation 被註解，不是 active endpoint |
| `LoginController` / `SmsController` / `PartnerController` | 多數 annotation 被註解，屬遺留搬遷線索 |
| `gameList` / `login launch` | provider integration 有價值，但 money / rollback 風險低於 callback |
| `gsc-pushbet` | 目前看起來偏注單推送 / 驗證，money mutation 與 failure window 不如 transfer |

## Step 3 建議主線

下一步建立：

```text
projects/iwin/third_games_api/flows/gsc-transfer-bet-settle-rollback/
```

預計輸出：

- `flow.md`
- `career-interview.md`
- `materials/evidence.md`
- `materials/decision-notes.md`
- `materials/interview.md`
- `materials/claim-boundary.md`

Step 3 必須至少補：

- `GscController#transfer` 的初階 / 中階可讀正常流程。
- provider request 欄位、action、money conversion 與 response。
- Redis game mapping / platform routing。
- gameserver `PGTRANSFERINOUT` handler 與 wallet mutation boundary。
- Mongo `third_log_gsc` / `third_transaction_gsc` 的角色與冪等限制。
- rollback 分支與 failure window。
- 不可誇大的履歷 / 面試邊界。

## 自動維護判斷

已更新：

- `projects/iwin/third_games_api/README.md`：同步 Step 2 已建立與下一步。
- `projects/iwin/third_games_api/step2-flow-comparison.md`：本輪新建。

未更新：

- `senior-owner-playbook/01-senior-owner-flow-inventory.md`：目前 worktree 已有其他 project 的未提交變更；為避免混入本輪 commit，這輪先不動共用 dashboard。Step 3 建立單條 flow 後再同步更合理。
- `senior-owner-playbook/04-interview-casebook.md`：尚未完成 Step 3 / Step 4，不新增面試案例。
- `senior-owner-playbook/05-resume-master-zh.md`、`08-application-autobiography-zh.md`：沒有 Nick 本人 evidence，不更新。

## 下一步建議

只推薦一件事：

```text
iwin third_games_api gsc-transfer-bet-settle-rollback Step 3
```

原因：

- Step 2 已完成候選比較，且 `gsc-transfer-bet-settle-rollback` 的交易風險、downstream evidence、面試價值最高。
- Step 3 會產出單條 flow 學習包；不更新正式履歷 / 自傳。
- 需要 commit；不需要 push，除非 Nick 本輪明確要求。
