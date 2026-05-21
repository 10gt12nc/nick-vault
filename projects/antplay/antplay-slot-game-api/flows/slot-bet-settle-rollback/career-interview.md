# slot-bet-settle-rollback Career Interview

日期: 2026-05-21

## 0. 定位

本檔是 `slot-bet-settle-rollback` Step 5 後的面試 / 履歷邊界稿。它把 Step 3 的 code-backed 深掃與 Step 4 面試稿，收斂成可演練的 Senior Backend / Platform Backend case；正式履歷仍以 project-level consolidation / rolling resume package 為準。

證據層級:

- Project-level: 真實開發過 + code-backed。
- Flow-level: 真實開發過 + code-backed；Nick / `10gt12nc` 有本 flow path-specific commits。
- Step 5 結論: 可作 `antplay-slot-game-api` project-level 履歷 claim 的強化 evidence，也可作 Senior Backend 面試主案例。

## 1. 30 秒講法

我深挖過 AntPlay slot game API 的下注結算主線。它不是單純 `/game/bet` 回一個開獎結果，而是同時串起 bet record state、single / transfer wallet、provider settle / rollback、request log MQ 與補通知 job。這個 case 我會拿來講 money correctness、state transition、failure window 和 owner boundary；但我不會誇大成完整 wallet / ledger owner。

## 2. 90 秒講法

這條 flow 從 `/game/bet` 進來，`GameFacade#bet` 先做玩家、game、currency、voucher / buy free 與 runtime decision 驗證，再算 bet amount。接著依 wallet type 分成兩種路徑：single wallet 主要靠 provider API 做 balance / bet / settle / rollback；transfer wallet 則由 Game API 在本地 DB / Redis wallet 先扣 bet amount，開獎後再加回 total win。

資料狀態的核心是 `pt_bet_record`，狀態從 DEAL 走到 RESULT，成功 settle 後更新 notify success；如果取消，會走 CANCEL 再 call rollback。面試時我會強調幾個風險：transfer wallet 已扣款但 bet record 或 RESULT 失敗、RESULT 已寫但 provider settle 失敗、request log MQ 失敗但不該影響主交易，以及 deadlock catch 目前看到補償呼叫被註解，不能把它講成完整補償。

## 3. 3 分鐘講法

我最近整理 AntPlay slot game API 的下注結算主線。這條 flow 從 `/game/bet` 進來，先做玩家、game、currency、voucher / buy free 的驗證，再依 agent wallet type 分成 single wallet 和 transfer wallet。

single wallet 主要靠 provider API 做 balance / bet / settle / rollback；transfer wallet 則是在 Game API 本地維護 wallet，下注前先從 DB / Redis wallet 扣 bet amount，開獎後再把 total win 加回去。

資料狀態上，核心是 `pt_bet_record`。一局 bet 會從 CREATE / DEAL 走到 RESULT，成功 settle 後更新 notify success；如果要 rollback，會先改成 CANCEL 再 call provider rollback。這裡最需要注意的是 failure window，例如 transfer wallet 已扣款但後面 deadlock、RESULT 已寫但 provider settle 失敗、request log MQ 失敗但主交易不該被阻斷。

這條 flow 我不會包裝成完整 wallet / ledger owner，但它很適合展示我對 money correctness、state transition、retry / compensation、observability 和 owner boundary 的理解。

## 4. STAR 版本

Situation:

Slot game API 的下注結算同時牽涉玩家餘額、bet record、provider callback、request log 與補通知 job；如果只看 Controller，很容易誤判真正的 commit boundary。

Task:

我把 `slot-bet-settle-rollback` 拆成可追的 production money flow，確認哪裡是交易狀態、哪裡只是 audit / projection，並整理出面試可講的 failure scenarios。

Action:

- 從 `GameController#bet` 追到 `GameFacade#bet`、`GameFlowFacade#getBeforeBetMoney / afterBet / settle / cancel`。
- 對照 `AgentApiFacade#betSettle / betRollback`，拆出 single wallet 與 transfer wallet 的不同一致性責任。
- 追 `BetRecordManageService` 的 DEAL / RESULT / CANCEL / FAIL state transition，確認 RESULT 更新依賴 `step = 2`。
- 檢查 request log MQ、notify job 與 deadlock compensation 的邊界，特別標出補償呼叫被註解的風險。

Result:

這條 case 可以用來說明我如何讀懂下注 / 結算主線、如何分辨 source of truth 與 audit log、如何找 failure window，以及如何保守界定「可面試講」和「正式履歷 claim」。

## 5. 可面試講

- `/game/bet` 如何串起 validation、bet amount、wallet 扣款、bet record、math result、settle / rollback。
- single wallet 與 transfer wallet 的差異：single wallet 靠 provider，transfer wallet 在 Game API 本地 DB / Redis mutation。
- `pt_bet_record` 的 step transition：CREATE、DEAL、RESULT、CANCEL、FAIL。
- 為什麼 `setStatusResult` 要限制 `step = 2`。
- RESULT 後 settle 失敗時，補通知 job 如何依 `notify = 0`、`step = 4`、`notify_count` 補 call `betSettle`。
- request log MQ 是 audit / observability，不是交易 source of truth。
- deadlock catch 有算 refund，但實際 refund / fail 標記目前被註解，所以要保守講成「風險點與待補 evidence」，不能講成已完整實作。

## 6. 5 個 Failure Scenario

1. Transfer wallet 已扣款，但 bet record save / RESULT 更新失敗。
   - 講法: 先問 wallet debit 與 bet record 是否同一 transaction；目前 Step 3 evidence 不能證明完整 atomic。
2. RESULT 已寫入，但 provider settle / money_inout 失敗。
   - 講法: notify job 可依 `notify = 0`、`step = RESULT` 補 call settle，但這是 retry / repair，不是完整 reconciliation。
3. Single wallet bet callback 成功與本地 bet record 狀態不一致。
   - 講法: 要靠 bet id、state transition、rollback path 與 provider response audit 追，不可只看 API 是否回成功。
4. CANCEL 已寫入，但 rollback call provider 失敗。
   - 講法: rollback notify job 可補 call；仍要確認 idempotency、notify_count 上限與 exhausted 狀態。
5. Deadlock / DataAccessException 發生在 transfer wallet 已變動後。
   - 講法: code 中看得到 refund amount 計算與 `CompensationService#refundTMOnDeadlock`，但實際呼叫目前被註解，所以 owner 要優先補成可觀測、可重試的補償流程。

補充:

- Request log MQ 失敗屬 audit loss / delay，不應 rollback 主交易；但要有告警與補寫策略。

## 7. Owner 改善方向

如果我是 owner，我會把改善拆成四層:

- State: 明確定義 DEAL、RESULT、CANCEL、FAIL 與 notify success 的狀態語意，避免「record 狀態成功」和「provider / wallet 成功」混在一起。
- Idempotency: provider settle / rollback 以 bet id 或 deterministic transaction id 做冪等，並確認 provider 端重送語意。
- Repair: 把 notify retry、transfer wallet compensation、deadlock refund 變成可查、可重試、可告警的 repair table / outbox-like 流程。
- Reconciliation: 定期比對 bet record、wallet transaction、provider response、notify status；request log MQ 只作 audit，不當唯一真相。

## 8. 可放履歷候選句

Step 5 後可併入 project-level bullet:

> 參與 AntPlay slot game API 下注 / 結算主線維護，理解 `/game/bet` 到 bet record、single / transfer wallet、provider settle / rollback 與補通知的 failure window。

更適合放在 `antplay-slot-game-api` project-level 經驗下，不建議單獨拉成一條「完整下注結算 owner」履歷 bullet。`05` / `08` 若要更新，仍應由 project-level consolidation / rolling resume package 統一吸收。

## 9. 不可誇大

- 不說主導完整 AntPlay slot betting engine。
- 不說獨立設計完整 wallet / ledger / reconciliation。
- 不說建立 exactly-once / outbox / full compensation。
- 不說完整 RTP / dark pool / player control owner。
- 不說完整 provider settle / rollback owner。
- 不說已解決 deadlock 補償，因為目前看到的 refund / fail 標記呼叫被註解。

## 10. Step 5 Claim Gate

- 可放履歷: 參與 AntPlay slot game API / runtime 開發維護，處理下注、bet record、single / transfer wallet、provider settle / rollback、request log MQ 與補通知相關一致性議題。
- 可面試講: 用 `/game/bet` 展示 money correctness、state transition、failure window、retry / compensation、observability 與 owner boundary。
- 不可誇大: 不說主導完整 slot betting engine、不說完整 wallet / ledger / reconciliation owner、不說 deadlock compensation 已完整落地。
- 不直接更新 05 / 08: 單條 flow Step 5 是 evidence；正式輸出版仍應吃 project-level consolidation。
