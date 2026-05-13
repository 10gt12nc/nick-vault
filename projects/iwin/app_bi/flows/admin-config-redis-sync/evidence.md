# Evidence - app_bi 後台設定同步 Redis

狀態: Step 3 evidence
掃描等級: Level 2 Flow 深掃

## 本次實際掃描範圍

已重讀 KB:

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/CONVENTIONS.md`

已重讀 vault:

- `projects/iwin/app_bi/README.md`
- `projects/iwin/app_bi/step2-flow-comparison.md`
- `projects/iwin/app_bi/flows/point-control-admin-operation/*`
- `senior-owner-playbook/06-todo.md`

已讀 code repo:

- `/Users/nick/Git/iwin/app_bi`

已看 branch / log:

- 目前分支: `main`
- 近期主線 log
- 遠端分支清單
- `RedisSynchronize.php` / `NewPay.php` / `Server.php` / `Channel.php` / views / migrations 相關 path-specific log
- 重要 commit diff:
  - `82d2419 feat(#bugs/channel_publish): 修正渠道商同步正式服 redis whatsapp不見的問題`
  - `b141309 feat(#RD-24): 運營配置redis同步加上whatsapp`
  - `8ea905d feat(#RD-152): 商户列表管理同步 新增redis key 无内嵌商户list`
  - `ea9bf27 feat(#saveMerchantSettingToRedis): MerchantSettingToRedis 改成只存status = 1`

已讀 workspace 參考:

- `/Users/nick/Git/iwin/iwin-workspace/docs/專案分析/app-bi-同步到正式服按鈕對照.md`
- `/Users/nick/Git/iwin/iwin-workspace/docs/專案分析/app_bi/kb/catalog/appbi-configuration.md`

未掃:

- 未 checkout 每個遠端分支逐一比對。
- 未逐檔逐行掃完整 `app_bi`。
- 未掃 `game_api`、`game_job`、`iwin_gameserver`、`payment`、`third_games_api` runtime consumer。
- 未確認 Nick 本人 MR / ticket / commit。

## 已確認 Code Evidence

### `RedisSynchronize` 是主要同步 controller

檔案:

- `/Users/nick/Git/iwin/app_bi/app/admin/controller/RedisSynchronize.php`

已確認 method:

- `savePayClientSettingToRedis()` 讀 `PayTypeChannel`，逐 channel 寫 `settings.payTypeList`。
- `saveMerchantSettingToRedis()` 讀 `merchant`，寫 `settings.merchantList`。
- `saveLayerToRedis()` 讀 `userLayer`，寫 `settings.defaultLayer` 與 `settings.layers`。
- `saveConvenientPaySettingToRedis()` 讀 `convenientPay`，寫 `settings.convenientPay`。
- `saveMerchantManagerToRedis()` 讀 `merchant_account`，寫 `settings.merchantAccountList` 與 `settings.notEmbeddedMerchantList`。
- `saveProToRedis()` 讀 `promptmessages`，寫 Redis hash `promptmessages`。
- `saveVipPaySettingToRedis()` 讀 `vipPay`，寫 `settings.vipPay`。
- `savePayTypeSettingToRedis()` 讀 `paytype`，寫 `settings.payTypeConfig`。
- `saveToRedis()` 讀 `activitySetting`，寫 `activity:conf:{channel}`，並呼叫 `sendGmCommand()`。
- `save()` 讀 `settings`，寫 `clientSettings`、`serviceSettings`、`settings`、`center`、`gameSocket:*` 等。
- `saveSettingToRedisPaySetting()` 讀 `settings.paySetting`、`settings.official`、`channel_exts`，寫 `settings.paySetting`、`settings.paySetting:{Type}`、`channel:{code}`、`settings.official`、`clientSettings.official`，並呼叫 `sendGmCommand()`。
- `saveSettingsV2ToRedis()` 讀 `third_game_category_list`、`third_game`、`third_platform`、`gamesetting`，寫 `Game:List:V2`、`Game:List:ThirdIdList`、`Game:List:ThirdIdMapping`、`Game:ThirdPlatform:{platform}`。

### Redis 寫入 helper

檔案:

- `/Users/nick/Git/iwin/app_bi/app/common/controller/Base.php`

已確認:

- `Base::getRedisSetting()` 回傳 local Redis。
- `Base::addSettings($key, $content, $channel = 0)` 可選 Redis DB 後寫入 hash `settings`。
- `Base::addWaitSync()` / `Base::removeWaitSync()` 使用 Redis hash `waitSyn` 記錄待同步狀態。

### API consumer evidence

檔案:

- `/Users/nick/Git/iwin/app_bi/app/api/controller/v1/Pay.php`

已確認:

- `Pay::list()` 讀 `settings.payTypeList`、`settings.merchantList`、`settings.convenientPay`。
- `Pay::detail()` 讀 `settings.convenientPay`、`settings.vipPay`、`settings.merchantList`、`settings.payTypeList`。

邊界:

- 這只確認 `app_bi` API 內有讀 Redis 設定。
- 不代表已確認遊戲 runtime / Java service 的 consumer。

### 前端觸發 evidence

已確認:

- `public/views/newPay/payClientSetting.js` 呼叫 `RedisSynchronize/savePayClientSettingToRedis`。
- `public/views/newPay/merchantSetting.js` 呼叫 `RedisSynchronize/saveMerchantSettingToRedis`。
- `public/views/newPay/merchantManager.js` 呼叫 `RedisSynchronize/saveMerchantManagerToRedis`。
- `public/views/newPay/convenientPaySetting.js` 呼叫 `RedisSynchronize/saveConvenientPaySettingToRedis`。
- `public/views/newPay/vipPaySetting.js` 呼叫 `RedisSynchronize/saveVipPaySettingToRedis`。
- `public/views/newPay/payTypeSetting.js` 呼叫 `RedisSynchronize/savePayTypeSettingToRedis`。
- `public/views/settings/settingsV2.js` / `gameCategorySettings.js` / `thirdGameSettings.js` / `thirdPlatformSettings.js` 呼叫 `RedisSynchronize/saveSettingsV2ToRedis?from=...`。
- 多個 `activityManage` 頁面呼叫 `RedisSynchronize/saveToRedis`。

### Git history evidence

已確認 commit:

- `82d2419`: 渠道商同步正式服 Redis 修正 `whatsapp` 不見問題。
- `b141309`: 營運配置 Redis 同步加上 `whatsapp`。
- `8ea905d`: 商戶列表管理同步新增 `notEmbeddedMerchantList` Redis key。
- `ea9bf27`: `saveMerchantSettingToRedis` 改成只同步 `status = 1`。

解讀:

- 這些 commit 證明設定同步不是純 CRUD，實際會遇到「欄位漏投影」、「runtime Redis key 不完整」、「啟用狀態邊界」問題。
- 目前 commit 作者不是 Nick；不能當成 Nick 實作 claim。

## 已確認 / 推測 / 待確認

### 已確認

- `app_bi` 有大量手動同步 Redis 的後台入口。
- `RedisSynchronize` 是主要 hub。
- 設定從 MySQL 查詢後轉 JSON 寫入 Redis。
- Redis key 包含 `settings`、`clientSettings`、`serviceSettings`、`channel:{code}`、`activity:conf:*`、`Game:List:*` 等。
- 有 `waitSyn` 機制標示待同步。
- 有 `opLog` 記錄同步操作。
- 部分 flow 會發 `sendGmCommand()` 通知 reload。
- git history 有 Redis 同步欄位缺漏和同步邊界修正。

### 推測

- 多數 Redis projection 是 runtime service 的讀取來源。
- `sendGmCommand()` 是通知長駐服務重新讀設定的機制。
- `waitSyn` 主要是提示，不是強一致保障。
- 先刪再寫的活動同步可能有短暫空窗。

### 待確認

- 下游 `sendGmCommand()` receiver。
- runtime 是否每次讀 Redis 或有本地 cache。
- GM command 失敗時是否有 retry / compensation。
- Redis 寫入失敗是否會留下 partial state。
- 是否有 DB vs Redis reconcile job。
- Nick 實際參與程度。

## Secret / 安全檢查

本文件只記錄:

- repo path
- class / method
- table / Redis key 名稱
- commit hash 與 commit message

未寫入:

- token
- password
- private key
- internal IP
- production URL
- 客戶資料
- Redis 實際連線資訊
