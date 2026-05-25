# Interview Notes - big-win-notification

日期: 2026-05-25

## Step 4 狀態

本檔是 `big-win-notification` 的正式 Step 4 面試稿。這條 flow 是 Nick 有 direct evidence 的 Kafka derived notification case，但不是完整 push platform claim。

## 面試主軸

這條 flow 的主軸是 derived notification:

```text
settled_bets -> big-win rule -> privacy / translation / payload formatting -> push_user topic
```

不要講成交易正確性或完整推播平台。安全說法是「我參與過初版，並能用 current code 分析 notification flow 的可靠性與邊界」。

## 可講問題

### Q1: 為什麼用 settlement event 做通知？

因為中大獎通知是下注結算後的衍生事件，不應阻塞下注主流程。代價是它變成 eventual / best-effort notification，要處理重送、漏送、重複送與下游失敗。

### Q2: 這條 flow 的 source of truth 是什麼？

中大獎判斷的輸入是 `BetRecord` 結算結果；通知 message 只是衍生資料，不是錢包或獎金 source of truth。

### Q3: Kafka 重送會怎樣？

current code 每次產生新的 UUID `_id`，如果同一 settled event 重送，可能再產生一筆通知。完整做法應該用 bet id + notification type 做 deterministic idempotency key，或讓下游用 source event key 去重。

### Q4: Producer send failure 怎麼處理？

`KafkaProducerService` 用 async future callback log 成功 / 失敗，呼叫端不等待，也沒有 durable retry。這表示目前偏 best-effort，重要通知要補 outbox、alert 或補推工具。

### Q5: 玩家隱私要注意什麼？

current code 有 `maskPlayerName`，但 payload 仍包含 `fullPlayerName`。如果下游不需要完整帳號，應移除或限制使用，避免把隱私資料擴散到 notification topic。

## 30 秒版本

我參與過 AntPlay job 裡的中大獎通知 flow。它消費 `settled_bets`，當玩家 `totalWin` 達到投注額與 voucher bet 的 10 倍時，會組 push user message，包含遮罩玩家名稱、遊戲代碼、翻譯名稱、幣別與金額，送到 `push_user` topic。這條我會定位成 derived notification，不是交易 source of truth；面試重點會講重複通知、producer failure、缺翻譯 fallback 與隱私邊界。

## 90 秒版本

這條 flow 是 settlement event 後的中大獎通知。Job consumer 消費 `settled_bets`，反序列化成 `SettleBetsVo`，逐筆檢查 `BetRecord` 的 bet、voucher bet 與 total win。current code 的條件是 `totalWin >= (bet + voucherBet) * 10`。符合條件後，它會依 agent 找通知 channel，再依 currency 決定遊戲名稱翻譯語系，組成 push user message 送到 `push_user` topic。

我會保守說這是我參與初版、後續多人接手的 flow。它的重點不是保證交易正確，而是 notification reliability。比如 Kafka event 重送可能重複通知；producer send failure 目前只 log；缺遊戲翻譯會跳過通知；payload 有遮罩玩家名稱但也帶 full player name，這會牽涉隱私最小化。這些都是 Senior Backend 在非同步通知 flow 裡要能回答的邊界。

## 3 分鐘版本

我可以分享一條 AntPlay slot job 裡的中大獎通知 flow。這條 flow 的定位不是下注主交易，而是 settlement event 後的 derived notification。上游結算完成後會送 `settled_bets` Kafka event，job 端的 `BigWinConsumerService` 會讀 `SettleBetsVo` 裡的一批 `BetRecord`，檢查 bet、voucher bet 和 total win。current code 的門檻是 `totalWin >= (bet + voucherBet) * 10`，符合條件才進入通知流程。

接著它會依 `agentId` 找代理，並依幣別決定遊戲名稱語系。current code 是 CNY 用 `cn`，其他幣別用 `en`，再從 `TransGames` 找對應 game code 的名稱。如果找不到翻譯，它會 log error 並跳過通知。這裡的 owner decision 是顯示正確性和通知完整性之間的取捨：跳過可以避免錯名，但會漏通知；如果要更穩，應該補 fallback 和告警。

payload 會送到 `push_user` topic，包含 `_id`、`type=3`、`channel=agent_{agentId}_all`，以及玩家遮罩名稱、game code、翻譯名稱、prize、currency。這裡我會特別注意 privacy，因為 current payload 也帶 `fullPlayerName`。如果下游只需要公開展示，full name 應該移除或放到更嚴格的 internal audit channel。

可靠性上，這條 flow 比較像 best-effort notification。producer 是 async send，callback 只 log success / failure，consumer 不等待也沒有 durable retry。如果 event 重送，current `_id` 每次 UUID，可能重複通知。比較好的設計是用 `betRecord.id + notificationType` 當 deterministic idempotency key，讓 retry 和下游去重都更安全。

我會保守說，這條我有 `#303` direct evidence，參與初版 consumer 和金額格式；但 current behavior 後續有 Gill、Arnold、Eliot 修改，所以不說主導完整推播平台。我會把它當作 Kafka derived notification 的案例，講清楚非同步通知的 reliability、privacy、translation fallback 和 owner trade-off。

## STAR

| 面向 | 內容 |
| --- | --- |
| Situation | AntPlay slot 需要在玩家結算後達到中大獎門檻時，送出通知給對應代理。 |
| Task | 參與中大獎通知初版，把 settled bet event 轉成可推送的 notification message。 |
| Action | 建立 / 維護 `BigWinConsumerService`，消費 `settled_bets`，依 win / bet 倍數判斷 big-win，組 `push_user` message；後續用 current code 分析玩家遮罩、翻譯、producer failure 與重送風險。 |
| Result | 形成可面試的 Kafka derived notification case，可回答 event-driven notification、best-effort delivery、privacy 與 fallback 邊界。 |

## Failure Scenarios

| 情境 | 面試回答重點 |
| --- | --- |
| Kafka event 重送 | `_id` 每次 UUID，可能重複通知；要補 deterministic key 或下游去重。 |
| producer send failure | async callback 只 log，屬 best-effort；重要通知要 outbox / retry / alert。 |
| 缺遊戲翻譯 | current code skip，避免錯名但會漏通知；建議 fallback + metric。 |
| fullPlayerName 擴散 | public notification 不一定需要 full account，應最小化 payload。 |
| 金額單位錯 | `bet / voucherBet / totalWin` 單位要一致，否則門檻與顯示都會錯。 |
| cache stale | translation cache 60 秒更新，需觀測與 refresh 策略。 |

## Senior 追問短答

### Q1: 這條 flow 的 source of truth 是什麼？

中大獎判斷依據是 settled bet record；通知 message 只是衍生資料，不是交易或錢包 source of truth。

### Q2: 怎麼避免重複通知？

用 deterministic key，例如 `betRecord.id + notificationType`，讓 producer outbox 或 downstream consumer 去重。UUID 只能 trace，不適合防重。

### Q3: producer send failure 要不要讓 consumer retry？

要看通知重要性。若只是展示，可以 best-effort 但要 metric / alert；若業務要求必達，就要 outbox / retry job，不該只 log。

### Q4: 缺翻譯怎麼辦？

current code skip 是保守做法，但要告警。更完整可以用 game code 或 default language fallback，避免安靜漏通知。

### Q5: fullPlayerName 為什麼危險？

通知 topic 可能被多個下游消費；公開展示只需要 masked name，full account 應該限制在 internal audit。

## Lead / Architect 追問

| 追問 | 回答方向 |
| --- | --- |
| 只能改一件事先改什麼？ | deterministic notification key，讓 retry / replay 可以安全去重。 |
| 怎麼補 observability？ | send success / failure、translation missing、duplicate suppressed、generated notification count。 |
| 要不要做 outbox？ | 若通知是營運必達，應做；若只是 UX，可先 metric + replay tool。 |
| privacy 怎麼設計？ | public payload 只放 masked name，full account 只放權限更嚴的 audit path。 |
| 怎麼和產品確認門檻？ | 明確定義 bet、voucherBet、totalWin 單位與公式，補測試案例。 |

## 面試常見陷阱

| 陷阱 | 避免方式 |
| --- | --- |
| 說自己做完整推播平台 | 改說參與中大獎通知初版與 current code-backed 分析 |
| 把通知當交易真相 | 強調它是 derived notification |
| 忽略重送重複通知 | 主動提 deterministic idempotency key |
| 忽略 producer failure | 說明 current best-effort 與 outbox / alert 改善 |
| 忽略隱私 | 主動指出 `fullPlayerName` payload boundary |

## 可用反問

- 你們通知類事件要求必達，還是 best-effort？
- Push notification 有沒有 outbox / retry / DLT？
- Notification topic 裡允許放完整玩家帳號嗎？
- 缺翻譯時你們偏好 fallback 還是 skip？
- 你們如何避免 event replay 造成重複通知？

## Step 4 結論

Step 4 已完成正式面試稿。下一步 Step 5 應追 claim gate: `_id` / bet id 是否可作下游去重、`BetIdPersistence` 用途、下游 push consumer / frontend 隱私邊界，以及是否回填 project-level consolidation。未完成 Step 5 前，不更新 `05 / 08`。
