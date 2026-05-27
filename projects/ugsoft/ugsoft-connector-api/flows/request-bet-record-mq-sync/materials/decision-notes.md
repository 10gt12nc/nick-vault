# Decision Notes - request-bet-record-mq-sync

## 1. Watermark 不是 completion guarantee

Redis watermark 只能代表 sync service 認為某個時間窗已處理，不代表資料已 durable 入庫。若 MQ publish failure 被 producer catch 掉，watermark 仍可能前進，形成漏資料。

較穩的 decision：

- producer 回傳 publish success / failure。
- 或使用 local outbox，寫入 outbox 成功才推水位。
- 或水位只推到已確認 downstream consume / ack 的位置，但成本較高。

## 2. Empty response 是否可推水位

空資料 response 被視為成功，這是合理選擇，因為 provider 已回答該區間沒資料。但前提是 provider API 的空資料語意可信；若 provider 有 eventual delay，則需要安全 buffer 或後續更大窗口補掃。

## 3. Cross-day 查重是必要設計

Bet record table 以 `pt_day` 作重要查詢 / 分區條件時，sync items 必須依事件日期分組。否則跨日窗口會查錯 day，導致重複 MQ 或漏判 existing records。

## 4. Duplicate key 要和 consumer 對齊

Producer side 用 `providerBetId|currency` 去重；DB existing query 回傳同樣概念。下游 consumer 也必須使用一致的 duplicate boundary，否則 producer / consumer 兩邊會有認知差。

## 5. DerPlay currency 應拆 watermark 粒度

DerPlay 逐 currency 拉資料，但共同 watermark 若由任一 currency success 推進，會有 partial success 風險。比較好的設計是：

- watermark key 包含 `agent + provider + currency`。
- 或全部 currency success 才推共同 watermark。
- 失敗 currency 要有 retry / alert。

## 6. Pagination 不能只靠短時間窗賭運氣

短時間窗可以降低超過 pageSize 的機率，但不是 correctness guarantee。若 provider response 提供 `total / page / pageSize`，owner 要確認：

- 是否需要 loop 下一頁。
- 是否在 total > pageSize 時 alert。
- 是否有手動補跑策略。

## 7. Manual catch-up 要有 runbook

手動 `startTime / endTime` 可以補長時間故障，但要控制：

- provider rate limit。
- 單次資料量。
- DB IN query 批次。
- MQ 堆積與 consumer lag。
- 是否會重複發送已存在資料。

## 8. Observability 應看 pipeline 而非只看 job success

只看 Quartz job success 不夠。較完整的 metrics 應包含：

- provider response success / fail count。
- fetched items count。
- duplicate skipped count。
- MQ sent success / fail count。
- watermark advance count / lag。
- per agent / provider / currency failure。
- downstream consumer lag / DLQ count。
