# agent-bonus-receive-transfer career interview

更新時間：2026-05-19
Step：3
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 面試定位

這條 flow 適合當作「佣金可領餘額如何轉成 wallet side effect」的 code-backed 面試案例。它能展示我會怎麼拆 Mongo balance、Redis projection、短 lock、game_job 結算狀態、GM 上分與 reconciliation。

這不是正式履歷 claim。目前 path-specific history 沒看到 Nick / `10gt12nc` 直接修改此 flow，所以只能說「我分析過 / 我會這樣設計與排查」，不能說「我開發 / 主導」。

## 一句話版本

我分析過 `game_api` 的代理佣金領取 / 轉帳 flow：佣金由 `game_job` 算入 Mongo `agent_money`，`game_api` 允許代理轉帳給下級或透過 GM command 領到遊戲錢包；核心風險是 Mongo、Redis projection、GM 上分與 audit log 不在同一個 transaction。

## 30 秒版本

這條 flow 有兩個入口：`getDayFenx_transfer` 做佣金轉帳，`getDayFenx_enter` 做佣金領取。轉帳會扣自己的 Mongo `agent_money.money`，加到對方 `agent_money.money`，同步 Redis `GameAgent:{rid}`，再寫轉帳 log。領取會先送 GM 上分，成功後清 Mongo 可領金額、更新 Redis，再寫 `vc_countlog`。

我會特別看三件事：第一，短時間 Redis lock 不是 idempotency；第二，GM 成功但 Mongo 清零失敗可能造成重領；第三，Redis projection 與 Mongo source of truth 不一致時要能對帳。

## 2 分鐘版本

代理佣金 flow 的上游是 `game_job`。`AgentBonusWashJob` 先算每日佣金，`AgentBonusSettlementJob` 再把 `agent_day_money.bonus` 累加到 Mongo `agent_money.money`。`game_api` 則負責玩家操作面：轉帳與領取。

轉帳入口在 `PartnerController#getDayFenx_transfer`，實作是 `AgentShareServiceImpl#transferReceive`。它會檢查金額、不能轉給自己、短時間重複提交、收益計算 job 是否執行中，還會確認轉帳對象在自己的代理 link 裡。然後先扣自己的 Mongo `agent_money.money`，再 upsert 對方的 `agent_money`，更新 Redis `GameAgent:{rid}`，最後寫兩筆 `agent_transfer_Money`。

領取入口在 `getDayFenx_enter`，實作是 `bonusReceive`。它查 `agent_money.money` 和 `transferMoney`，用 GM command 對 gameserver 上分。一般佣金與轉帳佣金可能拆成兩次 GM deposit，成功後才清 Mongo `money=0`、更新 Redis、寫 `vc_countlog`。

Senior 角度的重點是 transaction boundary。GM 上分、Mongo update、Redis update、log insert 不是同一個 transaction；如果 GM 成功但後面失敗，玩家錢包已變，但 Mongo 可領金額可能仍存在。正式設計應該有 operation id、deterministic billNos、pending / success / failed 狀態與 reconciliation。

## STAR 版本

Situation：代理佣金系統需要支援玩家把可領佣金轉給下級，或領到遊戲錢包。

Task：我的分析目標是確認佣金可領餘額、Redis projection、GM 上分與 audit log 的一致性邊界，並判斷能否作履歷 claim。

Action：我從 `PartnerController`、`AgentShareServiceImpl`、`ShareCommonService`、Mongo model、Redis key、GM command 追到 `game_job` 的 `AgentBonusWashJob` / `AgentBonusSettlementJob`，整理出短 lock、job 狀態互斥、Mongo / Redis projection mismatch、GM 成功但本地清零失敗與 log 缺失風險。

Result：這條 flow 可作 Senior Backend 面試案例，用來講 money-like balance consistency、idempotency 與 reconciliation。但因未看到 Nick / `10gt12nc` direct path evidence，目前不寫入正式履歷，也不說自己開發或主導。

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

## 下一步建議

```text
iwin game_api agent-bonus-receive-transfer Step 4
```
