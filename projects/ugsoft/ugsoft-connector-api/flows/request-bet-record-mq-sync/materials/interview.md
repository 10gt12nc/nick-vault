# Interview - request-bet-record-mq-sync

## Step 3 口說定位

這份是 Step 3 的初稿。正式 Step 4 應再把內容壓成面試可背、可追問、可防誇大的版本。

## 核心一句話

這條 flow 是 UGSoft connector 的 provider bet record job sync：Quartz 定期拉 AntPlay / DerPlay bet record，用 Redis watermark 控制時間窗，依 `pt_day` 與 `providerBetId|currency` 查重後，把缺的注單送到 BetRecord MQ，補 callback path 以外的 late data。

## 面試回答骨架

1. 先說它不是 callback path，而是 job-driven 補資料 path。
2. 說入口：AntPlay / DerPlay Quartz jobs，可自動跑也可手動指定時間。
3. 說時間窗：自動模式用 Redis watermark，避開最新幾分鐘，避免 provider 資料尚未穩定。
4. 說 provider fetch：adapter 拉 bet record，normalize 成 common response。
5. 說查重：依事件日期算 `pt_day`，查 `pt_bet_record`，key 是 `providerBetId|currency`。
6. 說入 MQ：未存在的資料轉成 `ConnectBetRecordMqDto`，送共用 BetRecord MQ。
7. 說風險：MQ publish failure 被 catch、watermark 可能前進；DerPlay per-currency partial success；pagination 未完整 loop。
8. 說改善：publish success / outbox 後再推水位、per-currency watermark、DLQ replay、metrics。

## 常見追問初稿

### 為什麼 callback path 之外還要 sync job？

Provider callback 可能晚到、漏打、或下游 MQ / consumer 曾短暫異常。Sync job 可以拉 provider bet record API 補資料，但它是補資料 / eventual consistency path，不是完整 reconciliation。

### 為什麼要用 watermark？

每分鐘 job 如果固定掃很大區間，provider API 與 DB 查重壓力都高。Watermark 讓自動模式只掃最近尚未處理的小區間，再加安全 buffer 降低邊界漏資料。

### 為什麼要避開最新幾分鐘？

Provider bet record 可能有延遲或尚未 settle 完，避開最新幾分鐘可以降低拉到不穩定資料的機率。

### 為什麼要依 `pt_day` 分組？

`pt_bet_record` 的查重條件包含 `pt_day`。一個 sync window 可能跨日，如果只查單一天，跨日資料可能被判斷為不存在或查錯 partition。

### duplicate key 為什麼要帶 currency？

同 provider bet id 在多幣別或資料 normalize 場景下可能需要 currency 一起判斷。這也要和下游 consumer 的 duplicate boundary 對齊，否則 producer 與 consumer 判重會不一致。

### 最大 failure window 是什麼？

MQ producer catch publish exception，不往外拋。若 provider response 成功、資料不存在、MQ publish 失敗但被吞掉，sync service 仍可能推進 watermark，導致下一輪不再掃同一時間窗。

### DerPlay 多 currency 有什麼風險？

如果多個 currency 中只有一個成功，code 目前可能以 any success 推進共同 watermark。這會讓失敗 currency 的同時間窗有被跳過的風險。

### 你會怎麼改？

第一，把 MQ publish 成功與否回傳給 sync service，或改成 outbox，確保 durable publish 後才推水位。第二，DerPlay watermark 拆到 agent + provider + currency 粒度。第三，補 pagination loop、DLQ replay 與 metrics，讓補資料不是黑盒。

## Step 4 待補

- 補正式 90 秒版。
- 補正式 3 分鐘版。
- 補追問題庫排序。
- 補「可以說 / 不要說」面試防誇大清單。
