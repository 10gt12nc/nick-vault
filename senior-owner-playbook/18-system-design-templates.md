# System Design Templates

本檔是可選架構加強，不是投遞前必做。

目的：把已完成 evidence 的 production flows 抽象成面試可講的 0 到 1 system design template。它不是每個系統重掃 code，也不是宣稱 Nick 主導過完整 0 到 1 大系統。

## 使用邊界

- 證據來源：已完成的 `flow.md`、`career-interview.md`、`contribution-claim-consolidation.md`、domain `architecture-map.md` / `integration-map.md`。
- 掃描深度：Level 2 萃取。必要時才回 code 補查關鍵邊界；不做全 repo Level 3 重掃。
- 面試定位：展示從 production flow 抽象出 API、state、DB、MQ、idempotency、reconciliation、observability 與 rollout plan 的能力。
- 履歷邊界：不新增正式履歷 claim；正式履歷仍以 `05 / 08` 與 project-level contribution consolidation 為準。

推薦模板：

| 順序 | Template | 定位 | 狀態 |
| --- | --- | --- | --- |
| 1 | Provider Integration template | payment provider、遊戲 provider、callback、query、補償、對帳 | v1 completed |
| 2 | Wallet / Bet-Settle template | wallet source of truth、bet record、settle、rollback、transaction boundary | pending |
| 3 | MQ / Batch / Projection template | Kafka / RabbitMQ、report projection、retry、DLQ、重跑、資料修復 | pending |
| 4 | Slot Math / RTP Validation template | math-core contract、simulation、result validation、版本相容 | pending / optional |

## Provider Integration Template v1

### 1. 面試定位

這份模板回答：

```text
如果我要從 0 設計一個第三方 provider integration 系統，
如何處理 request、callback、query、timeout unknown、重送、補償、對帳與人工修復？
```

適用場景：

- 第三方金流 provider 充值 / 提現。
- 第三方遊戲 provider seamless wallet / transfer wallet。
- Provider callback -> MQ -> admin/report persistence。
- 商戶 / provider gateway 類 backend 職缺。

不適用或不可誇大：

- 不代表 Nick 主導過完整 payment platform。
- 不代表 Nick 設計過完整 wallet / ledger / reconciliation。
- 不代表系統已具備 exactly-once、完整 outbox 或全自動對帳。
- 不把 `k3s-deploy`、system map 或 analysis-only flow 升級成正式履歷成果。

### 2. Evidence 對照

| Evidence | 用途 | Claim 邊界 |
| --- | --- | --- |
| `projects/iwin/payment/flows/payment-order-provider-request/flow.md` | payment provider request、merchant order id、sign、amount unit、provider accepted / timeout unknown | Nick 可保守寫參與多 provider request / query / callback；不可寫完整金流 owner |
| `projects/iwin/payment/flows/payment-provider-callback/flow.md` | callback guard、ack、MQ、order state、上分 / 退款副作用 | code-backed callback framework 分析；不單獨寫成 callback owner |
| `projects/iwin/payment/contribution-claim-consolidation.md` | payment project-level claim gate | 可寫 provider / 商戶對接；不可寫 wallet / ledger / reconciliation owner |
| `projects/iwin/iwin_gameserver/flows/third-party-transfer-in-out/career-interview.md` | 遊戲 provider 投派、wallet mutation、log projection、reconciliation 風險 | 可併入第三方 provider 投派整合；不可寫完整 gameserver wallet owner |
| `projects/ugsoft/ugsoft-connector-api/flows/transfer-wallet-in-out-query/flow.md` | transfer wallet request / query、provider transaction lookup、Redis guard、DB lookup | 可寫 provider connector / transfer wallet；不可寫完整 wallet owner |
| `projects/ugsoft/ugsoft-connector-api/flows/provider-callback-bet-settle-to-mq/flow.md` | provider callback、adapter callback、MQ producer、admin consumer 入庫 | 可寫 callback / MQ supporting；不可寫 exactly-once / outbox owner |
| `projects/iwin/integration-map.md` / `projects/ugsoft/integration-map.md` | 跨 project source of truth 與排查順序 | 架構視角，不新增履歷 claim |

### 3. 0 到 1 核心切法

```mermaid
flowchart LR
  Client["Client / Merchant / Provider"]
  Gateway["Provider Gateway API"]
  Adapter["Provider Adapter"]
  Order["Local Order / Transaction"]
  Inbox["Callback Inbox / Raw Event"]
  MQ["MQ / Async Worker"]
  Effect["Wallet / Merchant Callback / Downstream Effect"]
  Query["Provider Query / Reconciliation"]
  Repair["Admin Repair / Manual Review"]
  Obs["Logs / Metrics / Alerts"]

  Client --> Gateway
  Gateway --> Order
  Gateway --> Adapter
  Adapter --> Client
  Client --> Inbox
  Inbox --> MQ
  MQ --> Effect
  Query --> Adapter
  Query --> Order
  Repair --> Order
  Gateway --> Obs
  Inbox --> Obs
  MQ --> Obs
  Effect --> Obs
```

核心不是「打一支 provider API」，而是建立一個可以承受外部不可靠事件的邊界：

1. API gateway 層：驗簽、白名單、參數 normalize、trace id。
2. Provider adapter 層：每個 provider 的 request / callback / query contract 隔離。
3. Local state 層：本地 order / transaction 是內部 source of truth。
4. Raw event / inbox 層：保存 provider request / callback / query evidence。
5. Async worker 層：將 callback 或查單結果轉成下游副作用。
6. Reconciliation 層：處理 timeout unknown、callback missing、provider / local state mismatch。
7. Admin repair 層：人工修復必須有權限、audit、狀態 transition guard。
8. Observability 層：能追單、追 callback、追 MQ、追 downstream effect。

### 4. 最小資料模型

#### `provider_order`

| 欄位 | 用途 |
| --- | --- |
| `id` | internal id |
| `merchant_order_id` | 本地單號，送給 provider 的主追蹤 key |
| `provider` | provider code |
| `provider_order_id` | provider 回傳單號，可能 request 後才有 |
| `account_id` | 玩家 / 商戶帳號 |
| `amount` / `currency` | 金額與幣別，避免單位混淆 |
| `direction` | deposit / withdraw / transfer-in / transfer-out / bet-settle |
| `status` | local state machine |
| `request_payload_hash` | request evidence，避免存敏感原文時仍可比對 |
| `last_provider_status` | provider latest status |
| `created_at` / `updated_at` | 查 aging / timeout |

#### `provider_event`

| 欄位 | 用途 |
| --- | --- |
| `event_id` | internal event id |
| `provider` | provider code |
| `merchant_order_id` | 對回 local order |
| `provider_transaction_id` | provider side transaction key |
| `event_type` | callback / query / retry / manual |
| `event_status` | provider event status |
| `raw_payload_ref` | raw evidence reference，不在 KB 放 secret |
| `sign_valid` | 驗簽結果 |
| `processed_status` | pending / processed / ignored / failed |
| `idempotency_key` | provider + event type + transaction id |
| `received_at` / `processed_at` | callback delay / replay |

#### `provider_effect`

| 欄位 | 用途 |
| --- | --- |
| `effect_id` | 下游副作用 id |
| `merchant_order_id` | 對回 order |
| `effect_type` | credit / refund / debit / mq_publish / merchant_callback |
| `target_system` | wallet / admin / report / merchant callback |
| `status` | pending / success / failed / unknown |
| `retry_count` | 重送次數 |
| `error_code` / `error_message` | 補償依據 |

### 5. State Machine

#### Payment / Transfer 類

```text
NEW
-> LOCAL_CREATED
-> PROVIDER_REQUESTED
-> PROVIDER_ACCEPTED
-> CALLBACK_RECEIVED
-> EFFECT_APPLIED
-> SUCCESS
```

異常分支：

```text
PROVIDER_REQUESTED -> REQUEST_TIMEOUT_UNKNOWN
PROVIDER_ACCEPTED -> CALLBACK_MISSING
CALLBACK_RECEIVED -> EFFECT_FAILED
EFFECT_FAILED -> MANUAL_REPAIR_PENDING
MANUAL_REPAIR_PENDING -> SUCCESS / FAILED / CANCELLED
```

#### Bet-settle callback 類

```text
CALLBACK_RECEIVED
-> VALIDATED
-> MERCHANT_CALLBACK_SUCCESS
-> MQ_PUBLISHED
-> CONSUMED
-> BET_RECORD_SAVED
-> REPORT_READY
```

異常分支：

```text
VALIDATED -> MERCHANT_CALLBACK_FAILED
MERCHANT_CALLBACK_SUCCESS -> MQ_PUBLISH_FAILED
MQ_PUBLISHED -> CONSUME_FAILED
BET_RECORD_SAVED -> REPORT_LAGGING
```

### 6. Idempotency 設計

#### Key 選擇

| 情境 | 建議 key |
| --- | --- |
| payment request | `provider + merchant_order_id` |
| payment callback | `provider + merchant_order_id + provider_transaction_id + event_status` |
| withdraw refund | `provider + original_order_id + refund_type` |
| transfer wallet | `agent + account + transfer_reference_id`，DB lookup 比 Redis guard 重要 |
| bet-settle | `provider + provider_bet_id + currency + event_type` |
| gameserver transfer | `provider + event_type + transaction_id / bet_id` |

#### Owner 判斷

- Redis lock 只能防連點，不是長期冪等。
- DB unique key 或 processed transaction record 要在下游副作用前建立。
- 如果先改 wallet 再補 processed record，中間 crash 仍可能 double apply。
- callback 重送應回既有結果，不應重做副作用。
- 已終態訂單不能被舊 callback 覆蓋。

### 7. Failure Window

| Failure window | 後果 | Owner 解法 |
| --- | --- | --- |
| local order insert success, provider request fail | 本地有單，provider 無單 | 標 request failed，可重試 / 換 provider / 人工取消 |
| provider request timeout | provider 可能已 accepted | 進 unknown，不直接成功或失敗；靠 query / reconciliation |
| provider accepted, callback missing | 訂單卡住 | aging monitor + provider query job + manual queue |
| callback ack success, MQ publish fail | provider 不再重送，但內部沒處理 | callback inbox / outbox / publisher confirm / alert |
| MQ consumed, downstream effect fail | order 與 wallet / report 不一致 | effect table + retry / DLQ / repair |
| provider callback duplicate | 重複上分 / 重複退款 / 重複 bet record | idempotency key + terminal-state guard |
| provider success, local transaction persist fail | 查單 / lookup 缺資料 | transaction outbox / provider query fallback / manual repair |
| report projection lag | 後台查不到，誤判交易失敗 | report 不當 source of truth；提供 lag alert 與 trace map |

### 8. Reconciliation / 對帳策略

最小可行對帳分三層：

1. Order aging：找 `PROVIDER_ACCEPTED`、`PROCESSING`、`REQUEST_TIMEOUT_UNKNOWN` 超時單。
2. Provider query：用 `merchant_order_id` / provider transaction id 查 provider 終態。
3. Local effect compare：比對 local order、wallet / transaction log、provider event、report projection。

對帳輸出不應直接亂改狀態，而是產生 repair candidates：

```text
order_id
provider_status
local_status
wallet_effect_status
recommended_action
risk_level
evidence_links
```

人工修復必須：

- 有二次確認。
- 有權限邊界。
- 有 before / after audit。
- 只能走合法 state transition。
- 修復後可再 reconciliation 驗證。

### 9. Observability

最少要能查：

- `merchant_order_id`
- `provider_order_id`
- `provider_transaction_id`
- `transfer_reference_id`
- `callback event id`
- `MQ message id`
- `effect id`

核心指標：

| 指標 | 用途 |
| --- | --- |
| provider request success / timeout rate | provider 穩定性 |
| callback delay p50 / p95 / p99 | provider 延遲與卡單 |
| unknown aging count | 需要查單 / 對帳 |
| duplicate callback count | provider 重送與冪等壓力 |
| MQ publish fail / consume fail | async pipeline 健康 |
| manual repair count | 系統補償能力與操作風險 |
| provider / local mismatch count | 對帳差異 |

告警優先級：

1. 金額副作用失敗：wallet credit / refund / transfer effect failed。
2. Unknown aging：provider accepted 但長時間無終態。
3. MQ publish / consume fail：callback 進來但內部沒落地。
4. Duplicate spike：可能 provider 重送或上游 retry 異常。
5. Report lag：不要誤判成交易失敗，但要讓營運知道延遲。

### 10. Rollout Plan

#### Phase 1：Adapter isolation

- 每個 provider 一個 adapter。
- 統一 request / callback / query interface。
- 所有 provider payload normalize 成 common DTO。
- 保留 raw event reference 與 trace id。

#### Phase 2：State and idempotency

- 明確 order state machine。
- 建 provider event inbox。
- 在下游副作用前建立 idempotency record。
- 終態 guard：成功 / 失敗 / 取消不可被舊事件覆蓋。

#### Phase 3：Async reliability

- callback 不直接做重副作用。
- callback inbox -> worker / MQ。
- producer confirm / retry / DLQ。
- effect table 保存下游副作用狀態。

#### Phase 4：Reconciliation and repair

- aging query job。
- provider query job。
- mismatch report。
- manual repair SOP。
- repair 後自動 recheck。

#### Phase 5：Observability and operations

- dashboard：provider request、callback delay、unknown aging、MQ fail、manual repair。
- runbook：卡單、重複 callback、provider timeout、MQ lag、report lag。
- provider onboarding checklist：sign、IP、amount unit、callback ack、query API、idempotency key。

### 11. 面試 3 分鐘講法

```text
如果我要設計一個 provider integration 系統，我不會只把它看成打一支第三方 API。

我會先切成三層：provider adapter、本地狀態、非同步補償。Adapter 負責隔離不同 provider 的 request、callback、query、簽章、金額單位與欄位差異；本地狀態用 order 或 transaction 當內部 source of truth；callback 或查單結果再透過 inbox / MQ / worker 去做 wallet、merchant callback 或 report 這些下游副作用。

這裡最重要的是 timeout unknown 和重送。Provider request timeout 不能直接當失敗，因為 provider 可能已經 accepted；要進 unknown 狀態，靠 query 或 reconciliation 收斂。Provider callback 也通常是 at-least-once，所以 callback handler 必須驗簽、保存 raw event、用 provider transaction id 或 merchant order id 做 idempotency，已終態訂單不能被舊 callback 覆蓋。

下游副作用我會獨立追狀態，例如 wallet credit、refund、bet record MQ、report projection。因為 callback ack、MQ publish、consumer 寫 DB、wallet mutation 都不是同一個 transaction。短期至少要有 terminal-state guard、effect retry、aging monitor 和人工 repair；長期可以補 callback inbox / outbox、DLQ replay、provider query job 和 reconciliation dashboard。

我實際經驗比較貼近 provider / 商戶對接與既有 production flow 維護，例如 payment provider request / callback / query / withdraw、遊戲 provider transfer / bet-settle callback、MQ 入庫與報表 projection。我不會把它說成我主導完整金流或完整 wallet / ledger；但我能從這些 flow 拆出 source of truth、idempotency、failure window、補償與可觀測性設計。
```

### 12. 常見追問

| 追問 | 回答要點 |
| --- | --- |
| Provider request timeout 怎麼辦？ | 不直接成功 / 失敗，標 unknown；用 provider query / reconciliation 收斂。 |
| Callback 已 ack，但 MQ publish 失敗？ | 需要 callback inbox / outbox、publisher confirm、alert；至少 raw event 要可 replay。 |
| Redis lock 夠不夠防重？ | 不夠。Redis lock 防連點；真正冪等要靠 DB unique / processed event / transaction record。 |
| 已成功訂單收到失敗 callback？ | 終態 guard；舊事件只能記 audit，不覆蓋終態。 |
| 查單結果和 callback 衝突？ | 以 provider 終態與本地 state machine 合法 transition 收斂；保留兩邊 evidence，必要時人工 repair。 |
| Report 查不到是不是交易沒成功？ | 不一定。Report 是 projection；先查 local order / wallet / provider event，再看 projection lag。 |
| 要不要 exactly-once？ | 面試不硬說 exactly-once；實務上用 at-least-once + idempotency + reconciliation。 |
| 最先補什麼？ | 先補 trace key、idempotency guard、unknown aging monitor；再補 outbox / reconciliation dashboard。 |

### 13. 不可誇大清單

- 不說「我設計完整 payment platform」。
- 不說「我建立完整 reconciliation」。
- 不說「wallet / ledger 是我 owner」。
- 不說「MQ 已 exactly-once」。
- 不說「所有 provider 都已防重」。
- 不說「system map 證明我主導整個平台」。
- 不說「分析過的 flow 都是我開發」。

## Relationship Check

本檔新增的是 system design template，不是新履歷 claim。

- `05-resume-master-zh.md`：不更新。
- `08-application-autobiography-zh.md`：不更新。
- `04-interview-casebook.md`：不更新；本模板可作 case 延伸材料。
- `17-salary-negotiation.md`：不更新；不新增談薪 claim。
- `06-todo.md`：需標示 Provider Integration template v1 已完成。
- `11-senior-interview-readiness.md`：需標示第一份 system design template 已完成。
