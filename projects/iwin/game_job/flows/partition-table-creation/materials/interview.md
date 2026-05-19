# Interview Notes: partition-table-creation

## 面試主軸

這條 flow 適合拿來講：「批次與報表系統不是只有 job 本身，還要確保跨日 / 跨月時分表已存在，否則 writer、projection、query 都可能在 rollover 時失敗。」

證據層級：專案存在 / code-backed。不能說是 Nick 真實開發或主導。

完成狀態：Step 5，已轉成正式面試 case，並完成 claim gate；只作 code-backed 面試素材。

## 口語開場

我分析過一條分表建立 job。它每天跑一次，讀 classpath 裡的 SQL template，替 BI DB 和所有 active channel DB 建立下一天或下一月的分表。這條不直接處理交易，但它是很多 batch / log writer 的前置依賴。

## 深入講法

```text
它的核心是 table rollover reliability。

BI template 會建 bi_log 的月表，例如 payment_order_yyyy_m。
Game template 會依 active channel db 建日表或月表，例如 log_reel_yyyy_m_d。

表名由檔名決定：xxx-day.sql 或 xxx-month.sql。
SQL 用 CREATE TABLE IF NOT EXISTS，重跑基本安全。

風險是：
1. 已存在表不會套 schema migration。
2. 單表或單 channel 失敗只記 log，可能 partial success。
3. 檔名格式錯不會 fail fast。
4. job 只建下一期，不一定補歷史缺表。
```

## STAR 素材

### Situation

跨日或跨月後，某個 batch job、log writer 或報表查詢突然報 table not found。

### Task

判斷是分表未建立、channel DB 漏建、template schema 不一致，還是 job enable / schedule 沒跑。

### Action

1. 查 `BiTaskStatus:createTable` task log，確認 create table job 是否有跑。
2. 查 config 的 `createTableEnable` 與 cron，確認部署是否啟用。
3. 依缺失表名回推 template：是 `initBiTable` 還是 `initGameTable`，是 `day` 還是 `month`。
4. 查 active channel list，確認該 channel `db_name` 是否存在且被 job 掃到。
5. 對照 SQL template，確認 `${tableName}` 會被替換成預期 DB.table。
6. 查 job log 裡單表 create failure，避免 task 看似成功但某張表失敗。
7. 若是 schema drift，不能只重跑 create table，要另外做 migration / repair。

### Result

能把問題拆成 schedule、template、table naming、channel DB、SQL execution、schema drift 六段，避免把跨日缺表誤判成上游 writer 或報表邏輯錯。

## 3 分鐘正式講法

```text
我分析過 game_job 裡一條 table rollover job。它不是交易主流程，但它支撐 log writer、batch projection 和報表查詢。

它每天由 Quartz 觸發，先讀 initBiTable template，替固定 bi_log DB 建下一月或下一天的 BI 分表；再讀 initGameTable template，查 active channel list，對每個 channel 的 db_name 建 game log 分表。

這條我會從 owner 角度看幾個點：

第一，CREATE TABLE IF NOT EXISTS 只讓重跑相對安全，不代表 schema evolution。既有表如果欄位或索引落後，job 不會自動修。

第二，多 channel DB 建表會有 partial success。某個 channel 或某張表失敗，如果只是 log，後面 writer 才爆 table not found，排查成本會高。

第三，table naming 是 contract。像 filename 只有 day / month 會被解析，錯成 mouth 這種情況應該 fail fast，不能靜默建立錯表。

所以如果我要把它補強，會加 filename validation、dry-run expected list、per table / per channel summary、failed alert、指定日期補建工具，以及 schema drift check。
```

## 面試官追問與回答

### Q1：`CREATE TABLE IF NOT EXISTS` 是否代表完全 idempotent？

不是。它只代表表已存在時不重建，不代表 schema 會自動更新。若 template 改了，既有表仍可能 schema drift。

### Q2：為什麼要建下一天 / 下一月？

這是提前 rollover。等 writer 或 batch 真正寫入時，表應該已經存在，避免跨日 / 跨月第一批流量失敗。

### Q3：如果某個 channel DB 建表失敗，怎麼發現？

目前主要靠 log，不夠。比較好的做法是每輪輸出 per channel / per table summary：expected、created、already existed、failed，並把 failed table list 接到 alert。

### Q4：看到 `player_bet_demo-mouth.sql` 你會怎麼處理？

我會先把 template filename validation 加成 fail fast，只允許 `day` / `month`。目前 `mouth` 不會被當成 `month`，可能建立沒有月份 suffix 的表，這種錯應該在啟動或 job 開始前就被發現。

### Q5：這可以寫履歷嗎？

目前不建議。這條缺 Nick direct evidence，而且本身是支撐性 flow。可以作面試補充，不作正式履歷成果。

### Q6：為什麼不用 Flyway / Liquibase？

分表 rollover 和 schema evolution 是兩個問題。這條 job 用 template 快速建立大量日期 / 月份分表與多 channel DB，適合處理「下一期表要先存在」。但已存在表的欄位、索引、型別變更，仍應該由 migration / drift check / repair SOP 管理。

### Q7：如果 job 停了兩天怎麼辦？

目前 code 只看下一天 / 下一月，沒有看到指定日期補建能力。owner 做法會是補一個可 dry-run 的 backfill 工具：指定 DB、template、日期 / 月份，先列出 expected table，再執行並輸出 summary。

### Q8：這條的 production-grade 標準是什麼？

不是「有跑完」就好，而是能回答本輪應該建幾張表、實際成功幾張、哪些已存在、哪些失敗、失敗後誰收到告警、是否可安全補建、是否能辨識 schema drift。

## 可用句子

- 分表建立不是交易流程，但它是 batch / log writer 的前置可靠性條件。
- `CREATE TABLE IF NOT EXISTS` 解決重跑，不解決 schema evolution。
- 多 channel DB 的建表 job 一定要看 partial success，不能只看 job 結束。
- Template naming 應該 fail fast，因為錯字會變成錯表名或無 suffix 表。
- Rollover job 的 owner 重點是 expected table list、failed summary、alert 與 backfill。
