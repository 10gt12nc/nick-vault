# app_bi - admin-config-redis-sync

更新時間：2026-05-14
Step：3 單條 flow 深挖重整
掃描等級：Level 2 Flow 深掃
證據層級：專案存在 / code-backed；Nick 貢獻待確認
格式狀態：舊平鋪格式可沿用，尚未遷移到 `materials/`

## 本次重整結論

`admin-config-redis-sync` 是 `app_bi` 後台把 MySQL 設定投影到 Redis 的 control plane flow。它不是單純按鈕或 CRUD，而是：

```text
後台設定變更
-> 人工按同步到正式服
-> 從 MySQL 讀設定
-> 組成 runtime 需要的 JSON / hash / key
-> 寫 Redis
-> 部分設定送 GM reload command
-> runtime / API 讀 Redis projection
```

這條 flow 的 Senior / Owner 價值在於：

- DB source of truth 與 Redis projection 的一致性。
- 多 channel / 多 key 同步時的 partial success。
- 欄位投影遺漏造成 runtime 設定不完整。
- reload command 失敗時，後台是否仍回成功。
- `waitSyn` / `opLog` 是否足以支援排查與人工補償。

但邊界要講清楚：本次只確認到 `app_bi` 後台同步端與部分 `app_bi` API 讀取端；尚未掃 `game_api`、`game_job`、`iwin_gameserver`、`payment`、`third_games_api` 等下游 runtime consumer。因此不能寫成 Nick 主導設定中心、完整 runtime config owner，或改善 production 指標。

## 本次自動重讀與掃描範圍

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`

已重讀 vault：

- `projects/iwin/app_bi/README.md`
- `projects/iwin/app_bi/step1-candidate-flows.md`
- `projects/iwin/app_bi/step2-flow-comparison.md`
- `projects/iwin/app_bi/flows/admin-config-redis-sync/flow.md`
- `projects/iwin/app_bi/flows/admin-config-redis-sync/evidence.md`
- `projects/iwin/app_bi/flows/admin-config-redis-sync/decision-notes.md`
- `projects/iwin/app_bi/flows/admin-config-redis-sync/claim-boundary.md`

已看 source repo：

- `/Users/nick/Git/iwin/app_bi`
- 目前分支：`main`
- 遠端分支清單：已列出但未 checkout 逐一比對。
- 近期主線 log：已看。
- path-specific log：已看 `RedisSynchronize.php`、`Base.php`、`Pay.php`、`NewPay.php`、`Server.php`、`Channel.php`、`public/views/newPay/**`、`public/views/settings/**`、`db/migrations/**`。

已看重要 diff：

- `82d2419 feat(#bugs/channel_publish): 修正渠道商同步正式服 redis whatsapp不見的問題`
- `b141309 feat(#RD-24): 運營配置redis同步加上whatsapp`
- `8ea905d feat(#RD-152): 商户列表管理同步 新增redis key 无内嵌商户list`
- `ea9bf27 feat(#saveMerchantSettingToRedis): MerchantSettingToRedis 改成只存status = 1`

未掃 / 待確認：

- 未 checkout 每個遠端分支。
- 未逐檔逐行掃完整 `app_bi`。
- 未逐 commit diff 掃所有相關 commit。
- 未掃下游 runtime repo：`game_api`、`game_job`、`iwin_gameserver`、`payment`、`third_games_api`。
- 未確認 Nick 本人 MR / ticket / commit / production issue。

## 舊文件狀態判斷

| 文件 | 狀態 | 判斷 |
| --- | --- | --- |
| `flow.md` | 本次重整 | 原本方向可用，但太快導向 Step 4，且掃描範圍與邊界應放回主報告 |
| `evidence.md` | 需同步 | 證據方向可用，本次補上最新重整範圍 |
| `decision-notes.md` | 可沿用 | 技術硬底子方向正確，可在 Step 4 前暫不擴寫 |
| `claim-boundary.md` | 可沿用 | 已明確不寫履歷、不說主導 |

## 系統位置

已確認：

- 產品：iwin
- 專案：`app_bi`
- 類型：PHP / ThinkPHP 後台與 BI / control plane
- 主要 controller：`app/admin/controller/RedisSynchronize.php`
- 共用 Redis helper：`app/common/controller/Base.php`
- 部分讀取端：`app/api/controller/v1/Pay.php`
- 前端觸發：`public/views/newPay/**`、`public/views/settings/**`、多個 `activityManage` 頁面

推測：

- Redis projection 會被 payment / game / client / runtime service 讀取。
- 不同設定的 runtime 讀取方式可能不同：有些 API 每次讀 Redis，有些服務可能需要 reload。

待確認：

- 下游 runtime 是否直接讀這些 key。
- `sendGmCommand()` receiver 在哪個 repo。
- reload 是否有 ack、retry、idempotency 或 reconcile。

## Code 路徑

主要寫入端：

- `app/admin/controller/RedisSynchronize.php`
- `app/common/controller/Base.php`

相關後台設定入口：

- `app/admin/controller/NewPay.php`
- `app/admin/controller/Server.php`
- `app/admin/controller/Channel.php`

部分讀取端：

- `app/api/controller/v1/Pay.php`

前端觸發入口：

- `public/views/newPay/payClientSetting.js`
- `public/views/newPay/merchantSetting.js`
- `public/views/newPay/merchantManager.js`
- `public/views/newPay/convenientPaySetting.js`
- `public/views/newPay/vipPaySetting.js`
- `public/views/newPay/payTypeSetting.js`
- `public/views/settings/settingsV2.js`
- `public/views/settings/gameCategorySettings.js`
- `public/views/settings/thirdGameSettings.js`
- `public/views/settings/thirdPlatformSettings.js`

## 正常 Flow

### 1. 後台設定被修改

已確認：

- 支付、商戶、玩家層級、活動、系統、遊戲清單、三方平台等設定存在於 MySQL。
- 多個管理頁有「同步到正式服」類按鈕。
- 部分 controller 會呼叫 `Base::addWaitSync()` 標記待同步。

推測：

- `waitSyn` 是提醒或 UI 狀態，不是強一致機制。

### 2. 管理員按同步到正式服

已確認：

- `public/views/newPay/payClientSetting.js` 會打 `RedisSynchronize/savePayClientSettingToRedis`。
- `public/views/newPay/merchantSetting.js` 會打 `RedisSynchronize/saveMerchantSettingToRedis`。
- `public/views/settings/settingsV2.js`、`gameCategorySettings.js`、`thirdGameSettings.js`、`thirdPlatformSettings.js` 會打 `RedisSynchronize/saveSettingsV2ToRedis?from=...`。
- 多個活動管理頁共用 `RedisSynchronize/saveToRedis`。

邊界：

- 前端只當觸發 evidence，不把前端按鈕寫成主要成果。

### 3. Controller 從 MySQL 讀取設定

已確認的設定來源包含：

- `payTypeChannel` / `paytype`
- `merchant` / `merchant_account`
- `userLayer`
- `convenientPay`
- `vipPay`
- `promptmessages`
- `settings`
- `activitySetting`
- `channel_exts`
- `third_game_category_list`
- `third_game`
- `third_platform`
- `gamesetting`

重要邊界：

- `saveMerchantSettingToRedis()` 目前只同步 `status = 1` 的 merchant。這是 code-backed 的狀態投影邊界，不是推測。
- 多數同步方法用 `try/catch` 包起來，失敗會回同步失敗；但不同 method 對下游 reload 失敗的處理不一致。

### 4. 組成 Redis projection

已確認：

- 支付方式會補支付名稱與 code 後寫入 `settings.payTypeList`。
- 商戶設定會 decode `gears` 後寫入 `settings.merchantList`。
- 商戶帳號會寫入 `settings.merchantAccountList`。
- 非內嵌商戶名單會寫入 `settings.notEmbeddedMerchantList`。
- 玩家層級會寫入 `settings.defaultLayer` 與 `settings.layers`。
- 便利支付、VIP 支付、支付類型會寫入 `settings.convenientPay`、`settings.vipPay`、`settings.payTypeConfig`。
- 活動設定會寫入 `activity:conf:{channel}`。
- 系統設定會寫入 `clientSettings`、`serviceSettings`、`settings` 等 key。
- 營運 / 提現設定會寫入 `settings.paySetting`、`settings.paySetting:{Type}`、`channel:{code}`、`clientSettings.official`。
- 遊戲 V2 設定會寫入 `Game:List:V2`、`Game:List:ThirdIdList`、`Game:List:ThirdIdMapping`、`Game:ThirdPlatform:{platform}`。

Owner 要看的不是「寫 Redis 成功」而是：

- 投影欄位是否完整。
- 是否只投影應啟用的資料。
- 多 key 是否會部分成功。
- Redis state 能否從 MySQL 重建。
- runtime 是否真的讀到新版本。

### 5. 寫入 Redis

已確認：

- `Base::addSettings($key, $content, $channel)` 會選 Redis DB 後寫入 hash `settings`。
- 多數支付 / 商戶 / 層級設定是逐 channel 寫入。
- `saveToRedis()` 活動設定會先刪舊的 `activity:conf:*`，再重建活動 hash。
- `saveSettingToRedisPaySetting()` 會逐 channel 寫多個 key，並改寫 `channel:{code}`。
- `saveSettingsV2ToRedis()` 會一次寫遊戲清單與三方映射類 key。

推測：

- 多 channel loop 中，如果中途失敗，前面 channel 可能已成功、後面未成功。
- 先刪再寫的 flow 可能有短暫空窗。

### 6. 記錄同步結果

已確認：

- 多數成功後會 `Common::opLog(...)`。
- 多數成功後會 `Base::removeWaitSync(...)`。

待確認：

- `opLog` 是否有 before / after diff。
- 是否能查到每個 channel、每個 key 的同步結果。
- 若部分成功，是否會保留待同步狀態。

### 7. 部分設定通知 runtime reload

已確認：

- `saveToRedis()` 會呼叫 `sendGmCommand()`，並依結果回傳通知客戶端更新成功或失敗。
- `saveSettingToRedisPaySetting()` 會呼叫 `sendGmCommand()`，但即使 server error 或未回應，最後仍回 `成功`。

推測：

- 這表示「Redis 已寫入」與「runtime 已套用」是兩個不同狀態。
- 對某些設定來說，後台同步成功不等於線上已生效。

待確認：

- receiver 是否真的 reload。
- 失敗是否可重送。
- reload 是否 idempotent。
- runtime 是否有本地 cache。

## 資料與狀態

### MySQL

已確認涉及多種設定表：

- 支付 / 商戶 / 支付類型 / 便利支付 / VIP 支付
- 玩家層級
- 活動設定
- 系統設定
- 渠道附加設定
- 遊戲 / 三方遊戲 / 三方平台設定

### Redis

已確認 key / hash 類型：

- `settings`
- `clientSettings`
- `serviceSettings`
- `waitSyn`
- `channel:{code}`
- `activity:conf:{channel}`
- `Game:List:V2`
- `Game:List:ThirdIdList`
- `Game:List:ThirdIdMapping`
- `Game:ThirdPlatform:{platform}`

### GM command

已確認：

- 部分同步會送 `RELOADCLIENT` 類 command。
- 目前本 flow 只確認 sender 端。

未確認：

- receiver。
- ack。
- retry。
- downstream idempotency。

### Audit / log

已確認：

- 多數同步會寫 `opLog`。
- code 也有 `logRecord()` 記錄錯誤或 GM command 結果。

待確認：

- 是否足以還原同步前後差異。
- 是否可用於事故後人工補償。

## State Transition

這條 flow 的狀態不是單一資料表更新，而是多層狀態：

```text
MySQL 設定已修改
-> waitSyn 標示待同步
-> Redis projection 已更新
-> reload command 已送出
-> runtime 已套用新設定
```

已確認：

- `waitSyn` 表示系統承認「DB 設定與 Redis projection 可能不同步」。
- `removeWaitSync()` 代表後台認定同步完成。

推測：

- `removeWaitSync()` 不一定代表所有 runtime 已套用。
- 若 Redis 寫入成功但 reload 失敗，系統可能進入「Redis 新、runtime 舊」的中間狀態。

## Failure Window

### 1. DB 已改，但未同步 Redis

已確認：

- 有 `waitSyn` 機制。

風險：

- 後台看到新設定，runtime 還讀舊 Redis。
- 支付方式、商戶、活動、遊戲清單、渠道客服設定可能不一致。

### 2. Redis 寫入部分成功

已確認：

- 多個 method 逐 channel loop 寫 Redis。
- `saveSettingToRedisPaySetting()` 同步多個 Redis key。

風險：

- A channel 成功、B channel 失敗。
- `settings` 更新成功，但 `clientSettings` 或 `channel:{code}` 未更新。
- 後台只看到「同步成功 / 失敗」單一結果，難知道哪個 key 出問題。

### 3. 先刪後寫的短暫空窗

已確認：

- `saveToRedis()` 活動設定會刪 `activity:conf:*` 後重建。

風險：

- 如果 runtime 在刪除後、重建前讀取，可能看到空設定。
- 如果重建中途失敗，可能留下不完整 activity config。

### 4. 欄位投影遺漏

已確認：

- `82d2419` 與 `b141309` 都補了 `whatsapp` 類欄位投影。
- `8ea905d` 新增 `notEmbeddedMerchantList` Redis key。

風險：

- DB 有欄位，但 Redis projection 沒同步，runtime 表現會像「同步成功但功能缺資料」。
- 新需求如果只改後台 DB / UI，沒有同步 Redis projection，線上可能不生效。

### 5. 狀態投影邊界錯誤

已確認：

- `ea9bf27` 把 merchant 同步條件改成只同步 `status = 1`。

風險：

- 若把停用資料也投影到 runtime，可能出現不該使用的商戶。
- 若過濾條件太嚴，也可能造成應顯示資料不見。

### 6. Reload 失敗但後台仍回成功

已確認：

- `saveSettingToRedisPaySetting()` 會 log GM command 失敗，但最後仍回成功。

風險：

- 管理員以為設定已生效。
- Redis 是新設定，但 runtime 若靠 reload 才套用，可能仍是舊設定。
- 事故排查時，需要分清楚「寫 Redis 成功」與「runtime 套用成功」。

## Owner Decision

如果我是 owner，會先把問題拆成「現在可從 code 確認」與「未掃下游前不能宣稱」。

### 目前可確認的現況

- MySQL 是設定來源。
- Redis 是 runtime / API projection。
- 同步多數靠人工按鈕觸發。
- `waitSyn` 可提醒待同步。
- `opLog` 可留下操作紀錄。
- 部分 reload command 失敗處理不一致。
- commit history 證明欄位漏投影與狀態投影邊界是真實維護問題。

### 我會優先補的設計保護

1. 補同步結果粒度
   - 每個 channel / key 記錄成功或失敗。
   - 失敗時保留待同步狀態。

2. 補 projection version / checksum
   - 每次同步記錄 version、operator、updated_at、checksum。
   - 讓 DB 與 Redis 可以比對。

3. 補 reload ack 邊界
   - 把「Redis 已寫入」和「runtime 已套用」拆成不同狀態。
   - reload 失敗時不要只顯示成功。

4. 高風險 key 避免先刪後寫
   - 活動 / 遊戲清單 / 支付配置可評估 temp key + swap。
   - 降低短暫空窗。

5. 補 reconcile
   - 定期或手動比對 MySQL enabled settings 與 Redis projection。
   - 發現漏欄位、漏 channel、舊資料殘留。

### 暫時不宣稱已完成的能力

- 完整設定中心。
- runtime config owner。
- reload ack / retry / reconcile 已落地。
- production incident 改善。
- Nick 本人開發此 flow。

## 面試與履歷邊界

目前可以作為面試分析素材：

- 我如何從後台同步按鈕追到 MySQL -> Redis projection。
- 我如何辨識設定同步的 partial success、欄位漏投影、reload 失敗與 stale config 風險。
- 我如何把「同步成功」拆成 Redis 已寫入、runtime 已套用、使用者實際看到新設定三個狀態。

目前不可寫進履歷：

- 主導 Redis 設定同步平台。
- 設計完整設定中心。
- 負責全鏈路 runtime config owner。
- 修復 `whatsapp` / `notEmbeddedMerchantList` / merchant status 投影問題。
- 改善同步成功率、延遲、事故率。

證據層級：

- 本 flow 分析：`分析素材 / learning-only`
- code 功能存在：`專案存在 / code-backed`
- Nick 個人貢獻：`待確認`

## Step 5 狀態

已完成 Step 5 履歷 / 自傳更新判定。

結論：

- 不更新正式履歷 / 自傳。
- 本 flow 保留為面試分析素材。
- Nick 個人實作貢獻仍是 `待確認`。
- 若未來要寫入履歷，必須先補 Nick 本人 MR / ticket / commit / production issue / 本人確認。

## 下一步

只推薦一件事：

```text
app_bi point-control-admin-operation Step 5 重整
```

原因：

- `point-control-admin-operation` 已於 2026-05-14 重新完成 Step 4。
- 舊 Step 5 暫不視為最新完成。
- 下一步應重新檢查是否更新履歷 / 自傳。
- 目前預期不更新履歷，除非補到 Nick 本人 evidence。
