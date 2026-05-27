# Interview - connect-bet-record-mq-ingestion

## 狀態

本檔目前是 Step 3 附錄草稿。正式 30 秒 / 90 秒 / 3 分鐘面試稿要在 Step 4 完成。

## 目前可用 30 秒草稿

這條 flow 是 UGSoft 的 provider bet record MQ consumer。上游 connector 把 provider callback 或 job sync 的注單資料送進 RabbitMQ，admin-api consumer 收到後解析 payload，依 event time 算 `ptDay`，查 `pt_bet_record` 是否已有同一 provider bet id，沒有才寫入正式 bet record。寫入後會 fire-and-forget 發 quota update，所以 bet record 是主事實，quota 是衍生 view。面試可以延伸 duplicate boundary、跨日 late data、MQ retry / DLQ、quota publish failure 與 outbox 補強。

## Step 4 要補

- 90 秒版。
- 3 分鐘版。
- STAR 版。
- failure scenario 問答。
- Lead / Architect 追問。
- 不可誇大的回答模板。
