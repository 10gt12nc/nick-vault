# partition-table-creation career-interview

完成狀態：Step 5。

證據層級：專案存在 / code-backed。這條 flow 目前沒有 Nick / `10gt12nc` direct path evidence，不能寫成真實開發或主導；Step 5 判定不更新正式履歷 / 自傳，只作 table rollover / schema rollout 的面試分析素材。

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

## STAR 正式版

### Situation

跨日或跨月後，某個 log writer、batch projection 或報表查詢可能遇到 table not found。表面看起來像上游寫入或報表邏輯錯，但真正原因可能是分表 rollover job 沒跑、某個 channel DB 沒建到、template 命名錯誤，或既有表 schema drift。

### Task

我的分析目標不是證明這條是交易主流程，而是把 table rollover 這個支撐性 flow 拆清楚：它什麼時候跑、建哪些 DB / table、怎麼命名、哪些 failure 會被隱藏，以及 production 要怎麼補觀測與補救。

### Action

1. 先從 Quartz 入口確認 `CreateTableJobQuartz` 的 task name、daily 類型、timeout 與 `@DisallowConcurrentExecution`，避免誤判是否可能併發。
2. 再讀 `CreateTableJob`，拆成 BI 固定 `bi_log` 建表與 game active channel DB 建表兩段。
3. 追 `InitTableConfig` 的檔名解析，確認只有 `day` / `month` 會轉成下一天 / 下一月 suffix。
4. 對照 `initBiTable` 與 `initGameTable` template，確認 SQL 來源是 classpath trusted template，`CREATE TABLE IF NOT EXISTS` 只提供存在性 idempotency。
5. 檢查 config 與 history，標出 main / k3s 目前都 disabled、direct path 沒有 Nick commit，避免履歷 claim 誇大。
6. 把 owner 風險整理成 filename validation、partial success summary、schema drift check、manual backfill / dry-run、per channel alert。

### Result

最後這條可以作為面試中的 reliability case：我能把跨日 / 跨月缺表問題拆成 schedule、template、table naming、channel DB、SQL execution、schema drift 六段，也能說出 `CREATE TABLE IF NOT EXISTS` 能解決什麼、不能解決什麼。

## 面試官追問與回答

### Q1：`CREATE TABLE IF NOT EXISTS` 是否代表完全 idempotent？

不是。它只表示表已存在時不重建，能讓 rollover job 重跑時比較安全；但它不會更新既有表欄位、索引或型別，所以不能取代 migration 或 schema drift repair。

### Q2：為什麼只建下一天 / 下一月？

這是提前 rollover：讓 writer / batch 到新日期或新月份時，表已經存在。缺點是如果 job 停了一段時間，單純重跑不一定補回歷史缺表，所以 production 需要指定日期 / 月份的補建工具與 dry-run。

### Q3：如果某個 channel DB 建表失敗，怎麼判斷？

目前 code 主要靠 log，這是風險。比較好的做法是每輪產生 per channel / per table summary：expected、created、already existed、failed，最後如果有 failed table list，就讓 task status 或 alert 明確呈現非全成功。

### Q4：`player_bet_demo-mouth.sql` 這種檔名錯誤會怎樣？

目前解析只認 `day` / `month`，`mouth` 不會被當成月份，所以可能產生沒有月份 suffix 的非預期表名。這種錯應該加 filename validation，啟動或 job 開始時 fail fast。

### Q5：為什麼不用 Flyway / Liquibase？

這條 job 的目標是大量日期 / 月份分表與多 channel DB 的 rollover，template 建表可以處理「下一期表要先存在」這件事。但 schema evolution 是另一個問題，仍需要 migration framework、drift check 或 repair SOP 來管理。

### Q6：如果要做成 production-grade，你會補什麼？

我會補六件事：template filename validation、dry-run expected table list、per table / per channel summary、failed table alert、指定日期 / 月份補建工具、schema drift 檢查。這些比單純把 exception log 出來更能支撐 owner 排查。

### Q7：這條能寫履歷嗎？

不建議寫正式履歷成果。Step 5 重新確認後，direct path 仍沒看到 Nick / `10gt12nc` commit，也沒有本人確認、MR、ticket 或 production issue evidence。可以在面試中當作我分析過 batch reliability / table rollover 的例子，但不能說我開發或主導。

## Step 5 claim gate

結論：不更新 `senior-owner-playbook/05-resume-master-zh.md` 或 `08-application-autobiography-zh.md`。

原因：

- direct path history 只看到 Arnold 的 first commit 與 k3s migration。
- Nick / `10gt12nc` 在 `CreateTableJob`、Quartz wrapper、`InitTableConfig`、SQL template、mapper path 沒有 commit 命中。
- `origin/k3s` 的 prod cron 對齊 commit 仍維持 `createTableEnable=false`，且作者不是 Nick。
- 沒有 Nick 本人確認此 flow 是實際參與開發。
- 這條是支撐性 reliability case，不適合單獨當履歷主成果。

## 可面試講

- 分表建立是 production batch / log writer 的前置依賴，不是單純 DBA 雜事。
- `CREATE TABLE IF NOT EXISTS` 解決重跑安全，但不解決 schema migration。
- 大量 channel DB 建表要能看 partial success，不能只看 job 最後有沒有結束。
- template naming 應該 fail fast，避免錯字造成表名錯誤。
- 面試中可把 incident 排查拆成 schedule、template、channel DB、SQL execution、schema drift。

## 不可誇大

- 不說 Nick 開發這條 flow。
- 不說 Nick 主導 schema migration 平台。
- 不說 production 實際啟用已驗證。
- 不說這條有完整 migration、rollback 或 schema drift 管理。
- 不寫任何量化改善。

## 下一步

```text
iwin game_api agent-bonus-receive-transfer Step 5
```
