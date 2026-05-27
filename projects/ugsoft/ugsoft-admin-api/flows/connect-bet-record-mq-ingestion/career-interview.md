# connect-bet-record-mq-ingestion Career Interview

## 定位

- Flow：`connect-bet-record-mq-ingestion`
- Project：`ugsoft-admin-api`
- 目前 Step：Step 3
- 證據層級：`真實開發過 + code-backed`、`code-backed / 主管或團隊 context`、`分析素材 / 待確認` 混合。
- 用途：先作單條 flow 的面試 / 履歷候選素材；尚未進 Step 4 / Step 5，因此不直接更新 `05 / 08`。

## 30 秒摘要草稿

這條 flow 是 UGSoft 的 provider bet record MQ 入庫。connector 把 provider callback 或 job sync 的注單資料送到 RabbitMQ，admin-api consumer 收到後解析 payload，依 event time 算 `ptDay`，用 `agentId + providerBetId` 做重複檢查，沒有資料才寫入 `pt_bet_record`。寫入成功後再非同步發 quota update message。這裡可以講 MQ 解耦、late data `ptDay`、duplicate boundary、quota eventual consistency 與 outbox / DLQ 補強方向。

## 目前可講的 Senior 能力點

1. 能把 provider callback / job sync 和 downstream ingestion 分開看。
2. 知道 `ptDay` 必須用 event time，不適合用 consumer time。
3. 能指出 duplicate query、DB unique constraint、上游 `providerBetId|currency` 去重三者要對齊。
4. 能區分 `pt_bet_record` 是主事實，quota remaining 是衍生 view。
5. 能主動說明 bet record save 成功但 quota publish 失敗的 failure window。
6. 能保守指出目前 evidence 不足以宣稱 exactly-once / outbox /完整 replay。

## 可安全使用的履歷素材候選

> 參與 UGSoft 後台 API / control plane 中 RabbitMQ bet record 非同步入庫與資料處理維護，處理 provider bet record payload、重複檢查、currency default、`ptDay` 分區與 quota update supporting flow。

使用前提：

- 這句只能作 `ugsoft-admin-api` project-level consolidation 的補充素材。
- 正式寫入 `05 / 08` 前，要等 Step 5 claim gate 或 project contribution consolidation refresh。
- 不寫「主導完整 RabbitMQ 架構」、「完整 quota owner」、「完整 provider gateway owner」。

## 可面試講但不一定放履歷

- listener catch exception 對 redelivery / ack 語意的影響。
- duplicate query 和 entity unique constraint 不完全一致的風險。
- quota update publish failure 不回滾 bet record 的 trade-off。
- 若要改善，可以用 outbox、publisher confirm、DLQ replay、quota rebuild job 或 deterministic idempotency key。

## 不可誇大

- 不說完整 wallet / ledger / quota system owner。
- 不說完整 reconciliation / exactly-once / outbox 已落地。
- 不把 `arnold` commits 當 Nick direct evidence。
- 不說 production incident owner 或改善百分比。
- 不說完整 UGSoft 平台 owner。

## 待 Step 4 打磨

- 90 秒面試版本。
- 3 分鐘面試版本。
- STAR 版本。
- 常見追問與保守回答。
- 面試官追問「如果 quota publish 失敗怎麼辦」的 owner answer。
