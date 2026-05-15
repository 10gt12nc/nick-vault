# game-round-record-query - Evidence

更新時間：2026-05-15
掃描等級：Level 2 Flow 深掃
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 自動重讀紀錄

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`

已重讀 vault：

- `projects/iwin/app_bi/README.md`
- `projects/iwin/app_bi/step1-candidate-flows.md`
- `projects/iwin/app_bi/step2-flow-comparison.md`
- `projects/iwin/app_bi/flows/point-control-admin-operation/flow.md`
- `projects/iwin/app_bi/flows/admin-config-redis-sync/flow.md`
- `projects/iwin/app_bi/flows/daily-game-record-summary/flow.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/06-todo.md`

既有 flow 狀態：

- `game-round-record-query`：Step 3 已完成；本次 Step 4 轉成面試 case。
- `point-control-admin-operation`：Step 5 已完成，可沿用。
- `admin-config-redis-sync`：Step 5 已完成，可沿用。
- `daily-game-record-summary`：Step 5 已完成，可沿用。

## Source Repo 狀態

已看 repo：

| Repo | 分支 | 工作區狀態 | 本次角色 |
| --- | --- | --- | --- |
| `/Users/nick/Git/iwin/app_bi` | `main` | clean | 後台查詢端 |
| `/Users/nick/Git/iwin/iwin_gameserver` | `main` | clean | writer / log pipeline 線索 |
| `/Users/nick/Git/iwin/game_job` | `main` | clean | schema / log table 線索 |
| `/Users/nick/Git/iwin/game_api` | `main` | clean | 其他 reader 線索 |
| `/Users/nick/Git/iwin/third_games_api` | `beta` | clean | provider reader / serial id 線索 |

未掃：

- 未 checkout 每個遠端分支。
- 未逐 commit diff。
- 未完整掃所有 game-specific `_getRecord()` branch。
- 未掃 wallet / provider callback / currency ledger 的完整 truth source。

## app_bi Evidence

### `GameRound.php`

路徑：

- `/Users/nick/Git/iwin/app_bi/app/admin/controller/GameRound.php`

已確認：

- `_initialize()` 載入 `../app/common/GameRound.php` 與 request params。
- 多數 `*GameRound()` action 只把固定 game id 傳入 `_getGameInfo()`。
- `antplayGameRound()` / `pgGameRound()` 會查 `third_game` platform 後呼叫 `_getGameInfo($this->body['game_id'] ?? 'all')`。
- `_getGameInfo()` 要求至少有 `rid`、`roundid`、`serial_id` 任一條件。
- `rid` 會轉成 `uid = ...`，`roundid` 會轉成 `serial_no = ...`，`serial_id` 會轉成 `serial_id = "..."`
- 有 `rid` 時會檢查 channel cookie、`findUserByPlayerId()`，並用玩家 channel 作為 log DB index。
- 沒有 `rid` 時，`logIndex` 預設為 `1`。
- 時間空白時預設今天；只給起訖其中之一會拒絕。
- 查詢條件使用 `date_format(game_start_time, ...) BETWEEN ...`。
- 依日期倒序組出 `log_reel_{Y_n_j}` 查詢。
- 非 operation log 會先收 raw rows，再用 PHP 切分頁，最後 `_getRecord()` 解析。

風險線索：

- 只用 `roundid` / `serial_id` 且沒有 `rid` 時，可能只查預設 log DB index。
- `date_format(game_start_time, ...)` 可能影響 index 使用。
- 跨多日逐表查詢與 PHP 分頁，長區間 / 高頁數可能成本變高。
- `serial_id` 以字串插入 where；本次只記錄風險線索，不做安全結論。

### `GameRoundService.php`

路徑：

- `/Users/nick/Git/iwin/app_bi/app/business/GameRoundService.php`

已確認：

- 主要提供 Truco 類遊戲牌面解析 helper。
- 不是本 flow 的主要查詢 service。

### `app/common/GameRound.php`

路徑：

- `/Users/nick/Git/iwin/app_bi/app/common/GameRound.php`

已確認：

- 記錄大量 game id 與顯示字典。
- `_getRecord()` 依 game id 解析 `common_data` 時會用這些 mapping。

### `Base.php`

路徑：

- `/Users/nick/Git/iwin/app_bi/app/common/controller/Base.php`

已確認：

- `slotsLogDBV($w)` delegate 到 `slotsLogDB($w)`。
- `slotsLogDB($w)` 從 Redis settings 取得 log DB connection config 後建立 MySQL connection。
- 本 evidence 不記錄任何 host / user / password。

## iwin_gameserver Evidence

### `GamePlayer.java`

路徑：

- `/Users/nick/Git/iwin/iwin_gameserver/slots-games/slots-game-common/src/main/java/com/slots/game/common/data/GamePlayer.java`

已確認：

- 一般遊戲 `sendReelToLog()` 會建立 `LogReelBaseData`，設定 `currCurrency`、`spinCurrency`、`winCurrency`、`lastUserCurrency`、`serialNo`、`serialId`、`common_data` 等，透過 `LogController.pushLog()` 送出 `REEL_NORMAL`。
- GSC / Antplay / AP / PG 類有 `sendReelToLog_*` method，註解明確寫到戰績表 `slots_logv1.log_reel_`。
- Antplay / GSC 依 bet / settle / refund 送不同 log type；bet 傾向 insert，settle / refund 傾向 update。

### `LogController.java`

路徑：

- `/Users/nick/Git/iwin/iwin_gameserver/slots-common/src/main/java/com/slots/common/controller/LogController.java`

已確認：

- `pushLog(long uid, eGameLogType logType, MessageLite msg)` 透過 log pool 建 task。
- `sendMsg2()` 會建立 packet、設定 log server index 與 uid，再 dispatch log node。
- log server null 時會寫 error log。

### `LogJobCrons.java` / `LogReel*Job`

路徑：

- `/Users/nick/Git/iwin/iwin_gameserver/slots-game-log/src/main/java/com/slots/game/LogJobCrons.java`
- `/Users/nick/Git/iwin/iwin_gameserver/slots-game-log/src/main/java/com/slots/game/job/player/LogReelJob.java`
- `/Users/nick/Git/iwin/iwin_gameserver/slots-game-log/src/main/java/com/slots/game/job/player/LogReelAntplayBetJob.java`
- `/Users/nick/Git/iwin/iwin_gameserver/slots-game-log/src/main/java/com/slots/game/job/player/LogReelAntplaySettleJob.java`
- `/Users/nick/Git/iwin/iwin_gameserver/slots-game-log/src/main/java/com/slots/game/job/player/LogReelGSCBetJob.java`
- `/Users/nick/Git/iwin/iwin_gameserver/slots-game-log/src/main/java/com/slots/game/job/player/LogReelGSCSettleJob.java`

已確認：

- `REEL_NORMAL` 依 `gameStartTime` 分日期後 `batchInsertLogReel()`。
- GSC / Antplay bet 走 `batchInsertLogReel()`。
- GSC / Antplay settle 走 `batchUpdateLogReel()`。
- table name 以 `AbstractLogJob.getYYMMDDTable(LogReelJob.TABLE_NAME, gameStartTime)` 組出。
- super user 會寫 `log_reel_point` 線索。

### `Mapper.java`

路徑：

- `/Users/nick/Git/iwin/iwin_gameserver/slots-game-log/src/main/java/com/slots/game/sql/mapper/Mapper.java`

已確認：

- `batchInsertLogReel()` insert `uid`、`channel`、`game_id`、`room_id`、`serial_no`、`serial_id`、`curr_currency`、`spin_currency`、`win_currency`、`score_currency`、`rebate_currency`、`last_user_currency`、`common_data`、`game_start_time` 等欄位。
- `batchUpdateLogReel()` 更新金額與 `common_data`，條件是 `WHERE serial_id = ...`。

待確認：

- `serial_id` 是否全域唯一。
- update miss 是否有告警或補償。

## Schema / Reader Evidence

### `log_reel-day.sql`

路徑：

- `/Users/nick/Git/iwin/game_job/src/main/resources/initGameTable/log_reel-day.sql`

已確認欄位：

- `uid`
- `time`
- `channel`
- `game_id`
- `room_id`
- `serial_id`
- `serial_no`
- `curr_currency`
- `spin_currency`
- `win_currency`
- `score_currency`
- `last_user_currency`
- `reel_id`
- `spin_bet`
- `spin_rate`
- `type`
- `common_data`
- `have_offline`
- `game_start_time`
- `free_no`
- `rebate_currency`

已確認 index：

- `open_id`
- `time`
- `uid`
- `game_id`
- `game_start_time`
- `channel`

### 其他 reader

路徑：

- `/Users/nick/Git/iwin/third_games_api/src/main/resources/mapper/logone/LogReelYmdDao.xml`
- `/Users/nick/Git/iwin/game_api/src/main/resources/mapper/logone/LogReelYmdDao.xml`

已確認：

- 也有讀取 `log_reel` 的 mapper。
- 可以依 channel、uid、game id、room id、serial no、time range 查詢。
- 這些是其他讀取線索，不是本 app_bi flow 的主要實作。

## Git Log Evidence

app_bi path-specific log：

```text
2ee9ab4 feat(#PG): PgGameRound.html status
2007327 Merge remote-tracking branch 'origin/main' into feature/PG
8797ca5 feat(#PG): Oneapi -> PG
1c88cde feat(#Gsc): gsc -> oneapi
0267158 feat(#Gsc): pg -> GSC
411396d feat(#Gsc): 新增三方遊戲GSC
b09da3c feat(#antplay_reel_status): antplay牌局查询rollback要显示失败
d638b1c feat(#RD-146): antplay金幣流水查詢,牌局查詢修正
98e89fb feat(#RD-146): app bi新增antplay
e83c729 feat(#first commit): first commit
```

iwin_gameserver path-specific log：

```text
4843791 fix(#373):  antplay 支援投派整合 first
053e2be fix(#antplay): log reel 優化
da7dc4b fix(#antplay): log reel 優化
116e8ec feat(#GSC):  GSC
73b2524 Revert "feat(#PG):  退款"
571b6fa feat(#PG):  退款
a889980 feat(#PG):  bet_result 投派整合
2fcbde5 feat(#PG): log
2f476c2 feat(#PG): 风控审核日志
a5fbcb3 feat(#PG): GSC+ PG
04ffd25 feat(#144): iwin gameserver fix-arrayList清空; 改不分類
0f4a972 feat(#144): iwin gameserver map會覆蓋
```

## 證據層級判斷

| 說法 | 證據層級 | 判斷 |
| --- | --- | --- |
| app_bi 有遊戲局紀錄查詢 | 專案存在 / code-backed | code 已確認 |
| 查詢會讀 `log_reel_{date}` | 專案存在 / code-backed | code 已確認 |
| iwin_gameserver 有 log writer 線索 | 專案存在 / code-backed | code 已確認 |
| Nick 開發 / 維護此 flow | 待確認 | 無 Nick 個人 evidence |
| 可寫正式履歷 | 待確認 | 目前不可 |
| 可當面試分析素材 | 分析素材 / learning-only | 可，但要保守 |
