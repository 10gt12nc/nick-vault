# Decision Notes - app_bi 後台設定同步 Redis

完成狀態：所屬 flow 已完成 Step 5
文件角色：`materials/decision-notes.md` 技術決策附錄

## 這條 flow 要學的技術點

這條不是「Redis 怎麼 hset」而已，核心是:

```text
MySQL 設定來源
-> Redis projection
-> runtime reload / read
-> stale / partial / rollback / reconcile
```

Senior / Owner 要能講清楚的是：設定同步成功到底代表什麼。

## 設定同步的常見設計選項

### Option A: 手動同步，直接覆寫 Redis

目前 `app_bi` 多數接近這種方式。

優點:

- 簡單。
- 營運可控。
- 出問題時可手動重點同步。

缺點:

- DB 與 Redis 可能長時間不一致。
- 多 key / 多 channel 容易 partial success。
- 若缺少版本與 checksum，不容易知道 runtime 實際讀到哪一版。

適用:

- 設定量不大。
- 更新頻率低。
- 可以接受人工同步。

### Option B: 儲存設定時自動同步 Redis

優點:

- 減少忘記同步。
- 後台設定與 Redis 更接近即時一致。

缺點:

- 後台儲存交易會綁 Redis availability。
- Redis 故障會讓後台改設定失敗。
- 若沒有 outbox / retry，失敗後更難補償。

適用:

- 設定變更必須即時生效。
- 有明確 retry / audit / rollback。

### Option C: DB 為 truth source，背景 job 投影到 Redis

優點:

- 可重試。
- 可集中 observability。
- 可做版本、checksum、reconcile。

缺點:

- 架構比較複雜。
- 有短暫延遲。
- 需要處理 job 卡住與順序問題。

適用:

- 高風險設定。
- 多服務共同讀取。
- 需要可恢復與可觀測。

## Owner 應補的保護

### 1. Version / checksum

每次投影後記錄:

- `version`
- `updated_at`
- `operator`
- `source_table`
- `checksum`

用途:

- 排查 Redis 是否落後 DB。
- 確認 runtime 讀到哪一版。
- 避免只靠「同步成功」文字判斷。

### 2. Atomic swap

高風險 key 避免:

```text
DEL old
HSET new...
```

可考慮:

```text
write temp key
validate
rename / swap version pointer
```

用途:

- 避免中間空窗讀到空資料。
- 降低活動 / 遊戲清單 / 支付配置短暫消失風險。

### 3. Per channel result

多 channel 同步不要只回單一成功，應回:

- 成功 channel
- 失敗 channel
- 失敗 key
- error message
- 是否保留 waitSyn

用途:

- 避免 partial success 被包成成功。
- 方便人工補同步。

### 4. Reload ack

如果 runtime 需要 reload，`sendGmCommand()` 不只要送出，還要知道:

- 是否送達。
- 哪些節點已 reload。
- 哪些節點失敗。
- 失敗是否可重送。

目前 `app_bi` 只確認到 sender 端；receiver 未掃。

### 5. Reconcile

定期比對:

```text
MySQL enabled settings
vs
Redis projection keys
```

用途:

- 找出漏同步、欄位缺漏、舊資料殘留。
- 支援事故後復原。

## 面試追問方向

- 為什麼設定中心不能只靠 Redis？
- Redis projection 與 source of truth 怎麼定義？
- 如果同步到第 5 個 channel 失敗，前 4 個成功要 rollback 嗎？
- 如果 reload 失敗但 Redis 已更新，要回成功還是失敗？
- 如果新欄位忘了同步到 Redis，要怎麼靠測試或檢查避免？
- 如何設計一個可觀測的設定同步平台？

## 本地 code 邊界

這些是 owner 改善方向，不是本地已完成能力:

- version / checksum
- atomic swap
- reload ack
- reconcile job
- per channel sync report
- approval before-after diff

目前本地 code 已確認的是:

- 手動同步入口。
- MySQL -> Redis projection。
- 部分 waitSyn / opLog。
- 部分 reload command。
- 真實 commit 顯示欄位投影會漏、同步邊界會修。
