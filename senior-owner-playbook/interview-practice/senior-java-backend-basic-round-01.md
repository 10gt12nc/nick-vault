# Senior Java Backend Basic Round 01

## 定位

- 類型：基本功診斷 + production case 翻譯訓練。
- 主題：Spring transaction、DB / MQ consistency、provider callback idempotency。
- 目的：確認 Nick 能不能把抽象八股題轉成 production 場景，再用 flow / risk / guard / trade-off 回答。

## Round Summary

本輪最大發現不是「完全不會」，而是抽象題沒有場景時容易卡住。後續問答要先把題目翻譯成具體 production flow，例如：

```text
抽象題 -> 對到某條 flow -> 說出風險 -> 說出 guard / trade-off
```

Nick 可以用這種開頭把答案拉回主場：

```text
我用 payment callback 場景來講。這題的風險是...
```

或：

```text
如果放在 MQ projection 場景，問題會是...
```

## Q1. Spring `@Transactional` rollback

### 原題

Spring `@Transactional` 預設什麼情況會 rollback？什麼情況不會 rollback？如果 catch exception 後沒有重新丟出，transaction 會怎樣？

### Nick 原始回答摘要

- 會 rollback。
- 系統掛了、不可抗力因素就不會 rollback。
- catch exception 後沒有重新丟出，會繼續交易。

### 評分

`3 / 10`

### 判斷

Nick 抓到「catch 後沒重新丟出，transaction 會繼續」這個重點，但 rollback 條件不精準。

### 修正版回答

```text
Spring @Transactional 預設遇到 RuntimeException / Error 會 rollback；
checked exception 預設不 rollback，除非指定 rollbackFor。

如果 exception 被 catch 掉、沒有重新 throw，Spring 會以為方法正常結束，就會 commit。
所以 service 裡不能隨便 catch 後吞掉 exception，除非我明確知道這個狀態就是要 commit，
或我有另外的補償、狀態標記與後續處理。
```

### 人話版

```text
@Transactional 不是看到錯就一定 rollback，它是看方法最後有沒有把會觸發 rollback 的 exception 丟出去。
你 catch 掉不丟，它就當成功。
```

### 下次追問

- self-invocation 為什麼會讓 transaction 失效？
- checked exception 要 rollback 時怎麼設定？
- 多個 service 方法互相呼叫時，transaction boundary 要怎麼看？

## Q2. DB 更新成功，但 MQ 發送失敗

### 原題

DB 更新成功，但 MQ 發送失敗，可能造成什麼問題？如果是 payment callback 或報表 projection，你會怎麼設計讓它可追蹤、可重試、可補？

### Nick 原始回答摘要

- 會數據不一致。
- 覺得題目缺上下文，不知道是在說哪個場景。
- 提到三方打我們系統，如果沒回成功，他們會重發，我們限制三次。

### 評分

`2 / 10`

### 判斷

「數據不一致」方向正確，但回答太空。`限制三次` 只是 retry policy，不是完整可靠性設計。

### 場景翻譯

```text
訂單 DB 已經改成成功，但通知下游的 MQ 沒送出去。
結果可能是本地狀態成功了，但下游上分、報表 projection 或通知沒有發生。
```

### 修正版回答

```text
DB 成功但 MQ 發送失敗，會造成本地狀態已經變更，但下游沒有收到事件。
例如 payment 訂單成功了，但 game lobby 沒有上分；或下注紀錄寫進 DB，但報表 projection 沒更新。

我不會只靠失敗重送三次，因為三次都失敗後仍然可能漏資料。
我會讓這件事可追蹤、可重試、可補：
例如用 outbox table 或 durable event log，
把要發的事件跟 DB transaction 一起落地，
再由背景 job / publisher 去送 MQ。

送失敗時保留狀態、重試次數、錯誤原因與告警；
必要時可以由人工或補償 job 重新送出。
```

### 人話版

```text
不能只有「失敗就重送三次」。
要先確保這筆該送的事件有被記下來，
不然三次都失敗後，你連漏哪筆都不知道。
```

### 下次追問

- Outbox pattern 怎麼解決 DB 成功但 MQ 失敗？
- 如果 MQ publish 成功，但 consumer 失敗呢？
- 報表 projection 重跑時怎麼避免重複寫入？

## Q3. Provider callback 重送防重

### 原題

Provider callback 重送時，怎麼避免重複入帳或重複退款？你會在哪幾層做 guard？哪些地方不能直接宣稱 exactly-once？

### Nick 原始回答摘要

- 回調會先查這張單。
- 題目太抽象，看不懂在問什麼，希望改成人話。

### 評分

`3 / 10`

### 判斷

「先查訂單」是正確第一步，但還需要補齊入口、consumer、下游副作用三層 guard。

### 場景翻譯

```text
三方 provider 同一筆付款 callback 打很多次，我們怎麼避免同一筆訂單重複上分或重複退款？
```

### 修正版回答

```text
我會做多層 guard。

第一層在 callback 入口查訂單狀態，
只有 WAIT / PROCESSING 這種可處理狀態才往下走；
如果已經成功、失敗、退回或異常，就直接回 provider ack，不再重做副作用。

第二層在 MQ consumer 再查一次訂單狀態，
因為 MQ retry 或重複投遞也可能讓同一筆再進來。

第三層是真正動錢的下游也要有 idempotency key，
例如 billNo / orderNo，避免上分或退款 API 被打兩次就真的加兩次錢。

但我不會直接宣稱 exactly-once。
要看 DB unique key、下游 game lobby / wallet 是否真的用 billNo 查重，
以及 callback ack 和 MQ enqueue 是否有 durable event 保護。
```

### 人話版

```text
不是只查一次訂單。
入口要擋，consumer 要再擋，真正動錢的下游也要能用單號防重。
少一層就可能在 retry 時出事。
```

### 下次追問

- `billNo` 是 correlation key 還是 idempotency guarantee？
- 提現失敗退款為什麼比充值成功更危險？
- callback ack 和 MQ enqueue 中間失敗怎麼辦？

## 下一輪建議

不要直接跳新題。先讓 Nick 用自己的話重答這三題，每題 60 秒內：

```text
Q1. 你在 service 裡更新訂單，途中發生錯誤。什麼情況 DB 會 rollback？什麼情況明明有錯但 DB 還是 commit？

Q2. 訂單 DB 已經改成功，但通知下游的 MQ 沒送出去。你怎麼避免這筆資料永遠漏掉？

Q3. 三方 provider 同一筆付款 callback 打了很多次。你要怎麼避免玩家被重複加錢？
```
