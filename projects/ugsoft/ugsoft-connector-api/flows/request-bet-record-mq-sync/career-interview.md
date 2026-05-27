# request-bet-record-mq-sync Career Interview

## 閱讀定位

- Flow：`request-bet-record-mq-sync`
- Step：Step 3 附屬 career / interview 草稿
- 狀態：可作 Step 4 前的草稿；不是正式 Step 4 面試稿，也不是 Step 5 claim gate。
- Evidence：Nick / `10gt12nc` 有 BetRecord MQ、job、跨日 `pt_day`、currency 修正 direct evidence；Redis watermark / 最新 provider behavior 多為 code-backed / 團隊 context。

## 90 秒草稿

UGSoft connector 除了 provider callback 之外，還有一條 job-driven bet record sync flow，用來補晚到或 callback 未完整落地的 provider 注單資料。它由 Quartz job 觸發，AntPlay / DerPlay 各有 sync job，進到 `ConnectBetRecordSyncService` 後會掃 enabled agents，排除單一錢包 agent，DerPlay 還會依 currency 逐一拉資料。

自動模式會用 Redis watermark 控制時間窗，大概只掃最近一小段且避開最新幾分鐘；手動模式則可以指定 start / end 補跑。provider 回來資料後，service 會依事件日期分組，查 `pt_bet_record` 已存在的 provider bet id，再用 `providerBetId|currency` 避免同批或 DB 已有資料重複送 MQ。未存在的資料會轉成 `ConnectBetRecordMqDto`，送到和 callback path 共用的 BetRecord MQ，下游由 admin-api consumer 入庫。

我會把這條 flow 定位成 late data sync / compensation-like path，而不是完整 reconciliation。它的 owner 風險在 watermark 是否能安全前進：如果 MQ publish failure 被 producer catch 掉，sync service 仍可能推進 watermark；DerPlay per-currency 也有 partial success 風險。面對這類設計，我會優先補 durable publish result、per-currency watermark、pagination 與 retry / DLQ replay 的觀測。

## 3 分鐘草稿

這條 flow 是 UGSoft connector 的 bet record 補資料路徑，解決的是 provider callback path 之外，平台如何定期拉 provider bet record，補進 RabbitMQ，再由 admin-api consumer 寫入正式 bet record。

入口是兩個 Quartz job：`AntplayBetRecordSyncJob` 和 `DerplayBetRecordSyncJob`。job 可以帶 `startTime / endTime`，有帶就是手動補跑；沒有帶就是自動模式。自動模式由 `ConnectBetRecordSyncService` 讀 Redis watermark 決定時間窗。沒有 watermark 時從最近約 10 分鐘往前掃到避開最新約 3 分鐘；有 watermark 時會帶一段 safety buffer，避免邊界漏資料。

service 會先掃 enabled agents。單一錢包 agent 會被跳過，因為這條 sync path 不處理 single-wallet 模式。DerPlay 還會檢查 `derplayOpen`，並依 agent currencies 逐一呼叫 provider。AntPlay / DerPlay 都會經過各自 adapter 呼叫 provider bet record API，最後 normalize 成 common response。

provider response 成功後，service 會把 items 依事件日期分組，因為同一個時間窗可能跨日。每一天會呼叫 `ConnectBetRecordSyncRepository` 查 `pt_bet_record`，條件包含 `pt_day`、`agent_id`、`provider`、`provider_bet_id`，並用 `providerBetId|currency` 作 key。這樣可以避免同批重複，也避免已入庫資料再次送 MQ。未存在的 item 會依 provider 欄位轉成 `ConnectBetRecordMqDto`，交給 `ConnectBetRecordMqService#send`。

Senior 角度我會特別注意三個 failure window。第一是 provider API fail 時不能推 watermark，這點 code 有做。第二是 MQ publish failure：producer catch exception 後不往外拋，sync service 可能仍以為成功並推進 watermark，最壞會漏資料。第三是 DerPlay per-currency：如果任一 currency 成功就推共同 watermark，可能讓失敗 currency 的資料被跳過。要強化的話，我會讓 MQ publish success 成為 watermark 推進條件，或採 outbox；DerPlay watermark 拆到 currency 粒度；另外補 pagination 與 DLQ replay / metrics。

履歷上我會保守說「參與 provider connector 的 bet record MQ / job sync / late data 補資料與跨日查重維護」，不會說自己主導完整 reconciliation 或 exactly-once pipeline。

## 可放履歷的保守素材

- 參與 UGSoft provider connector / gateway 維護，包含 request / bet record MQ、Quartz job sync、跨日查重與 currency 修正。
- 支援 provider callback 外的 job-driven bet record 補資料路徑，透過 Redis watermark、`pt_day` 查重與 RabbitMQ 串接下游入庫。
- 針對 provider bet record sync 的 failure window 做 code-backed 分析，包含 watermark 推進、MQ publish failure、per-currency partial success 與 pagination 風險。

## 面試可講

- 為什麼 callback path 之外還需要 sync job。
- 自動 watermark 與手動補跑的差異。
- 為什麼要依事件日期分組查 `pt_day`。
- `providerBetId|currency` 的 duplicate boundary。
- MQ publish failure 被 catch 後，watermark 仍可能前進的風險。
- DerPlay 多 currency sync 的 partial success 風險。

## 不可誇大

- 不說完整 reconciliation。
- 不說 exactly-once。
- 不說完整 outbox / DLQ 平台已建立。
- 不說完整 bet record owner。
- 不把 `arnold` commits 當 Nick direct evidence。

## Step 4 待補

- 把 90 秒 / 3 分鐘稿打磨成正式面試回答。
- 補 8-12 題追問與回答要點。
- 把「我做過」與「我分析過」的邊界再收細。
- 檢查是否能和 `provider-callback-bet-settle-to-mq` 互補，而不是重複。
