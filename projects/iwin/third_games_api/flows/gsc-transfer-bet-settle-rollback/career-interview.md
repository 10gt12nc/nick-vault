# GSC transfer bet / settle / rollback Career Interview

完成狀態：Step 4 已轉成面試 case。

證據層級：`分析素材 / learning-only`、`專案存在 / code-backed`。

目前沒有 Nick 本人參與 evidence，因此本文件只提供保守面試素材，不直接更新正式履歷 master 或自傳。

## 面試主軸

這條 case 要講的是「第三方遊戲 seamless wallet callback 的 transaction boundary」，不是講 controller CRUD。

最有價值的三個點：

1. `third_games_api` 是 provider adapter，不是錢包 source of truth。
2. gameserver wallet mutation 成功後，`third_games_api` 才寫 Mongo audit，兩者不是同一個 transaction。
3. `ROLLBACK` 分支目前 code-backed 行為是不呼叫 gameserver mutation，只回算 balance 並寫 Mongo，所以必須標成待確認，不能說已完整補償。

## 30 秒摘要

我整理過一條第三方遊戲 GSC seamless wallet transfer flow。Provider callback 進 `third_games_api` 後，系統會驗簽、檢查玩家與幣別、用 Redis 找 gameserver routing，再呼叫 gameserver `PGTRANSFERINOUT` 做實際錢包異動，最後把 callback 與 transaction evidence 寫進 Mongo。

這條 flow 的面試重點是交易一致性：source of truth 在 gameserver wallet，不在 adapter Mongo；provider retry / timeout 需要 wallet 層冪等；而目前 `ROLLBACK` 分支沒有呼叫 wallet mutation，所以我會把它當成需要 spec / production evidence 確認的風險點，而不是包裝成已完成補償。

證據層級：`專案存在 / code-backed`、`分析素材 / learning-only`。

## 3 分鐘版本

我會先從系統分層說起。這條 flow 的入口是 GSC `POST /api/seamless/transfer`，落在 `third_games_api` 的 `GscController#transfer`。這層主要做 adapter 工作：驗 request body、驗簽、檢查 currency、查玩家、解析 transaction，再用 Redis cached platform config 找到對應 gameserver `center_http`。

非 rollback 的情境會組成 `PGTRANSFERINOUT` command，帶 `accountId`、`addMoney`、`BetCoin`、`validBetCoin`、`spinCurrency`、`transactionId`、`gameId`、`betId`、`createTime` 等欄位送到 gameserver。gameserver 端由 `HttpService` dispatch 到 `HttpPGTransferInOut` 與 `PGTransferInOutJob`，真正扣款 / 加款發生在 job 內呼叫 player wallet method 的地方。

所以我不會把 `third_games_api` Mongo 當成錢包帳本。Mongo 的 `third_log_gsc` 和 `third_transaction_gsc` 比較像 callback audit / reconciliation evidence。這帶出第一個 failure window：gameserver wallet mutation 成功後，如果 adapter 寫 Mongo 失敗，內部錢包可能已經成功，但 provider 看到的結果或 audit evidence 可能不一致。這時不能靠 provider 盲目 retry，wallet mutation 層必須用 provider transaction id / wager code 做冪等或回傳同一筆結果。

第二個風險是 `ROLLBACK`。目前 code 顯示 `action == ROLLBACK` 時沒有呼叫 gameserver，只用 request amount 加目前 balance 算 response，再寫 Mongo。這可能符合某種 provider validation 語意，也可能是補償缺口。沒有 provider spec 或 production issue evidence 前，我只會把它標成待確認。

如果要改善，我會先定義三件事：第一，gameserver wallet ledger 是 money source of truth；第二，provider transaction id / wager code / player id 是冪等核心；第三，adapter audit、game log、wallet ledger 要能用同一組 correlation key 對帳與 repair。

證據層級：`專案存在 / code-backed`、`分析素材 / learning-only`。

## 面試官可能追問

| 追問 | 回答方向 | 證據層級 |
| --- | --- | --- |
| 這是你主導的嗎？ | 目前我不能說主導；我能說我做過 code-level 追蹤與交易一致性分析，已確認 adapter、gameserver wallet、Mongo audit 的邊界。 | `分析素材 / learning-only` |
| 為什麼 Mongo 不能當 source of truth？ | 因為 code path 是 gameserver wallet mutation 成功後才寫 Mongo；它不是和 wallet 同一個 transaction。 | `專案存在 / code-backed` |
| provider retry 怎麼避免重複扣加？ | 最終冪等要在 wallet mutation 層，以 provider transaction id / wager code / player id 收斂；adapter Mongo 只能做 early check 和 audit。 | `分析素材 / learning-only` |
| gameserver 成功但 Mongo 失敗怎麼辦？ | money success 應以 wallet ledger 為準，audit failure 進 repair / reconciliation；若回 provider error，wallet 層更要能可重入。 | `分析素材 / learning-only` |
| rollback 為什麼特別危險？ | 目前 code-backed 行為是不呼叫 gameserver mutation，只回算 balance；必須查 provider spec，不能直接說它完成 wallet rollback。 | `專案存在 / code-backed` |
| 一次多筆 transactions 怎麼看？ | 目前 Step 3 看到主要 local variables 保留最後一筆；若 provider spec 允許多筆，這是資料正確性風險。 | `專案存在 / code-backed` |

## 我應該怎麼回答

### 如果被問「你會怎麼修？」

我會先補事實，不會直接改：

1. 查 provider spec，確認 `transfer` 是否允許多筆 transactions，以及 `ROLLBACK` 是 query / validation response 還是真正 reversal。
2. 查 gameserver wallet method 底層，確認 `transactionId` / `betId` 是否有唯一性或可重入結果。
3. 查 Mongo index 與 reconciliation job，確認 audit 缺口能不能 repair。

如果確認目前缺冪等，我會把最終 idempotency 放在 wallet mutation 層，adapter 層保留 request audit 與 duplicate fast path。這樣即使 provider timeout 或 Mongo 寫入失敗後重送，也能回到同一筆交易結果。

### 如果被問「為什麼不只在 controller 查 Mongo？」

因為 controller 查 Mongo 只能保護「Mongo 已寫入」的交易。這條 flow 是先呼叫 gameserver，再寫 Mongo。如果 wallet 成功但 Mongo 失敗，下一次 retry 時 controller 看 Mongo 可能查不到，仍有重複扣加風險。所以最後防線必須在 wallet mutation 層。

### 如果被問「rollback 你怎麼講才不誇大？」

我會說：目前 code-backed 行為是 `ROLLBACK` 不呼叫 gameserver mutation，只回算 balance 並寫 Mongo。我不能說它已完成 wallet 補償。比較成熟的回答是：先對 provider spec 定義 rollback 語意，再決定要不要補 reversal transaction、idempotency key 與 reconciliation evidence。

## 可以展現的 Senior 能力

- 能分清楚 adapter、wallet ledger、audit projection 的責任。
- 能找 transaction boundary，而不是只看 API success。
- 能把 provider retry / timeout / duplicate request 轉成 idempotency 設計。
- 能把 rollback 語意從 code behavior、provider spec、production evidence 三個層次拆開。
- 能保守標註 evidence，不把分析素材包裝成個人戰功。

## 不能誇大的地方

- 不可說 Nick 主導 GSC provider 串接。
- 不可說 Nick 修過這條 rollback。
- 不可說系統已經 exactly-once。
- 不可說 Mongo 與 gameserver wallet mutation 是同一個 transaction。
- 不可說 rollback 已完整補償 wallet。
- 不可寫改善比例、事故下降、成功率提升。

## 如果被問「這是你主導的嗎？」

保守回答：

> 這條目前我不會說是我主導的。我的 evidence 是 code-level 追蹤與 flow analysis：我能說清楚 provider callback 到 gameserver wallet mutation 的路徑，也能指出 Mongo audit、provider retry、rollback 語意的風險。若要把它寫成正式履歷成果，我會先補本人 MR / ticket / production issue 或 owner evidence。

證據層級：`分析素材 / learning-only`。

## 對應履歷 bullet

目前不建議放入正式履歷。若只是面試準備或內部學習索引，可以保守寫：

- `分析素材 / learning-only`：針對第三方遊戲 seamless wallet callback 做 code-level 追蹤，釐清 provider request、gameserver wallet mutation、Mongo audit、retry / idempotency 與 rollback 語意邊界。
- `專案存在 / code-backed`：GSC transfer callback 由 `third_games_api` adapter 接收，並透過 gameserver `PGTRANSFERINOUT` 完成非 rollback 情境的錢包異動。

若 Nick 後續補本人參與 evidence，才能再評估是否改寫成「協助排查 / 補強 / 維護」類履歷句。
