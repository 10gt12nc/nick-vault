# Evidence：GSC transfer bet / settle / rollback

## 本輪實際掃描範圍

Vault：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/iwin/third_games_api/README.md`
- `projects/iwin/third_games_api/step1-candidate-flows.md`
- `projects/iwin/third_games_api/step2-flow-comparison.md`

Source repo：

- `/Users/nick/Git/iwin/third_games_api`
  - branch：`beta`
  - local HEAD：`4915ea5`
  - `origin/beta`：`4915ea5`
  - ahead / behind：`0 / 0`
  - Step 3 建立時與本次 KB 更新後深度檢查，皆已執行 `git fetch --all --prune`，沒有 checkout / pull / merge / rebase。
- `/Users/nick/Git/iwin/iwin_gameserver`
  - branch：`main`
  - local HEAD：`30a9fcb`
  - `origin/main`：`30a9fcb`
  - ahead / behind：`0 / 0`
  - Step 3 建立時與本次 KB 更新後深度檢查，皆已執行 `git fetch --all --prune`，沒有 checkout / pull / merge / rebase。

## 已讀 code path

`third_games_api`：

- `src/main/java/com/slots/web/controller/GscController.java`
  - `transfer(...)`
  - `moneyInoutGetBalance(...)`
  - Mongo insert helpers
  - duplicate helper methods
- `src/main/java/com/slots/web/common/utils/GetGameRedis.java`
- `src/main/java/com/slots/web/schedule/ScheduleServer.java`

`iwin_gameserver`：

- `slots-center/src/main/java/com/slots/center/service/HttpService.java`
- `slots-center/src/main/java/com/slots/sql/job/HttpPGTransferInOut.java`
- `slots-center/src/main/java/com/slots/center/job/http/PGTransferInOutJob.java`
- `slots-games/slots-game-common/src/main/java/com/slots/game/common/data/GamePlayer.java`

## 已讀 path history / branch 線索

`third_games_api` `GscController.java` path history：

- `08340d0 feat:create the GSC MongoDB column of createdTime for backup`
- `7f6bc3a feat(#GSC):移除TODO, gameId動態取得`
- `329279c feat(#GSC):game code設126...`
- `7fde77c feat(#GSC):新增pushbet API; 新增/transfer rollback判斷; 新增stage的login`
- `e4a2bba feat(#GSC): 增加判斷條件符合PG 驗證`
- `575db56 fea(#GSC):修改回調路徑,串接/transfer API`
- `e8edc0d feat(#new):串接GSC_PG遊戲`

`iwin_gameserver` GSC / PG related branch 線索：

- `origin/Nick-GSC_PG` path history 曾出現 `b58eb58 feat(#PG): GSC+ PG`。
- 因為沒有本人確認，這只作為 branch / code existence 線索，不標成 Nick 真實開發過。

其他 branch 線索：

- `third_games_api origin/Test001-Nick` 對 `GscController.java` 看到 `540e76c feat(#oneapi): 處理sign問題`。
- `third_games_api origin/checkPerformance` 看到 Redis / DB 取出存內存相關 commit 線索。

## 已確認事實

- GSC `transfer` endpoint 會驗 request body、sign、currency、member account。
- Sign source string 使用 operator code、request time、固定 action 字串與設定 key 組合後做 MD5；本文件不保存設定值。
- `memberAccount` 必須是數字字串，並能查到 `AccountVo`。
- `transactions` 會被 loop 讀取，但主要 local variables 只保留最後一筆 transaction。
- 非 rollback 情境會先查餘額，再用 Redis mapping 找 `gameId`，再送 gameserver `PGTRANSFERINOUT`。
- `ROLLBACK` 情境不呼叫 gameserver，只計算 response balance 並寫 Mongo。
- `third_games_api` 成功呼叫 gameserver 後，會寫 `third_log_gsc` 與 `third_transaction_gsc`。
- `GetGameRedis` 會從 Redis 快取 third platform 與 game mapping。
- `ScheduleServer` 每 5 秒呼叫 `GetGameRedis.init()` refresh cache。
- gameserver `HttpService` 會 dispatch `PGTRANSFERINOUT` 到 `HttpPGTransferInOut`。
- `PGTransferInOutJob` 會檢查玩家與餘額，呼叫 `modifyAndGetCoinPG`，然後回 HTTP response 並送 log / bet log。
- 本次依更新後 KB 回頭檢查既有 Step 3，結論是主體可沿用；已補強 `flow.md` 的業務問題、系統位置、入口與 code 路徑、DB / Redis / MQ / 外部 API、state transition、Lead / Architect 追問與下一步 evidence。

## 推測

- `third_games_api` Mongo collection 比較像 callback audit / transaction evidence，不是 money source of truth。
- 真正 money source of truth 應在 gameserver wallet / player coin mutation。
- `ROLLBACK` 分支可能是 provider validation / 特殊語意需求，但目前沒有 spec evidence 可確認。
- `PGTransferInOutJob` 的 idempotency 若存在，可能在 `modifyAndGetCoinPG` 或更底層資料寫入；本輪未確認。

## 待確認

- Mongo `third_log_gsc` / `third_transaction_gsc` 是否有 unique index。
- `GamePlayer.modifyAndGetCoinPG` 底層是否以 `transactionId` / `betId` 去重。
- provider spec 是否允許 `transfer` 一次送多筆 transactions。
- `ROLLBACK` 是否應該真的反向異動 wallet。
- gameserver response 送出後，後續 event / reel log / bet log 失敗時的 retry / repair 機制。
- Mongo insert 失敗時是否有補償 job 或手動對帳 SOP。
- production alert / dashboard / reconciliation report 是否存在。

## 未掃範圍

- 沒有切到 `third_games_api` `origin/k3s`、`origin/main`、`origin/Test001-Nick` 等分支逐行比對，只讀 path log 線索。
- 沒有切到 `iwin_gameserver` `origin/Nick-GSC_PG` 逐檔逐行，只讀 path log 線索。
- 沒有讀 provider GSC 官方規格。
- 沒有查 production Redis / Mongo / DB schema。
- 沒有讀 ticket、MR、production incident、部署紀錄。
- 沒有確認 Nick 本人實際參與。

## Secret / 敏感資料處理

- 本文件沒有寫入實際 key、token、password、internal IP、production URL 或客戶資料。
- 只保留 class / method / collection / Redis key 類 code-backed 名稱。
