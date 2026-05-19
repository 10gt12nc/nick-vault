# partition-table-creation career-interview

完成狀態：Step 3。

證據層級：專案存在 / code-backed。這條 flow 目前沒有 Nick / `10gt12nc` direct path evidence，不能寫成真實開發或主導；只作 table rollover / schema rollout 的面試分析素材。

## 一句話版本

這是一條分表 rollover job：每天定時讀 classpath SQL template，替 BI DB 與所有 channel game DB 建立下一天 / 下一月分表，避免跨日或跨月時 log writer、batch projection、報表查詢因表不存在而失敗。

## 30 秒版本

我分析過 `game_job` 的分表建立 job。它不是業務交易 flow，而是可靠性前置任務：Quartz 每天觸發後，讀 `initBiTable` 與 `initGameTable` 的 SQL template，計算下一天 / 下一月表名，對 `bi_log` 和所有 active channel DB 執行 `CREATE TABLE IF NOT EXISTS`。

這條適合講 table rollover 的風險：template 檔名要正確、已存在表不會自動 migrate、單張表或單個 channel 失敗可能形成 partial success，而且 job 目前主要靠 log 觀測，缺少 per table summary 和 fail-fast。

## 3 分鐘版本

`CreateTableJob` 由 `CreateTableJobQuartz` 包起來，設定 task name `BiTaskStatus:createTable`、daily task、timeout 5 分鐘，再走 `BiJobBase.runTask()`。

核心邏輯分兩段：

1. 載入 `initBiTable/*.sql`，對固定 `bi_log` DB 建下一天 / 下一月表。
2. 載入 `initGameTable/*.sql`，查 active channel list，對每個 channel 的 `db_name` 建 game log 分表。

表名是從 template 檔名解析，例如 `log_reel-day.sql` 會變成明天的 `log_reel_yyyy_m_d`，`payment_order-month.sql` 會變成下個月的 `payment_order_yyyy_m`。

Senior 風險不在 SQL 多複雜，而在 rollover reliability：

- `CREATE TABLE IF NOT EXISTS` 讓重跑具備基本 idempotency，但 schema 改動不會套到已存在分表。
- 建表錯誤被 catch 成 log，單張表或單個 channel 失敗可能不會讓整個 task 明確 fail。
- 如果 job 沒跑，隔天可能只建下一期，未必補回缺失期。
- template 檔名若錯，例如 `player_bet_demo-mouth.sql`，目前邏輯不會視為 month，可能建立非預期表名。

所以這條不是履歷主線，但可以用來面試講 batch 系統的前置依賴、schema rollout、observability 與 fail-fast。

## 可面試講

- 分表建立是 production batch / log writer 的前置依賴，不是單純 DBA 雜事。
- `CREATE TABLE IF NOT EXISTS` 解決重跑安全，但不解決 schema migration。
- 大量 channel DB 建表要能看 partial success，不能只看 job 最後有沒有結束。
- template naming 應該 fail fast，避免錯字造成表名錯誤。

## 不可誇大

- 不說 Nick 開發這條 flow。
- 不說 Nick 主導 schema migration 平台。
- 不說 production 實際啟用已驗證。
- 不說這條有完整 migration、rollback 或 schema drift 管理。
- 不寫任何量化改善。

## 下一步

```text
iwin game_job partition-table-creation Step 4
```
