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

## 下一步

```text
iwin payment payment-channel-config-selection Step 4
```
