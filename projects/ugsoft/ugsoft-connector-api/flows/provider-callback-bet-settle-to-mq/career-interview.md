# provider-callback-bet-settle-to-mq Career / Interview

## Step 3 狀態

本檔是 `ugsoft-connector-api provider-callback-bet-settle-to-mq` 的 Step 3 初版 career / interview 素材。Step 3 只把 code-backed flow 整理成可讀學習包與保守口說素材；尚未完成 Step 4 正式面試 case，也尚未完成 Step 5 claim gate。

本輪不直接更新：

- `05-resume-master-zh.md`
- `08-application-autobiography-zh.md`
- `04-interview-casebook.md`
- `17-salary-negotiation.md`

原因：單條 flow Step 3 不是 project-level final consolidation。

## 定位

這條 flow 可作 UGSoft 非 iwin 廣度補強，主軸是 provider callback / bet-settle / MQ eventual consistency。

證據層級：

- `真實開發過 + code-backed`：Nick / `10gt12nc` 在 callback 寫 MQ、`ConnectBetRecordMqService`、DerPlay / AntPlay bet record MQ、admin-api consumer 初版與 currency / pt_day 修正有 direct commits。
- `code-backed / 主管或團隊 context`：provider IP whitelist、二級代理 subAgent / response rewrite、amount scaling、error code propagation、latest callback behavior 多為 `arnold` commits；Nick 已確認 `arnold` 是主管，不是 Nick direct evidence。
- `分析素材 / 待確認`：production branch、incident、完整 retry / DLQ / outbox / reconciliation。

## 30 秒說法

UGSoft connector 有一條 provider callback 到 MQ 的 flow。AntPlay / DerPlay 打 callback 進來後，connector 先做 IP 白名單、驗簽、agent / wallet type 檢查，再透過 adapter callback 呼叫商戶端的 info / balance / betSettle。只有商戶端 callback 成功時，connector 才把 bet-settle payload 正規化成 `ConnectBetRecordMqDto` 丟到 RabbitMQ；下游 `ugsoft-admin-api` consumer 再做 duplicate check、寫 bet record，並觸發 quota update。

## 90 秒說法

我會把這條 case 定位成 provider integration 的 eventual consistency，而不是 exactly-once。provider callback 進 `ConnectCallbackController`，AntPlay 有 info、balance、bet_settle，DerPlay 則是 form callback，依 action 分 getBalance 和 betNSettle。

Service 會先驗簽、查 agent、檢查 wallet type。bet-settle 不是直接入庫，而是先透過 `AdapterCallback` 呼叫商戶側 money_inout / betSettle API；只有 adapter response code 成功時，才用 `ConnectBetRecordMqService` 建立 MQ payload。AntPlay 和 DerPlay 的 payload 不同，所以這裡會處理 amount、currency、game code、providerBetId、settle time 等 normalize。

MQ 發到 `ugSoftConnectBetRecord.direct`，下游 `ugsoft-admin-api` 的 `ConnectBetRecordListener` consume 後，會做 payload validation、duplicate check、寫 `BetRecord`，成功後再 fire-and-forget 發 quota update。這條 flow 的重點是 callback 重送、MQ 重送、amount 單位、currency、provider bet id 去重與 consumer idempotency；我不會說這是完整 exactly-once，而會說它是需要下游去重和補償的非同步資料流。

## 可用 STAR 草稿

### Situation

UGSoft connector 需要接第三方 provider callback，處理單一錢包 / 下注結算類資料。這類 callback 不能只看 API 是否回成功，還要確保成功的 bet-settle 會被正規化、送 MQ、被下游 consumer 寫入 bet record，且重送時不會重複入庫。

### Task

我整理與參與的範圍是 callback 後 bet record MQ pipeline：provider callback 入口、adapter callback 成功判斷、MQ DTO mapping、RabbitMQ exchange / queue、admin-api consumer 入庫與 duplicate boundary。

### Action

我把 flow 分成 producer 和 consumer 兩段：connector 先驗簽 / 檢查 agent / wallet type，呼叫商戶 callback，成功後才送 MQ；admin-api consume 後做 providerBetId / currency / ptDay / agentId 相關查重，再寫 bet record。中間特別標出 amount unit、currency default、provider game prefix、callback 重送與 MQ publish failure 的風險。

### Result

這條 case 可以支撐我面試時說明 provider callback / MQ eventual consistency 的設計與風險判斷：哪裡是同步 callback，哪裡是非同步落地，哪些欄位是 duplicate key，哪些地方還需要 retry / DLQ / outbox / monitoring 補強。

## 可用履歷素材候選

保守 bullet：

- 參與 UGSoft provider connector callback / bet-settle / bet record MQ pipeline，處理 AntPlay / DerPlay callback、MQ payload normalization、provider bet id、currency / pt_day 與 downstream bet record 入庫邊界。

更保守版本：

- 參與 UGSoft connector 的 provider callback 與 RabbitMQ 非同步資料處理維護，協助整理 bet-settle payload、duplicate boundary 與下游 bet record consumer 對接。

Step 3 判斷：以上只是候選素材；正式是否回填 `05 / 08` 要等 Step 5 或 project-level consolidation refresh。

## 不能說

- 不說我主導完整 callback / MQ / admin consumer 全鏈路。
- 不說我設計完整 exactly-once / outbox / DLQ。
- 不說我完整 owner bet record / quota / reconciliation。
- 不說最新 IP whitelist、subAgent rewrite、amount scaling 全部是 Nick direct work；目前多為 `arnold` / 團隊 context。
- 不說有量化改善或 production incident owner，除非之後補 ticket / incident evidence。

## Step 4 要補

- 3 分鐘正式面試講法。
- Senior / Lead 追問：重送、MQ duplicate、consumer idempotency、amount scaling、currency、DLQ / retry。
- 誇大陷阱：exactly-once、完整 reconciliation、完整 owner。
- 這條 flow 和 `transfer-wallet-in-out-query`、`request-bet-record-mq-sync` 的差異。
