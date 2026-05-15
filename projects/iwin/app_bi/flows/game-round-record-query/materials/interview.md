# Interview - app_bi game-round-record-query

更新時間：2026-05-15
完成狀態：Step 5 已完成
文件角色：`materials/interview.md` 詳細面試稿附錄
掃描等級：Level 2 Flow 深掃延伸
證據層級：分析素材 / learning-only；app_bi 查詢端為專案存在 / code-backed；iwin_gameserver writer 有 Nick commit 線索；正式履歷不更新

## 本次結論

這條 flow 可以轉成面試 case，但只能保守講成：

```text
我分析過一條遊戲局紀錄查詢 / 玩家申訴排查 flow。
表面上是 app_bi 後台查詢，實際要往下追每日 log_reel 分表與 iwin_gameserver log writer。
我會從 troubleshooting log vs transaction truth、channel shard、serial_id update、每日分表查詢與 observability 角度看風險。
```

不能講成：

```text
我主導遊戲局紀錄查詢。
我負責玩家申訴系統。
我建立 log_reel pipeline。
我設計遊戲結算 / wallet correctness。
我改善查詢效能或修復 production issue。
```

原因：目前只確認 code 功能存在，Nick 個人實作貢獻仍是 `待確認`。這條適合作為 troubleshooting / production risk 的面試分析素材，不適合直接放正式履歷。

Step 5 補充：`iwin_gameserver` 的 log writer / Antplay-GSC 戰績相關 path 有 `10gt12nc` commit 線索，但它應該另開後端 writer / provider integration flow 深挖；本文件仍只把 `app_bi game-round-record-query` 當查詢入口與面試分析 case。

## Step 4 前檢查

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`

已重讀 vault：

- `projects/iwin/app_bi/README.md`
- `projects/iwin/app_bi/step1-candidate-flows.md`
- `projects/iwin/app_bi/step2-flow-comparison.md`
- 本 flow 的 `flow.md`
- 本 flow 的 `career-interview.md`
- 本 flow 的 `materials/evidence.md`
- 本 flow 的 `materials/decision-notes.md`
- 本 flow 的 `materials/interview.md`
- 本 flow 的 `materials/claim-boundary.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/06-todo.md`

已重讀 code repo：

- `/Users/nick/Git/iwin/app_bi`
- `/Users/nick/Git/iwin/iwin_gameserver`
- app_bi 目前分支：`main`
- iwin_gameserver 目前分支：`main`
- app_bi path-specific log：`GameRound.php`、`GameRoundService.php`、`app/common/GameRound.php`、`public/views/gameRound/**`
- iwin_gameserver path-specific log：`GamePlayer.java`、`LogJobCrons.java`、`Mapper.java`

既有 Step 狀態：

| Step | 狀態 | 判斷 |
| --- | --- | --- |
| Step 1 | 可沿用 | 已列 candidate flows，且已同步此 flow 進度 |
| Step 2 | 可沿用 | 已同步 Step 5 完成與下一步轉 payment |
| Step 3 | 可沿用 | 已確認 app_bi 查詢端與 iwin_gameserver log writer 線索 |
| Step 4 | 本次完成 | 轉成保守面試 case |
| Step 5 | 已完成 | 不更新正式履歷 / 自傳；Nick writer evidence 另開後端 flow |

本次不做：

- 不更新正式履歷 / 自傳。
- 不宣稱 Nick 實作。
- 不新增 Step。
- 不把補對帳、補 observability、補跨 shard search 升級成新任務名稱。

## 30 秒版本

我分析過一條遊戲局紀錄查詢 flow。它主要用於玩家申訴與 production troubleshooting，後台會依玩家 ID、牌局號、第三方 `serial_id` 與日期區間查 `log_reel` 每日分表，再把 `common_data` 解析成遊戲可讀紀錄。這條 flow 的重點是：後台查到的是戰績 log / projection，不是完整交易 truth。真的要判斷金額正確性，還要對 wallet、currency log、provider transaction 與 settlement flow。

## 3 分鐘版本

```text
這條 flow 我會定位成 troubleshooting / 戰績查詢，不是遊戲結算主流程。

後台入口在 app_bi 的 GameRound controller。使用者會輸入玩家 ID、牌局號、第三方 serial_id 或日期區間。_getGameInfo 會先檢查至少有一個查詢條件，再組出 game id、uid、serial_no、serial_id 和 game_start_time 的查詢條件。若有玩家 ID，會查玩家 channel，並用 channel 決定 log DB index；接著依日期倒序查 log_reel_{Y_n_j} 每日分表，最後用 _getRecord 把 common_data 解析成各遊戲的後台顯示格式。

但我不會只看 app_bi。往下追到 iwin_gameserver 後，可以看到遊戲端會透過 GamePlayer.sendReelToLog* 建立戰績資料，再由 LogController.pushLog 丟到 log server，最後由 LogJobCrons / LogReel*Job 分日期寫入 log_reel。其中 Antplay / GSC 類 third-party flow 有 bet insert、settle update 的線索，也就是同一個 serial_id 可能先有下注紀錄，再更新結算資料。

這裡的 production 風險主要有幾個。

第一是 source of truth 邊界。log_reel 對後台查詢很重要，但它是戰績 log / projection，不是完整 wallet ledger。玩家說金額不對時，不能只看這張表，要再對 currency log、wallet、provider transaction 和 settlement 狀態。

第二是查不到資料的判斷。查不到不代表玩家一定沒玩，可能是日期分表錯、channel shard 選錯、只用 serial_id 導致查預設 log index、log pipeline 延遲，或 writer 寫入失敗。

第三是 third-party 的 bet / settle update。若 bet insert 成功但 settle update 失敗，後台可能看到未結算或不完整戰績。batchUpdateLogReel evidence 目前看到是用 serial_id 更新，這就要追問 serial_id 是否全域唯一、update miss 有沒有告警與補償。

第四是查詢效能。app_bi 會 loop 每日分表，且時間條件用了 date_format(game_start_time, ...)。長區間、高頁數、只用 round id 查詢時，都可能造成查詢慢或漏資料。

如果我是 owner，我會先補三件事：第一，明確標示這是 troubleshooting log，不是交易 truth；第二，補查詢 SOP，要求客服排查時知道要對哪些資料源；第三，補 observability，例如 log writer failure、serial_id update miss、慢查詢、跨 shard 查詢策略和查詢範圍限制。
```

## 面試主軸

### 面試官問：這不就是後台查牌局嗎？

可以回答：

不只看後台查詢。這條 flow 的價值在於它連到玩家申訴與 production troubleshooting。後台查的是 `log_reel` 戰績分表，但如果要判斷玩家金額是否真的正確，就要知道 `log_reel` 只是 troubleshooting projection，還要往 wallet、currency log、provider transaction 與 settlement flow 對。

### 面試官問：source of truth 是哪裡？

可以回答：

對這個後台查詢來說，主要資料來源是 `log_reel_{date}`。但對交易正確性來說，`log_reel` 不是完整 source of truth。它應該和 wallet ledger、currency log、provider transaction、settlement 狀態一起看。也就是查詢 source 和交易 truth 要分開。

### 面試官問：查不到局怎麼辦？

可以回答：

我不會直接判定玩家沒玩。會先檢查日期區間、`game_start_time` 對應的分表、玩家 channel / log DB index、是否只用 `serial_id` 或 `roundid` 查到預設 log index、writer 是否延遲或失敗。如果是第三方遊戲，還要確認 provider transaction 是否存在，以及 bet / settle 是否都寫入。

### 面試官問：查到局但金額不對怎麼辦？

可以回答：

先拆欄位語意：`spin_currency` 是下注，`win_currency` 是派彩，`curr_currency` / `last_user_currency` 是前後餘額，`common_data` 是遊戲顯示細節。然後不能只看 `log_reel`，要對 currency log、wallet ledger、provider transaction，以及 settle / refund 狀態。這樣才能判斷是顯示解析錯、log projection 不完整、settle update miss，還是真的交易錯。

### 面試官問：`serial_id` 為什麼重要？

可以回答：

第三方遊戲通常會把 provider transaction / bet id 放進 `serial_id`，它是排查和關聯 bet / settle / refund 的 key。code evidence 顯示 Antplay / GSC 類 flow 可能先 insert bet，再用 `serial_id` update settle。這裡要追問 `serial_id` 是否全域唯一、是否需要 game id / channel / date 限定、update miss 有沒有告警。

### 面試官問：每日分表有什麼風險？

可以回答：

每日分表有利於大量 log 的寫入、清理與按日期查詢，但跨日查詢會變成多表 loop，排序和分頁會比較麻煩。app_bi evidence 看到是逐日查表、收集資料再切分頁。這對後台排查可用，但長區間或高頁數查詢可能慢，也要避免日期欄位和 table date 不一致導致漏查。

### 面試官問：如果你接手，會先改什麼？

可以回答：

我會先補可觀測性和排查 SOP，不會先大重構。第一，明確標示 `log_reel` 是 troubleshooting log，不是交易 truth。第二，規範玩家申訴 SOP：查戰績、查 currency、查 wallet、查 provider transaction、查 settlement 狀態。第三，補 log writer failure、serial_id update miss、慢查詢、跨 shard 查詢的監控和告警。第四，視查詢量再考慮直接 range compare、限制查詢區間、要求 player id / channel 或做正式跨 shard search。

## Senior 追問清單

- `log_reel` 和 wallet ledger 誰是 source of truth？
- 查不到局時，如何區分玩家沒玩、log 延遲、分表錯誤、channel 選錯？
- `serial_id` 是否全域唯一？如果不是，update 條件要怎麼補？
- bet insert 成功、settle update 失敗時怎麼發現？
- refund 是否會更新同一筆 `serial_id`，還是另有 log type？
- `date_format(game_start_time, ...)` 對 index 使用有什麼影響？
- 跨多日查詢如何處理分頁與排序？
- 只用 `roundid` / `serial_id` 不帶玩家 ID，是否會查錯 shard？
- `common_data` schema 改版時，後台解析如何避免壞掉？
- 查詢 log 是否有操作 audit？

## Lead / Architect 追問清單

- 是否需要把 player complaint 查詢做成統一 troubleshooting portal？
- 是否需要將 log projection、wallet ledger、provider transaction 建立共同 correlation id？
- `serial_id` update miss 是否要有 DLT / retry / reconciliation？
- log writer failure 應該如何告警與重放？
- 長期來看，每日分表查詢是否需要 search index 或 archive query service？
- 後台查詢是否應限制查詢範圍，避免壓垮 log DB？
- 多 provider 的 bet / settle / refund 狀態機是否應標準化？
- `log_reel` 是否會被其他下游當 source 使用？如果會，資料契約是什麼？
- 客服看到「無資料」時，系統是否應提示可能原因，而不是只顯示空結果？

## 可展示 Senior 能力

- 從 app_bi 查詢入口追到 iwin_gameserver writer，而不是停在 controller。
- 分清楚 troubleshooting log、projection、transaction truth。
- 能用玩家申訴情境拆查不到局、金額不一致、settle update miss。
- 能看出分表查詢、channel shard、serial_id、game_start_time 對排查的影響。
- 能提出 owner decision：查詢 SOP、observability、update miss 告警、查詢範圍限制。
- 能保守講 code-backed 分析，不把自己包成實作 owner。

## 不可誇大邊界

不能說：

- 我主導遊戲局紀錄查詢。
- 我負責玩家申訴系統。
- 我建立 `log_reel` pipeline。
- 我設計遊戲結算或 wallet correctness。
- 我改善查詢效能。
- 我修過 `serial_id` update miss 或 provider settlement issue。

可以說：

- 我分析過這條 flow。
- 我能說明它在 production troubleshooting 裡的位置。
- 我能拆出查詢 source 與交易 truth 的差異。
- 我能指出下一步要補哪些 evidence 才能升級履歷 claim。

## 下一步

只推薦一件事：

```text
payment Step 1
```

原因：

- 本 flow Step 5 已完成。
- 面試素材已整理，正式履歷 / 自傳不更新。
- 下一步應轉向更高價值的金流 source of truth，而不是繼續在 app_bi 查詢頁硬挖。
