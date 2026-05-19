# agent-bonus-receive-transfer decision notes

更新時間：2026-05-19
Step：5
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## Decision 1：Mongo `agent_money` 應是佣金可領餘額 source of truth

現況：

- `game_job` 把每日佣金累加到 Mongo `agent_money.money`。
- `game_api` 的轉帳 / 領取也讀寫 `agent_money.money`。
- Redis `GameAgent:{rid}.money` 主要用於顯示 / 快取。

建議：

- owner contract 要明確定義：Mongo 是 source of truth，Redis 是 projection。
- Redis / Mongo 不一致時，以 Mongo 重建 Redis。
- 補差異監控與修復工具。

## Decision 2：短 lock 不是 idempotency

現況：

- `Bonus:ReceiveLook:{rid}` 只擋短時間重複提交。
- `bonusReceive` 每次 GM 上分使用新的 UUID billNos。
- `transferReceive` 沒有 operation id 或外部 request id。

風險：

- lock 過期後重送可能形成第二次 money side effect。
- GM 成功、本地清零失敗後，玩家可再次領取。

建議：

- 轉帳 / 領取建立 operation id。
- 同一 operation retry 使用 deterministic billNos。
- Mongo 建 pending operation，再做 GM / Mongo / Redis / log 收斂。

## Decision 3：領取應有 pending / unknown 狀態

現況：

- `bonusReceive` 先 GM 上分，成功後才清 Mongo / 更新 Redis / 寫 log。
- GM catch 只回失敗；GM 成功後本地任一步失敗的狀態不明。

建議：

- 先寫 pending receive operation。
- GM 成功後改 success。
- Mongo / Redis / log 失敗時標 unknown，交給 reconciliation。

## Decision 4：轉帳是雙邊 balance update，要有 audit-first 或 atomic plan

現況：

- 先更新轉出者 Mongo，再 upsert 轉入者 Mongo，再更新 Redis，再寫兩筆 log。
- `@Transactional` 不能被視為跨 Mongo / Redis atomic transaction。

建議：

- 先寫 transfer operation，狀態 pending。
- 轉出 / 轉入用同一 operation id。
- 若任一步失敗，可以重放或補償，不靠人工猜測。

## Decision 5：game_job 與 game_api 要有明確互斥契約

現況：

- `game_api` 用 `Daily:Task:{date}.purchase_status` 判斷收益計算是否執行中。
- `game_job` settlement 會累加 `agent_money.money`。

建議：

- task status 應定義完整 lifecycle：idle、washing、washed、settling、settled、failed。
- game_api 只在 settled 後開放領取 / 轉帳。
- failed / partial 狀態應 fail closed，避免玩家在半成品資料上操作。
