# claim-boundary

## Step 5 判定

結論：不更新正式履歷 / 自傳。

本 flow 目前只可作為 `專案存在 / code-backed` 與 `分析素材 / learning-only`。

不更新：

- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

原因：

- 沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認。
- 沒有確認 Nick 是 payment owner。
- Step 4 failure / consistency 深挖已完成，但只證明 code-backed flow 與風險分析，不證明 Nick 本人貢獻。
- 下游 `billNo` 去重、DB unique key、reconciliation 還是待確認。

本輪可安全收斂：

- 作為 Senior Backend 面試素材，可講提款建單、扣分、自動審核 / 自動出款、provider 終態、失敗退款、MQ retry、終態 guard 與人工補償邊界。
- 作為學習素材，可整理 owner decision：pending event / outbox、failed MQ produce alarm、provider query / reconciliation、game lobby `billNo` 去重、`WAIT` / `PROCESSING` aging dashboard。

本輪不做：

- 不更新 `senior-owner-playbook/05-resume-master-zh.md`。
- 不更新 `senior-owner-playbook/08-application-autobiography-zh.md`。
- 不把本 flow 寫成 Nick 主導 / 設計 / 修復成果。

## 可寫入面試素材

可以保守說：

- 「我分析過 iwin payment 的玩家提款、自動審核 / 自動出款與失敗退款 flow。」
- 「這條 flow 涉及 `payment_order` 狀態、game lobby 扣分 / 退款、provider 代付、callback、MQ retry 與人工審核 fallback。」
- 「我會用它討論 money correctness、idempotency、retry、compensation、reconciliation。」

## 需要 Nick 確認後才可升級

若 Nick 確認曾參與，可升級成：

- `真實開發過`
- `真實排查過`
- `真實修復過`
- `真實 owner / maintenance 經驗`

需要 evidence：

- MR / PR link。
- ticket / issue。
- commit author。
- production incident / postmortem。
- Nick 口頭確認具體做過哪一段。

## 禁止 claim

- 禁止寫「主導 iwin 自動出款」。
- 禁止寫「設計金流提款一致性架構」。
- 禁止寫「解決重複退款問題」。
- 禁止寫「負責 payment owner」。
- 禁止寫「建立完整 reconciliation 機制」。
- 禁止寫「保證不會重複上下分」。

## 下一步

- 全域下一步狀態：目前沒有預設 project flow 下一步；請以 senior-owner-playbook/01-senior-owner-flow-inventory.md 與 senior-owner-playbook/06-todo.md 為準。

## 履歷 claim 分層（2026-05-18 KB 對齊）

- 可放履歷：目前不單獨升級成本 flow 的真實開發成果；但不得否定 Nick 在 `payment` 的整體實際開發經驗。project-level payment contribution consolidation 已完成，payment 履歷只保守寫 provider 對接 / 維護與 order consistency。
- 可面試講：code-backed / 分析過。可用本 flow 說明 money correctness、狀態轉移、冪等、retry、補償、人工修復或 runtime config consistency。
- 不可誇大：不得把本 flow 寫成 Nick 主導完整 payment / wallet owner、設計整套金流架構、解決全部對帳或 production incident，除非後續補到本人 MR / ticket / production issue / 本人確認與重要 diff。
