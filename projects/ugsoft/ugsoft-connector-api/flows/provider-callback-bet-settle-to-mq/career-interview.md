# provider-callback-bet-settle-to-mq Career / Interview

## Step 5 狀態

本檔是 `ugsoft-connector-api provider-callback-bet-settle-to-mq` 的 Step 4 面試 case，並已完成 Step 5 單條 flow claim gate。Step 5 判定本 flow 可回填 project-level provider connector / callback / MQ claim 作強化 evidence；但不直接更新 `05-resume-master-zh.md`、`08-application-autobiography-zh.md`、`04-interview-casebook.md` 或 `17-salary-negotiation.md`。

Step 5 後的保守結論：

- 可以面試講 provider callback / bet-settle / RabbitMQ / downstream consumer eventual consistency。
- 可以說 Nick / `10gt12nc` 有 callback 寫 MQ、`ConnectBetRecordMqService`、admin-api BetRecord MQ 入庫、currency / pt_day 類 direct commits。
- 可以回填 `ugsoft-connector-api contribution-claim-consolidation.md` 作 project-level provider connector / callback / MQ evidence。
- Provider IP whitelist、subAgent rewrite、amount scaling、error code propagation、latest callback behavior 多為 `arnold` / 團隊 context；Nick 已確認 `arnold` 是主管，不是 Nick direct evidence。

## 定位

這條 flow 可作 UGSoft 非 iwin 廣度補強，主軸是 provider callback / bet-settle / MQ eventual consistency。

證據層級：

- `真實開發過 + code-backed`：Nick / `10gt12nc` 在 callback 寫 MQ、`ConnectBetRecordMqService`、DerPlay / AntPlay bet record MQ、admin-api consumer 初版與 currency / pt_day 修正有 direct commits。
- `code-backed / 主管或團隊 context`：provider IP whitelist、二級代理 subAgent / response rewrite、amount scaling、error code propagation、latest callback behavior 多為 `arnold` commits。
- `分析素材 / 待確認`：production branch、incident、完整 retry / DLQ / outbox / reconciliation。

## 30 秒說法

UGSoft connector 有一條 provider callback 到 MQ 的 flow。AntPlay / DerPlay 打 callback 進來後，connector 先做白名單、驗簽、agent / wallet type 檢查，再透過 adapter callback 呼叫商戶端 betSettle。只有商戶端 callback 成功時，connector 才把 payload 正規化成 `ConnectBetRecordMqDto` 丟 RabbitMQ；下游 `ugsoft-admin-api` consumer 再做 duplicate check、寫 bet record，並觸發 quota update。這條不是 exactly-once，而是靠 MQ 與 consumer idempotency 收斂。

## 90 秒說法

這條 case 我會定位成 provider integration 的 eventual consistency。provider callback 進 `ConnectCallbackController`，AntPlay 有 info、balance、bet_settle，DerPlay 是 form callback，依 action 分 getBalance 和 betNSettle。

Service 先做驗簽、agent、wallet type 檢查。bet-settle 不會直接寫 bet record，而是先透過 `AdapterCallback` 呼叫商戶側 money_inout / betSettle API；只有 adapter response code 成功時，才用 `ConnectBetRecordMqService` 建立 MQ payload。AntPlay 和 DerPlay payload 不同，所以這裡要處理 amount、currency、game code、providerBetId、settle time 這些 normalization。

MQ 發到 `ugSoftConnectBetRecord.direct`，下游 `ugsoft-admin-api` 的 `ConnectBetRecordListener` consume 後，做 payload validation、duplicate check、寫 `BetRecord`，成功後再 fire-and-forget 發 quota update。這條 flow 的追問重點是 callback 重送、MQ 重送、amount 單位、currency、provider bet id 去重與 consumer idempotency；我不會說這是完整 exactly-once，而會說它需要下游去重、監控和補償。

## 3 分鐘面試講法

UGSoft connector 這邊我整理過一條 provider callback 到 MQ 的下注結算 flow。它的情境是 AntPlay 或 DerPlay provider 在玩家一局遊戲結算後，打 callback 到 UGSoft connector，connector 需要把這筆 bet-settle 轉成平台可落地的 bet record。

入口在 `ConnectCallbackController`。AntPlay 有 `/connect/callback/antplay/info`、`balance`、`bet_settle`，DerPlay 則是 `/connect/callback/derplay/{connectorAgentId}`，用 `action` 區分 `getBalance` 和 `betNSettle`。Controller 會做 request validation、provider IP whitelist，並在 finally 寫 callback request log。這裡有一個 current behavior 是 AntPlay request log 用 `connectorAgentId`，避免 provider 自己的 agentId 污染 request serial key。

Business 層在 `ConnectCallbackService` 和 `CallbackDerplayService`。AntPlay 會驗簽，balance / betSettle 會檢查 wallet type；DerPlay 會用 cert 找 access token 和 currency，然後 parse `DerplayBetNSettleMessage`。接著系統透過 `AdapterCallback` 呼叫商戶端的 callback API。這一步很重要，因為這條 flow 的語意不是 provider callback 一進來就入庫，而是商戶端 callback 成功後才把 bet-settle 送進 MQ。

MQ producer 是 `ConnectBetRecordMqService`。它會把 AntPlay / DerPlay 不同格式的 payload normalize 成同一個 `ConnectBetRecordMqDto`，欄位包含 time、agentId、provider、providerBetId、game、account、bet、totalWin、status、currency 等。AntPlay 主要是 BigDecimal 欄位；DerPlay 的 message 裡 amount 是 long，還要處理 game prefix、settle time、currency。這也是 provider integration 很容易出錯的地方。

發送端會 publish 到 `ugSoftConnectBetRecord.direct`。下游在 `ugsoft-admin-api`，有 `ConnectBetRecordListener` consume 這個 queue，交給 `ConnectBetRecordConsumerService` parse payload、檢查 agentId / provider / providerBetId / currency，再依 ptDay 和 id 做 duplicate check，沒存在才寫 `BetRecord`。寫入後還會 fire-and-forget 發 quota update。

我會主動說這不是 exactly-once。它比較像 at-least-once delivery 加 downstream idempotent consumer。主要 failure window 有幾個：adapter callback 成功但 MQ publish 失敗，producer catch log 後不往外拋；MQ consumer 失敗時 retry / DLQ 要看 listener container 設定；callback 或 job sync 重送時，duplicate key 口徑要一致；amount / currency mapping 錯會造成帳務或報表錯誤。所以如果我要 owner 這條 flow，我會優先補 publish failure metric、queue depth / consumer lag、consumer success / duplicate / invalid payload 指標，以及必要時 outbox 或 replay 機制。

證據邊界上，我可以說 Nick / `10gt12nc` 有 callback 寫 MQ、BetRecord MQ consumer、currency / pt_day 類 direct commits；但最新 IP whitelist、subAgent rewrite、amount scaling、error code propagation 多是主管 / 團隊 context，我不會把它包成我獨立主導完整 callback / MQ architecture。

## STAR 版本

### Situation

UGSoft connector 需要接第三方 provider callback，處理單一錢包 / 下注結算類資料。這類 callback 不能只看 endpoint 是否回成功，還要確保成功的 bet-settle 會被正規化、送 MQ、被下游 consumer 寫入 bet record，且 provider 或 MQ 重送時不會重複入庫。

### Task

我整理與參與的範圍是 callback 後 bet record MQ pipeline：provider callback 入口、adapter callback 成功判斷、MQ DTO mapping、RabbitMQ exchange / queue、admin-api consumer 入庫與 duplicate boundary。目標是把它整理成能面試講清楚的 production flow，而不是只說我接過 API。

### Action

我把 flow 拆成 producer 和 consumer 兩段。connector 先驗簽、檢查 agent / wallet type，呼叫商戶 callback，成功後才送 MQ；admin-api consume 後做 providerBetId / currency / ptDay / agentId 相關查重，再寫 bet record。中間特別標出 amount unit、currency default、provider game prefix、callback 重送、MQ publish failure 和 consumer idempotency 的風險。

### Result

這條 case 可以支撐我說明 provider callback / MQ eventual consistency 的設計與風險判斷：哪裡是同步 callback，哪裡是非同步落地，哪些欄位是 duplicate key，哪些地方還需要 retry / DLQ / outbox / monitoring 補強。它也能和 transfer wallet flow 互補，一條講商戶主動 transfer，一條講 provider callback 後的 bet record pipeline。

## 可用履歷素材候選

保守 bullet：

- 參與 UGSoft provider connector callback / bet-settle / bet record MQ pipeline，處理 AntPlay / DerPlay callback、MQ payload normalization、provider bet id、currency / pt_day 與 downstream bet record 入庫邊界。

更保守版本：

- 參與 UGSoft connector 的 provider callback 與 RabbitMQ 非同步資料處理維護，協助整理 bet-settle payload、duplicate boundary 與下游 bet record consumer 對接。

Step 5 判斷：以上可作 `ugsoft-connector-api` project-level rolling consolidation 的強化素材；正式投遞版仍吃 project-level consolidation，不由單條 flow 直接改 `05 / 08`。

## 常見追問

### Q1：callback 重送怎麼處理？

目前不要假設 producer 端 exactly-once。provider callback 重送時，connector 可能再次送 MQ，真正的防重點在 downstream consumer。admin-api consumer 會 parse payload，確認 agentId、provider、providerBetId、currency，再依 event time 算 ptDay 做查重，沒存在才 save。這是 at-least-once + idempotent consumer 的思路。

### Q2：MQ publish 失敗怎麼辦？

目前 `ConnectBetRecordMqService#send` catch exception log，不往外拋。這代表 callback response 和 MQ 入列之間可能不一致。如果要補強，我會加 publish failure metric / alert，並考慮 outbox 或 request log replay，讓已成功的 adapter callback 可以補送 MQ。

### Q3：為什麼要商戶 callback 成功後才送 MQ？

因為這條 flow 的 business 語意是 provider callback 進來後，還要同步呼叫商戶端 betSettle / money_inout。若商戶端回失敗，卻先把 bet record 入庫，會造成平台 bet record 與商戶錢包狀態不一致。因此目前成功送 MQ 的條件是 adapter callback response code 成功。

### Q4：AntPlay 和 DerPlay payload 差在哪？

AntPlay callback 是 JSON DTO，金額欄位已是 BigDecimal。DerPlay 是 form callback，message 裡有 betAmount、winAmount、settleTime、betID 等欄位，amount 單位和 game code prefix 要另外 normalize。這也是 provider integration 常出錯的地方，尤其 history 裡可以看到 amount scaling / currency default 多次修正。

### Q5：consumer 怎麼防重？

consumer 會先把 MQ payload parse 成 BetRecord payload，拿 providerBetId、currency、agentId、event time 算出 ptDay，再查是否已有同一筆資料。未存在才寫 BetRecord。這裡要注意 producer 端的 id / providerBetId / currency 口徑必須和 consumer 查重口徑一致，否則會重複或漏判。

### Q6：這和 `request-bet-record-mq-sync` 有什麼不同？

這條是 callback-driven：provider 主動通知 bet-settle，connector 成功後送 MQ。`request-bet-record-mq-sync` 是 job-driven：系統定時或手動拉 provider bet record，用時間窗和查重補資料。前者是即時 callback pipeline，後者比較像補漏 / late data pipeline。

### Q7：這和 `transfer-wallet-in-out-query` 有什麼不同？

`transfer-wallet-in-out-query` 是商戶主動呼叫 transfer in / out / 查單，重點是 transferReferenceId、provider transaction、request log、transaction / lookup。這條 callback / MQ flow 是 provider 主動 callback，重點是 bet-settle payload normalization、MQ delivery、downstream consumer idempotency 和 bet record persistence。

## Lead / Architect 追問

### Q：如果要設計成更可靠，你會補什麼？

我會先補觀測和 replay，而不是直接宣稱要重寫。第一層是 metrics：callback success / failure、adapter callback code、MQ publish failure、queue depth、consumer lag、consumer save success / duplicate / invalid payload、quota update failure。第二層是 durable recovery：若 publish MQ 失敗，可以用 outbox 或 request log replay 補送。第三層是 consumer idempotency：明確定義 providerBetId + agentId + currency + ptDay 的唯一性，避免 callback 和 job sync 同時補資料時撞出重複。

### Q：為什麼不用同步直接入庫？

同步直接入庫會把 provider callback latency、商戶 callback、BetRecord DB、quota update 都綁在同一個 request 裡，任何一段慢或失敗都會影響 provider callback response。用 MQ 可以把入口和資料落地解耦，也方便削峰。不過代價是要承擔 eventual consistency、duplicate、missing message 和觀測成本。

### Q：這條 flow 的 owner 指標是什麼？

我會看四層：callback 層的成功率與驗簽 / whitelist failure；MQ producer 的 publish success / failure；queue / consumer 的 lag、duplicate、invalid payload、save success；資料層的 bet record 對帳和 quota update failure。單看 callback 200 不夠，因為 200 不代表 consumer 已落庫。

### Q：如果 provider callback 成功但 MQ 沒送出去，怎麼修？

這是 producer 端主要 failure window。短期可以靠 callback request log 和手動 replay 補；中期應加 publish failure alert；長期可以在同一個 local transaction 裡寫 outbox，再由 relay 發 MQ，讓 publish 失敗可重試。因為 provider callback 和 MQ 不在同一個原子交易，這點不能說已經完全解決。

## 誇大陷阱

- 不說 exactly-once；說 at-least-once + idempotent consumer。
- 不說我主導完整 outbox / DLQ；目前只看到 code-backed 風險判斷。
- 不說我完整 owner callback / MQ / admin consumer / quota 全鏈路。
- 不說 amount scaling / subAgent rewrite / whitelist 全部是 Nick direct work；目前多為主管 / 團隊 context。
- 不說已完整解決所有重送、漏送、對帳問題。

## Step 5 claim gate

Step 5 已完成。單條 flow 結論：

- 可升級到 project-level claim：provider connector callback / bet-settle / bet record MQ pipeline、AntPlay / DerPlay callback payload normalization、connector producer + admin consumer 對接、currency / pt_day / duplicate boundary。
- 只能保留為 code-backed / 團隊 context：IP whitelist、subAgent rewrite、amount scaling、error code propagation、request log connectorAgentId、完整 callback service refactor。
- 不可誇大：完整 callback / MQ architecture owner、exactly-once / outbox / DLQ owner、完整 bet record reconciliation / quota owner、production incident owner。
- `05 / 08 / 04 / 17` 不直接更新；本輪只回填 project-level consolidation 與索引狀態。
