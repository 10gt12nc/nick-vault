# third-party-transfer-in-out Claim Boundary

更新時間：2026-05-20

## Evidence labels

| Claim | 可用層級 | 說明 |
| --- | --- | --- |
| `iwin_gameserver` 有第三方遊戲投注 / 派彩 / 退款 flow | 專案存在 / code-backed | 已由 `HttpService`、money jobs、log jobs 確認 |
| flow 橫跨 `slots-center`、`slots-games/slots-game-common`、`slots-game-log` | 專案存在 / code-backed | 已讀 code path |
| upstream adapter 會送 `PGTRANSFERINOUT`、`ANTPLAYTRANSFERINOUT`、`GSC_*` command | 專案存在 / code-backed | 只做 `third_games_api` 最小掃描 |
| HTTP OK 在 wallet mutation 後、log side effect 前送出 | 專案存在 / code-backed | 已讀 `sendMoneyChange2Center()` |
| gameserver wallet 層防重未確認 | 分析素材 / learning-only | 本輪未看到明確 guard，但不能宣稱一定沒有 |
| 可用於 Senior / Owner 面試討論 | 分析素材 / learning-only | Step 4 已整理 30 秒 / 2 分鐘 / 5 分鐘版本 |
| project-level career boundary | 部分真實開發過 + code-backed | 2026-05-20 project-level consolidation 已找到 Nick / `10gt12nc` direct commits |
| Nick 實作此 flow 的部分 gameserver path | 真實開發過 + code-backed | Antplay / GSC / PG center_http command、money job、`GamePlayer` log dispatch、log reel path 有 direct commits |
| 完整 flow owner claim | 待確認 | 仍需要 MR / ticket / production owner evidence |
| production incident / 改善比例 | 待確認 | 本輪沒有證據 |

## Step 5 結論

| 用途 | 判斷 | 原因 |
| --- | --- | --- |
| 正式履歷 master | 可保守更新 | 只能併入 project-level 第三方 provider 投派整合 claim |
| 投遞用自傳 | 可保守更新 | 用「參與」口徑，不寫主導完整 gameserver |
| 面試素材 | 可用 | 可說有直接開發 evidence，但 owner / incident / 改善仍不可誇大 |
| project-level career 索引 | 已更新 | 由 `contribution-claim-consolidation.md` 統一收口 |
| 未來升級 claim | 待確認 | 若要寫完整 owner，仍需更多 evidence |

## 可放面試

- 我分析過一條第三方遊戲投注 / 派彩 / 退款 flow，並用它討論 wallet correctness、idempotency、failure window 與 reconciliation。
- 這條 flow 的 code 顯示 wallet update、HTTP response、log write 是分段完成，因此 owner 需要定義 OK 語意與補償策略。
- 如果要強化，我會先補防重 key 與 reconciliation，而不是只加 log。

## 可放正式履歷的範圍

2026-05-20 project-level consolidation 後，本 flow 可併入正式履歷 master，但只能用保守 project-level 句型。

可用句型：

```text
參與第三方遊戲 provider 投派整合與 gameserver 錢包 / 投注流水串接，處理 Antplay / GSC / PG 類 bet / settle / refund / transfer-in-out flow 與 log projection。
```

限制：不能寫成主導完整第三方遊戲整合、完整 gameserver wallet owner、完整防重 / 對帳架構或 production incident 改善。

## Step 4 面試可用說法

可說：

- 「我參與過 iwin gameserver 的第三方遊戲投派整合與 log projection 相關開發。」
- 「我會把它拆成 adapter、gameserver wallet、log server 三段來看。」
- 「我看到的 owner 風險是 wallet update 和 log projection 不在同一 transaction，所以要處理 idempotency 與 reconciliation。」
- 「如果我要改善，會先確認 provider idempotency key，再設計 wallet mutation 前的防重。」

不可說：

- 「我負責完整第三方遊戲投派整合。」
- 「我設計了 gameserver wallet 防重。」
- 「我解決了 duplicate callback 問題。」
- 「我建立了 outbox / reconciliation 系統。」

## 禁止 claim

- 主導 iwin 第三方遊戲錢包整合。
- 設計完整遊戲錢包一致性架構。
- 解決第三方遊戲重複扣款 production incident。
- 改善投注 / 派彩效能或錯帳率。
- 擔任 gameserver owner。

## 履歷 claim 分層（2026-05-18 KB 對齊）

- 可放履歷：可併入 `iwin_gameserver` project-level 第三方 provider 投派整合 claim，限 Antplay / GSC / PG 類 bet / settle / refund / transfer-in-out 與 log projection。
- 可面試講：真實開發過 + code-backed。可用 gameserver wallet transfer flow 說明 provider transfer in/out、玩家餘額、DB proxy、log writer、failure window 與 reconciliation。
- 不可誇大：不得寫成 Nick 主導 gameserver、完整 wallet owner、完整第三方遊戲整合 owner 或解決 duplicate callback production incident。
