# Claim Boundary - slot-bet-settle-rollback

日期: 2026-05-21

## 1. 目前可說

- 已完成 `slot-bet-settle-rollback` Step 3 Level 2 code-backed 深掃與 Step 4 面試 case。
- 可說「已深挖 AntPlay game API `/game/bet` 的下注 / 開獎 / settle / rollback 主線」。
- 可說「能解釋 single wallet 與 transfer wallet 在 bet flow 裡的責任差異」。
- 可說「能指出 bet record RESULT 後 settle 失敗、transfer wallet 扣款後 deadlock、request log MQ failure 等 failure windows」。
- 可說「能把補通知 job、request log MQ、deadlock compensation 風險拆成 source of truth / audit / repair / reconciliation 四層」。

## 2. 暫時不寫正式履歷

Step 4 不直接更新:

- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

原因:

- Flow Step 4 是面試 case 整理，不是 claim gate。
- 是否能寫成正式履歷，要等 Step 5 做單條 flow claim boundary。
- Project-level 履歷仍以 `contribution-claim-consolidation.md` 的 rolling 結論為主。

## 3. 不可誇大

- 不說主導完整 slot betting / settlement engine。
- 不說主導完整 wallet / ledger / reconciliation。
- 不說完整 provider callback owner。
- 不說完整 RabbitMQ / Kafka architecture owner。
- 不說 exactly-once / outbox。
- 不說 deadlock compensation 已完整落地，因為 refund / fail 標記目前看到被註解。

## 4. Step 5 前待補 evidence

- 追 `31d7a46`、`54078fe`、`a2b2af5` 重要 diff，看 deadlock compensation 從哪裡來、後續是否 revert / comment out。
- 追 `257e136`、`7fa9b7a`，確認 transfer wallet 扣款提前與 update wallet 修正的上下文。
- 追 `09047fd`，確認 request log duplicate id issue 與 bet id / request time 的關係。
- 若需要更強 claim，補 production issue / ticket / MR 或 Nick 本人確認。
