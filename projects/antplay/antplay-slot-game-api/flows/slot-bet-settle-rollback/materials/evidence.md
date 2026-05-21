# Evidence - slot-bet-settle-rollback

日期: 2026-05-21

## 1. 掃描範圍

Vault:

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `projects/antplay/antplay-slot-game-api/README.md`
- `projects/antplay/antplay-slot-game-api/step1-candidate-flows.md`
- `projects/antplay/antplay-slot-game-api/step2-flow-comparison.md`
- `projects/antplay/antplay-slot-game-api/contribution-claim-consolidation.md`

Source repo:

- `/Users/nick/Git/antplay/antplay-slot-game-api`
- branch: `develop`
- local HEAD: `079aa66`
- local `origin/develop`: `079aa66`
- ahead / behind: `0 / 0`
- source working tree: clean
- remote refs: 前一輪 fetch 失敗；依 KB 不反覆重試
- 最新性: 未確認最新遠端；依本地 refs / 本地 working tree 保守分析

## 2. Code Evidence

| Area | Path | Evidence |
| --- | --- | --- |
| API entry | `GameController.java:107-118` | `POST /game/bet` 呼叫 `gameFacade.bet(game, bo)` |
| Main orchestration | `GameFacade.java:497-855` | 驗證、agent / wallet、RTP、bet amount、扣款、bet record、math result、afterBet |
| Exception handling | `GameFacade.java:858-898` | deadlock / data access catch；transfer wallet refund 與 fail 標記目前被註解 |
| Transfer wallet debit | `GameFlowFacade.java:110-139` | transfer wallet 從 Redis 取 balance，非 voucher 時先扣 `betAmount * 100` |
| Bet record build | `GameFlowFacade.java:142-223` | create / save bet record，debug path 可 `setStatusDeal` |
| Result write | `GameFlowFacade.java:251-350` | `setStatusResult` 寫 detail / win / status，然後 settle |
| Settle | `GameFlowFacade.java:414-435` | 找 RESULT bet record，呼叫 `AgentApiFacade#betSettle`，成功後 `settleRedis` |
| Cancel / rollback | `GameFlowFacade.java:447-458` | `setStatusCancel` 後 call `betRollback` |
| Provider settle | `AgentApiFacade.java:146-240` | single wallet call provider，transfer wallet 本地加 `totalWin`，更新 notify |
| Provider rollback | `AgentApiFacade.java:248-268` | call provider rollback，更新 notify |
| Request log MQ | `AgentApiFacade.java:385-418` | provider request log 送 RabbitMQ；例外 catch + log |
| Bet state SQL | `BetRecordManageService.java:161-214` | DEAL / RESULT SQL update，RESULT 限制 `step = 2` |
| Cancel / fail SQL | `BetRecordManageService.java:284-314` | CANCEL / FAIL update；FAIL 在 deadlock catch 目前被註解 |
| Transfer wallet mutation | `TransferBalanceService.java:210-260` | DB wallet update 成功後 increment Redis |
| Compensation service | `CompensationService.java:28-31` | `refundTMOnDeadlock` 存在，但 flow catch 中呼叫被註解 |
| Notify job | `BetRecordNotifyMoneyInOutService.java:101-123` | 查 `findNotifySettle` 後補 call `betSettle(b, true)` |
| Rollback job | `BetRecordNotifyRollbackService.java:101-121` | 查 `findNotifyRollback` 後補 call `betRollback(b, true)` |

## 3. Git / Commit Evidence

Nick / `10gt12nc` path-specific log 線索:

- `31d7a46`、`54078fe`、`a2b2af5`: deadlock / transfer wallet compensation。
- `d3e0002`、`71fff7b`: RequestLog 改丟 RabbitMQ 非同步執行。
- `718a207`: db partition v2。
- `18d63b8`、`9d14f91`、`4a1cb57`: `UseSchema` / schema route 修正。
- `340c71b`: Redis 加 currency。
- `257e136`: deduct balance earlier。
- `7fa9b7a`: fix updatePlayerWallet。
- `09047fd`: request-log time / duplicate id issue。
- `e137c6f`: 新增 `bet_type`。

判斷:

- 這些能支撐「Nick / `10gt12nc` 有參與 game runtime / transfer wallet / request log / partition / deadlock 相關維護」。
- Step 3 不足以直接證明 Nick 主導整條 `slot-bet-settle-rollback`；需 Step 5 做 claim gate。

## 4. 已確認

- `/game/bet` 是主入口。
- `GameFacade#bet` 是主要 orchestration。
- Transfer wallet 下注前先扣本地 DB / Redis wallet。
- `afterBet` 寫 RESULT 後呼叫 settle。
- single wallet 走 provider settle / money_inout；transfer wallet 本地加 total win。
- request log MQ 失敗不阻斷主流程。
- 補通知 job 存在，會補 call settle / rollback。
- Deadlock catch 中的補償目前主要是 log，實際 refund / fail 標記呼叫被註解。

## 5. 未掃 / 待確認

- 未掃 provider 端實作。
- 未掃 live DB schema / migration 實際狀態。
- 未做 Level 3 逐 commit diff。
- 未確認 remote 最新狀態。
- 未完整掃 `antplay-slot-game-job`。
- 未確認 production incident / ticket。

## 6. Step 4 Output Evidence

2026-05-21 Step 4 補充:

- 重讀本 flow 的 `flow.md`、`career-interview.md`、`materials/evidence.md`、`materials/interview.md`、`materials/claim-boundary.md`、`materials/decision-notes.md`，並同步檢查 project README、inventory、todo 與 interview casebook。
- 本輪未新增 source repo 掃描，也未重試 fetch；依 KB 沿用 Step 3 的本地 refs / 本地 working tree evidence，遠端最新性仍標示為未確認。
- `career-interview.md` 已轉成 Step 4 正式面試主稿，包含 30 秒、90 秒、3 分鐘、STAR 版本、failure scenarios、owner 改善方向與 Step 5 待補。
- `materials/interview.md` 已補完整 Senior / Lead 追問、六個 failure scenarios、補通知與 reconciliation 的回答邊界。
- `flow.md` 已更新 Step 4 狀態與下一步 Step 5。
- 本輪不更新 `senior-owner-playbook/05-resume-master-zh.md` / `08-application-autobiography-zh.md`；正式履歷仍以 Step 5 claim gate 與 project-level contribution consolidation 為準。
