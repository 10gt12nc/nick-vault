# Interview Notes：GSC transfer bet / settle / rollback

完成狀態：Step 4 已轉成可講案例。

證據層級：`專案存在 / code-backed`、`分析素材 / learning-only`。沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認，不寫成真實開發成果。

## 面試主軸

這條 flow 適合講「第三方遊戲 provider callback 如何安全地落到內部錢包」。

不要把重點放在 controller API 寫法，而是放在：

- external callback vs internal wallet source of truth
- retry / timeout / duplicate request
- rollback semantics
- Mongo audit 與 wallet ledger 一致性
- response timing 與後續 log projection

## 面試回答公式

```text
我先說這不是單純接 API，而是第三方遊戲 callback 到內部錢包的一致性問題。
接著說 adapter、Redis routing、gameserver wallet、Mongo audit 怎麼分工。
然後挑三個 failure window：provider retry、wallet 成功但 Mongo 失敗、ROLLBACK 不改 wallet。
最後說我會怎麼保守補 evidence 和設計 owner decision。
```

## Q1：這條 flow 在做什麼？

它接 GSC provider 的 `/api/seamless/transfer` callback。API 會驗簽、檢查幣別和玩家，解析 provider transaction，然後把非 rollback 的交易轉成 gameserver `PGTRANSFERINOUT` command。真正錢包異動在 gameserver，`third_games_api` 後續把 callback 與 transaction evidence 寫進 Mongo。

## Q2：你怎麼判斷 source of truth？

我會看哪裡真正改錢。這條 flow 裡 `third_games_api` 只是組 request 和寫 Mongo；真正扣加錢是在 gameserver job 呼叫 player wallet method。所以 Mongo 不能直接當帳本，它比較像 audit / reconciliation evidence。

## Q3：這條 flow 最大的一致性風險是什麼？

最大風險是跨系統沒有單一 transaction。gameserver wallet mutation 成功後，`third_games_api` 才寫 Mongo；如果 Mongo 寫失敗或 API response 出問題，provider 可能重送。這時若 wallet 層沒有用 provider transaction id 做冪等，就可能重複扣加。

## Q4：Rollback 有什麼特別？

目前 code 顯示 `ROLLBACK` 分支不呼叫 gameserver，只用目前餘額加上 request amount 算 response balance，並寫 Mongo。這代表我不能直接說它有完成 wallet rollback。正確做法是回頭查 provider spec 與 production evidence，確認這是特殊 validation response，還是真的需要補 reversal transaction。

## Q5：如果要改善 idempotency，你會怎麼做？

我會把 provider transaction id / wager code 加上 player id 作為冪等 key，並把最終保護放在 wallet mutation 層。adapter 層也可以先查 Mongo 或寫入 request evidence，但不能只靠 adapter，因為 adapter audit write 可能發生在 wallet 成功之後。

## Q6：Mongo audit 有什麼價值？

它可以用來查 provider callback、交易 action、bet id、balance、request time、settle time，做 issue investigation 和 reconciliation。但它不是原子地跟 wallet mutation 一起 commit，所以在 money correctness 上必須區分 audit evidence 與 wallet source of truth。

## Q7：你會怎麼設計補償？

我會先定義成功狀態：wallet mutation 成功就是 money success，audit / log failure 則進 repair queue。repair job 用 provider transaction id、bet id、player id 去補寫 Mongo 或報表投影。若 provider retry 進來，wallet 層要回同一筆交易結果，不能再次扣加。

## Q8：這條 flow 可以怎麼講成 Senior / Owner case？

可以講成「我追一條第三方 seamless wallet flow 時，不只看 API happy path，而是把 provider callback、Redis routing、gameserver wallet、Mongo audit、retry、rollback、reconciliation 邊界拆開」。這能展示 owner 思維：知道哪裡是真正帳本、哪裡只是投影、哪個 failure window 會讓外部與內部狀態分歧。

## Q9：如果被問「這是你主導的嗎？」

不能說主導。保守回答是：

> 這條目前我不會說是我主導的。我的 evidence 是 code-level flow analysis：我能說清楚 provider callback 到 gameserver wallet mutation 的路徑，也能指出 retry、Mongo audit、rollback 語意的風險。若要寫成正式履歷成果，我會先補本人 MR / ticket / production issue 或本人確認。

## Q10：這條 case 跟一般 payment callback 有什麼不同？

相同點是外部 provider 都會有 retry、timeout、duplicate callback、audit log 與 reconciliation 問題。

不同點是遊戲 flow 還多了 bet amount、valid bet、prize amount、spin currency、game id、wager code 等語意，而且 rollback 可能不是單純訂單退款，而是要和遊戲局、wallet ledger、報表 log 一起對齊。

## Lead / Architect 追問版

### 如果 provider 重送，你怎麼避免重複扣加？

我不會只靠 adapter 查 Mongo，因為 Mongo 寫入是在 gameserver wallet mutation 之後，兩者不是同一個 transaction。最終保護要落在 wallet mutation 層，用 provider transaction id / wager code / player id 組成 idempotency key。adapter 層可以做 early duplicate check 和 audit，但不能當最後防線。

### 如果 gameserver 已成功，但 `third_games_api` 寫 Mongo 失敗，你怎麼定義成功？

money success 應以 gameserver wallet mutation 為準。Mongo 失敗要進 repair / reconciliation，而不是讓 provider retry 造成第二次扣加。API response policy 要看 provider spec，但前提是 wallet 層必須先具備可重入結果。

### `ROLLBACK` 不改 wallet，你會怎麼處理？

我會先查 provider spec，確認這個 branch 是不是只要求回傳計算結果。如果 spec 定義 rollback 應該反向異動 wallet，那現況就是風險，需要補 reversal transaction 和 idempotency。如果 spec 不是 wallet mutation，文件與 monitoring 要清楚標示，避免被誤解成已補償。

## 可展現能力對照

| 能力 | 這條 case 怎麼展現 | 證據層級 |
| --- | --- | --- |
| Transaction boundary | 能指出 adapter、gameserver wallet、Mongo audit 不是同一個 transaction | `專案存在 / code-backed` |
| Idempotency | 能說明 provider retry 不能只靠 adapter Mongo，要追 wallet mutation 層 | `分析素材 / learning-only` |
| Rollback semantics | 能保守指出 rollback code 行為與 provider spec 待確認 | `專案存在 / code-backed` |
| Reconciliation | 能提出 wallet ledger、callback audit、game log 的 join key / repair 思路 | `分析素材 / learning-only` |
| Claim discipline | 能清楚說這是分析素材，不是 Nick 主導成果 | `分析素材 / learning-only` |

## 追問時的保守句

- 「這部分我目前只追到 code-backed evidence，還不能說 production 已經怎麼處理。」
- 「如果要寫正式履歷，我會先確認本人參與 evidence 與底層 wallet 冪等實作。」
- 「目前看到 rollback 分支沒有 wallet mutation，所以我會把它標成待確認，而不是包裝成已完成補償。」
