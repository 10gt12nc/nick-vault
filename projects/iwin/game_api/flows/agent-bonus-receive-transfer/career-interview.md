# agent-bonus-receive-transfer career interview

更新時間：2026-05-19
Step：5
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 面試定位

這條 flow 是 code-backed 面試案例，不是正式履歷 claim。它適合用來展示我能拆 money-like balance flow：Mongo 可領佣金、Redis projection、game_job 結算狀態、GM 上分、audit log 與 reconciliation。

目前 path-specific history 沒看到 Nick / `10gt12nc` 直接修改此 flow，所以面試時只能用「我分析過 / 我會這樣設計與排查」口吻，不能說「我開發 / 主導」。

## 一句話版本

我分析過 `game_api` 的代理佣金領取 / 轉帳 flow：上游 `game_job` 把佣金累加到 Mongo `agent_money`，玩家端 API 再做轉帳或 GM 上分；真正的風險是 GM、Mongo、Redis projection 與 audit log 不在同一個 transaction。

## 30 秒版本

這條 flow 有兩個主要操作：`transferReceive` 讓代理把可領佣金轉給下級，`bonusReceive` 讓代理把佣金領到遊戲錢包。轉帳會扣自己的 Mongo `agent_money.money`、加對方的 money、同步 Redis `GameAgent:{rid}`，再寫兩筆轉帳 log。領取則先送 GM deposit，成功後才清 Mongo 可領金額、更新 Redis、寫 `vc_countlog`。

我會把 Mongo `agent_money` 視為可領佣金 source of truth，Redis 只當 projection。短時間 Redis lock 只能防連點，不是真正 idempotency；如果 GM 成功但 Mongo 清零失敗，玩家錢包已加但可領餘額還在，這就是重領與對帳風險。

## 2 分鐘版本

代理佣金 flow 的上游在 `game_job`。`AgentBonusWashJob` 會計算每日佣金，`AgentBonusSettlementJob` 再把 `agent_day_money.bonus` 累加到 Mongo `agent_money.money`。`game_api` 負責玩家操作面：轉帳與領取。

轉帳入口是 `PartnerController#getDayFenx_transfer`，實作是 `AgentShareServiceImpl#transferReceive`。它會檢查金額、不能轉給自己、短時間 lock、收益計算 job 是否執行中，並確認 `cRid` 是自己的團隊成員。成功後先扣自己的 `agent_money.money`，再 upsert 對方的 `agent_money`，更新 Redis projection，最後寫轉出 / 轉入兩筆 `agent_transfer_Money`。

領取入口是 `getDayFenx_enter`，實作是 `bonusReceive`。它讀 `agent_money.money` 和 `transferMoney`，必要時拆成 reason 61 / 62 兩次 GM deposit。GM 成功後才把 Mongo `money` 清零、更新 Redis `GameAgent:{rid}.money` 與 `total_money`，再寫 `vc_countlog`。

Senior 角度我會先抓 transaction boundary：GM command、Mongo update、Redis update、Mongo log insert 都不是同一個 transaction。比較穩的設計會補 operation id、deterministic billNos、pending / success / unknown 狀態，以及能從 GM wallet log 和 Mongo 重新對帳的 reconciliation。

## 5 分鐘深講版

這條 flow 可以分成三段看。

第一段是「佣金生成」。佣金不是在 `game_api` 當下算出來的，而是由 `game_job` 先算每日佣金，再 settlement 到 Mongo `agent_money.money`。所以 `game_api` 看到的是可領餘額，不是原始投注 / 分潤規則。

第二段是「玩家操作」。轉帳與領取都先檢查 `Bonus:ReceiveLook:{rid}` 短 lock，再看 `Daily:Task:{date}.purchase_status`，避免 job 正在結算時操作半成品資料。轉帳還會檢查代理上下級 link，領取則會查玩家帳號資料與 channel money ratio 後送 GM。

第三段是「side effect 收斂」。轉帳至少有雙邊 Mongo balance、Redis projection、兩筆 audit log；領取至少有 GM wallet side effect、Mongo 清零、Redis projection、領取 log。這些 side effect 任一步失敗，都可能產生不同類型的帳務不一致。

如果我是 owner，我不會只補 try-catch。我會先定義 Mongo `agent_money` 是佣金 source of truth，Redis 是可重建 projection；再為轉帳 / 領取建立 operation id。領取要先寫 pending operation，再用 deterministic billNos 送 GM，GM 成功後才推進到 local success；如果 Mongo / Redis / log 任一步失敗，就標 unknown 交給 reconciliation。這樣重試才不會創造第二次錢包 side effect。

## STAR 版本

Situation：代理佣金系統支援玩家把可領佣金轉給下級，或領到遊戲錢包。

Task：我的分析目標是確認可領餘額、Redis projection、GM 上分與 audit log 的一致性邊界，並把它整理成可面試但不誇大的案例。

Action：我從 `PartnerController` 入口追到 `AgentShareServiceImpl`、`ShareCommonService`、Mongo model、Redis key、GM command，再補讀 `game_job` 的 `AgentBonusWashJob` / `AgentBonusSettlementJob`。我整理出短 lock 不等於 idempotency、job 狀態互斥、Mongo / Redis projection mismatch、GM 成功但本地清零失敗、audit log 缺失等 failure window。

Result：這條 flow 可以作為 Senior Backend 面試案例，用來講 money-like balance consistency、idempotency、transaction boundary 與 reconciliation。但因為沒有 Nick / `10gt12nc` direct path evidence，目前不寫進正式履歷，也不說我開發或主導。

## Senior Backend 口吻

我會強調 transaction boundary 與資料一致性：

- Mongo `agent_money` 是佣金可領餘額 source of truth。
- Redis `GameAgent:{rid}` 是 projection，壞了應能從 Mongo 重建。
- GM deposit 是外部 side effect，必須有 deterministic idempotency key。
- 短 lock 只能防連點，不等於可重試安全。
- audit log 若在最後才寫，失敗時會影響客服與對帳。

## Platform Backend 口吻

我會強調跨系統 contract：

- `game_job` 和 `game_api` 要有明確 task lifecycle，不能只靠單一 status 粗略判斷。
- `game_api` 和 gameserver GM command 要有 billNos 重送語意。
- Mongo / Redis / GM / audit log 要有共同 operation id。
- reconciliation 應能從 operation、GM wallet log、`agent_money` 與 audit log 反查同一筆事件。

## System Owner 口吻

我會強調 owner decision：

- 先定義哪個資料是真的：佣金可領餘額以 Mongo 為準，Redis 是 projection，錢包餘額以 gameserver 為準。
- 任何跨系統 side effect 都要能重試、查帳、補償。
- 玩家重送不能產生新 billNos；客服補單也不能靠人工猜。
- job failed / settling / partial 狀態要 fail closed，避免玩家在半成品資料上操作。

## 常見追問

| 問題 | 保守回答 |
| --- | --- |
| 佣金 source of truth 是 Redis 還是 Mongo？ | Mongo `agent_money` 比較像可領餘額 source of truth；Redis `GameAgent` 是 projection / 顯示快取。 |
| `@Transactional` 能保證一致嗎？ | 不能直接當作跨 Mongo、Redis、GM command 的 transaction；這裡保守視為多資源 eventual consistency。 |
| 短 lock 能防重領嗎？ | 只能擋短時間連點，不是 idempotency。GM 成功但本地失敗後，lock 過期仍可能重送。 |
| GM 成功但 Mongo 清零失敗怎麼辦？ | 應標 unknown / pending，查 GM wallet log 後 reconciliation，不應讓玩家用新 billNos 重送。 |
| 轉帳扣自己成功、加對方失敗怎麼辦？ | 需要 transfer operation id 與可重放 / 補償機制；否則會出現雙邊 balance 不一致。 |
| game_job 正在算佣金時為什麼要擋？ | settlement 會累加 `agent_money.money`，若同時領取 / 轉帳，會有 race condition。 |

## 可說

- code-backed 分析過 `game_api` 代理佣金領取 / 轉帳 flow。
- 能說明 `transferReceive` 如何扣 / 加 Mongo `agent_money`、更新 Redis projection、寫轉帳 log。
- 能說明 `bonusReceive` 如何送 GM 上分、清 Mongo、更新 Redis、寫領取 log。
- 能指出短 lock 不等於 idempotency、GM 成功後本地失敗可能重領、Mongo / Redis projection mismatch 等風險。
- 能提出 operation id、pending state、deterministic billNos、reconciliation 與監控設計。

## 不可說

- 我開發了代理佣金領取 / 轉帳。
- 我主導代理分潤系統或佣金結算。
- 我修復過佣金重領 / 錯帳 production incident。
- 這條可以直接寫進正式履歷。

## Step 5 結論

本 flow 已完成 Step 5 面試收斂。它可作為 code-backed 面試案例，但不更新正式履歷 / 自傳。

Step 5 已重新確認 Nick / `10gt12nc` evidence 與本人確認缺口：目前未看到 agent bonus path direct evidence，也沒有本人確認，因此不能說我開發或主導這條 flow。後續 `game_api contribution claim consolidation` 已完成，仍維持本 flow 只作 code-backed 面試素材。

```text
iwin iwin_gameserver center-http-deposit-withdraw Step 4
```
