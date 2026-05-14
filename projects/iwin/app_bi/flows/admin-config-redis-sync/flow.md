# app_bi - 後台設定同步 Redis

狀態: Step 3 單條 flow 深挖
掃描等級: Level 2 Flow 深掃
對標標籤: Senior Backend / Platform Backend / System Owner

## 本次結論

`admin-config-redis-sync` 不是單一功能，而是一組後台「同步到正式服」操作。它把 MySQL 後台設定投影到 Redis，讓支付、活動、遊戲清單、渠道、客戶端設定等 runtime 或 API 讀取。

這條 flow 的 Senior 價值在於:

- 它是 control plane -> runtime projection。
- 它有 DB / Redis / GM command / 前端按鈕 / API consumer 的跨層關係。
- 它有真實 bug / feature commit 證明 Redis 欄位缺漏會造成 runtime 設定不完整。

但這條目前只確認到 `app_bi` 發送端與部分 `app_bi` API 讀取端。尚未掃 `game_api`、`game_job`、`iwin_gameserver` 等真正 runtime consumer，所以不能包裝成 Nick 主導完整後端設定平台，也不適合直接更新履歷。

## 系統位置

已確認:

- `app_bi` 是 PHP / ThinkPHP 後台與 BI 專案。
- `RedisSynchronize` 是主要設定同步 controller。
- 前端多個頁面有「同步到正式服」按鈕，主要 POST 到 `/admin.php/RedisSynchronize/*`。
- 共用工具 `Base::addSettings()` 會把內容寫入 Redis hash `settings`，可指定 channel DB。
- 部分同步會額外寫入 `clientSettings`、`serviceSettings`、`channel:{code}`、`activity:conf:*`、`Game:List:V2` 等 key。
- 部分同步會呼叫 `sendGmCommand()` 發送 `RELOADCLIENT` 類命令，要求遊戲 / 客戶端重新載入設定。

推測:

- Redis 裡的設定是多個 runtime service 的快速讀取來源。
- 設定同步後如果沒有 reload / cache invalidation，runtime 可能持續讀舊值。
- 多渠道逐一寫入時，任一 channel 失敗可能造成 partial success。

待確認:

- `game_api` / `game_job` / `iwin_gameserver` 是否直接讀這些 Redis key。
- `sendGmCommand()` 下游 receiver 是否具備 idempotency、retry、ack、reconcile。
- 「同步成功」回應是否足以代表所有 runtime 都已套用新設定。

## 入口

已確認主要入口:

- 支付客戶端設定: `RedisSynchronize::savePayClientSettingToRedis()`
- 商戶設定中心: `RedisSynchronize::saveMerchantSettingToRedis()`
- 使用者層級設定: `RedisSynchronize::saveLayerToRedis()`
- 快捷支付設定: `RedisSynchronize::saveConvenientPaySettingToRedis()`
- 商戶列表管理: `RedisSynchronize::saveMerchantManagerToRedis()`
- 支付提示: `RedisSynchronize::saveProToRedis()` / `NewPay::saveProToRedis()`
- VIP 支付列表: `RedisSynchronize::saveVipPaySettingToRedis()`
- 支付類型: `RedisSynchronize::savePayTypeSettingToRedis()`
- 活動設定: `RedisSynchronize::saveToRedis()`
- 系統設定: `RedisSynchronize::save()`
- 營運 / 訂單 / 提現設定: `RedisSynchronize::saveSettingToRedisPaySetting()`
- 遊戲 / 三方遊戲 V2 設定: `RedisSynchronize::saveSettingsV2ToRedis()`

前端入口不是這次主軸，只作為觸發 evidence。iwin-workspace 舊表列出大量「同步到正式服」按鈕，但本次只把它當參考，不直接搬入新文檔。

## 正常 Flow

### 1. 後台操作觸發同步

營運或管理員在後台頁面點擊「同步到正式服」。前端 JS 發送 POST 到對應 controller，例如 newPay、activityManage、settings、channel 等頁面會打到 `RedisSynchronize` 的不同 method。

已確認:

- newPay 支付相關設定會打 `savePayClientSettingToRedis()`、`saveMerchantSettingToRedis()`、`saveMerchantManagerToRedis()`、`saveConvenientPaySettingToRedis()` 等。
- settings / game settings V2 會打 `saveSettingsV2ToRedis()`。
- 活動管理多個頁面共用 `saveToRedis()`。

### 2. Controller 從 MySQL 讀取設定

同步 method 依設定種類查不同表:

- `payTypeChannel` / `paytype`
- `merchant` / `merchant_account`
- `userLayer`
- `convenientPay`
- `vipPay`
- `settings`
- `activitySetting`
- `third_game_category_list` / `third_game` / `third_platform` / `gamesetting`
- `channel_exts`

已確認:

- 多數查詢會過濾 `status = 1` 或 `status = 0` 的啟用資料。
- `saveMerchantSettingToRedis()` 曾從 `status in [0,1]` 改成只同步 `status = 1`，這是設定投影邊界的真實調整。

### 3. Controller 轉換資料格式

設定通常不是原樣寫 Redis，而是轉成 runtime 需要的 JSON 結構:

- 支付方式會補 `name`、`code`。
- 商戶設定會 decode `gears`。
- 活動設定會依活動 type 組成不同 `param` / `clientParam` / `hideParam`。
- 遊戲 V2 設定會合併自研遊戲與三方遊戲，並處理分類排序、平台狀態、第三方 game id mapping。
- 營運支付設定會拆成 `paySetting` 與 `paySetting:{Type}`。

Owner 要看的不是「有沒有寫 Redis」，而是:

- 投影資料是否完整。
- 是否只包含啟用資料。
- 是否有欄位遺漏。
- 是否能從 DB 重新建出同一份 Redis state。

### 4. 寫入 Redis

已確認寫入型態:

- `settings` hash: `payTypeList`、`merchantList`、`merchantAccountList`、`notEmbeddedMerchantList`、`layers`、`convenientPay`、`vipPay`、`payTypeConfig`、`paySetting`、`official` 等。
- channel DB: 多數 method 會依 channel id `select()` 後寫入。
- `clientSettings` / `serviceSettings`: 系統設定依 label 額外寫一份。
- `channel:{code}` string: 營運 / 渠道資訊會改寫 channel JSON。
- `activity:conf:{channel}` hash: 活動設定會先刪再重建。
- `Game:List:V2` / `Game:List:ThirdIdList` / `Game:List:ThirdIdMapping` / `Game:ThirdPlatform:{platform}`: 三方遊戲與遊戲清單設定。

### 5. 移除待同步提示 / 記錄操作 log

多數同步成功後會呼叫:

- `Base::removeWaitSync(__CLASS__, '{method}')`
- `Common::opLog(...)`

已確認:

- `waitSyn` hash 是「有哪些設定待同步」的提示來源。
- `opLog` 能留下後台操作紀錄。

待確認:

- `opLog` 是否足以追到同步前後差異。
- 是否有操作者、channel、設定 key、before / after 的完整 audit。

### 6. 通知 runtime reload

部分 method 會送出:

```text
cmd = RELOADCLIENT
type = 2 或 3
notice = 1
```

已確認:

- 活動設定 `saveToRedis()` 會寫 Redis 後呼叫 `sendGmCommand()`，失敗會回傳「通知客户端更新失败」。
- 營運 / 訂單 / 提現設定 `saveSettingToRedisPaySetting()` 會呼叫 `sendGmCommand()`，但即使回傳錯誤，目前只記 log，最後仍回 `成功`。

推測:

- 不同 setting 對 reload 的依賴程度不同。
- 某些 API 是每次讀 Redis，可能不需 reload；某些 runtime 長駐服務可能需要 reload。

待確認:

- 哪些 key 是 API 即時讀取，哪些 key 是服務啟動或 cache 後讀取。
- GM command 失敗時是否有人工補償或重送機制。

## State Transition

這條 flow 的狀態不是單一 DB row，而是「MySQL truth -> Redis projection -> runtime applied」。

```text
未修改
-> MySQL 設定已修改，Redis 尚未同步
-> Redis 已寫入，但 runtime reload 未確認
-> runtime 已讀到新設定
```

已確認:

- code 有 `waitSyn` 機制，代表系統承認「DB 已改但 Redis 未同步」這個中間狀態。
- 成功同步後會移除 waitSyn。

推測:

- `waitSyn` 是 UI 提醒，不等於強一致機制。
- 如果 Redis 寫入部分成功、reload 失敗、或下游讀取舊快取，使用者仍可能看到舊設定。

## Failure Window

### 1. DB 已改，但 Redis 未同步

風險:

- 後台看到新設定，runtime 還用舊設定。
- 支付方式、商戶、活動、渠道資訊不一致。

已確認 evidence:

- `Base::addWaitSync()` / `removeWaitSync()` 以 Redis `waitSyn` hash 標記待同步。

### 2. Redis 部分寫入成功

風險:

- 多 channel 迴圈寫入時，前幾個 channel 成功，後面 channel 失敗。
- 多 key 組合寫入時，`settings` 已更新，但 `clientSettings` / `channel:{code}` 未更新。

已確認 evidence:

- `saveSettingToRedisPaySetting()` 會同時寫 `settings.paySetting`、`settings.paySetting:{Type}`、`channel:{code}`、`settings.official`、`clientSettings.official`。
- `saveToRedis()` 會先刪 `activity:conf:*` 再逐筆寫入。

### 3. Redis 寫入成功，但 runtime 未 reload

風險:

- Redis 是新資料，但服務仍使用本地記憶體或舊快取。
- 後台顯示成功，但實際用戶端未生效。

已確認 evidence:

- `saveToRedis()` 會根據 `sendGmCommand()` 結果決定是否回失敗。
- `saveSettingToRedisPaySetting()` 只記錄 GM command 錯誤，最後仍回成功。

### 4. 欄位投影遺漏

風險:

- DB 有新欄位，但 Redis JSON 沒寫入，runtime 表現像「設定同步成功但某欄位不見」。

已確認 evidence:

- `RD-24` commit 補了營運配置同步的 `whatsapp`。
- `bugs/channel_publish` commit 補了渠道商同步正式服 Redis 的 `whatsapp`。
- `RD-152` commit 補了 `notEmbeddedMerchantList` Redis key。
- `feature/merchantSettingToRedis` commit 把商戶設定同步改成只寫 `status = 1`。

## Owner Decision

這條 flow 最值得練的是「設定投影怎麼保證可恢復、可觀測、可回滾」。

### 目前可從 code 看出的做法

- 用 MySQL 當設定持久化來源。
- 手動點同步，把設定投影到 Redis。
- 用 `waitSyn` 提醒哪些設定需要同步。
- 用 `opLog` 記錄同步操作。
- 部分設定用 `sendGmCommand()` 通知 runtime reload。

### Senior / Owner 會追問的問題

- Redis 寫入是否需要 atomic swap，而不是先刪再寫？
- 同步結果要不要 per channel / per key 回報？
- GM command 失敗時，是否該回失敗、保留 waitSyn，或進入重試隊列？
- 是否需要 `version`、`updated_at`、`operator`、`checksum` 來比對 MySQL 與 Redis？
- 是否需要 reconcile job 定期比對 DB 與 Redis projection？
- 高風險設定是否需要 approval / audit before-after diff？

## 履歷邊界

目前不可寫:

- 不可寫 Nick 主導 `app_bi` Redis 同步架構。
- 不可寫 Nick 完成設定中心、配置平台或全鏈路 owner。
- 不可寫改善百分比或 production incident 修復，除非補到本人 MR / ticket / commit。

可作為學習與面試素材:

- 後台設定同步到 Redis 的 production flow 分析。
- control plane / runtime projection 的一致性風險。
- Redis projection、reload、audit、reconcile 的 owner 思考。

## 下一步

2026-05-14 專案層級狀態:

- `app_bi` Step 1 已拆成獨立 `step1-candidate-flows.md`。
- Step 2 尚待依新 Step 1 同步候選排序與證據層級。
- 因此目前專案下一步先做 `app_bi Step 2 重整`。

本 flow 在 Step 2 重整乾淨後，建議下一步仍是:

```text
app_bi admin-config-redis-sync Step 4
```

原因:

- Step 3 已能形成一條完整分析主線。
- 不需要先自創「下游定位」新任務。
- Step 4 會把這條 flow 轉成保守面試 case，但仍不更新履歷。
