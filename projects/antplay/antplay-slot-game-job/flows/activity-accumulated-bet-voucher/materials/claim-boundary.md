# Claim Boundary - activity-accumulated-bet-voucher

日期: 2026-05-25

## Claim 分層

| 層級 | 判斷 |
| --- | --- |
| 真實開發過 | 不足。Nick 有 merge evidence，但 current implementation 主要由 Gill 開發，後續 Arnold / Eliot 調整。 |
| code-backed | 可成立。current code path 可完整追到 consumer、Redis key、voucher utils 與 config toggle。 |
| 可面試講 | 可成立，但要說成 code-backed reward correctness 分析素材。 |
| 可直接履歷 | 不建議單獨新增正式 bullet；可作 `antplay-slot-game-job` project-level supporting evidence。 |
| 不可誇大 | 不能說 Nick 主導活動累計投注、完整 reward platform、完整 idempotency / reconciliation。 |

## 可保守說

- 我有看過 / 分析過 AntPlay job 的活動累計投注送 voucher flow。
- 這條 flow 用 Kafka `settled_bets` event、Redis accumulated bet 與 BetVoucher DB 發放 reward。
- 我能說明 Daily / Period reward 的 Redis / DB 防重邊界，以及 Kafka 重送可能造成的 duplicate reward 風險。
- Project-level 可保守帶到「activity accumulated bet / voucher reward flow code-backed analysis」。

## 不可說

- 不說 Nick 主導或主要開發這條 flow。
- 不說完整活動平台 owner。
- 不說 `BetVoucherService` 已有完整 idempotency。
- 不說 Kafka + Redis + DB 已經 exactly-once。
- 不把 Gill / Arnold / Eliot 的 commits 說成 Nick direct contribution。

## 待 Step 5 補強

- 追 `BetVoucherService#addVoucher` 實作與 DB unique key / idempotency evidence。
- 追 Nick merge MR 是否只是 merge 還是有 review / fix / ticket evidence。
- 追 current production 是否仍啟用此 consumer，及 activity config 是否真有 production case。
- 判斷是否能回填 project-level consolidation；目前只作 supporting evidence，不升級正式履歷。
