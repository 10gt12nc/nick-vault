# Career / Interview - app_bi game-round-record-query

更新時間：2026-05-15
完成狀態：Step 5 已完成
證據層級：分析素材 / learning-only；app_bi 查詢端為專案存在 / code-backed；iwin_gameserver writer 有 Nick commit 線索；正式履歷不更新

## 結論

這條 flow 可以當面試分析案例；目前不建議寫進正式履歷 / 自傳。

Step 5 判定：

- 不更新 `senior-owner-playbook/05-resume-master-zh.md`。
- 不更新 `senior-owner-playbook/08-application-autobiography-zh.md`。
- `app_bi` 查詢端未看到 Nick author。
- `iwin_gameserver` log writer / Antplay-GSC 戰績相關 path 有 `10gt12nc` commit 線索，但應另開後端 flow 深挖，不混在 app_bi 查詢頁 claim。

可講成：

```text
我分析過一條遊戲局紀錄查詢 / 玩家申訴排查 flow。它表面上是 app_bi 後台查詢，但實際要往下追每日 log_reel 分表與 iwin_gameserver log writer。這條的重點不是「查表」，而是要分清楚 troubleshooting log、戰績 projection、wallet / provider transaction truth 之間的邊界。我會從分表、channel shard、serial_id update、bet / settle / refund、查詢效能與 observability 角度看風險。
```

不能講成：

```text
我主導遊戲局紀錄查詢。
我負責玩家申訴系統。
我建立 log_reel pipeline。
我設計遊戲結算 / wallet correctness。
我改善查詢效能或修復 production issue。
```

原因：

- 目前確認 code 功能存在，但 Nick 個人貢獻待確認。
- `app_bi` 是後台查詢端，不是遊戲結算主系統。
- `log_reel` 是 troubleshooting / 戰績 projection，不是完整交易 truth。
- 沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認前，不放正式履歷。

## 已確認 code

- app_bi 後台會依玩家 ID、牌局號、`serial_id` 與日期區間查 `log_reel_{date}`。
- 查詢端會用 `Base::slotsLogDBV()` 取得 log DB 連線設定。
- 有玩家 ID 時，會用玩家 channel 決定 log DB index；沒有玩家 ID 時目前 evidence 看到預設 `logIndex = 1`。
- 查詢條件使用 `game_start_time`，並依日期倒序 loop 每日分表。
- `_getRecord()` 依 `game_id` 解析 `common_data`，轉成後台可讀的遊戲紀錄。
- iwin_gameserver 有 `GamePlayer.sendReelToLog* -> LogController.pushLog -> LogJobCrons -> Mapper.batchInsertLogReel / batchUpdateLogReel` 的 writer 線索。
- Antplay / GSC 類 flow evidence 顯示 bet 可能 insert，settle / refund 可能 update by `serial_id`。

## 面試 30 秒版

我分析過一條遊戲局紀錄查詢 flow。它主要用在玩家申訴或 production troubleshooting，後台會依玩家 ID、牌局號、第三方交易單號和日期查 `log_reel` 每日分表，再把 `common_data` 解析成遊戲可讀紀錄。這條 flow 的重點是不要把後台查到的戰績 log 誤當完整交易 truth；如果要判斷金額正確性，還要對 wallet、currency log、provider transaction 和 settlement flow。

## 面試 3 分鐘版

這條 flow 我會定位成 troubleshooting / 戰績查詢，不是遊戲結算主流程。

後台入口在 `app_bi` 的 `GameRound` controller。使用者會輸入玩家 ID、牌局號、第三方 `serial_id` 或日期區間。`_getGameInfo()` 會先檢查至少有一個查詢條件，再組出 game id、uid、serial_no、serial_id 和 `game_start_time` 的查詢條件。若有玩家 ID，會查玩家 channel，並用 channel 決定 log DB index；接著依日期倒序查 `log_reel_{Y_n_j}` 每日分表，最後用 `_getRecord()` 把 `common_data` 解析成各遊戲的後台顯示格式。

但我不會只看 app_bi。往下追到 iwin_gameserver 後，可以看到遊戲端會透過 `GamePlayer.sendReelToLog*` 建立戰績資料，再由 `LogController.pushLog()` 丟到 log server，最後由 `LogJobCrons` / `LogReel*Job` 分日期寫入 `log_reel`。其中 Antplay / GSC 類 third-party flow 有 bet insert、settle update 的線索，也就是同一個 `serial_id` 可能先有下注紀錄，再更新結算資料。

這裡的 production 風險主要有幾個。

第一是 source of truth 邊界。`log_reel` 對後台查詢很重要，但它是戰績 log / projection，不是完整 wallet ledger。玩家說金額不對時，不能只看這張表，要再對 currency log、wallet、provider transaction 和 settlement 狀態。

第二是查不到資料的判斷。查不到不代表玩家一定沒玩，可能是日期分表錯、channel shard 選錯、只用 serial_id 導致查預設 log index、log pipeline 延遲，或 writer 寫入失敗。

第三是 third-party 的 bet / settle update。若 bet insert 成功但 settle update 失敗，後台可能看到未結算或不完整戰績。`batchUpdateLogReel()` evidence 目前看到是用 `serial_id` 更新，這就要追問 `serial_id` 是否全域唯一、update miss 有沒有告警與補償。

第四是查詢效能。app_bi 會 loop 每日分表，且時間條件用了 `date_format(game_start_time, ...)`。長區間、高頁數、只用 round id 查詢時，都可能造成查詢慢或漏資料。

如果我是 owner，我會先補三件事：第一，明確標示這是 troubleshooting log，不是交易 truth；第二，補查詢 SOP，要求客服排查時知道要對哪些資料源；第三，補 observability，例如 log writer failure、serial_id update miss、慢查詢、跨 shard 查詢策略和查詢範圍限制。

## 可展示能力

- 從 app_bi 後台查詢入口往下追到 iwin_gameserver log writer。
- 分辨 troubleshooting log、projection、wallet / provider source of truth。
- 能用玩家申訴情境拆查不到局、金額不一致、settle update miss。
- 能看出每日分表、channel shard、`serial_id`、`game_start_time` 對排查的影響。
- 能保守區分 code-backed 功能與 Nick 個人 claim。

## 履歷候選句

目前不建議寫進正式履歷。

如果未來 Nick 補到本人 evidence，才可考慮非常保守版本：

```text
參與遊戲局紀錄查詢 / 玩家申訴排查相關功能維護，協助釐清後台查詢、戰績分表、第三方交易單號與遊戲 log pipeline 的資料邊界，提升異常局號排查與資料一致性判斷能力。
```

Step 5 已完成；目前不放入：

- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

## Claim Boundary

可以說：

- 我分析過遊戲局紀錄查詢 / 玩家申訴排查 flow。
- 我能從 app_bi 查詢端追到 `log_reel` 每日分表與 iwin_gameserver log writer 線索。
- 我能指出查詢 log、戰績 projection、wallet / provider truth 的邊界。
- 我能用 code evidence 說明分表、channel shard、`serial_id` update、查詢效能與 observability 風險。

必須保守說：

- 這是 code-backed 分析素材。
- Nick 個人實作貢獻待確認。
- 目前不寫進正式履歷。

尚未確認：

- Nick 本人 MR / ticket / commit / production issue。
- Nick 是否維護過 `GameRound`、`log_reel` writer 或玩家申訴排查。
- 正式環境是否有 log writer retry、serial_id update miss 告警、跨 shard 查詢工具與對帳 SOP。

不能說：

- 主導遊戲局紀錄查詢。
- 負責玩家申訴系統。
- 建立 `log_reel` pipeline。
- 設計遊戲結算 / wallet correctness。
- 改善查詢效能或修過 production incident。

## 詳細附錄

- 主研究報告：`flow.md`
- 證據附錄：`materials/evidence.md`
- 技術決策附錄：`materials/decision-notes.md`
- 面試稿附錄：`materials/interview.md`
- 履歷邊界附錄：`materials/claim-boundary.md`

## 下一步

只推薦一件事：

```text
payment Step 1
```

原因：

- 本 flow Step 5 已完成，不更新正式履歷 / 自傳。
- `app_bi` 主要 flow 已收斂；下一個高價值方向是金流 source of truth。
- `payment Step 1` 會先找金流 repo 的 production flows，不會直接寫履歷。
