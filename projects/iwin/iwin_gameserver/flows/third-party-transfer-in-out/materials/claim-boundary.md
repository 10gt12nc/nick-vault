# third-party-transfer-in-out Claim Boundary

更新時間：2026-05-15

## Evidence labels

| Claim | 可用層級 | 說明 |
| --- | --- | --- |
| `iwin_gameserver` 有第三方遊戲投注 / 派彩 / 退款 flow | 專案存在 / code-backed | 已由 `HttpService`、money jobs、log jobs 確認 |
| flow 橫跨 `slots-center`、`slots-games/slots-game-common`、`slots-game-log` | 專案存在 / code-backed | 已讀 code path |
| upstream adapter 會送 `PGTRANSFERINOUT`、`ANTPLAYTRANSFERINOUT`、`GSC_*` command | 專案存在 / code-backed | 只做 `third_games_api` 最小掃描 |
| HTTP OK 在 wallet mutation 後、log side effect 前送出 | 專案存在 / code-backed | 已讀 `sendMoneyChange2Center()` |
| gameserver wallet 層防重未確認 | 分析素材 / learning-only | 本輪未看到明確 guard，但不能宣稱一定沒有 |
| 可用於 Senior / Owner 面試討論 | 分析素材 / learning-only | Step 4 已整理 30 秒 / 2 分鐘 / 5 分鐘版本 |
| project-level career boundary | 分析素材 / learning-only | Step 5 已新增 project-level `career-interview.md` |
| Nick 實作或主導此 flow | 待確認 | 需要 MR / ticket / commit / 本人確認 |
| production incident / 改善比例 | 待確認 | 本輪沒有證據 |

## Step 5 結論

| 用途 | 判斷 | 原因 |
| --- | --- | --- |
| 正式履歷 master | 暫不更新 | 缺 Nick 本人 MR / ticket / commit / production issue / 本人確認 |
| 投遞用自傳 | 暫不更新 | 不能寫成 Nick 主導成果；也不適合塞入「熟悉」型空泛描述 |
| 面試素材 | 可用 | 必須用「我分析過 / 如果我是 owner」語氣 |
| project-level career 索引 | 可更新 | 只整理可說 / 不可說與下一步，不新增正式 claim |
| 未來升級 claim | 待確認 | 補本人 evidence 後再重評 |

## 可放面試

- 我分析過一條第三方遊戲投注 / 派彩 / 退款 flow，並用它討論 wallet correctness、idempotency、failure window 與 reconciliation。
- 這條 flow 的 code 顯示 wallet update、HTTP response、log write 是分段完成，因此 owner 需要定義 OK 語意與補償策略。
- 如果要強化，我會先補防重 key 與 reconciliation，而不是只加 log。

## 暫不放正式履歷

目前不建議把這條 flow 放進正式履歷 master，除非後續補到 Nick 本人 evidence。可先放在面試準備素材。

暫存句型，現階段不可放正式履歷：

```text
分析 iwin gameserver 第三方遊戲投注 / 派彩 / 退款 flow，拆解 wallet mutation、log projection、idempotency 與 reconciliation 風險。
```

原因：這句的安全動詞是「分析」，不是「實作 / 主導 / 改善」。若放進正式履歷，容易被讀成 Nick 個人成果，現階段 evidence 不足。

## Step 4 面試可用說法

可說：

- 「我分析過 iwin gameserver 的第三方遊戲投派整合 flow。」
- 「我會把它拆成 adapter、gameserver wallet、log server 三段來看。」
- 「我看到的 owner 風險是 wallet update 和 log projection 不在同一 transaction，所以要處理 idempotency 與 reconciliation。」
- 「如果我要改善，會先確認 provider idempotency key，再設計 wallet mutation 前的防重。」

不可說：

- 「我負責第三方遊戲投派整合。」
- 「我設計了 gameserver wallet 防重。」
- 「我解決了 duplicate callback 問題。」
- 「我建立了 outbox / reconciliation 系統。」

## 禁止 claim

- 主導 iwin 第三方遊戲錢包整合。
- 設計完整遊戲錢包一致性架構。
- 解決第三方遊戲重複扣款 production incident。
- 改善投注 / 派彩效能或錯帳率。
- 擔任 gameserver owner。
