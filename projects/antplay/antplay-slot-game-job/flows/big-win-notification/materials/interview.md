# Interview Notes - big-win-notification

日期: 2026-05-25

## Step 3 狀態

本檔是 `big-win-notification` 的 Step 3 初版面試素材。正式 Step 4 尚未完成。

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

## 30 秒版本草稿

我參與過 AntPlay job 裡的中大獎通知 flow。它消費 `settled_bets`，當玩家 `totalWin` 達到投注額與 voucher bet 的 10 倍時，會組 push user message，包含遮罩玩家名稱、遊戲代碼、翻譯名稱、幣別與金額，送到 `push_user` topic。這條我會定位成 derived notification，不是交易 source of truth；面試重點會講重複通知、producer failure、缺翻譯 fallback 與隱私邊界。

## 90 秒版本草稿

這條 flow 是 settlement event 後的中大獎通知。Job consumer 消費 `settled_bets`，反序列化成 `SettleBetsVo`，逐筆檢查 `BetRecord` 的 bet、voucher bet 與 total win。current code 的條件是 `totalWin >= (bet + voucherBet) * 10`。符合條件後，它會依 agent 找通知 channel，再依 currency 決定遊戲名稱翻譯語系，組成 push user message 送到 `push_user` topic。

我會保守說這是我參與初版、後續多人接手的 flow。它的重點不是保證交易正確，而是 notification reliability。比如 Kafka event 重送可能重複通知；producer send failure 目前只 log；缺遊戲翻譯會跳過通知；payload 有遮罩玩家名稱但也帶 full player name，這會牽涉隱私最小化。這些都是 Senior Backend 在非同步通知 flow 裡要能回答的邊界。

## Step 4 待整理

- 正式 3 分鐘版本。
- STAR。
- Failure scenarios 表格。
- Senior / Lead 追問短答。
- 可用反問。
- 不可誇大邊界。
