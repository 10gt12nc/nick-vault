# connect-bet-record-mq-ingestion Career Interview

## 定位

- Flow：`connect-bet-record-mq-ingestion`
- Project：`ugsoft-admin-api`
- 目前 Step：Step 5
- 證據層級：`真實開發過 + code-backed`、`code-backed / 主管或團隊 context`、`分析素材 / 待確認` 混合。
- 用途：單條 flow 的正式面試素材與 flow-level claim gate；可回填 `ugsoft-admin-api` project-level consolidation，但不直接更新 `05 / 08`。

## 30 秒摘要

這條 flow 是 UGSoft 的 provider bet record MQ 入庫。connector 把 provider callback 或 job sync 的注單資料送到 RabbitMQ，admin-api consumer 收到後解析 payload，依 event time 算 `ptDay`，用 `agentId + providerBetId` 做重複檢查，沒有資料才寫入 `pt_bet_record`。寫入成功後再非同步發 quota update message。這裡可以講 MQ 解耦、late data `ptDay`、duplicate boundary、quota eventual consistency 與 outbox / DLQ 補強方向。

## 90 秒版本

這條 flow 我會把它定位成 provider bet record 的 consumer 端。上游 `ugsoft-connector-api` 可能從 provider callback 或 job sync 拿到注單資料，最後都送到同一條 BetRecord MQ。`ugsoft-admin-api` 這邊的 `ConnectBetRecordListener` 收 message，交給 `ConnectBetRecordConsumerService` parse payload，檢查 `agentId / provider / providerBetId / currency`，再用 message event time 算 `ptDay`。

入庫前會查 `pt_bet_record` 是否已有同一個 `ptDay + agentId + providerBetId`。不存在才建立 `BetRecord`，設成 `CREATE` step，補 currency default，寫入正式表。寫入後再 fire-and-forget 發 quota update message。這個設計的重點是 BetRecord 是主事實，quota 是衍生 view；所以 quota publish 失敗時不回滾 bet record，但需要補償或重建策略。

我會主動講幾個風險：listener catch exception 後 retry / ack 語意要確認；duplicate query 和 entity unique boundary 不完全一致；quota update publish 失敗會造成 quota view 落後；如果要做得更穩，可以補 publisher confirm、outbox、DLQ replay、依 `pt_bet_record` rebuild quota。

## 3 分鐘版本

這條 flow 可以從上下游開始講。`ugsoft-connector-api` 負責接 provider callback 或定期 sync provider bet record，它不直接把所有下游都同步做完，而是把標準化後的 BetRecord payload 丟到 RabbitMQ。`ugsoft-admin-api` 是 consumer / ingestion side，負責把 MQ message 落到正式 `pt_bet_record`。

consumer 的第一步是 `ConnectBetRecordListener` 收 message，真正業務邏輯在 `ConnectBetRecordConsumerService`。service 會 parse JSON payload，如果 payload 解析失敗、`agentId` 不合法、provider / providerBetId / currency 缺失，就 log 並 skip。正常情況下，它會用 payload 裡的 event time 算 `ptDay`。這點很重要，因為 provider 資料可能晚到或跨日，如果用 consumer 當下時間，查重和分區都可能查錯。

接著它會查 `pt_bet_record` 是否已存在。最新 code 是用 `ptDay + agentId + providerBetId` 查；entity unique boundary 又包含 provider 和 currency，所以我不會說這裡已經完全解決所有 duplicate 問題，而是說目前有 duplicate check 和 DB unique boundary，真正 owner 要確認兩邊 key 是否對齊。不存在時才建立 `BetRecord`，補上 provider、providerBetId、game、account、bet / win 金額、currency、`BetRecordStep.CREATE`、notify flags，然後 save。

save 成功後會發 quota update MQ。這裡我會特別把主事實和衍生 view 分開：`pt_bet_record` 是主事實，quota remaining 是從 bet record 推導出的 view。quota update 用 fire-and-forget 的好處是 bet record 入庫不被 quota 系統拖慢；代價是如果 publish 失敗，bet record 已經存在，但 quota view 沒更新。這時要靠 outbox、publisher confirm、重送 job、DLQ replay，或從 `pt_bet_record` rebuild quota 來補。

這條 flow 面試時最能展現的是 MQ eventual consistency、late data 的 `ptDay` 判斷、duplicate / idempotency boundary、以及我不會把目前 evidence 誇大成 exactly-once。Nick direct evidence 可支撐 BetRecord MQ 初版、consumer 調整、mapper query 和 currency default；quota monitoring、id 生成、amount normalization 是後續主管 / team current behavior，我會當成我理解過的現況，不拿來誇成我主導。

## 目前可講的 Senior 能力點

1. 能把 provider callback / job sync 和 downstream ingestion 分開看。
2. 知道 `ptDay` 必須用 event time，不適合用 consumer time。
3. 能指出 duplicate query、DB unique constraint、上游 `providerBetId|currency` 去重三者要對齊。
4. 能區分 `pt_bet_record` 是主事實，quota remaining 是衍生 view。
5. 能主動說明 bet record save 成功但 quota publish 失敗的 failure window。
6. 能保守指出目前 evidence 不足以宣稱 exactly-once / outbox /完整 replay。

## Step 5 Claim Gate

### 可放履歷

> 參與 UGSoft 後台 API / control plane 中 RabbitMQ bet record 非同步入庫與資料處理維護，處理 provider bet record payload、重複檢查、currency default 與 `ptDay` 分區等資料入庫邊界。

使用限制：

- 這句只能併入 `ugsoft-admin-api` project-level consolidation，不單獨包成一個正式主成果。
- 本輪不直接更新 `05 / 08`，因為那要走 project-level consolidation refresh 或 rolling resume package。
- `quota update` 只能作 supporting flow / 面試追問，不寫成自己主導完整 quota monitoring。

### 可面試講但不一定放履歷

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

## Step 5 收斂

- 已完成 direct evidence / current behavior 邊界切分。
- 可回填 project-level contribution consolidation 作為 BetRecord MQ evidence。
- 不直接把單條 flow 寫進 `05 / 08`；若要更新履歷 master，要另做 `ugsoft-admin-api contribution claim consolidation refresh` 或 rolling resume package。
