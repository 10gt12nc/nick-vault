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
- 下一步建議轉 Step 4，把 config consistency / partial sync / cold-cache fallback 轉成面試 case。

## 下一步

```text
iwin payment payment-channel-config-selection Step 4
```
