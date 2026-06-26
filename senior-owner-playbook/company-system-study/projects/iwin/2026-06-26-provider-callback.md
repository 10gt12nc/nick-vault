# 2026-06-26 - iwin / Provider Callback

## 1. Project

- Project: `iwin`
- Local path: `/Users/nick/Git/iwin`
- Evidence level: `專案存在 / code-backed` + `分析素材 / learning-only`；Nick 個人參與仍需本人確認或 MR / ticket / commit evidence。

## 2. Topic

- Topic: Provider callback
- Scope: `projects/iwin/payment/flows/payment-provider-callback/flow.md` 與 `career-interview.md` 已整理出的 payment callback flow。
- Why this topic now: 這是 Nick 三個核心 story 裡 `Provider Integration` 的高價值切片，能支撐 callback trust boundary、idempotency、terminal state、MQ retry 與 troubleshooting 的面試回答。

## 3. What Was Observed

- API / entry: provider 透過 `/pay/notify`、`/withdraw/notify` 類 callback endpoint 通知充值 / 提現結果。
- Service / module: provider controller 解析 callback、驗 IP / sign、查 `payment_order`、做 provider status mapping，再透過 `asynUpdateOrderStatus` 送 XXL-MQ。
- DB / cache / MQ: 主要狀態在 `payment_order`；玩家業務資料會更新 `log_user` / `user_behaviour`；商戶設定與部分 endpoint 由 Redis / config 支撐；後續處理透過 XXL-MQ consumer。
- External integration: consumer 會呼叫 game lobby / center 做充值上分或提現失敗退款，並把 `billNo` / `billNos` 作為跨系統追蹤 key。
- Batch / job: 本週未確認定時對帳 job；既有 flow 只指出查單、人工修復與 reconciliation 邊界仍待補 evidence。
- Config / deployment: 本週未讀 deployment；只確認 callback flow 的 provider config、白名單、簽章與下游 endpoint 依設定運作。

## 4. Why It Might Be Designed This Way

- Current design: callback controller 不直接完成所有副作用，而是驗證後送 MQ，consumer 再更新訂單、玩家資料與外部 game lobby / center。
- Possible reason: provider callback 是外部事件，可能重送、timeout、狀態不一致；用 MQ 可以把 provider ack 與內部處理解耦，讓後續處理可重試。
- Evidence: 既有 flow 已確認 controller guard、consumer guard、XXL-MQ retry、`updateUserInfo` 分支、`billNo` 往下游傳遞與提現失敗退款 retry 防護。
- Unconfirmed assumption: 下游 game lobby / center 是否用 `billNo` 做強制 idempotency、`payment_order.bill_no` 是否有 unique key、callback raw event 是否有 inbox / replay table。

## 5. Production Risk / Pain

- Consistency: provider 狀態、payment order、玩家餘額副作用不是單一 DB transaction，必須靠狀態機、retry、人工修復與對帳收斂。
- Idempotency: 入口與 consumer 有終態 guard，但 end-to-end exactly-once 未確認，尤其下游 wallet / game lobby 是否查重仍待 evidence。
- Timeout / retry: provider 重送 callback 與 MQ retry 都是正常風險；提現失敗退款若處理不當，可能造成重複退款。
- Performance: callback 本身不應被下游慢處理卡死；MQ 解耦能降低入口延遲，但會帶來 backlog / lag / retry 觀測需求。
- Observability: `billNo` 是核心 trace key；需要 log / dashboard 能串起 provider callback、MQ、order、wallet mutation 與人工修復。
- Operational risk: `ProducerUtil.addProduce` 若 produce 失敗只 log，可能出現 provider 已 ack 但內部未處理的 failure window。

## 6. Decision / Trade-off

| Option | Pros | Cons | Migration cost | Worth now? |
| --- | --- | --- | --- | --- |
| Current design: callback verify -> MQ -> consumer side effect | provider 入口輕、可 retry、適合多 provider callback | ack 與 durable enqueue 邊界不夠明確；end-to-end idempotency 要靠多層 guard | 既有成本最低 | 可接受，但要補觀測與 failure window |
| Callback inbox / outbox | callback accepted 後可追蹤、可 replay、可補償 | 多一張表與狀態機，需清楚定義 replay / duplicate 行為 | 中 | 若 callback accepted but not processed 是真實痛點，值得 |
| Full reconciliation dashboard | 可定期比對 provider / payment / wallet，降低人工查 log | 成本高，涉及 provider statement、wallet record、ops workflow | 高 | 第二階段，不是第一個改動 |

## 7. Git History Signal

Not checked this week.

本次只使用 nick-vault 既有 flow / career-interview 文件，不重新掃公司 repo 或 git history。既有 flow 提到 bugfix history 曾支撐重複退款與 id collision 風險，但本週不重新驗證 commit diff。

## 8. If Redesigning Today

- Minimal improvement: 先補 callback / MQ / consumer 的觀測與告警，特別是 `produce failed`、consumer retry、終態 guard hit rate、callback failure reason。
- Larger redesign: 加 callback inbox / durable event，讓「已收 callback」與「內部已處理」可查、可重放、可人工補償。
- What must be verified first: 下游 game lobby / center 是否以 `billNo` 做 idempotency、`payment_order` 是否有 unique constraint、是否已有對帳或人工修復工具。
- What should not be changed yet: 不應一開始就把整套 payment callback 改成完整 Saga / microservice / event sourcing；目前最重要是確認 failure window 與補觀測。

## 9. Interview Takeaway

### 30 秒

> 我會把 provider callback 當成跨系統狀態收斂問題來看。callback 進來後先驗 IP / sign，再把 provider 狀態 mapping 到內部 order state，後續透過 MQ consumer 更新訂單與玩家餘額副作用。真正要注意的是 duplicate callback、MQ retry、terminal state guard，以及提現失敗退款不能因 retry 造成重複退款。

### 90 秒

> 這條 flow 不是單純接 API。provider callback 進來時，payment 先處理 trust boundary，例如 IP 白名單、簽章、provider status mapping，並確認內部 `payment_order` 還在可處理狀態。如果訂單已經成功、退回或異常，就直接 ack provider 並停止，避免重複 callback 造成二次處理。驗證通過後，系統透過 MQ 交給 consumer 做後續狀態更新、充值上分或提現失敗退款。
>
> Senior 角度我會看三個風險：第一，callback ack 和 MQ enqueue 不是同一個 transaction；第二，consumer 要能 idempotent，不能假設 MQ 只送一次；第三，提現失敗退款是最高風險分支，因為退款成功後如果 retry 又跑一次，可能造成重複退款。所以這類 flow 的重點是 source of truth、terminal state guard、`billNo` trace key、reconciliation / repair boundary，而不是單純 controller 寫完就好。

## 10. Evidence Boundary

- Can say: 我分析 / 梳理過 iwin payment provider callback flow，理解 callback 驗證、狀態轉換、MQ retry、終態 guard、提現失敗退款與 `billNo` correlation 的風險。
- Cannot exaggerate: 不說我主導設計 payment callback、不說修復重複退款、不說整套 payment 已達 exactly-once、不說我是完整 payment owner。
- Need Nick confirmation: 是否本人實際維護 / 修改過這條 callback flow；是否有 MR / ticket / commit / production issue 可支撐正式履歷 claim。

## 11. Non-goal

本週明確不做：

- 不掃 `/Users/nick/Git/iwin` 全 repo。
- 不重新讀 payment source code。
- 不更新正式履歷、自傳、Story 或 production flow。
- 不把 code-backed 分析素材升級成 Nick direct owner claim。
- 不設 automation。

## 12. KB Suggestion

- Keep only in company-system-study: Yes。這包目前作為第二條週排程的試跑 packet。
- Consider backfilling to interview materials: 暫不回填。`19` 與 flow `career-interview.md` 已有正式面試素材，除非 Nick 後續覺得本包 30 / 90 秒更好，再明確要求回填。
- No further action: 目前不建立 backlog。下一次若要跑，可選 `antplay / Bet-settle-rollback` 測試 wallet 類 packet。
