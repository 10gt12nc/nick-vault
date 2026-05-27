# Claim Boundary - connect-bet-record-mq-ingestion

## 目前結論

Step 3 只建立 flow learning package，尚未完成 Step 5 claim gate。本 flow 可以先作 `ugsoft-admin-api` project-level RabbitMQ / bet record async processing 的候選 evidence，但不直接更新 `05 / 08`。

## 可說

- `ugsoft-admin-api` 有 BetRecord MQ consumer，負責 provider bet record 入庫。
- Nick / `10gt12nc` 有 BetRecord MQ 初版、consumer 調整、mapper query、currency default direct evidence。
- 這條 flow 可面試講 provider bet record ingestion、duplicate check、`ptDay`、currency default、quota async failure window。

## 只能保守說

- 參與 / 維護 BetRecord MQ 入庫與相關資料處理。
- 參與後台 API / control plane 的非同步資料處理。
- 對 quota update 只能說理解下游 supporting flow；quota monitoring 最新實作主要是 `arnold` context。

## 不可說

- 主導完整 RabbitMQ architecture。
- 主導完整 wallet / ledger / quota system。
- 完整 exactly-once / outbox / DLQ / replay 平台已落地。
- 完整 provider gateway / connector owner。
- 主導完整 UGSoft 平台。
- `arnold` 的 quota monitoring、id 生成、amount normalization 是 Nick direct evidence。

## 待 Step 5 追查

- 重要 diff / blame 是否能進一步確認 Nick direct contribution 範圍。
- latest duplicate boundary 和 Nick direct evidence 的關係。
- 是否有本人確認或 ticket / MR 能補強 quota update 相關經驗。
- 是否需要回填 project-level contribution consolidation refresh。
