# GSC transfer bet / settle / rollback Flow

## 閱讀定位

本文件是 `iwin third_games_api gsc-transfer-bet-settle-rollback Step 3` 的主報告。

證據層級：`專案存在 / code-backed`。目前沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認，因此不能寫成 Nick 真實開發成果。

掃描深度：Level 2 Flow 深掃。這輪已重讀 vault KB、`third_games_api` Step 1 / Step 2、`third_games_api` 最新 `beta` 分支與 `iwin_gameserver` 最新 `main` 分支，並補看 `iwin_gameserver` 的 `origin/Nick-GSC_PG` path history 作為分支線索。沒有切分支、沒有 pull、沒有修改公司專案。

本 flow 研究的是 GSC provider 打進 `POST /api/seamless/transfer` 後，`third_games_api` 如何驗簽、解析交易、查玩家、找 gameserver，再透過 gameserver 做投注 / 派彩錢包異動；以及 `ROLLBACK` action 在目前 code 裡的特殊處理方式。

## 白話導讀

這條 flow 可以先想成「第三方遊戲把一筆下注 / 派彩 / 回滾結果送回 iwin，iwin 要決定玩家錢包要不要扣錢或加錢」。

`third_games_api` 不是最終錢包，它比較像 provider adapter：

1. 接 GSC 的 transfer callback。
2. 驗證簽名、幣別、玩家是否存在。
3. 把 provider 的交易資料轉成 iwin gameserver 看得懂的 `PGTRANSFERINOUT` command。
4. 呼叫 gameserver，由 gameserver 真的改玩家餘額與投注統計。
5. 回覆 provider，並在 Mongo 留 callback / transaction evidence。

最值得注意的是：`ROLLBACK` 在這個 endpoint 裡走一條很特別的路。它不呼叫 gameserver，只用「目前餘額 + request amount」算出一個 response balance，然後寫 Mongo。這可能是為了配合 GSC 驗證或特定 provider 語意，但從 money correctness 角度看，它不是完整 rollback wallet mutation，必須標成 `待確認`。

## Code 分層對照

| 層級 | 已確認 code | 角色 |
| --- | --- | --- |
| Provider API | `third_games_api/src/main/java/com/slots/web/controller/GscController.java` `transfer(...)` | 接 `POST /api/seamless/transfer`，驗簽、驗玩家、解析 transactions、呼叫 gameserver、寫 Mongo |
| Redis routing | `third_games_api/src/main/java/com/slots/web/common/utils/GetGameRedis.java` | 快取 third platform / game mapping / center routing |
| Redis refresh | `third_games_api/src/main/java/com/slots/web/schedule/ScheduleServer.java` | 每 5 秒重新初始化 `GetGameRedis` |
| Gameserver HTTP dispatch | `iwin_gameserver/slots-center/src/main/java/com/slots/center/service/HttpService.java` | 接 `PGTRANSFERINOUT` command，建立 job |
| Gameserver job wrapper | `iwin_gameserver/slots-center/src/main/java/com/slots/sql/job/HttpPGTransferInOut.java` | 用 `accountId` 找玩家，丟進 game pool |
| Wallet mutation job | `iwin_gameserver/slots-center/src/main/java/com/slots/center/job/http/PGTransferInOutJob.java` | 檢查餘額、呼叫 `modifyAndGetCoinPG`、回覆 HTTP、推 log / bet log |
| Player wallet method | `iwin_gameserver/slots-games/slots-game-common/src/main/java/com/slots/game/common/data/GamePlayer.java` | 錢包異動落點之一；本輪只追到呼叫點，未逐行展開底層持久化 |

## 最小架構圖

```mermaid
flowchart LR
  GSC["GSC provider"] --> API["third_games_api /api/seamless/transfer"]
  API --> Redis["Redis cached config: platform + game mapping + center route"]
  API --> Mongo["Mongo bi_log: third_log_gsc / third_transaction_gsc"]
  API --> GS["iwin_gameserver center_http PGTRANSFERINOUT"]
  GS --> Wallet["GamePlayer.modifyAndGetCoinPG"]
  GS --> Logs["game coin log / reel log / bet log"]
```

## 正常流程圖

```mermaid
flowchart TD
  A["GSC transfer callback"] --> B["validate request body"]
  B --> C["verify sign and currency"]
  C --> D["load AccountVo by memberAccount"]
  D --> E["parse transactions"]
  E --> F{"action == ROLLBACK?"}
  F -- "yes" --> R1["query current balance"]
  R1 --> R2["calculate response balance only"]
  R2 --> R3["write Mongo evidence"]
  R3 --> Z["return success to provider"]
  F -- "no" --> G["query current balance"]
  G --> H["map gameCode to gameId from Redis"]
  H --> I["build PGTRANSFERINOUT params"]
  I --> J["call gameserver center_http"]
  J --> K{"gameserver Err"}
  K -- "OK" --> L["parse balance"]
  L --> M["write Mongo evidence"]
  M --> Z
  K -- "insufficient / player not found / other" --> E1["return mapped provider error"]
```

## 正常流程逐步說明

1. GSC 呼叫 `POST /api/seamless/transfer`，body 內含 operator、member、product、currency、game type、transactions、request time 與 sign。
2. `GscController#transfer` 先做 request validation。若 binding error，直接回 provider invalid request。
3. API 用 `operatorCode + requestTime + "transfer" + gscKey` 計算 MD5，比對 provider sign。`gscKey` 是設定值，本文件不記錄實值。
4. API 檢查幣別，並要求 `memberAccount` 是數字字串，再用 account id 查 `AccountVo`。
5. API 逐筆讀 `transactions`，但目前 local variables 會保留最後一筆 transaction 的 `amount`、`bet`、`validBetAmount`、`prizeAmount`、`wager_code`、`action`、`wagerStatus`、`gameCode`、`settleAt`。
6. 若 `action` 是 `BONUS`、`JACKPOT`、`PROMO`，`addMoney = amount`；其他情境用 `prizeAmount - betAmount` 代表本次淨異動。
7. API 先用 `moneyInoutGetBalance(memberAccount)` 呼叫 gameserver 查目前餘額。
8. 若是 `ROLLBACK`，目前 code 不呼叫 gameserver 修改錢包，只回覆 `amount + currentBalance`，並寫 Mongo。
9. 若不是 `ROLLBACK`，API 從 Redis `Game:List:ThirdIdList` 的 `PG` mapping 取得 `gameId`。
10. API 以 `PGTRANSFERINOUT` 呼叫 gameserver，帶入 `accountId`、`addMoney`、`BetCoin`、`validBetCoin`、`spinCurrency`、`transactionId`、`gameId`、`betId`、`reason`、`createTime`。
11. gameserver `PGTransferInOutJob` 檢查玩家存在與餘額，呼叫 `modifyAndGetCoinPG` 進行錢包異動，然後回覆 HTTP，並推送 game coin / reel / bet log。
12. `third_games_api` 解析 gameserver response balance，寫入 `third_log_gsc` 與 `third_transaction_gsc`，再回 GSC success。

## 已確認資料狀態

| 狀態 / 資料 | 來源 | 用途 | 注意事項 |
| --- | --- | --- | --- |
| Provider request | GSC `transfer` callback | 原始交易事件 | 多筆 transactions 目前只留下最後一筆作為主要處理資料 |
| AccountVo | `third_games_api` account cache / lookup | 驗玩家、取得 `openId` / `centerId` | `memberAccount` 必須是數字 |
| Redis platform config | `GetGameRedis.thirdPlatformPG` | 找 gameserver `center_http` routing | 每 5 秒 refresh，但 refresh 失敗時的降級策略未完整確認 |
| Redis game mapping | `GetGameRedis.thirdIdList` | `gameCode` 對 `gameId` | 缺 mapping 會造成交易無法送 gameserver |
| Gameserver wallet | `GamePlayer.modifyAndGetCoinPG` | 真正錢包異動 | 本輪只追到呼叫點，底層持久化與冪等未完整確認 |
| Mongo `third_log_gsc` | `third_games_api` 寫入 | callback audit | 不是已確認的最終帳本 |
| Mongo `third_transaction_gsc` | `third_games_api` 寫入 | transaction evidence / query evidence | 本輪未看到 unique index evidence |

## Transaction Boundary

已確認：

- `third_games_api` 的 transaction boundary 不包含 gameserver wallet mutation 與 Mongo insert 的原子性。
- 真正扣款 / 加款發生在 `iwin_gameserver` 的 `PGTransferInOutJob#modifyCoin`，不是 `third_games_api`。
- `third_games_api` 是先呼叫 gameserver，成功後才寫 Mongo。
- gameserver job 會先完成 wallet mutation 與 HTTP OK response，再做後續 event / reel log / bet log 類投遞。

推論：

- 系統的 money source of truth 應在 gameserver / player wallet，不在 `third_games_api` Mongo。
- Mongo 更像 provider callback evidence、查詢輔助與 reconciliation 素材。

待確認：

- `GamePlayer.modifyAndGetCoinPG` 是否以 `transactionId` / `betId` 做底層冪等。
- Mongo collection 是否有 unique index。
- provider retry 時，gameserver 成功但 `third_games_api` Mongo 失敗的補償流程。

## Consistency 與 Idempotency

已確認風險：

| 面向 | 現況 | Owner 風險 |
| --- | --- | --- |
| Provider retry | `transfer` 內沒有明確呼叫 `hasBetId(...)` 做重送防護 | 同一筆 provider event 若重送，是否重複扣 / 加款取決於 gameserver 底層是否冪等 |
| 多筆 transactions | loop 只把最後一筆交易留在 local vars | 若 provider 一次送多筆，前面交易可能沒有完整處理 |
| duplicate action check | 目前只檢查同一 request 內 action 是否重複 | 不是跨 request 的 transaction idempotency |
| Mongo after wallet | gameserver 成功後才寫 Mongo | Mongo 寫失敗可能導致 provider 收到錯誤或 evidence 缺口，但 wallet 已變更 |
| HTTP response timing | gameserver 回 OK 後還會做 log / bet log | provider 成功語意與後續投影完整性不是同一個 atomic boundary |
| ROLLBACK | 不呼叫 gameserver，只回算餘額並寫 Mongo | 可能不是實際 wallet rollback，語意需要 provider spec 或 production evidence 確認 |

這裡最適合轉成 Senior / Owner 面試重點：第三方 seamless wallet 不能只問「API 有沒有回成功」，要問「source of truth 在哪裡、重送如何收斂、外部成功與內部投影不一致時誰負責修正」。

## Failure Window

| Window | 可能情境 | 已確認 / 推測 | 影響 | 建議追查 |
| --- | --- | --- | --- | --- |
| 驗簽失敗 | sign mismatch | 已確認會拒絕 | provider request 不進入錢包異動 | 只需保留 audit / alert 策略 |
| Redis mapping 缺失 | `gameCode` 找不到 `gameId` 或 center route | 已確認依 Redis routing | 交易無法送 gameserver | 查 Redis refresh 失敗告警與 fallback |
| 查餘額成功，扣款前餘額變動 | concurrent request | 推測 | `moneyInoutGetBalance` 只是 pre-check，不是 lock | 以 gameserver 原子扣款結果為準 |
| gameserver wallet 成功，HTTP response 到 API 失敗 | network / timeout | 待確認 | provider 可能重送，若無冪等可能重複扣加 | 追 gameserver transaction idempotency |
| gameserver 成功，Mongo insert 失敗 | Mongo exception | 已確認 insert 在 gameserver 成功後 | callback evidence 缺失，API catch 可能回 ServerError | 補 outbox / retry / reconciliation |
| API 回 provider success，後續 bet log 失敗 | gameserver response 早於部分 log | 已確認 response timing 線索 | wallet 正確但報表 / 稽核投影可能缺口 | 補 log retry 與 reconciliation report |
| ROLLBACK 不改 wallet | `action == ROLLBACK` branch | 已確認 code 行為 | response 看似 rollback，但錢包未反向異動 | 必須用 provider spec / production issue 確認語意 |

## Retry / Compensation / Reconciliation

目前 code-backed 的可說法：

- Provider-facing API 有把 request 與 transaction 寫進 Mongo，具備 audit / reconciliation 素材。
- gameserver 端有 game coin log、reel log、bet log 等投影，能支撐跨系統核對。
- `queryBet` / `queryBetTime` 等 helper 存在，但 `transfer` 主線沒有用它們做完整重送防護。

不能腦補的部分：

- 不能說系統已經有完整 exactly-once。
- 不能說 rollback 已真正補償 wallet。
- 不能說 Mongo 是帳本或唯一 source of truth。
- 不能說 provider retry 一定安全，除非後續補到 gameserver 底層冪等 evidence。

建議 Owner decision：

1. 以 gameserver wallet ledger 作為 money source of truth。
2. 讓 provider `transactionId` / `wager_code` 在 wallet mutation 層具備唯一性或可重入結果。
3. 對 gameserver 成功但 Mongo 失敗建立 repair job 或 reconciliation query。
4. 明確定義 `ROLLBACK`：是 provider validation shortcut、查詢型 response，還是真正補償交易。
5. 把 `third_games_api` Mongo、gameserver coin log、provider transaction id 做成對帳 join key。

## Observability

已確認觀察點：

- `third_games_api` 會在 `third_log_gsc` 記錄 request / transaction / bet id / amount 類 callback evidence。
- `third_games_api` 會在 `third_transaction_gsc` 記錄 step、action、balance、beforeBalance、bet time、settle time。
- gameserver `PGTransferInOutJob` 會推 game coin log、reel log、bet log。

待補：

- 這些 log 是否有統一 trace id。
- Mongo 與 gameserver log 是否有 dashboard 或 daily reconciliation。
- `ROLLBACK` 分支是否有特別告警。
- Redis refresh 失敗是否會告警到 owner。

## Owner Decision 摘要

這條 flow 的 Owner 不是只維護 endpoint，而是要維護「外部 provider callback 與內部錢包 mutation 的一致性契約」。

真正需要 owner 決策的點：

- `transfer` 要不要支援一次多筆 transactions；如果 provider spec 允許多筆，目前實作要改成逐筆處理或明確拒絕。
- `ROLLBACK` 到底是不是 money mutation；如果不是，文件與 monitoring 要清楚標出，避免營運或工程誤解。
- provider retry 的冪等要放在 adapter 層、wallet 層，或兩層都放。
- Mongo insert 失敗時要回 provider error、success，還是進 repair queue。
- 報表投影與錢包帳本不一致時，以哪一邊修正另一邊。

## 面試 / 履歷邊界摘要

可用作面試素材：

- 第三方 seamless wallet callback 的 transaction boundary 分析。
- Provider retry / timeout / duplicate transaction 的冪等設計。
- Adapter Mongo audit 與 gameserver wallet source of truth 的切分。
- Rollback 語意不清時如何用 code evidence、provider spec、production reconciliation 收斂。

目前不建議放進正式履歷：

- 「主導 GSC provider 串接」。
- 「設計第三方錢包架構」。
- 「解決 rollback 一致性問題」。
- 「保證 exactly-once」。

正式履歷若要更新，必須等 Nick 補本人參與 evidence，並完成更深的 branch / commit / issue / production case 確認。

## 本 Step 3 結論

`gsc-transfer-bet-settle-rollback` 是一條高價值 Senior Backend flow，因為它把 provider API、Redis routing、gameserver wallet mutation、Mongo audit、rollback semantics、retry / idempotency 全部串在一起。

但它目前的履歷層級只能是 `專案存在 / code-backed` 與 `分析素材 / learning-only`。最重要的下一個洞是：補 Step 4 面試 case 時，要把「ROLLBACK 不改 wallet」與「gameserver 成功但 Mongo 失敗」整理成可講、保守、不誇大的交易一致性案例。
