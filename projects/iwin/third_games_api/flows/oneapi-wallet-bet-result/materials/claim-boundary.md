# Claim Boundary：OneAPI / PG bet_result

## 結論

本 flow 目前只能作 `專案存在 / code-backed` 與 `分析素材 / learning-only`。

不新增 `third_games_api` standalone 正式履歷成果。

## 可說

| 說法 | 證據層級 | 用途 |
| --- | --- | --- |
| OneAPI `/wallet/bet_result` 使用 HMAC-SHA256 驗簽 | 專案存在 / code-backed | 面試技術拆解 |
| Adapter 以 `transactionId + step` 查 Mongo 做 duplicate guard | 專案存在 / code-backed | 面試 idempotency 討論 |
| 成功後呼叫 gameserver `PGTRANSFERINOUT` 修改玩家錢包 | 專案存在 / code-backed | 面試 transaction boundary |
| gameserver PGTransferInOut 有 Nick / `10gt12nc` direct commits | 真實開發過 + code-backed，但屬 `iwin_gameserver` | `iwin_gameserver` project claim |
| `gameserver success -> Mongo insert` 有 failure window | 分析素材 / code-backed 推論 | Senior / Owner 面試 |

## 不可說

- Nick 開發 OneAPI adapter。
- Nick 主導 `third_games_api` 的 OneAPI / PG provider 串接。
- Nick 設計或落地完整 provider idempotency。
- Nick 解決 OneAPI production 錯帳。
- `third_games_api` 本 repo 有 Nick OneAPI production direct evidence。
- 下游 `iwin_gameserver` direct commits 等於 `third_games_api` direct contribution。

## 履歷判斷

正式履歷：

- 目前不新增 `third_games_api` standalone bullet。
- 若要講第三方遊戲經驗，仍沿用 project-level consolidation 的保守說法，且將 direct development evidence 歸到 `iwin_gameserver`。

面試素材：

- 可講「分析過 OneAPI / PG bet_result provider callback 的 HMAC、transactionId duplicate guard、gameserver wallet boundary、Mongo audit 與 retry failure window」。

## 待補 Evidence

- Nick 本人 MR / ticket / commit / production issue / 本人確認。
- OneAPI 官方 callback contract。
- Mongo production index / unique constraint。
- gameserver wallet mutation 前的 idempotency guard 是否存在。
- provider retry / timeout SLA。

## 下一步

```text
iwin third_games_api oneapi-wallet-bet-result Step 4
```
