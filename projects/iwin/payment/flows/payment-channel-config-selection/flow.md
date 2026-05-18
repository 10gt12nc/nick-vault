# iwin payment payment-channel-config-selection

完成狀態：Step 3 已完成
掃描等級：Level 2 Flow 深掃
證據層級：專案存在 / code-backed；Nick 貢獻待確認

本 flow 研究的是：

> 玩家進入充值 / 提現頁時，payment runtime 如何依玩家、channel、device、玩家層級、Redis 設定與商戶狀態，決定「可以看到哪些支付方式、哪些商戶檔位、哪些提現方式與限制」。

## 1. 閱讀定位

這條 flow 不是金流扣款 / 上分主線，而是金流入口前的 eligibility / runtime config selection。

| 層次 | 代表 code | 本 flow 判斷 |
| --- | --- | --- |
| 玩家 API 入口 | `PayPublicController#/payment/list`、`/payment/detail`、`/payment/public/withdrawConfig` | 前端取得可用支付方式、支付詳情、提現設定 |
| runtime 選擇邏輯 | `PayTypeServiceImpl#fetchPayTypeList`、`#fetchPayTypeDetail`、`#withdrawConfig` | 依玩家層級、channel、device、商戶 / 提現設定過濾 |
| Redis projection | `BaseServiceImpl#getPayTypeList`、`#getMerchantList`、`#getConvenientPayList`、`#getVipList`、`#getWithdrawConfig` | 優先讀 Redis；部分 key 空時 fallback DB 並寫回 Redis |
| 後台同步入口 | `app_bi RedisSynchronize`、`Base::addSettings` | 從 MySQL 設定表同步 `settings` hash 到各 channel Redis DB |

這條 flow 的 Senior / Owner 價值在於：DB 設定、Redis projection、runtime consumer 三者是否一致；以及玩家層級 / device / channel 過濾錯誤時，會不會造成支付入口不可用或錯商戶曝光。

## 2. 白話導讀

玩家打開充值頁時，後端不是直接把所有支付方式都丟給前端。

系統會先查玩家資料，算出玩家層級，再依 channel 找對 Redis DB。接著從 Redis 讀：

- `payTypeList`：有哪些支付類型。
- `merchantList`：哪些商戶、檔位、device、玩家層級可以用。
- `merchantAccountList`：商戶帳號 / provider id 對照。
- `convenientPay`：快捷支付 / 線下銀行卡。
- `vipPay`：銀商 / VIP 充值聯絡方式。
- `paySetting`：提現方式、單筆上下限、打碼倍數、保底限制、客服 URL。

如果設定同步錯、Redis 是舊的、玩家層級對不上，玩家最直覺會遇到：

- 充值頁沒有支付方式。
- 同一支付類型看不到可用商戶。
- iOS / Android 顯示不一致。
- 玩家層級不該看到的商戶被顯示。
- 提現最低 / 最高金額或綁定狀態顯示錯。

## 3. Code 分層對照

| 分層 | Code / key | 已確認行為 |
| --- | --- | --- |
| Route / API | `/payment/list` | 取得支付方式列表 |
| Route / API | `/payment/detail` | 取得指定 pay code 的商戶 / VIP / 快捷支付詳情 |
| Route / API | `/payment/public/withdrawConfig` | 取得玩家可用提現方式與限制 |
| Controller | `PayPublicController` | 只做參數整理後轉 service |
| Service | `PayTypeServiceImpl#fetchPayTypeList` | 查玩家層級，讀 payment config，過濾可見支付類型 |
| Service | `PayTypeServiceImpl#fetchPayTypeDetail` | 依 pay code 回傳快捷支付、VIP 充值或一般商戶列表 |
| Service | `PayTypeServiceImpl#withdrawConfig` | 讀提現設定並遮罩玩家已綁定帳號 |
| Redis / DB helper | `BaseServiceImpl#getPayTypeList` 等 | 優先讀 Redis，部分 key 空時 fallback DB |
| Redis hash | `settings` | 存放 payment runtime config projection |
| Redis fields | `payTypeList`、`merchantList`、`merchantAccountList`、`convenientPay`、`vipPay`、`paySetting`、`layers` | payment / app_bi 共用設定 key |
| DB tables | `paytype_channel`、`paytype`、`merchant`、`merchant_account`、`convenientPay`、`vipPay`、`settings`、`userLayer`、`log_user` | 設定來源與玩家層級來源 |
| app_bi sync | `RedisSynchronize#savePayClientSettingToRedis` 等 | 將 MySQL 設定同步到 Redis |

## 4. 最小架構圖

```mermaid
flowchart TD
  Admin["app_bi 後台設定"]
  DB["MySQL 設定表"]
  Sync["RedisSynchronize / Base::addSettings"]
  Redis["Redis settings hash / channel DB"]
  API["payment PayPublicController"]
  Service["PayTypeServiceImpl"]
  User["log_user / 玩家層級"]
  Client["玩家充值 / 提現頁"]

  Admin --> DB
  Admin --> Sync
  Sync --> Redis
  Client --> API
  API --> Service
  Service --> User
  Service --> Redis
  Service --> Client
```

## 5. 正常流程圖

```mermaid
sequenceDiagram
  participant C as Client
  participant API as PayPublicController
  participant S as PayTypeServiceImpl
  participant U as log_user
  participant R as Redis settings
  participant D as DB fallback

  C->>API: /payment/list(channel, uid, device)
  API->>S: fetchPayTypeList
  S->>U: calcPlayerLayer(uid)
  S->>R: get payTypeList / merchantList / convenientPay / vipPay
  alt Redis key empty
    S->>D: fallback query settings tables
    S->>R: write projection back
  end
  S->>S: filter by channel / device / player layer
  S-->>C: payment types

  C->>API: /payment/detail(payCode)
  API->>S: fetchPayTypeDetail
  S->>R: get merchantAccountList / activity / merchant / vip / convenient config
  S->>S: choose detail list by payCode
  S-->>C: merchant or VIP or convenient-pay detail
```

## 6. 正常流程逐步說明

1. 前端帶 `channel`、`uid`、`device` 呼叫 `/payment/list`。
2. `PayTypeServiceImpl#fetchPayTypeList` 切到 `bi_log`，用玩家 uid 計算或取得玩家層級。
3. 用 `Utils.getChannelId(uid)` 決定 Redis DB。
4. 從 Redis `settings` 讀 `payTypeList`、`merchantList`、`convenientPay`、`vipPay`。
5. 快捷支付看 `convenientPay.channel` 是否符合玩家 channel 或全渠道，並檢查玩家層級。
6. VIP / 銀商支付只要 `vipPay` 有資料就顯示該支付類型。
7. 一般商戶支付會檢查商戶 pay type、device 是否符合、玩家層級是否在 merchant layers。
8. `/payment/detail` 會依 pay code 分三種：快捷支付、VIP 充值、一般商戶。
9. 一般商戶詳情會補 `merchantAccountList` 裡的 merchant id，並依商戶檔位分組。
10. `/public/withdrawConfig` 會依玩家 channel 讀 `paySetting`，把提現設定轉成玩家可看的金額單位，並遮罩已綁定帳號。

## 7. Senior / Owner 深度區

### 7.1 Source of truth

設定來源分成三層：

| 層 | 角色 | 風險 |
| --- | --- | --- |
| MySQL 設定表 | 後台可維護的 source of truth | 改了 DB 不代表 runtime 已看到 |
| Redis `settings` projection | payment runtime 的主要讀取來源 | 同步失敗 / partial sync 會造成舊設定 |
| payment runtime filter | 最終決定玩家看到什麼 | 過濾條件錯會讓正確設定也顯示錯 |

這裡不能只說「設定存在」。Owner 要看 DB -> Redis -> payment runtime 三段是否一致。

### 7.2 Consistency

已確認：

- app_bi 會同步 `payTypeList`、`merchantList`、`merchantAccountList`、`convenientPay`、`vipPay`、`paySetting`、`layers` 到 Redis。
- payment runtime 會從 Redis 讀這些 key。
- `merchantList` 在 app_bi 同步時只同步 `status=1`，payment runtime 讀取後也再過濾 `status=1`。
- 玩家層級是 payment selection 的核心條件。

待確認：

- 同步多個 key 時是否有 atomic version / batch id。
- 多 channel Redis DB 是否可能只同步部分成功。
- payment instance 是否有本地 cache 或只讀 Redis。
- Redis key 空時 fallback DB 後，是否會立刻回傳新資料。

### 7.3 Failure window

| 斷點 | 可能結果 | Evidence | Owner 要補 |
| --- | --- | --- | --- |
| DB 已改，Redis 未同步 | 玩家仍看到舊商戶 / 舊檔位 | app_bi 手動 sync projection | waitSyn dashboard、同步 SLA |
| Redis 只同步部分 key | `payTypeList` 有新 code，但 `merchantList` 沒對應商戶 | 多 key 分散寫入 | versioned config / batch validation |
| `payTypeList` cache miss fallback | fallback DB 後可能回傳原本空 list | `getPayTypeList` 寫 Redis 後未重新 assign return list | cold-cache test / 修正回傳 |
| `merchantAccountList` 與 `merchantList` 不一致 | 前端看到商戶但缺 merchant id | detail 用 merchant name + channel 對應 | sync validation |
| 玩家層級變動但 Redis / `log_user` 未同步 | 玩家看錯支付方式 | runtime 用 `log_user.userLayer` | layer update audit |
| `paySetting` 空或格式錯 | 提現配置錯或 exception | `getWithdrawConfig` 讀 settings JSON | schema validation / default safe response |

### 7.4 Idempotency / retry

這條 flow 沒有直接 money mutation，但它仍需要「設定同步 idempotency」：

- 同一份設定重複同步應得到同一份 Redis projection。
- 部分同步失敗時，要知道哪些 channel / key 失敗。
- key 空時 fallback DB 不應讓第一個請求拿到空結果。
- runtime 讀到不完整 config 時，應 fail closed，例如不顯示不可確認商戶，而不是曝光錯商戶。

### 7.5 Observability

已看到：

- app_bi 同步 method 會寫 `opLog`。
- 部分同步成功會 `removeWaitSync`。
- payment list/detail 為空時會 log channel、玩家層級、uid。

不足：

- 未看到 config version / hash。
- 未看到每個 channel / key 的同步結果表。
- 未看到 payment runtime 對 Redis key miss / schema mismatch 的 structured alert。
- 未看到「某支付方式曝光給哪些層級 / device」的線上診斷工具。

### 7.6 Owner decision

這條 flow 面試時不要講成「後台同步 Redis」而已，要講成：

1. MySQL 是設定來源，Redis 是 runtime projection，payment API 是最後 eligibility decision。
2. 支付列表和支付詳情要分兩層：先決定支付類型，再決定該類型下有哪些商戶 / VIP / 快捷支付 detail。
3. 玩家層級、channel、device 是核心 filter，不可只看商戶 status。
4. config sync 要有版本、驗證與回滾，不然 partial sync 會讓玩家看到不一致入口。
5. 這條 flow 不直接改錢，但會決定玩家能不能進入 money flow，所以仍屬於 payment correctness 的前置條件。

## 8. 面試 / 履歷邊界摘要

可作面試素材：

- Runtime config selection：DB 設定、Redis projection、payment API eligibility。
- 玩家層級、device、channel、商戶 status 與商戶帳號對應的交叉過濾。
- Redis projection partial sync、cold-cache fallback、config versioning 的 owner decision。

目前不可放正式履歷：

- 未找到 Nick 直接修改這條 payment list/detail/withdrawConfig 或 app_bi payment config sync 主線的 path-specific evidence。
- app_bi 設定同步相關 history 目前主要是 gill / arnold。
- payment 相關 history 中 `10gt12nc` 只支撐 provider request / insert consistency 題材，不支撐本 flow owner claim。

## 9. Step 3 結論

`payment-channel-config-selection` 已完成 Step 3。這條 flow 的核心不是商戶 CRUD，而是：

```text
app_bi MySQL 設定
-> Redis settings projection
-> payment runtime 依玩家 / channel / device / layer 過濾
-> 前端看到可用支付方式與提現設定
```

下一步建議做 Step 4，把這條 runtime config flow 轉成可面試 case，重點放在 config consistency、partial sync、cold-cache fallback 與 fail closed。

## 10. 下一步建議

只推薦一件事：

```text
iwin payment payment-channel-config-selection Step 4
```
