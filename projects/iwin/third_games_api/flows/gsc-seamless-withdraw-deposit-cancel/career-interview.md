# GSC seamless withdraw / deposit / rollback / cancel Career Interview

## 定位

完成狀態：Step 4 已完成，正式面試 case 已建立；下一步做 Step 5 單條 flow claim gate。

證據層級：`專案存在 / code-backed`、`分析素材 / learning-only`。目前不更新正式履歷 / 自傳。

## 面試主軸

這條 flow 可以包成「第三方遊戲 provider 分離式 wallet callback 的狀態機與 idempotency boundary」。

它最適合拿來展示三件事：

- 你能把 split endpoint 的 withdraw / deposit / rollback / cancel 串成狀態機。
- 你能指出 adapter Mongo evidence 和 gameserver wallet source of truth 的差異。
- 你能保守區分 `/transfer` 整合式主線與 split endpoint 的 legacy / compatibility / production 待確認狀態。

## 30 秒說法

```text
我分析過 GSC 分離式 seamless wallet flow，它不是單一 /transfer，而是 withdraw 扣款、deposit 派彩、rollback 退款、cancel other 四支 endpoint。third_games_api 會驗 MD5 sign、查 Redis mapping / center route、用 Mongo step 1 / 2 / 3 做前置與重複判斷，再送 gameserver GSC_BET / GSC_SETTLE / GSC_REFUND / GSC_OTHER。真正改錢在 gameserver，所以 Mongo step evidence 不能當最終錢包 source of truth。
```

## 90 秒說法

```text
GSC split flow 的核心是狀態機。withdraw 成功後會寫 Mongo step 1；deposit 要先確認 step 1 存在，再檢查 betId + action + step 2 是否重複；rollback / cancel 也是先要求 step 1，再檢查 step 3 duplicate。下游 gameserver 會依 GSC_BET、GSC_SETTLE、GSC_REFUND、GSC_OTHER 映射成 gscType 1 到 4，進 GSCTransferInOutJob 改玩家餘額與寫 GSC log。

我會特別注意 transaction boundary：adapter 是 gameserver 成功後才寫 Mongo。如果 gameserver 已改錢但 Mongo insert 失敗，下一次 provider retry 可能看不到 step evidence 而再次進入 money mutation。比較穩的設計是把 provider + betId + action / step 的 idempotency guard 放到 wallet mutation 前，Mongo 則當 audit / reconciliation evidence。
```

## 3 分鐘說法

```text
這條 case 我會用第三方遊戲 provider 分離式 wallet callback 來講。GSC split endpoint 不是一支 transfer API，而是 withdraw、deposit、rollback、cancel 四個入口。withdraw 是扣款，成功後留下 Mongo step 1；deposit 是派彩，要先看到 step 1，再用 betId + action + step 2 防重；rollback 和 cancel 都要求 step 1，然後用 betId + action + step 3 防重，但下游 command 不同，rollback 走 GSC_REFUND，cancel 走 GSC_OTHER。

我會先拆 source of truth。third_games_api 是 adapter，負責驗簽、幣別、玩家、Redis game mapping、center_http route、Mongo audit evidence；真正改玩家餘額的是 iwin_gameserver 的 GSCTransferInOutJob 和 PlayerData.modifyAndGetCoinGSC。所以 Mongo step evidence 不能被當成 wallet source of truth，它比較像 provider callback / transaction evidence。

這條 flow 的最大風險是 failure window：adapter 是 gameserver 成功後才寫 Mongo。如果 GSC_BET 已扣款但 Mongo step 1 沒寫成功，provider retry 可能看不到 step 1，withdraw 可能再次進下游，deposit / rollback 也可能因 step 1 缺失被拒絕。deposit、rollback、cancel 也有同樣問題。更好的 owner decision 是把 provider + betId + action / step 的 durable idempotency guard 放在 wallet mutation boundary 前，Mongo 留作 audit / reconciliation，並且對 gameserver success but Mongo fail、step 1 missing、Redis route miss、cancel / rollback step 3 數量做觀測與告警。

最後我會保守講 production status：commit history 顯示後面有 /transfer 投派整合 API，split endpoint 可能是舊模式或相容模式。沒有 provider route / traffic evidence 前，我不會說它是目前 production 主線，也不會把這條 flow 寫成我的正式開發成果；它是 code-backed 的面試分析素材。
```

## STAR 面試版

Situation：第三方遊戲 provider 可能用分離式 callback 通知下注、派彩、退款與取消，任何重送或順序錯誤都可能影響玩家餘額。

Task：要把 split endpoint 的狀態機、冪等點與真正 money boundary 拆清楚，避免把 adapter Mongo evidence 誤當成錢包 source of truth。

Action：我會把 withdraw / deposit / rollback / cancel 分別對到 Mongo step 1 / 2 / 3 與 gameserver `GSC_BET / GSC_SETTLE / GSC_REFUND / GSC_OTHER`，再檢查 duplicate guard、gameserver 成功後才寫 Mongo 的 failure window，以及 cancel / rollback 共用 step 3 的語意風險。

Result：這條 flow 可以清楚講出 provider callback 的 transaction boundary、retry 風險、observability 指標與 owner 改善方向，但目前不升級成正式履歷成果。

## 目前可講

- GSC split endpoint 的 `withdraw -> deposit / rollback / cancel` 狀態轉換。
- Adapter 驗簽、Redis routing、Mongo step evidence、gameserver wallet mutation boundary。
- `cancel` 與 `rollback` 都落 step `"3"`，但 action / downstream command 不同，語意需要 provider spec。
- 與 `/transfer` 整合式 endpoint 的差異：split endpoint 更容易遇到 out-of-order 與 step evidence 缺口。
- owner 改善方向：wallet mutation boundary 前的 durable idempotency、Mongo audit / reconciliation、step missing / duplicate / route miss 的觀測告警。

## Senior / Lead 追問

Q：如果 `withdraw` gameserver 已扣款，但 Mongo step 1 寫失敗怎麼辦？
A：不能只靠 adapter Mongo 查重。provider retry 可能看不到 step 1，再次進入扣款。應在 wallet mutation 前建立 durable idempotency guard，例如 provider + betId + action / step，並用 gameserver currency log、adapter Mongo、provider statement 做 reconciliation。

Q：`deposit` 先到但 step 1 還沒寫，應該直接失敗嗎？
A：目前 code-backed 行為是回 Bet Not Exist。Owner 要確認 provider retry contract；如果 provider 會重送，可以保守拒絕並觀測 step missing。若 out-of-order 很常見，則需要 pending state 或延遲重試策略。

Q：rollback 和 cancel 都寫 step 3，會不會混淆？
A：目前靠 action 和 downstream command 區分，rollback 送 `GSC_REFUND`，cancel 送 `GSC_OTHER`。這需要 provider spec 確認互斥規則，不能只靠 step number 判斷交易生命週期。

Q：為什麼不把 Mongo 當 source of truth？
A：因為 Mongo 是 gameserver success 後才寫。真正改錢在 gameserver；Mongo 比較適合當 audit evidence、duplicate helper 和 reconciliation join point。

Q：這條 split endpoint 和 `/transfer` 怎麼選主線？
A：current code 兩者都存在，但 commit history 顯示後續有 `/transfer` 投派整合需求。沒有 provider routing / traffic / production config evidence 前，只能保守說 split 是舊式、相容或待確認 production 使用狀態。

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
iwin third_games_api gsc-seamless-withdraw-deposit-cancel Step 5
```
