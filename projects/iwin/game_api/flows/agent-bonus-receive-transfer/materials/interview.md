# agent-bonus-receive-transfer interview notes

更新時間：2026-05-19
Step：5
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## Step 5 前檢查

Step 3 已完成，且可沿用：

- `flow.md`：已整理白話導讀、Code 分層、最小架構圖、正常流程、transaction boundary、failure window、owner decision。
- `materials/evidence.md`：已記錄 `game_api` / `game_job` remote refs、code path、path-specific history、Nick / `10gt12nc` author filter。
- `materials/decision-notes.md`：已補 Mongo source of truth、短 lock vs idempotency、pending / unknown、雙邊 balance、job lifecycle。
- `materials/claim-boundary.md`：已明確標示 code-backed，不更新正式履歷。

本輪 Step 5 只做面試收斂，不新增履歷 claim。

## 面試主軸

這條 flow 不是「代理功能介紹」，而是「佣金可領餘額如何轉成 wallet side effect」。

面試時應抓三個核心：

1. `game_job` 算出佣金，Mongo `agent_money` 保存可領餘額。
2. `game_api` 讓代理轉帳給下級或領到遊戲錢包。
3. GM、Mongo、Redis、audit log 跨資源不一致，需要 idempotency、pending state、reconciliation。

## 30 秒講法

我分析過 `game_api` 的代理佣金領取 / 轉帳 flow。佣金先由 `game_job` 算入 Mongo `agent_money`，`game_api` 再提供轉帳與領取。轉帳會更新雙方 `agent_money`、Redis projection 與轉帳 log；領取會先送 GM 上分，再清 Mongo 可領金額、更新 Redis、寫領取 log。

Senior 角度我會注意：短 lock 只能防連點，不是真正 idempotency；GM 成功但 Mongo 清零失敗會有重領風險；Redis 只是 projection，必須能從 Mongo 對帳和重建。

## 3 分鐘講法

這條 flow 的上游是 `game_job`。`AgentBonusWashJob` 先算每日佣金，`AgentBonusSettlementJob` 再把 `agent_day_money.bonus` 累加到 Mongo `agent_money.money`，這代表玩家目前可領佣金。

`game_api` 有兩個主要入口。第一個是 `getDayFenx_transfer`，讓代理把可領佣金轉給下級。實作 `transferReceive` 會檢查金額、不能轉給自己、短時間重複提交、收益計算 job 是否執行中，還會確認對方在自己的代理 link 裡。成功後會扣自己的 Mongo `agent_money.money`，upsert 對方 `agent_money`，同步 Redis `GameAgent:{rid}`，最後寫兩筆 `agent_transfer_Money`。

第二個是 `getDayFenx_enter`，讓代理把佣金領到遊戲錢包。`bonusReceive` 會查 `agent_money.money` 和 `transferMoney`，必要時拆成 reason 61 / 62 兩次 GM deposit。GM 成功後才清 Mongo `money=0`、更新 Redis、寫 `vc_countlog`。

這裡的關鍵是 transaction boundary。GM command、Mongo update、Redis update、Mongo log insert 都不是同一個 transaction。如果 GM 已經上分，但 Mongo 清零或 log insert 失敗，系統會進入不明狀態。正式 owner 設計應該先建立 operation id 和 pending state，用 deterministic billNos 送 GM，後續再用 reconciliation 收斂，而不是讓玩家用新 UUID 重送。

## 5 分鐘講法

我會把這條 flow 拆成「佣金來源、玩家操作、side effect 收斂」三層。

佣金來源層由 `game_job` 處理。它先根據每日資料算出佣金，再 settlement 到 Mongo `agent_money.money`。所以 `game_api` 不是原始佣金計算者，它是可領餘額操作入口。

玩家操作層分轉帳與領取。轉帳更新雙邊 `agent_money`，領取則呼叫 GM 把佣金變成 wallet balance。兩者都用了 `Bonus:ReceiveLook:{rid}` 做短時間 lock，也會檢查 `Daily:Task:{date}.purchase_status`，避免 job 正在結算時操作。

side effect 收斂層是風險最高的地方。轉帳會先扣自己、加對方、更新 Redis、寫兩筆 log；領取會先 GM 上分，再清 Mongo、更新 Redis、寫領取 log。任何一步失敗，都會造成不同種類的不一致：玩家看到錯誤餘額、audit log 缺失、GM 錢包和 Mongo 可領餘額不一致，甚至重領。

我的 owner decision 會是：Mongo `agent_money` 是佣金 source of truth，Redis 是 projection；轉帳 / 領取都要有 operation id；GM billNos 要由 operation id 派生；本地 side effect 失敗要標 unknown 並由 reconciliation 查 GM wallet log 和 Mongo 狀態收斂。

## STAR 版本

Situation：代理佣金系統支援玩家把可領佣金轉給下級，或領到遊戲錢包。

Task：我需要把這條 flow 的資料狀態、跨系統 side effect 與一致性風險整理成可面試案例，同時避免誇大成我實作過。

Action：我讀了 `PartnerController`、`AgentShareServiceImpl`、`ShareCommonService`、Mongo model、Redis key、GM command 使用點，也補讀 `game_job` 的 `AgentBonusWashJob` / `AgentBonusSettlementJob`。我把 flow 拆成 Mongo source of truth、Redis projection、GM wallet side effect、audit log 與 job lifecycle，整理出重送、部分成功、projection mismatch、log 缺失和 job race 的風險。

Result：這條 flow 可以支撐 Senior Backend 面試中的 idempotency、transaction boundary、failure window、reconciliation 討論；但目前沒有 Nick / `10gt12nc` direct path evidence，所以只作 code-backed 面試素材，不寫正式履歷。

## Senior / Lead 追問

| 追問 | 回答方向 |
| --- | --- |
| 你怎麼定 source of truth？ | 佣金可領餘額以 Mongo `agent_money` 為準，Redis 是 projection，wallet balance 以 gameserver 為準。 |
| 你會怎麼補 idempotency？ | 建 operation id；同一 operation retry 使用 deterministic billNos；轉帳與領取都用 operation state 收斂。 |
| GM 成功、本地失敗怎麼辦？ | 標 unknown，不讓玩家用新 billNos 重送；由 reconciliation 查 GM wallet log 後補清 Mongo / Redis / log。 |
| 為什麼短 lock 不夠？ | 它只能防短時間連點，不能覆蓋 timeout、服務 crash、lock 過期後 retry，也不能保證 GM side effect 不重複。 |
| 轉帳雙邊更新要怎麼做？ | 先寫 pending transfer operation，再更新雙方 balance；每一步都帶 operation id，可重放或補償。 |
| job lifecycle 怎麼設計？ | 至少要 washing、washed、settling、settled、failed；API 只在 settled 狀態開放領取 / 轉帳。 |

## 反問面試官

- 你們 money-like balance flow 會怎麼定 source of truth 和 projection？
- 外部 wallet / GM command timeout 時，你們是以查單補償、message retry，還是人工 reconciliation 為主？
- 對於玩家可見餘額和實際 wallet balance 不一致，你們有自動修復或告警嗎？

## 可說 / 不可說

可說：

- 我分析過代理佣金領取 / 轉帳 flow。
- 我能指出 Mongo、Redis、GM、audit log 的一致性邊界。
- 我會用 operation id、deterministic billNos、pending / unknown state 和 reconciliation 改善。

不可說：

- 我開發或主導這條代理佣金 flow。
- 我修過佣金重領 production incident。
- 我負責完整代理分潤 / 佣金結算系統。
- 這條已可直接寫進正式履歷。

## Step 5 結論

Step 5 已完成，這條 flow 已正式收斂為 code-backed 面試案例。重新確認後，目前沒有 Nick / `10gt12nc` agent bonus path direct evidence，也沒有本人確認，因此不升級成真實開發過、不寫正式履歷。後續 `game_api contribution claim consolidation` 已完成，仍維持本判斷。

```text
- 歷史下一步已完成：iwin_gameserver center-http-deposit-withdraw 已完成到 Step 5；目前沒有預設下一步，請以 senior-owner-playbook/01-senior-owner-flow-inventory.md 與 senior-owner-playbook/06-todo.md 為準。
```
