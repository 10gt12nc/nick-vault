# Decision Notes - slot-bet-settle-rollback

日期: 2026-05-21

## 1. Single Wallet vs Transfer Wallet

| 類型 | 錢在哪裡 | 本 flow 責任 | 主要風險 |
| --- | --- | --- | --- |
| Single wallet | provider / agent side | Game API call provider bet / settle / rollback | provider timeout、重複 callback、notify repair |
| Transfer wallet | Game API local DB + Redis | 下注前扣本地 wallet，開獎後加 total win | DB / Redis / bet record 不一致、deadlock 補償 |

Owner 思考:

- Single wallet 的一致性重點是 provider callback idempotency。
- Transfer wallet 的一致性重點是本地 transaction boundary 與補償。

## 2. Bet Record Step 設計

`pt_bet_record` 用 step 表示一局 bet 的階段:

- `CREATE`: 初始建立。
- `DEAL`: 扣款成功 / 注單成立。
- `RESULT`: 開獎結果已寫入。
- `CANCEL`: 取消 / rollback。
- `FAIL`: 失敗狀態，但本 flow catch 中看到 fail 標記目前被註解。

設計價值:

- Step 是補通知 job 的查詢依據。
- `setStatusResult` 限制 `step = 2`，避免未成立注單直接寫開獎結果。

設計風險:

- Step 和 wallet mutation 不一定在同一 transaction boundary。
- Step 只能說明 Game API 本地狀態，不等於 provider 或 wallet 一定一致。

## 3. Retry / Compensation 分層

| 層 | 已看到的機制 | 限制 |
| --- | --- | --- |
| Provider notify retry | Quartz job 補 call `betSettle` / `betRollback` | 只補通知，不是完整對帳 |
| Request log audit | RabbitMQ async request log | 失敗只 log，不阻斷交易 |
| Transfer wallet compensation | `CompensationService#refundTMOnDeadlock` 存在 | flow catch 中實際呼叫被註解 |
| Bet record fail marking | `setStatusFail` 存在 | deadlock catch 中實際呼叫被註解 |

## 4. Owner 改善方向

如果要把這條 flow 做到更 owner-grade，可以補:

- 明確 transaction boundary：bet record DEAL、wallet debit、RESULT、wallet credit 分別在哪個 transaction。
- Outbox 或 repair table：把待補償 / 待通知變成可查、可重試、可告警的資料。
- Idempotency：provider settle / rollback 用 bet id 當 idempotency key，並確認 provider 語意。
- Reconciliation：定期比對 bet record、provider settle result、transfer wallet transaction。
- Observability：deadlock、notify retry、negative balance、MQ request log failure 都要有 metric / alert。

## 5. Step 4 面試決策主線

這條 case 面試時最值得呈現的 owner decision 不是「要不要用 MQ」，而是要把四種資料責任分層:

| 層 | 代表資料 / 機制 | 面試講法 |
| --- | --- | --- |
| State | `pt_bet_record` DEAL / RESULT / CANCEL / FAIL | 本地下注狀態主紀錄，但不等於 provider / wallet 一定一致 |
| Money side effect | transfer wallet DB + Redis、provider settle / rollback | 真正會影響玩家餘額，優先級高於 audit log |
| Repair | notify job、compensation service | 補通知與補償要可查、可重試、可告警 |
| Audit | request log MQ | 幫助 RCA 與客服查詢，不應阻斷主交易 |

面試回答優先順序:

1. 先說 source of truth 與 wallet type 差異。
2. 再說 state transition guard，例如 `setStatusResult` 只允許 DEAL -> RESULT。
3. 接著指出 failure window，尤其 transfer wallet 已變動後的 deadlock / partial failure。
4. 最後講 owner-grade 補強：idempotency、repair table / outbox-like pending state、reconciliation 與 alerts。

## 6. Step 5 Claim Decision

本 flow 的 claim gate 結論:

- 可以支撐 Nick / `10gt12nc` 參與 `antplay-slot-game-api` 下注結算主線與 transfer wallet failure-window 維護。
- 可以支撐 request log MQ async audit 的 direct evidence。
- 不應支撐「完整 production compensation 已落地」，因為本地 `develop` 看到 deadlock catch 裡的實際 refund / fail 標記呼叫被註解。
- 履歷包裝應放在 project-level `antplay-slot-game-api` 經驗，不應拆成 standalone owner claim。
