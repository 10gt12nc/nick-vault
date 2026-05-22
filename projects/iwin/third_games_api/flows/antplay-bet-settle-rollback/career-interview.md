# Antplay bet / settle / rollback Career Interview

## 定位

本文件是 `antplay-bet-settle-rollback` Step 3 的保守面試素材初版。正式面試 case 會在 Step 4 補齊。

證據層級：`專案存在 / code-backed`、`分析素材 / learning-only`。目前不更新正式履歷 / 自傳。

## 可用說法

可以說：

> 我有深讀過第三方遊戲 Antplay 舊版三段式 bet / settle / rollback flow，重點看 adapter 驗簽、Redis game / center routing、gameserver wallet mutation、Mongo transaction evidence，以及 retry / duplicate / Mongo 寫失敗造成的 money consistency 風險。

更精準一點：

> 這條 flow 的 adapter 會在 bet 時扣款並寫 step 1，settle / rollback 則要求 step 1 存在，再回查原始 bet 後打 gameserver 派彩或退款。風險是 gameserver 已改錢但 Mongo step 沒寫成功時，provider retry 可能因查不到 evidence 而重複扣款、派彩或退款，所以 owner decision 會希望把 idempotency 放到 wallet mutation boundary 或 durable request 狀態機。

## 不可用說法

不可說：

- 我主導 Antplay 對接。
- 我完整設計 `third_games_api` 的 Antplay adapter。
- 我負責完整第三方遊戲錢包 / 對帳 / reconciliation。
- 我已確認 production 仍走舊 `/antplay/bet`、`/settle`、`/rollback`。

## 面試可追問點

1. 為什麼 adapter Mongo step 不能當唯一 idempotency guard？
2. `bet` 成功但 Mongo step 1 寫失敗時，後續 `settle` / `rollback` 會怎樣？
3. `settle` / `rollback` retry 為什麼可能 double apply？
4. 原始 bet 金額應該放在 log collection 還是 transaction evidence？
5. 舊三段式 flow 和新整合式 `bet_settle-new` 可能代表什麼系統演進？

## Step 4 待補

- 30 秒版。
- 90 秒版。
- 3 分鐘版。
- STAR 故事。
- Senior / Lead 追問與回答。
- 和 GSC / OneAPI flow 的對照講法。
