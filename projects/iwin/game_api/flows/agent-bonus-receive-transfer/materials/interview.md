# agent-bonus-receive-transfer interview notes

更新時間：2026-05-19
Step：3
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## Step 3 結論

這條 flow 已可作 code-backed 面試素材雛形。主軸是：

- 代理佣金可領餘額。
- Mongo source of truth vs Redis projection。
- GM 上分與本地清零的 failure window。
- 短 lock 與真正 idempotency 的差異。
- game_job 佣金結算與 game_api 領取 / 轉帳互斥。

目前不更新正式履歷 / 自傳，原因是未看到 Nick / `10gt12nc` direct path evidence。

## 3 分鐘講法

這條 flow 是 `game_api` 的代理佣金領取與轉帳。上游 `game_job` 會先算每日佣金，最後累加到 Mongo `agent_money.money`，這個欄位代表玩家目前可領佣金。`game_api` 提供兩個主要操作：`getDayFenx_transfer` 轉帳給下級，`getDayFenx_enter` 領到遊戲錢包。

轉帳時，系統會先檢查金額、不能轉給自己、短時間重複提交，以及收益計算 job 是否正在執行。接著確認對方是不是自己的團隊成員。成功後，先扣自己的 Mongo `agent_money.money`，再 upsert 對方的 `agent_money.money`，同步 Redis `GameAgent:{rid}`，最後寫兩筆轉帳紀錄。

領取時，系統查 `agent_money.money` 與 `transferMoney`，然後透過 `GM_CMD_DEPOSIT` 上分到 gameserver。一般佣金與轉帳佣金可能拆成兩次 GM deposit。GM 成功後，才把 Mongo 可領金額清零，更新 Redis，並寫入 `vc_countlog`。

Senior 角度我會先看 transaction boundary。GM、Mongo、Redis、log 不是同一個 transaction，所以 GM 成功但 Mongo 清零失敗時，玩家錢包已加，但系統仍可能顯示可領，導致重領風險。正式設計應有 operation id、pending state、deterministic billNos、重放安全與 reconciliation。

## 常見追問

| 問題 | 保守回答 |
| --- | --- |
| 佣金 source of truth 是 Redis 還是 Mongo？ | Mongo `agent_money` 比較像可領餘額 source of truth；Redis `GameAgent` 是 projection / 顯示快取。 |
| `@Transactional` 能保證一致嗎？ | 不能直接當作跨 Mongo、Redis、GM command 的 transaction；要看實際 transaction manager，這裡保守視為多資源 eventual consistency。 |
| 短 lock 能防重領嗎？ | 只能擋短時間連點，不是 idempotency。GM 成功但本地失敗後，lock 過期仍可能重送。 |
| GM 成功但 Mongo 清零失敗怎麼辦？ | 應標 unknown / pending，查 GM wallet log 後 reconciliation，不應讓玩家用新 billNos 重送。 |
| 轉帳扣自己成功、加對方失敗怎麼辦？ | 需要 transfer operation id 與可重放 / 補償機制；否則會出現雙邊 balance 不一致。 |
| game_job 正在算佣金時為什麼要擋？ | 因為 settlement 會累加 `agent_money.money`，若同時領取 / 轉帳，會有 race condition。 |

## Lead / Architect 追問

| 追問 | 回答方向 |
| --- | --- |
| 你會怎麼重構這條 flow？ | 先建立 operation table / collection，狀態 pending -> gm_success -> local_success / unknown；所有 side effect 帶 operation id。 |
| deterministic billNos 怎麼設計？ | 用 operation id 派生 billNos，同一 operation retry 不產生新錢包 side effect。 |
| Redis projection 壞了怎麼修？ | 從 Mongo `agent_money` 重建 `GameAgent:{rid}`，並定期做差異檢查。 |
| 領取拆兩次 GM deposit 合理嗎？ | 要看 reason 61 / 62 的帳務語意；如果拆分是報表需要，兩次都要有獨立但同 operation 的 idempotency。 |
| task status 怎麼定義？ | 至少要 idle、washing、washed、settling、settled、failed，game_api 只在 settled 狀態開放操作。 |

## Step 4 待補

Step 4 應把本稿整理成：

- 一句話 / 30 秒 / 2 分鐘 / 5 分鐘版本。
- STAR 版本。
- Senior Backend / Platform Backend / System Owner 口吻。
- 可說 / 不可說。

下一步：

```text
iwin game_api agent-bonus-receive-transfer Step 4
```
