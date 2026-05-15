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
| Nick 實作或主導此 flow | 待確認 | 需要 MR / ticket / commit / 本人確認 |
| production incident / 改善比例 | 待確認 | 本輪沒有證據 |

## 可放面試

- 我分析過一條第三方遊戲投注 / 派彩 / 退款 flow，並用它討論 wallet correctness、idempotency、failure window 與 reconciliation。
- 這條 flow 的 code 顯示 wallet update、HTTP response、log write 是分段完成，因此 owner 需要定義 OK 語意與補償策略。
- 如果要強化，我會先補防重 key 與 reconciliation，而不是只加 log。

## 暫不放正式履歷

目前不建議把這條 flow 放進正式履歷 master，除非後續補到 Nick 本人 evidence。可先放在面試準備素材。

## 禁止 claim

- 主導 iwin 第三方遊戲錢包整合。
- 設計完整遊戲錢包一致性架構。
- 解決第三方遊戲重複扣款 production incident。
- 改善投注 / 派彩效能或錯帳率。
- 擔任 gameserver owner。
