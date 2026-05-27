# Interview - request-bet-record-mq-sync

## Step 4 狀態

本檔已整理為 Step 4 正式面試材料。主閱讀入口仍是 `flow.md`；面試口說優先讀 `career-interview.md`，本檔保留追問題庫與回答防線。

## 核心一句話

這條 flow 是 UGSoft connector 的 provider bet record job sync：Quartz 定期拉 AntPlay / DerPlay bet record，用 Redis watermark 控制時間窗，依 `pt_day` 與 `providerBetId|currency` 查重後，把缺的注單送到 BetRecord MQ，補 callback path 以外的 late data。

## 90 秒回答骨架

1. 這不是 callback 主路徑，而是 job-driven 補資料路徑。
2. 入口是 AntPlay / DerPlay Quartz job，可自動跑也可手動指定時間。
3. 自動模式用 Redis watermark + safety buffer 控制時間窗，並避開最新幾分鐘。
4. provider response 回來後依事件日期分 `pt_day` 查 DB。
5. 用 `providerBetId|currency` 做 DB existing 與同批去重。
6. 未存在資料轉成 BetRecord MQ DTO，走和 callback path 共用的 MQ / consumer。
7. 主動講風險：MQ publish failure 可能被 catch 後推水位、DerPlay currency partial success、pagination。
8. 收尾講 owner decision：publish success / outbox 後才推水位、per-currency watermark、metrics / DLQ / replay。

## 3 分鐘回答骨架

1. 先定位：callback 是 provider 主動推，sync 是平台主動拉，兩者最後收斂到同一條 BetRecord MQ。
2. 講 job：`AntplayBetRecordSyncJob` / `DerplayBetRecordSyncJob`，有手動 start / end，也有自動模式。
3. 講 watermark：自動模式讀 Redis，沒有水位掃最近短窗口，有水位則帶 safety buffer，並避開最新資料。
4. 講 agent boundary：enabled agents、skip single wallet、DerPlay `derplayOpen` 與 currencies。
5. 講 provider adapter：AntPlay / DerPlay 各自打 bet record API，再 normalize common response。
6. 講 duplicate：依事件日分組查 `pt_day`，查 `pt_bet_record` existing keys，key 是 `providerBetId|currency`。
7. 講 MQ：未存在 item 建 `ConnectBetRecordMqDto`，送 `ConnectBetRecordMqService#send`。
8. 講三個 senior 風險：MQ publish failure vs watermark、DerPlay per-currency partial success、pagination / pageSize。
9. 講改善：outbox / publish result、currency-level watermark、pagination loop、manual catch-up runbook、metrics。
10. 講邊界：不是完整 reconciliation、不是 exactly-once、不把 team context 說成自己主導。

## 常見追問與回答要點

### 1. 為什麼 callback path 之外還要 sync job？

Provider callback 可能晚到、漏打，或 MQ / consumer 曾經短暫異常。Sync job 是補資料與 eventual consistency path，用 provider bet record API 補回可能缺的資料。它不是完整 reconciliation，因為沒有看到完整對帳表、outbox、DLQ replay dashboard 或人工 reconcile workflow。

### 2. watermark 解決什麼問題？

每分鐘 job 若固定掃大區間，provider API、DB existing query 與 MQ 都會有壓力。Watermark 讓自動模式只掃最近尚未處理的小區間；safety buffer 則降低邊界秒數漏資料。但 watermark 只代表 sync service 進度，不代表資料 durable 入庫。

### 3. 為什麼要避開最新幾分鐘？

Provider bet record 可能還沒 settle 完，或 API 查詢資料有延遲。避開最新幾分鐘是用時間窗口換穩定性，降低拉到未穩定資料的機率。

### 4. 為什麼要依事件日期分 `pt_day`？

`pt_bet_record` 查重依賴 `pt_day`。一個 sync window 可能跨日，如果只用 job window 的單一天查，跨日資料可能被判斷為不存在或查錯 partition。這也是 Nick / `10gt12nc` direct evidence 比較強的地方之一。

### 5. duplicate key 為什麼用 `providerBetId|currency`？

Provider bet id 是業務唯一性核心，但在多幣別或資料 normalize 場景下，currency 也要納入判斷，避免不同 currency 被誤判為同一筆。這個 key 必須和下游 consumer 的 duplicate boundary 對齊。

### 6. 最大 failure window 是什麼？

MQ producer catch publish exception，不往外拋。若 provider response 成功、DB 判斷資料不存在、MQ publish 失敗但被吞掉，sync service 仍可能推進 watermark。下次自動 job 不再掃同一段，就有漏資料風險。

### 7. 如果你要修，第一刀修哪裡？

我會先讓 MQ publish 結果回到 sync service，或改成 outbox：先把要送的 message durable 存下來，outbox / relay 成功後再推水位。這比單純加 try-catch retry 更可靠，因為水位前進是 correctness boundary。

### 8. DerPlay 多 currency 有什麼問題？

DerPlay 逐 currency 拉資料。如果共同 watermark 只要任一 currency 成功就前進，其他 currency 失敗時可能被跳過。比較好的設計是 watermark 拆到 `agent + provider + currency`，或全部 currency 都成功才推共同水位。

### 9. pagination 為什麼重要？

Provider response 有 page / pageSize / total。短時間窗可以降低單次超過 pageSize 的機率，但不是正確性保證。高峰或補跑大區間時，要有 pagination loop、total > pageSize alert，或把時間窗拆小。

### 10. manual catch-up 有什麼風險？

手動補跑可以指定 start / end，但區間太大會造成 provider rate limit、DB IN query 壓力、MQ 堆積、consumer lag，也可能大量掃到已存在資料。需要分批、節流、觀測與可中止策略。

### 11. 這條 flow 怎麼觀測？

不能只看 Quartz job success。要看 provider fetch success / fail、fetched item count、duplicate skipped count、MQ sent / failed、watermark advance / lag、per agent / provider / currency failure、consumer lag / DLQ。

### 12. 這跟 callback MQ flow 怎麼互補？

Callback MQ 是 provider 主動通知的主路徑；sync job 是平台主動補抓的補路徑。兩者最好共用 DTO、queue、consumer 與 duplicate boundary，避免同一筆資料在不同路徑下有不同落庫語意。

## Lead / Architect 追問

### 你會選 outbox 還是 publisher confirm？

如果只要確認 broker 收到，publisher confirm 可以降低 publish uncertainty；但如果要把 DB / 水位 / message 形成更清楚的 recovery boundary，我更偏向 outbox。因為 watermark 前進是 state progress，應該跟「待發 message durable 記錄」綁在一起，而不是只靠一次 publish call 成功。

### watermark 要用 DB 還是 Redis？

Redis 適合輕量進度與高頻讀寫，但要接受它不是永久 audit log。若這條 sync 對 correctness 要求高，水位可以落 DB 或搭配 outbox / run history，至少留下每次 window、成功 / 失敗、sent count、failure reason，方便人工補償。

### 你怎麼避免補資料造成重複？

第一層是 producer 查 `pt_bet_record` existing keys；第二層是同批 `providerBetId|currency` 去重；第三層應該在下游 consumer / DB unique boundary 再保護一次。因為 MQ 是 at-least-once 思維，producer 去重不能取代 consumer idempotency。

### 如果 provider API fail 但只有一部分 currency fail 呢？

要避免用共同水位覆蓋不同 currency 的狀態。可以拆 currency-level watermark，也可以在共同水位設計下要求所有 currency 成功才推進，並對 fail currency 記錄 retry / alert。

### 如果補跑一整天資料怎麼辦？

我會拆時間窗與 provider / agent / currency 批次，限制單批 page size / MQ rate，觀測 consumer lag，並設可中止 run id。補跑不是單純把 start / end 拉大，否則會把 provider、DB、MQ、consumer 同時打爆。

## 誇大陷阱

- 問到 reconciliation：回答「補資料 / late data sync」，不要說完整對帳。
- 問到 exactly-once：回答「目前看是 MQ eventual consistency + duplicate boundary」，不要說 exactly-once。
- 問到 outbox / DLQ：回答「這是我會建議補的方向」，不要說已做。
- 問到自己做了什麼：Nick direct evidence 放在 MQ / job / pt_day / currency 修正；Redis watermark 與部分最新 behavior 是 code-backed / team context。
- 問到 arnold：明確說這是主管 / 團隊 context，不當自己的 direct commits。

## Case-specific 基本功

- Redis watermark：進度控制，不等於 durable completion。
- MQ at-least-once：consumer 仍要 idempotent。
- DB 查重：要和 table partition / `pt_day` 對齊。
- BigDecimal / amount normalize：provider amount 單位與 currency default 必須保守處理。
- Batch IN query：大量 ids 要分批，避免 SQL 過長或效能差。
- Observability：job success、provider success、MQ success、consumer success 要拆開看。

## Step 5 準備

Step 5 要回答：

- 這條 flow 能不能作為單條 flow 履歷素材。
- 哪些是 Nick / `10gt12nc` direct evidence。
- 哪些只是 code-backed / team context。
- 是否回填 `ugsoft-connector-api` consolidation。
- 是否仍不更新 `05 / 08 / 04 / 17`。
