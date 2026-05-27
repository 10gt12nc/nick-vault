# request-bet-record-mq-sync Career Interview

## 閱讀定位

- Flow：`request-bet-record-mq-sync`
- Step：Step 5 / 單條 flow claim gate
- 狀態：Step 4 面試 case 與 Step 5 claim gate 已完成。
- Evidence：Nick / `10gt12nc` 有 BetRecord MQ、job、跨日 `pt_day`、currency 修正 direct evidence；Redis watermark / 最新 provider behavior 多為 code-backed / 團隊 context。
- 使用方式：這份是面試口說稿與追問防線；Step 5 判定可回填 project-level consolidation，但不直接改 `05 / 08 / 04 / 17`。

## 這條 case 的定位

這條 flow 不要拿來講「完整對帳」或「完整金流」。它適合用來講：

- provider callback 之外，如何用 job 補抓 late data。
- async pipeline 如何處理 duplicate / partition / currency。
- watermark 設計的好處與風險。
- Senior / Owner 角度如何看 MQ publish、partial success、pagination 與 observability。

最好的面試用法是把它放在 `provider-callback-bet-settle-to-mq` 後面：callback 是主路徑，job sync 是補資料 / eventual consistency 路徑。兩條合起來可以證明你不只知道「API 怎麼接」，也知道 production data flow 會漏、會重、會晚到，需要補救與邊界。

## 90 秒正式版

UGSoft connector 除了 provider callback path 之外，還有一條 job-driven bet record sync flow，用來補 provider 資料晚到、callback 沒完整落地，或需要人工補跑指定區間的情境。

入口是 AntPlay / DerPlay 的 Quartz job。自動模式會由 `ConnectBetRecordSyncService` 讀 Redis watermark 算時間窗，避開最新幾分鐘，避免 provider 資料尚未穩定；手動模式則可以用 job 參數指定 start / end。service 會掃 enabled agents，排除單一錢包 agent；DerPlay 還會依 currency 逐一拉 provider bet record。

資料回來後，service 會依事件日期分組，查 `pt_bet_record` 是否已有對應資料，再用 `providerBetId|currency` 避免同批重複或 DB 已存在資料再次送 MQ。未存在的 item 會轉成 `ConnectBetRecordMqDto`，送到和 callback path 共用的 BetRecord MQ，下游由 admin-api consumer 入庫。

我會把這條 flow 定位成 late data sync / compensation-like path，不會說它是完整 reconciliation。最重要的 owner 風險是 watermark 是否能安全前進：如果 MQ publish failure 被 producer catch 掉，sync service 仍可能推進 watermark；DerPlay 多 currency 也有 partial success 風險。要強化的話，我會讓 publish success 成為推水位條件，或用 outbox / DLQ replay，再補 per-currency watermark、pagination 與 metrics。

## 3 分鐘正式版

這條 flow 是 UGSoft connector 的 provider bet record 補資料路徑。主 callback flow 是 provider 主動打進來，通知 bet-settle；這條 sync flow 則是平台主動去 provider 拉一段時間的 bet record，用來補晚到或缺漏資料，最後仍送到同一條 BetRecord MQ，由 admin-api consumer 寫入正式 bet record。

入口有兩個 Quartz job：`AntplayBetRecordSyncJob` 和 `DerplayBetRecordSyncJob`。job 可以帶 `startTime / endTime`，有帶就是手動補跑；沒帶就是自動模式。自動模式進到 `ConnectBetRecordSyncService` 後會讀 Redis watermark。沒有 watermark 時，預設掃最近一小段時間；有 watermark 時，從上一個水位加安全 buffer 往後掃，同時避開最新幾分鐘，降低 provider 資料尚未 settle 或尚未可查的風險。

接著 service 掃 enabled agents。單一錢包 agent 會被跳過，因為這條 job sync path 不處理 single-wallet 模式。DerPlay 還會檢查 `derplayOpen`，並依 agent currencies 逐 currency 呼叫 provider。AntPlay / DerPlay 各自透過 adapter 打 provider bet record API，回來後 normalize 成 common response。

response 失敗時，service 不推進 watermark，下一輪還能再補。response 成功但沒有資料時，代表 provider 告訴我們這個窗口沒有資料，這時可以前進水位。若有 items，service 會依事件日期分組，因為同一個時間窗可能跨日，查重時要對到 `pt_day`。查 DB 時會用 `pt_day / agent_id / provider / provider_bet_id` 找既有資料，再組成 `providerBetId|currency` key。這個 key 同時用來排除 DB 已存在資料與同批 response 內的重複資料。

未存在的 item 會轉成 `ConnectBetRecordMqDto`，送到 `ConnectBetRecordMqService#send`。這裡和 callback path 共用 MQ producer，所以後面仍由 admin-api 的 BetRecord consumer 落庫。

Senior 角度我會特別看三個風險。第一，MQ publish failure：目前 producer 會 catch exception 並 log，如果 exception 沒回到 sync service，service 可能仍推進 watermark，最壞會造成資料漏送且自動 job 不再補同一段。第二，DerPlay per-currency：如果任一 currency 成功就推共同 watermark，其他 currency 失敗時可能被跳過。第三，pagination：provider response 有 page / pageSize / total，但 Step 3 沒看到完整 pagination loop，高流量窗口要確認不會超出 pageSize。

所以我會把改善方向放在 production correctness：publish success 或 outbox 成功後才推水位；watermark 拆到 agent + provider + currency 粒度；補 pagination / manual catch-up runbook；並加上 fetched count、duplicate skipped、MQ sent / failed、watermark lag、consumer lag / DLQ 這些 metrics。履歷上我會保守說參與 provider connector 的 bet record MQ、job sync、跨日查重與 currency 修正，不會說自己主導完整 reconciliation 或 exactly-once pipeline。

## STAR 版

Situation：provider callback 是主資料來源，但 production 上 bet record 可能會晚到、漏 callback，或需要人工補跑指定時間窗。

Task：需要一條 connector job sync path，把 provider bet record 拉回來，避免直接重複入庫，也避免和 callback path 產生不一致。

Action：系統用 Quartz job 觸發 AntPlay / DerPlay sync；自動模式用 Redis watermark 控制時間窗，手動模式可指定 start / end；資料回來後依 event date 分 `pt_day` 查 `pt_bet_record` existing keys，用 `providerBetId|currency` 做查重，再把未存在資料轉成 `ConnectBetRecordMqDto` 送 BetRecord MQ。

Result：這條 flow 提供 callback 之外的 late data 補資料能力，也留下 clear failure boundary：provider fail 不推水位、跨日查重可避免 day boundary 錯誤，但 MQ publish failure、per-currency partial success、pagination 與 observability 仍是需要 owner 持續強化的地方。

## 追問防線

| 追問 | 回答要點 | 防誇大邊界 |
| --- | --- | --- |
| 為什麼 callback 之外還需要 sync job？ | callback 可能晚到 / 漏打 / 下游曾異常；sync job 是補資料與 eventual consistency path。 | 不說完整 reconciliation。 |
| watermark 解決什麼？ | 降低每分鐘掃大區間造成的 provider API / DB 查重壓力；安全 buffer 降低邊界漏資料。 | 不說 watermark 等於資料已入庫。 |
| 為什麼避開最新幾分鐘？ | provider 資料可能尚未 settle 或尚未可查；避開最新窗口降低不穩定資料。 | 不保證 provider 永遠準時。 |
| 為什麼依 `pt_day` 分組？ | `pt_bet_record` 查重依賴 `pt_day`；跨日窗口若只查單日可能漏判。 | 不說已驗證所有時區情境。 |
| duplicate key 為什麼帶 currency？ | provider bet id 在多幣別 / normalize 場景下需避免不同 currency 被誤判同筆。 | 要和下游 consumer duplicate boundary 對齊，否則仍有風險。 |
| MQ publish failure 最大風險？ | producer catch exception 後 sync service 可能仍推水位，造成漏資料。 | 不說目前已具備 outbox / exactly-once。 |
| DerPlay 多 currency 風險？ | 任一 currency success 就推共同水位時，其他 currency fail 可能被跳過。 | 這是 code-backed 風險分析，不包裝成已修。 |
| pagination 風險？ | 短窗口降低超過 pageSize 機率，但不是 correctness guarantee；要看 total / page loop。 | Step 3 未看到完整 pagination loop。 |
| manual catch-up 要注意什麼？ | rate limit、資料量、DB 批次、MQ 堆積、consumer lag、重複資料。 | 不說已有完整 runbook。 |
| 你會怎麼強化？ | publish success / outbox 後推水位、per-currency watermark、pagination、DLQ replay、metrics。 | 用「我會」或「建議」，不是「我已完成」。 |

## 面試時可以主動講的 owner decision

1. Watermark 是進度指標，不是資料完成指標。
2. 查重應該貼近下游 source of truth，所以要查 `pt_bet_record` 與 `pt_day`。
3. 補資料 pipeline 的成功指標不能只看 Quartz job success，要看 provider fetch、duplicate skipped、MQ sent、watermark lag、consumer lag。
4. 補資料 flow 不應該和 callback flow 爭主權；兩者要用同一個 DTO / consumer / duplicate boundary 收斂。

## 可用履歷素材

- 參與 UGSoft provider connector / gateway 的 request / bet record MQ、Quartz job sync、跨日查重與 currency 修正。
- 具備 provider callback path 與 job-driven late data sync path 的 code-backed 理解，可說明 watermark、duplicate boundary、MQ eventual consistency 與補資料風險。

## 不可說

- 不說完整 reconciliation owner。
- 不說 exactly-once。
- 不說完整 outbox / DLQ / replay 平台已建立。
- 不說完整 bet record pipeline owner。
- 不把 `arnold` commits、team context 或 Step 4 分析包成 Nick 親自開發。

## Step 5 結論

- 本 flow 可升級為單條 flow claim gate。
- 可回填 `ugsoft-connector-api` project-level consolidation，強化 provider connector / bet record MQ / job sync / late data 補資料 evidence。
- 不直接更新 `05 / 08 / 04 / 17`；正式履歷仍吃 project-level consolidation 與 rolling resume package。
