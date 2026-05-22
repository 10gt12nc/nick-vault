# bet-target-set-query

## 閱讀定位

- Flow 中文名稱：打碼目標設定 / 查詢 / 投注扣減
- Flow slug：`bet-target-set-query`
- 完成狀態：Step 3 completed
- 掃描等級：Level 2 Flow 深掃
- 證據層級：部分真實開發過 + code-backed；coupon 打碼目標入口有 Nick / `10gt12nc` direct commits，一般 BI / 全部打碼系統 owner 待 Step 5 判斷
- 本 flow 是：money rule / withdrawal gating rule / log projection
- 是否更新正式履歷 / 自傳：否。Step 3 只建立主學習包，不直接更新 `05` / `08`

## 白話導讀

這條 flow 在管「玩家拿到錢或優惠後，要再投注多少才算完成打碼，可以提款」。

它不是單純查一個欄位。真正的主線有三段：

1. 後台、活動、coupon 或其他系統替玩家增加打碼目標。
2. 玩家之後下注 / spin / 第三方 provider 投注時，系統累積已打碼量。
3. 查詢或提款檢查時，系統用「目標 - 已打碼」判斷玩家還差多少。

這條 flow 的風險是 money rule correctness：打碼目標算少，會讓玩家過早提款；打碼目標算多，會造成玩家無法提款或客服爭議；log 失敗則會讓後續稽核與排查困難。

## 初中階 Code 分層對照

```text
HTTP command：
  HttpService.doCommand()
  SET_BET_TARGET
  SET_BET_TARGET_COUPON
  QUERY_BET_TARGET

HTTP handler：
  HttpService.setPlayerBetTarget()
  HttpService.setPlayerBetTargetCoupon()
  HttpService.queryPlayerBetTarget()

Domain state：
  PlayerData.addWithDrawSpinNeeds()
  PlayerData.queryWithDrawBetTarget()
  PlayerData.addWithDrawSpinCount()
  PlayerData.resetWithDrawLimit()

State object：
  SpinNeedData.betTarget
  SpinNeedData.betTotal

Reason enum：
  SpinBetTargetConst.BI_HANDLE
  SpinBetTargetConst.COUPON
  SpinBetTargetConst.WITH_DRAW
  SpinBetTargetConst.REST_BET_TARGET
  activity / recharge / vip reasons

Log projection：
  SpinNeedData.sendBetLog()
  GameEnum.eGameLogType.PLAYER_BET_RECORD
  LogBetRecordJob
  LogJobCrons PLAYER_BET_RECORD
  Mapper.batchInsertBetRecord()
  log_spin_bet_YYYYMMDD
```

## 最小架構圖

```mermaid
flowchart LR
  Upstream["app_bi / game_api coupon / activities"] --> HttpService["HttpService command"]
  HttpService --> PlayerData["PlayerData"]
  PlayerData --> SpinNeeds["commExt.spinNeeds / SpinNeedData"]
  SpinNeeds --> LogController["LogController PLAYER_BET_RECORD"]
  LogController --> LogServer["slots-game-log"]
  LogServer --> LogTable["log_spin_bet_YYYYMMDD"]
  Spin["spin / provider bet"] --> PlayerData
  Query["QUERY_BET_TARGET / withdrawal check"] --> PlayerData
```

## 正常流程圖

```mermaid
flowchart TD
  A["上游送 SET_BET_TARGET / SET_BET_TARGET_COUPON"] --> B["HttpService 找 PlayerData"]
  B --> C["addWithDrawSpinNeeds(targetCnt, reason, rate, betCnt)"]
  C --> D["SpinNeedData.addBetTarget"]
  D --> E["更新 betTarget / betTotal"]
  E --> F["sendBetLog -> PLAYER_BET_RECORD"]
  F --> G["LogBetRecordJob / LogJobCrons 寫 log_spin_bet"]
  H["玩家 spin / provider 投注"] --> I["addWithDrawSpinCount(betCoin, gameId)"]
  I --> J["SpinNeedData.addBetTotal"]
  J --> F
  K["QUERY_BET_TARGET"] --> L["queryWithDrawBetTarget"]
  L --> M["回傳 max(betTarget - betTotal, 0)"]
```

## 正常流程逐步說明

1. 上游透過 center_http command 送 `SET_BET_TARGET`、`SET_BET_TARGET_COUPON` 或 `QUERY_BET_TARGET`。
2. `HttpService.doCommand()` 根據 command 分派到對應 method。
3. `SET_BET_TARGET` 呼叫 `setPlayerBetTarget(accountId, targetCnt)`，使用 `SpinBetTargetConst.BI_HANDLE`。
4. `SET_BET_TARGET_COUPON` 呼叫 `setPlayerBetTargetCoupon(accountId, targetCnt)`，使用 `SpinBetTargetConst.COUPON`。
5. `PlayerData.addWithDrawSpinNeeds()` 先排除內部帳號，再從 `commExt` 讀取 `spinNeeds`。
6. `SpinNeedData.addBetTarget()` 依既有目標是否完成，決定重設或累加 `betTarget`，並把 `betTotal` 歸零或保留。
7. `SpinNeedData.sendBetLog()` 發送 `PLAYER_BET_RECORD` 到 log server。
8. 玩家後續一般 spin 或 provider 投注時，`PlayerData.addWithDrawSpinCount()` 會累加 `betTotal`。
9. `QUERY_BET_TARGET` 呼叫 `PlayerData.queryWithDrawBetTarget()`，回傳剩餘打碼目標。
10. 提現或特殊條件下會呼叫 `resetWithDrawLimit()` / `resetSpinNeed()` 清掉目標。

## 業務問題

這條 flow 服務的是提款前的 wagering / turnover rule：

- 玩家收到 bonus / coupon / activity award 後，需要投注到一定量。
- 系統要知道玩家還差多少打碼量。
- 客服、後台或提款檢查要能解釋為什麼玩家不能提款。

錯誤後果：

- 目標未設定：玩家可能過早提款。
- 已打碼未累加：玩家明明投注了卻仍被限制。
- 目標重複累加：玩家被要求過高打碼。
- log 缺失：客服和稽核難以還原 target change / bet total。

## 系統位置

- 專案：`iwin_gameserver`
- module：`slots-center`、`slots-game-log`
- runtime state：`PlayerData.commExt.spinNeeds`
- log：`PLAYER_BET_RECORD` -> `log_spin_bet_YYYYMMDD`
- 上游已確認：center_http command；coupon command 有 direct commit evidence
- 下游待確認：app_bi 查詢 / payment 提款檢查 / game_api coupon caller 的完整 request contract

## 入口與 code path

已確認：

- `slots-center/src/main/java/com/slots/center/service/HttpService.java`
  - `SET_BET_TARGET`
  - `SET_BET_TARGET_COUPON`
  - `QUERY_BET_TARGET`
  - `setPlayerBetTarget()`
  - `setPlayerBetTargetCoupon()`
  - `queryPlayerBetTarget()`
- `slots-center/src/main/java/com/slots/center/data/PlayerData.java`
  - `addWithDrawSpinNeeds()`
  - `addWithDrawSpinCount()`
  - `queryWithDrawBetTarget()`
  - `resetWithDrawLimit()`
- `slots-center/src/main/java/com/slots/center/data/SpinNeedData.java`
  - `addBetTarget()`
  - `addBetTotal()`
  - `queryPlayerBetTarget()`
  - `sendBetLog()`
- `slots-game-log/src/main/java/com/slots/game/job/player/LogBetRecordJob.java`
- `slots-game-log/src/main/java/com/slots/game/LogJobCrons.java`
- `slots-game-log/src/main/java/com/slots/game/sql/mapper/Mapper.java`

## 資料與狀態

| 狀態 | 來源 | 說明 |
| --- | --- | --- |
| `betTarget` | `SpinNeedData` | 目前要求玩家完成的總打碼目標 |
| `betTotal` | `SpinNeedData` | 玩家已累積的打碼量 |
| `spinNeeds` | `PlayerData.commExt` | 玩家打碼目標與已打碼量的 runtime / persisted extension |
| `targetType` | `SpinBetTargetConst` | 來源原因，例如 BI、coupon、activity、withdraw reset |
| `targetChange` | `PlayerBetRecordRq` | 本次目標變化量 |
| `betCnt` | `PlayerBetRecordRq` | 本次投注 / 打碼累加量 |
| `log_spin_bet_YYYYMMDD` | log server | 打碼目標與已打碼量變更紀錄 |

## State transition

```text
無打碼目標
-> SET_BET_TARGET / SET_BET_TARGET_COUPON
-> betTarget = targetCnt * betRate, betTotal = 0
-> 玩家投注後 addBetTotal
-> betTotal 累加
-> query 回 betTarget - betTotal
-> betTotal >= betTarget 時 query 回 0 / check 通過
-> 提現或低餘額 reset 時清除 spinNeeds
```

## Consistency / idempotency

已確認：

- `addBetTarget()` 會在舊目標已完成時重置，在未完成時累加目標。
- `queryPlayerBetTarget()` 在 `betTotal >= betTarget` 時回 0。
- 內部帳號不維護打碼量。
- 每次 target / betTotal 變更都會送 `PLAYER_BET_RECORD` log。

風險 / 待確認：

- `HttpService.setPlayerBetTarget()` 直接 `findPlayer(accountId)`，目前未看到 offline fallback；若玩家不在線可能 NPE 或失敗，需確認上游是否只對在線玩家呼叫。
- `SET_BET_TARGET` / coupon command 未看到 request idempotency key；上游重送可能重複累加目標。
- `SpinNeedData` 在 `commExt` 中更新，需再確認 save / flush / DB persistence 時機。
- log server failure 不影響打碼目標本身，但會影響後續 audit。

## Failure window

| 情境 | 可能結果 | Owner 觀察點 |
| --- | --- | --- |
| 上游重送 `SET_BET_TARGET_COUPON` | `betTarget` 重複累加 | 需要 request id / coupon id guard |
| 玩家離線但上游呼叫設定 | 找不到 `PlayerData` 可能失敗 | 需確認是否要 `PlayerCache.loadToCache` |
| 設定成功但 log server 失敗 | rule 生效但 audit 缺紀錄 | 需補 durable log / replay |
| 玩家投注成功但 `addWithDrawSpinCount` 未跑 | 打碼未扣減，玩家不能提款 | 需用 spin / provider bet log 對帳 |
| 提現 reset 與投注累加交錯 | 目標或已打碼量可能被不預期清空 | 需明確 transaction / ordering |

## Observability

已確認：

- `SpinNeedData` 會記錄 target / total / count 的 system log。
- `PLAYER_BET_RECORD` 會進 log server。
- `LogBetRecordJob` / `LogJobCrons` 分日寫 `log_spin_bet_YYYYMMDD`。

建議補強：

- 用 `uid + sourceType + sourceBillNo/couponId + timestamp` 串 target change。
- 對 `SET_BET_TARGET` command 增加上游 request id。
- 建查詢視角：目前 `betTarget`、`betTotal`、最近 target change、最近 bet accumulation。

## Owner decision

1. 打碼目標是提款規則，不是單純後台設定。
2. `betTarget` 是 rule state，`betTotal` 是投注累積 state，兩者都要可 audit。
3. coupon 類 target 必須避免重複設定，否則玩家會被多要求打碼。
4. `log_spin_bet` 是 audit source，不是 rule source；缺 log 不代表 rule 沒生效。
5. offline player / cache persistence 是 Step 4 / Step 5 前要追的重點。

## 面試 / 履歷邊界摘要

可面試講：

- 我追過 iwin_gameserver 的打碼目標設定 / 查詢 flow，能說明 target 設定、投注累積、提款檢查與 audit log 的邊界。
- coupon 打碼目標入口有 Nick / `10gt12nc` direct commit evidence，可作 project-level coupon / bet target supporting evidence。

目前不直接寫正式履歷：

- Step 3 還未完成 Step 5 claim gate。
- 一般 BI handle / 全部打碼系統 owner 未確認。
- payment / app_bi / game_api 上游完整 contract 未掃完。

## Step 3 結論

本 flow 已完成 Step 3 主學習包。它是中高價值 money rule 面試素材，因為能把「優惠 / 活動 / 後台設定」和「玩家投注 / 提款限制 / audit log」串起來。

本輪不更新正式履歷 / 自傳。下一步應補 Step 4，把這條整理成正式面試 case，並保守處理 Nick direct evidence。

## 下一步

```text
iwin iwin_gameserver bet-target-set-query Step 4
```

原因：

- Step 3 已建立 code-backed 主學習包。
- 下一步要把打碼目標設定、投注扣減、重複設定風險、offline / log failure 轉成正式面試 case。
- 不直接更新 05 / 08；是否回填履歷等 Step 5 claim gate。
