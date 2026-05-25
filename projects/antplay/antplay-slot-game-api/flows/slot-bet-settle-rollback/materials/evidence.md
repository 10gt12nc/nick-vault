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
- `career-interview.md` 已轉成 Step 4 正式面試主稿，包含 30 秒、90 秒、3 分鐘、STAR 版本、failure scenarios、owner 改善方向以及當時 Step 5 待補（現已完成）。
- `materials/interview.md` 已補完整 Senior / Lead 追問、六個 failure scenarios、補通知與 reconciliation 的回答邊界。
- `flow.md` 已更新 Step 4 狀態與下一步 Step 5。
- 本輪不更新 `senior-owner-playbook/05-resume-master-zh.md` / `08-application-autobiography-zh.md`；正式履歷仍以 Step 5 claim gate 與 project-level contribution consolidation 為準。

## 7. Step 5 Claim Gate Evidence

2026-05-21 Step 5 補充:

- 重讀 KB、project README、contribution consolidation、Step 2、flow 主檔、career-interview、materials 與共用 inventory。
- Source repo 仍沿用本地 refs / 本地 working tree；前一輪 fetch 已失敗，依 KB 不重試。local `develop` = `079aa66`，本機既有 `origin/develop` = `079aa66`，ahead / behind = `0 / 0`。
- 重新追本 flow path-specific history、重要 diff、branch contains 與 current blame；本輪只讀 source repo，未改公司專案。

### Direct / Context Commit 判斷

| Commit | Author | 判斷 | Step 5 解讀 |
| --- | --- | --- | --- |
| `a2b2af5` | `10gt12nc` | direct evidence | 新增 `CompensationService`，在 `GameFacade#bet` 包 deadlock / lock exception handling，計算 transfer wallet refund 並呼叫補償 / cancel path 的早期版本 |
| `54078fe` | `10gt12nc` | direct evidence | 導入 `WalletFlag`，讓 `afterBet -> settle -> betSettle` 回傳 transfer wallet 是否已加 `totalWin`，用來計算 deadlock refund amount |
| `31d7a46` | `10gt12nc` | direct evidence | 清理 deadlock 補償相關註解 / log / exception propagation，強化 current flow 的 failure-window trace |
| `d3e0002` / `71fff7b` | `10gt12nc` | direct evidence | `AgentApiFacade` request log 從同步 DB log 改成 RabbitMQ async message，與本 flow provider call audit / observability 直接相關 |
| `257e136` / `7fa9b7a` | `eliot` | context evidence | transfer wallet 扣款提前與 `totalWin` 加回修正，屬本 flow 行為背景，不作 Nick direct claim |
| `09047fd` | `arnold` | context evidence | request-log time / duplicate bet id 修正，說明 bet id / request time 對 request log audit 的重要性，不作 Nick direct claim |

### Current Code Blame 摘要

- `GameFacade#bet` 的 deadlock try/catch、`WalletFlag holder`、`afterBet(... holder)` 與 refund 計算大量由 `10gt12nc` commits 留下。
- `AgentApiFacade#betSettle` 的 transfer wallet `walletChangeFlag.setTransferWalletChanged(...)`、notify count / success 相關行為有 `10gt12nc` direct lines。
- `AgentApiFacade#sendRequestLogMq` 主要由 `10gt12nc` 的 `d3e0002` 留下，是 request log async audit 的直接 evidence。
- `GameFlowFacade#getBeforeBetMoney` 的「下注前先扣 transfer wallet」目前 blame 為 `eliot`；因此只能作 flow context，不作 Nick direct claim。
- 目前 `GameFacade#bet` 中 `compensationService.refundTMOnDeadlock(...)` 與 `betRecordManageService.setStatusFail(...)` 實際呼叫在本地 `develop` 被註解；所以不能宣稱 deadlock compensation 已完整落地。

### Step 5 判定

- 可升級: 本 flow 可作為 `antplay-slot-game-api` project-level 履歷 claim 的強化 evidence。
- 可寫口徑: 「參與 AntPlay slot game API / runtime 開發維護，處理下注、bet record、single / transfer wallet、provider settle / rollback、request log MQ 與補通知相關一致性議題」。
- 可面試主案例: money correctness、state transition、failure window、retry / compensation、observability、owner boundary。
- 不可誇大: 不寫主導完整 slot betting / settlement engine；不寫完整 wallet / ledger / reconciliation；不寫 deadlock 補償已完整上線；不寫 exactly-once / outbox。
- 不更新 `05` / `08`: 單條 flow Step 5 只作 flow-level evidence；正式履歷輸出仍由 project-level consolidation / rolling resume package 統一吸收。
