# Claim Boundary - connect-bet-record-mq-ingestion

## Step 5 結論

本 flow 已完成 Step 5 claim gate。結論是：`connect-bet-record-mq-ingestion` 可以作為 `ugsoft-admin-api` project-level RabbitMQ / bet record async processing 的強 supporting evidence，支撐「真實開發過 + code-backed」的後台非同步資料處理經驗；但不能單獨升級成完整 RabbitMQ architecture、quota system、wallet / ledger 或 provider gateway owner。

## 可放履歷

- 參與 UGSoft 後台 API / control plane 中 RabbitMQ bet record 非同步入庫與資料處理維護。
- 可具體展開為：BetRecord MQ 初版、consumer 調整、mapper 查重、`ptDay` / provider bet record 入庫邊界、currency default。
- 寫法要併入 `ugsoft-admin-api` project-level consolidation，不單獨寫成「主導某完整系統」。

## 可面試講

- connector callback / job sync publish BetRecord MQ 後，admin-api consumer 如何 parse payload、validate、算 `ptDay`、查重、寫 `pt_bet_record`。
- 為什麼 `ptDay` 要用 event time，不能只用 consume time。
- duplicate query、DB unique constraint、上游 providerBetId / currency contract 要對齊。
- bet record save 成功但 quota update publish 失敗時，`pt_bet_record` 是主事實，quota remaining 是 eventual-consistent 衍生 view。
- 可提出 outbox、publisher confirm、DLQ replay、quota rebuild / reconciliation job 作為 owner 補強方向。

## 只能保守說 / Supporting

- `quota update`、quota monitoring、Redis idempotency、negative quota alert 是 latest current behavior / team context；可說「我理解這段 supporting flow 與風險」，不可說「我主導完整 quota monitoring」。
- 最新 `provider_bet_id` duplicate query、id 生成、amount normalization 是 `arnold` 後續修正 context；可用來描述 current behavior，不當 Nick direct evidence。

## 不可說

- 主導完整 RabbitMQ architecture。
- 主導完整 wallet / ledger / quota system。
- 完整 exactly-once / outbox / DLQ / replay 平台已落地。
- 完整 provider gateway / connector owner。
- 主導完整 UGSoft 平台。
- `arnold` 的 quota monitoring、id 生成、amount normalization 是 Nick direct evidence。

## Evidence 分層

| 說法 | Evidence 層級 | 判斷 |
| --- | --- | --- |
| BetRecord MQ 初版 / queue / listener / consumer | 真實開發過 + code-backed | `98ad763` by `10gt12nc` |
| consumer 調整 / job 多單 BetRecordMq | 真實開發過 + code-backed | `f641b04` by `10gt12nc` |
| mapper query / BetRecord + RequestLog MQ 收斂 | 真實開發過 + code-backed | `821bc2e` by `10gt12nc` |
| currency default CNY | 真實開發過 + code-backed | `c99a325` by `10gt12nc` |
| latest provider_bet_id duplicate / generated id | code-backed / supervisor context | `6ab331c` by `arnold` |
| latest provider amount normalization | code-backed / supervisor context | `b4fe846` by `arnold` |
| quota update / monitoring / Redis idempotency | code-backed / supervisor context | `f29f8e4` by `arnold` |

## 回填判斷

- 可回填：`ugsoft-admin-api contribution-claim-consolidation.md` 的 RabbitMQ / async evidence，標註本 flow Step 5 已完成。
- 不直接回填：`05-resume-master-zh.md`、`08-application-autobiography-zh.md`、`04-interview-casebook.md`、`17-salary-negotiation.md`。原因是本輪是單條 flow Step 5，不是 project-level consolidation refresh 或 rolling resume package。
- 後續若 Nick 要正式更新履歷輸出版，應先做 `ugsoft-admin-api contribution claim consolidation refresh` 或 rolling resume package。
