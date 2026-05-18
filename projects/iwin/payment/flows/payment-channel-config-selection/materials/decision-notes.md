# decision-notes

## Step 3 技術判斷

`payment-channel-config-selection` 的核心決策不是「Redis 比 DB 快」，而是：payment runtime 用 Redis projection 做玩家可見支付入口的即時判斷，因此 projection 必須可驗證、可回滾、可診斷。

| Decision | 現況 | 風險 | Owner 改善方向 |
| --- | --- | --- | --- |
| DB -> Redis projection | app_bi 將 payment config 寫入 Redis `settings` | DB 新設定不代表 runtime 已套用 | 加同步版本與 per-channel result |
| 多 key 分散同步 | `payTypeList`、`merchantList`、`merchantAccountList`、`vipPay`、`paySetting` 分開寫 | partial sync 導致 list/detail 不一致 | batch validation / readiness check |
| runtime filter | payment 依 channel、device、layers 過濾 | 條件交叉複雜，容易錯曝光或不曝光 | diagnostic API 顯示命中原因 |
| Redis miss fallback DB | 部分 helper key 空時查 DB 並寫回 Redis | 第一個 request 可能仍拿空結果，且不易告警 | cold-cache test / fail closed |
| 提現設定 JSON | `paySetting` 由 settings content 解析 | schema mismatch 會造成錯誤限制或 exception | JSON schema validation |

## Owner 口徑

最保守、最準確的講法：

> 我會把支付方式選擇當成 runtime config consistency 問題，不是單純查 Redis。DB 是 source of truth，Redis 是 projection，payment API 是最後的 eligibility decision。只要其中一層不同步，玩家就可能看不到支付方式或看到錯商戶。

## Step 3 owner decisions

1. **Fail closed**：設定不完整時不要曝光商戶。
2. **Versioned config**：多個 Redis key 應有同一個版本或 batch id。
3. **Sync validation**：同步後檢查 `payTypeList`、`merchantList`、`merchantAccountList` 是否互相對得上。
4. **Cold-cache safety**：Redis miss fallback DB 後，第一個 request 也要拿到正確結果。
5. **Explainability**：排查時要能知道某玩家為什麼看到或看不到某支付方式。

## Step 4 面試決策收斂

面試時不要把這條講成「Redis cache」；要講成「runtime eligibility 的設定一致性」。

| 情境 | Owner 判斷 | 面試重點 |
| --- | --- | --- |
| 玩家支付列表空白 | 先判斷是玩家層級 / channel / device filter，還是 Redis projection 缺 key | 排查順序要從 uid -> channel DB -> layer -> key -> sync history |
| list 有支付類型但 detail 空 | 高機率是 `payTypeList` 與 `merchantList` / `merchantAccountList` 不一致 | 多 key projection 需要 batch validation |
| Redis key miss | 允許 fallback DB，但本次 response 不能仍然空 | cold-cache test 是必要驗收 |
| 後台剛改設定但玩家沒看到 | DB 已更新不代表 runtime 生效 | 需要 per-channel readiness 與 config version |
| 設定不完整 | 應 fail closed，不曝光不可確認商戶 | money flow 前置條件要保守 |

Step 4 的結論是：這條 flow 可以用來展示 Senior / Owner 對「設定不是資料而已，而是會進入 money flow 的 runtime contract」的理解。

## 下一步

```text
iwin payment payment-channel-config-selection Step 5
```
