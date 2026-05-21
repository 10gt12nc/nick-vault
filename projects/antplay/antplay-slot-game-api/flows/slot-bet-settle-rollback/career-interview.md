# slot-bet-settle-rollback Career Interview

日期: 2026-05-21

## 0. 定位

本檔是 `slot-bet-settle-rollback` Step 3 後的保守面試 / 履歷素材。它不是正式履歷更新，也不是 Step 5 claim gate。

證據層級:

- Project-level: 真實開發過 + code-backed。
- Flow-level: code-backed / path-specific commits 線索；待 Step 5 判斷能否升級成正式 flow claim。

## 1. 3 分鐘講法草稿

我最近整理 AntPlay slot game API 的下注結算主線。這條 flow 從 `/game/bet` 進來，先做玩家、game、currency、voucher / buy free 的驗證，再依 agent wallet type 分成 single wallet 和 transfer wallet。

single wallet 主要靠 provider API 做 balance / bet / settle / rollback；transfer wallet 則是在 Game API 本地維護 wallet，下注前先從 DB / Redis wallet 扣 bet amount，開獎後再把 total win 加回去。

資料狀態上，核心是 `pt_bet_record`。一局 bet 會從 CREATE / DEAL 走到 RESULT，成功 settle 後更新 notify success；如果要 rollback，會先改成 CANCEL 再 call provider rollback。這裡最需要注意的是 failure window，例如 transfer wallet 已扣款但後面 deadlock、RESULT 已寫但 provider settle 失敗、request log MQ 失敗但主交易不該被阻斷。

這條 flow 我不會包裝成完整 wallet / ledger owner，但它很適合展示我對 money correctness、state transition、retry / compensation、observability 和 owner boundary 的理解。

## 2. 可面試講

- `/game/bet` 如何串起 validation、bet amount、wallet 扣款、bet record、math result、settle / rollback。
- single wallet 與 transfer wallet 的差異：single wallet 靠 provider，transfer wallet 在 Game API 本地 DB / Redis mutation。
- `pt_bet_record` 的 step transition：CREATE、DEAL、RESULT、CANCEL、FAIL。
- 為什麼 `setStatusResult` 要限制 `step = 2`。
- RESULT 後 settle 失敗時，補通知 job 如何依 `notify = 0`、`step = 4`、`notify_count` 補 call `betSettle`。
- request log MQ 是 audit / observability，不是交易 source of truth。
- deadlock catch 有算 refund，但實際 refund / fail 標記目前被註解，所以要保守講成「風險點與待補 evidence」，不能講成已完整實作。

## 3. 可放履歷候選句

Step 3 暫不直接放履歷。若 Step 5 補足 claim gate，可考慮併入 project-level bullet:

> 參與 AntPlay slot game API 下注 / 結算主線維護，理解 `/game/bet` 到 bet record、single / transfer wallet、provider settle / rollback 與補通知的 failure window。

目前這句只能作面試素材候選，不直接寫進 05 / 08。

## 4. 不可誇大

- 不說主導完整 AntPlay slot betting engine。
- 不說獨立設計完整 wallet / ledger / reconciliation。
- 不說建立 exactly-once / outbox / full compensation。
- 不說完整 RTP / dark pool / player control owner。
- 不說完整 provider settle / rollback owner。
- 不說已解決 deadlock 補償，因為目前看到的 refund / fail 標記呼叫被註解。

## 5. Step 4 應補

- 整理成 3 分鐘正式面試稿。
- 補 5 個 failure scenario。
- 補 Senior / Lead 追問與回答。
- 補「如果你是 owner 會怎麼改」：transaction boundary、outbox / retry、idempotency、compensation、reconciliation。
