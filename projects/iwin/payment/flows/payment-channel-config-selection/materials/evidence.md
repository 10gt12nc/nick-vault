# evidence

## 本次掃描定位

- 任務：`iwin payment payment-channel-config-selection Step 3`。
- 日期：2026-05-18。
- 掃描等級：Level 2 Flow 深掃。
- 證據層級：`專案存在 / code-backed`；Nick 貢獻 `待確認`。

## 自動重讀

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`

已重讀 vault：

- `projects/iwin/payment/README.md`
- `projects/iwin/payment/step1-candidate-flows.md`
- `projects/iwin/payment/step2-flow-comparison.md`
- `projects/iwin/app_bi/flows/admin-config-redis-sync/flow.md`
- `projects/iwin/app_bi/flows/admin-config-redis-sync/materials/evidence.md`

## source repo 狀態

### `/Users/nick/Git/iwin/payment`

- 已執行 `git fetch --all --prune` 更新 remote refs。
- 本機分支：`k3s`
- local HEAD：`bb7794e55386d914801887cc43b53d263c74d3c3`
- `origin/k3s` HEAD：`57306977c0e2f82f39becdf9076ce3e2a73952b8`
- ahead / behind：`0 / 1`
- 工作樹狀態：既有未追蹤 `payment/src/main/java/cn/com/payment/service/impl/.DS_Store`；本輪只讀未動。
- 判斷：本機工作樹落後遠端 1 commit，本輪不 pull、不 checkout、不改公司 repo；Step 3 以 `origin/k3s:<path>` 讀最新遠端內容。

### `/Users/nick/Git/iwin/app_bi`

- 已執行 `git fetch --all --prune` 更新 remote refs。
- 本機分支：`main`
- local HEAD：`4a206a28ab8f5be4329602cdc510ee9ea41efb25`
- `origin/main` HEAD：`fd9881fc417e01f960d758b4b91ba1a10b507855`
- ahead / behind：`0 / 4`
- 本輪未 pull、未 checkout、未改工作樹。
- 判斷：app_bi 本機仍落後 4 commits，後台同步入口以 `origin/main:<path>` 補判讀。

## 已掃 code path

payment：

- `payment/src/main/java/cn/com/payment/controller/PayPublicController.java`
- `payment/src/main/java/cn/com/payment/service/impl/PayTypeServiceImpl.java`
- `payment/src/main/java/cn/com/payment/service/impl/withdraw/BaseServiceImpl.java`
- `payment/src/main/java/cn/com/payment/service/IPayPublicService.java`
- `base/module/src/main/java/cn/com/constant/Constant.java`
- `payment/src/main/java/cn/com/payment/vo/PayTypeVO.java`
- `payment/src/main/java/cn/com/payment/vo/PayTypeChannelVO.java`
- `payment/src/main/java/cn/com/payment/vo/MerchantVO.java`
- `payment/src/main/java/cn/com/payment/vo/MerchantDTO.java`
- `payment/src/main/java/cn/com/payment/vo/ConvenientPayVO.java`
- `payment/src/main/java/cn/com/payment/vo/VipVO.java`
- `payment/src/main/java/cn/com/payment/vo/WithdrawConfigVO.java`
- `payment/src/main/java/cn/com/payment/mongo/entity/BankCardBlackEntity.java`
- `payment/src/main/java/cn/com/payment/mongo/entity/ZfbBlackEntity.java`
- `payment/src/main/java/cn/com/payment/mongo/entity/PixBlackEntity.java`

app_bi：

- `app/admin/controller/RedisSynchronize.php`
- `app/admin/controller/NewPay.php`
- `app/admin/controller/SettingConfig.php`
- `app/common/controller/Base.php`
- `public/views/newPay/**` 相關 payment config 頁面線索

## 已確認 facts

- `PayPublicController#/payment/list` 會呼叫 `PayTypeServiceImpl#fetchPayTypeList`。
- `PayPublicController#/payment/detail` 會呼叫 `PayTypeServiceImpl#fetchPayTypeDetail`。
- `PayPublicController#/payment/public/withdrawConfig` 會呼叫 `PayTypeServiceImpl#withdrawConfig`。
- `fetchPayTypeList` 會先取得玩家層級，再讀 Redis DB 中的 `payTypeList`、`merchantList`、`convenientPay`、`vipPay`。
- `fetchPayTypeList` 會依快捷支付 channel / layers、一般商戶 device / layers / pay type 過濾。
- `fetchPayTypeDetail` 會依 pay code 分成快捷支付、VIP 充值、一般商戶。
- 一般商戶 detail 會讀 `merchantAccountList`，以 merchant name + channel 補 merchant id。
- 非定額商戶 detail 會附 `activity` 設定。
- `withdrawConfig` 會讀 `paySetting`，遮罩玩家已綁定的 zfb / pix / bank account，並做金額單位轉換。
- `Constant.SETTINGS` 是 Redis hash 名稱 `settings`。
- payment runtime 讀取的 Redis fields 包含 `payTypeList`、`merchantList`、`merchantAccountList`、`convenientPay`、`vipPay`、`paySetting`、`layers`。
- app_bi `RedisSynchronize#savePayClientSettingToRedis` 會將 `paytype_channel` + `paytype` 投影成 Redis `settings/payTypeList`。
- app_bi `saveMerchantSettingToRedis` 會同步 `merchantList`，並先呼叫 `saveMerchantManagerToRedis` 同步 `merchantAccountList`。
- app_bi `saveConvenientPaySettingToRedis` 會同步 `convenientPay`。
- app_bi `saveVipPaySettingToRedis` 會同步 `vipPay`。
- app_bi `saveSettingToRedisPaySetting` 會同步 `paySetting`。
- app_bi `saveLayerToRedis` 會同步 `layers` 與 `defaultLayer`。

## path history evidence

payment：

- `c92a8c2` / Derek：`feat(#充值):修正redis paytypelist儲存格式`，新增 / 調整 `PayTypeChannelVO` 與 `BaseServiceImpl#getPayTypeList` 相關格式。
- `c4d29d3` / Derek：mybatis 切換不同 DB 連線，與此 flow 多 datasource 讀設定 / 玩家資料相關。
- `3259d12`、`e300f70`、`7d4de37` / arnold：Spring Boot / MyBatis Plus 升級相容修改。
- `10gt12nc` 目前只在相關 path 找到 `6539d7a` withdraw insert id copy 修正與 merge commit；不支撐本 flow 直接 claim。

app_bi：

- `ea9bf27` / gill：`saveMerchantSettingToRedis` 改成只存 `status=1`。
- `8ea905d` / gill：商戶列表管理同步新增 Redis key `notEmbeddedMerchantList`。
- `659b23d`、`c9be1be` / gill：paySetting URL 驗證相關。
- 目前未找到 `10gt12nc` 直接修改 app_bi payment config sync / NewPay / SettingConfig 主線。

## 合理推論

- `payment-channel-config-selection` 是 `app_bi/admin-config-redis-sync` 的 payment runtime consumer。
- 此 flow 不直接 money movement，但會決定玩家是否能進入充值 / 提現 money flow。
- `merchantList`、`merchantAccountList`、`payTypeList` 必須互相一致，否則 list/detail 會分裂。
- Redis cold cache 與 projection partial sync 是這條 flow 最重要的 production 風險。
- 目前不適合寫正式履歷 claim，只適合作為 runtime config consistency 面試素材。

## 待確認

- payment instance 是否除了 Redis 外還有本地 cache。
- config sync 是否有 version / batch id / atomic switch。
- Redis key miss 是否有告警。
- `payTypeList` fallback DB 後第一個 request 是否會拿到新資料或仍拿到空 list。
- `merchantAccountList` 與 `merchantList` 是否有同步後 validation。
- `paySetting` JSON schema 是否有後台驗證與 runtime defensive parse。
- 玩家層級變更後多久會影響 payment runtime。

## 未掃

- 未逐檔逐行掃完整 `payment`。
- 未逐 commit diff 展開所有 config / Redis 相關 history。
- 未掃 production Redis 實際資料。
- 未掃 DB schema / migration。
- 未掃前端 app / client 對 `/payment/list` / `/detail` 的展示邏輯。
- 未確認 Nick 本人 MR / ticket / production issue。

## Step 3 結論

- 本 flow 已完成 Step 3。
- 已建立 payment runtime config selection 的 code-backed 主線。
- 不更新正式履歷 / 自傳。
- 當時下一步建議轉 Step 4，把 config consistency / partial sync / cold-cache fallback 轉成面試 case。

## Step 4 補充掃描

- 任務：`iwin payment payment-channel-config-selection Step 4`。
- 日期：2026-05-18。
- 掃描等級：Level 2 Flow 深掃延伸；本輪重點是把 Step 3 code-backed flow 轉成面試 case，不新增未確認履歷 claim。

### Step 4 自動重讀

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`

已重讀本 flow：

- `flow.md`
- `career-interview.md`
- `materials/evidence.md`
- `materials/interview.md`
- `materials/decision-notes.md`
- `materials/claim-boundary.md`

### Step 4 source repo 狀態

`/Users/nick/Git/iwin/payment`：

- 已執行 `git fetch --all --prune` 更新 remote refs。
- 本機分支：`k3s`
- local HEAD：`bb7794e55386d914801887cc43b53d263c74d3c3`
- `origin/k3s` HEAD：`cd03174b60fefa0c7f312912c179a3b6b9818664`
- ahead / behind：`0 / 2`
- 工作樹狀態：既有未追蹤 `payment/src/main/java/cn/com/payment/service/impl/.DS_Store`；本輪只讀未動。
- 判斷：本機工作樹落後遠端 2 commits，本輪不 pull、不 checkout、不改公司 repo；Step 4 沿用 Step 3 已讀 code path，並以最新 `origin/k3s` path history 補核對。

`/Users/nick/Git/iwin/app_bi`：

- 已執行 `git fetch --all --prune` 更新 remote refs。
- 本機分支：`main`
- local HEAD：`4a206a28ab8f5be4329602cdc510ee9ea41efb25`
- `origin/main` HEAD：`fd9881fc417e01f960d758b4b91ba1a10b507855`
- ahead / behind：`0 / 4`
- 本輪未 pull、未 checkout、未改工作樹。
- 判斷：app_bi 本機仍落後 4 commits；Step 4 不新增 app_bi code 結論，只使用 Step 3 已確認的 Redis projection 主線。

### Step 4 path history 補核對

payment `origin/k3s` path-specific log 補看到：

- `4a0a261` / 10gt12nc：merge `feature/nimtestpay-dev` into `k3s`。
- `6539d7a` / 10gt12nc：`fix: clear copied order id before withdraw insert`。
- `03c28e3` / 10gt12nc：`fix: clear copied order id before payment insert`。
- `7d4de37` / arnold：Spring Boot / Java / Jakarta 大版本升級。
- `c92a8c2` / Derek：`feat(#充值):修正redis paytypelist儲存格式`。

判斷：

- 新看到的 10gt12nc path log 仍偏 provider request / insert consistency，不足以支撐 Nick 對 `payment-channel-config-selection` 的直接履歷 claim。
- Step 4 保留「專案存在 / code-backed；Nick 貢獻依三層 claim gate 判斷」。

## Step 4 結論

- 本 flow 已完成 Step 4。
- 已補 3 分鐘講法、高頻追問、production 排查順序、Lead / Architect 追問與 owner decision。
- 不更新正式履歷 / 自傳。
- 當時下一步建議做 Step 5：檢查 claim boundary、履歷 / 自傳是否需要更新；以目前 evidence 預期仍不更新正式履歷。

## Step 5 claim gate

- 任務：`iwin payment payment-channel-config-selection Step 5`。
- 日期：2026-05-18。
- 掃描等級：Level 2+ claim gate。
- 目標：確認本 flow 是否能更新正式履歷 / 自傳，並補最終 claim boundary。

### Step 5 自動重讀

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`

已重讀 vault：

- `flow.md`
- `career-interview.md`
- `materials/evidence.md`
- `materials/interview.md`
- `materials/decision-notes.md`
- `materials/claim-boundary.md`
- `projects/iwin/payment/README.md`
- `projects/iwin/payment/step1-candidate-flows.md`
- `projects/iwin/payment/step2-flow-comparison.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/04-interview-casebook.md`
- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`
- `senior-owner-playbook/06-todo.md`

### Step 5 source repo 狀態

`/Users/nick/Git/iwin/payment`：

- 已執行 `git fetch --all --prune` 更新 remote refs。
- 本機分支：`k3s`
- local HEAD：`bb7794e55386d914801887cc43b53d263c74d3c3`
- `origin/k3s` HEAD：`cd03174b60fefa0c7f312912c179a3b6b9818664`
- ahead / behind：`0 / 2`
- 工作樹狀態：既有未追蹤 `payment/src/main/java/cn/com/payment/service/impl/.DS_Store`；本輪只讀未動。
- 判斷：本機工作樹落後遠端 2 commits，本輪不 pull、不 checkout、不改公司 repo；claim gate 以 remote refs / `origin/k3s` path history 判斷。

`/Users/nick/Git/iwin/app_bi`：

- 已執行 `git fetch --all --prune` 更新 remote refs。
- 本機分支：`main`
- local HEAD：`4a206a28ab8f5be4329602cdc510ee9ea41efb25`
- `origin/main` HEAD：`fd9881fc417e01f960d758b4b91ba1a10b507855`
- ahead / behind：`0 / 4`
- 本輪未 pull、未 checkout、未改工作樹。
- 判斷：app_bi 本機仍落後 4 commits，本輪只用 remote refs / path history 判斷，不宣稱本機工作樹最新。

### Step 5 補查 path-specific history

payment 補查：

- `git log --all --author='10gt12nc'` 針對 `PayPublicController.java`、`PayTypeServiceImpl.java`、`BaseServiceImpl.java`、`PayTypeChannelVO.java`、`Constant.java`。
- `git log --all` 同路徑最近 history。
- `git show` 檢查 `03c28e3`、`6539d7a`、`c92a8c2` 的 relevant diff。

app_bi 補查：

- `git log --all --author='10gt12nc'` 針對 `RedisSynchronize.php`、`NewPay.php`、`SettingConfig.php`、`Base.php`、Laravel rewrite 後的 `app/Services/Pay/NewPay/*` 與 manager model。
- `git log --all` 同路徑最近 history。
- `git show` 檢查 `ea9bf27` 的 relevant diff。

### Step 5 已確認

- `10gt12nc` 在 payment 相關 path 有 `03c28e3`、`6539d7a` 與 merge commits。
- `03c28e3` 是充值建單前 `orderVO.setId(null)`，避免 BeanUtil 帶入玩家 id 造成 order insert 主鍵風險。
- `6539d7a` 是提款建單前 `orderInsert.setId(null)`，同樣屬於 order insert consistency。
- 這兩筆支撐 provider request / order insert consistency 題材，不支撐 payment list/detail/config selection claim。
- `c92a8c2` / Derek 才是 Redis `payTypeList` 格式與 `PayTypeChannelVO` 的直接 config evidence。
- app_bi `RedisSynchronize` 相關直接 evidence 目前主要是 gill / arnold，例如 `ea9bf27` 商戶同步只保留 `status=1`。
- 未找到 `10gt12nc` 直接修改 app_bi payment config sync / RedisSynchronize 主線。

### Step 5 claim 結論

- 不更新 `senior-owner-playbook/05-resume-master-zh.md`。
- 不更新 `senior-owner-playbook/08-application-autobiography-zh.md`。
- 可更新 / 保留 `senior-owner-playbook/04-interview-casebook.md` 作面試案例索引。
- 本 flow 最終定位：`專案存在 / code-backed` + `分析素材 / learning-only`。
- Nick 個人貢獻：未確認到可放履歷的直接 evidence。

### Step 5 未掃 / 待確認

- 未逐檔逐行掃完整 payment / app_bi。
- 未逐 commit diff 展開所有 payment config / Redis sync history。
- 未確認 Nick 本人 MR / ticket / production issue / 本人口述。
- 未掃 production Redis / DB 實際設定。

## 下一步

```text
- 全域下一步狀態：目前沒有預設 project flow 下一步；請以 senior-owner-playbook/01-senior-owner-flow-inventory.md 與 senior-owner-playbook/06-todo.md 為準。
```
