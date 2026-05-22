# GSC seamless withdraw / deposit / rollback / cancel Career Interview

## 定位

完成狀態：Step 3 已完成，主學習包已建立；下一步做 Step 4 正式面試 case。

證據層級：`專案存在 / code-backed`、`分析素材 / learning-only`。目前不更新正式履歷 / 自傳。

## 面試主軸

這條 flow 可以包成「第三方遊戲 provider 分離式 wallet callback 的狀態機與 idempotency boundary」。

它最適合拿來展示三件事：

- 你能把 split endpoint 的 withdraw / deposit / rollback / cancel 串成狀態機。
- 你能指出 adapter Mongo evidence 和 gameserver wallet source of truth 的差異。
- 你能保守區分 `/transfer` 整合式主線與 split endpoint 的 legacy / compatibility / production 待確認狀態。

## 30 秒草稿

```text
我分析過 GSC 分離式 seamless wallet flow，它不是單一 /transfer，而是 withdraw 扣款、deposit 派彩、rollback 退款、cancel other 四支 endpoint。third_games_api 會驗 MD5 sign、查 Redis mapping / center route、用 Mongo step 1 / 2 / 3 做前置與重複判斷，再送 gameserver GSC_BET / GSC_SETTLE / GSC_REFUND / GSC_OTHER。真正改錢在 gameserver，所以 Mongo step evidence 不能當最終錢包 source of truth。
```

## 90 秒草稿

```text
GSC split flow 的核心是狀態機。withdraw 成功後會寫 Mongo step 1；deposit 要先確認 step 1 存在，再檢查 betId + action + step 2 是否重複；rollback / cancel 也是先要求 step 1，再檢查 step 3 duplicate。下游 gameserver 會依 GSC_BET、GSC_SETTLE、GSC_REFUND、GSC_OTHER 映射成 gscType 1 到 4，進 GSCTransferInOutJob 改玩家餘額與寫 GSC log。

我會特別注意 transaction boundary：adapter 是 gameserver 成功後才寫 Mongo。如果 gameserver 已改錢但 Mongo insert 失敗，下一次 provider retry 可能看不到 step evidence 而再次進入 money mutation。比較穩的設計是把 provider + betId + action / step 的 idempotency guard 放到 wallet mutation 前，Mongo 則當 audit / reconciliation evidence。
```

## 目前可講

- GSC split endpoint 的 `withdraw -> deposit / rollback / cancel` 狀態轉換。
- Adapter 驗簽、Redis routing、Mongo step evidence、gameserver wallet mutation boundary。
- `cancel` 與 `rollback` 都落 step `"3"`，但 action / downstream command 不同，語意需要 provider spec。
- 與 `/transfer` 整合式 endpoint 的差異：split endpoint 更容易遇到 out-of-order 與 step evidence 缺口。

## 目前不可講

- Nick 主導 `third_games_api` GSC split endpoint。
- 已確認 split endpoint 是目前 production 主線。
- 已確認 Mongo 有 unique index。
- 已確認 gameserver wallet mutation 有完整 transaction-level idempotency。
- 已修復 GSC split endpoint production 錯帳。

## 履歷建議

目前不建議新增 `third_games_api` standalone 履歷 bullet。

可作為面試補充：

```text
分析過第三方遊戲 provider 分離式 wallet callback，能拆解 withdraw / deposit / rollback / cancel 狀態機、adapter audit evidence、gameserver wallet boundary 與 retry / duplicate failure window。
```

這句只能作面試素材，不應直接放正式履歷主成果。

## 下一步

```text
iwin third_games_api gsc-seamless-withdraw-deposit-cancel Step 4
```
