# payment-provider-callback claim-boundary

## Step 5 判定

結論：不更新正式履歷 / 自傳。

原因：

- 本 flow 已完成 Step 3 / Step 4，且具備 code-backed flow、failure window、面試稿與 claim boundary。
- 但目前沒有 Nick 本人 MR / PR、commit author、ticket 指派與完成紀錄、production issue / incident 紀錄或 Nick 本人確認。
- 因此證據層級維持 `專案存在 / code-backed` 與 `分析素材 / learning-only`，不能升級為 `真實開發過`。

本輪可安全收斂：

- 作為 Senior Backend 面試素材，可講金流 callback 的 trust boundary、訂單終態 guard、XXL-MQ retry、提現失敗退款重複補償風險、`billNo` cross-system trace 與 repair boundary。
- 作為學習素材，可整理 owner decision：callback inbox / outbox、下游 wallet idempotency、callback replay、對帳 job、人工補償 dashboard。

本輪不做：

- 不更新 `senior-owner-playbook/05-resume-master-zh.md`。
- 不更新 `senior-owner-playbook/08-application-autobiography-zh.md`。
- 不把本 flow 寫成 Nick 主導 / 設計 / 修復成果。

下一步：

- payment project 已完成 Top 5 flow 與 contribution consolidation；下一步回到 `game_api partner-deposit-withdraw-bill Step 5`。
- 目前同 project 第二優先是 `withdrawal-auto-review-refund`。

## 真實開發過

目前沒有可標記為 `真實開發過` 的項目。

需要以下任一 evidence 才能升級：

- Nick 本人 MR / PR。
- Nick 本人 commit author。
- ticket 指派與完成紀錄。
- production issue / on-call / incident 紀錄。
- Nick 本人口頭或文字確認。

## 專案存在 / code-backed

可以說：

- `payment` 專案存在 provider callback flow。
- callback controller 會處理 provider callback、白名單、簽章、狀態 mapping 與 ack。
- 主線透過 XXL-MQ 進入 `NotifyComsumer` 與 `updateUserInfo`。
- 充值成功會呼叫上分、更新訂單與玩家欄位。
- 提現成功會更新訂單與玩家欄位。
- 提現失敗會進退款分支，並有防 MQ retry 重複退款的處理。
- payment 會把訂單 `billNo` 以 `billNos` 傳給 game lobby / center。
- game lobby / center 會把 `billNo` 帶入玩家餘額異動與 currency log。
- app_bi local HEAD 有以 `bill_no` / 月表更新 `payment_order` 狀態的人工修復入口；但該 repo 本機落後遠端 4 commits，只能當 repair boundary 參考。
- 代表 provider 包含 NanaPay、Pay4z。
- path history 顯示 pay4z / nanapay / withdraw_notify_consumer 曾有 callback、sign、ack、重複退款相關修正。

## 分析素材 / learning-only

可以作為學習與面試分析，但不能當作 Nick 已完成的履歷成果：

- outbox / inbox 改善建議。
- provider adapter / callback core 重構建議。
- reconciliation dashboard / 對帳 job 建議。
- game lobby idempotency 檢查建議；目前只確認 `billNo` 傳遞與 log，不確認去重。
- callback ack 時機調整建議。

## 待確認

- Nick 是否參與 NanaPay 或 Pay4z。
- commit message 中有 Nick branch / ticket 的項目是否對應 Nick 本人工作。
- DB schema 是否有 `bill_no` unique key。
- game lobby / center 是否用 `billNos` 做查重、unique key 或 no-op idempotency。
- app_bi / admin 最新遠端的人工補單流程如何與 payment 訂單狀態互動。
- 是否有 callback raw event log / inbox table。
- 是否有定時對帳與 dead letter queue。

## 正式履歷目前不能寫

不能寫：

- 「主導 iwin 金流 callback 架構」
- 「設計 payment provider callback」
- 「解決 pay4z 重複退款」
- 「打造 exactly-once 金流交易」
- 「確認 game lobby wallet API 已用 billNo 去重」
- 「確認 payment_order.bill_no 有 DB unique key」
- 「負責完整 payment owner」
- 「改善成功率 / 降低事故率 X%」

## 可作為之後履歷候選的保守方向

等 Nick 補 evidence 後，可考慮改寫成：

- 「參與 / 維護三方金流 provider callback flow，處理 callback 驗證、訂單狀態轉移、MQ retry 與提現失敗退款冪等風險。」
- 「針對金流 callback 重送與 MQ retry 場景，梳理訂單終態 guard、退款補償與人工對帳邊界。」

是否能用「參與」或「維護」，取決於後續 evidence。

## 履歷 claim 分層（2026-05-18 KB 對齊）

- 可放履歷：目前不單獨升級成本 flow 的真實開發成果；但不得否定 Nick 在 `payment` 的整體實際開發經驗。project-level payment contribution consolidation 已完成，payment 履歷只保守寫 provider 對接 / 維護與 order consistency。
- 可面試講：code-backed / 分析過。可用本 flow 說明 money correctness、狀態轉移、冪等、retry、補償、人工修復或 runtime config consistency。
- 不可誇大：不得把本 flow 寫成 Nick 主導完整 payment / wallet owner、設計整套金流架構、解決全部對帳或 production incident，除非後續補到本人 MR / ticket / production issue / 本人確認與重要 diff。
