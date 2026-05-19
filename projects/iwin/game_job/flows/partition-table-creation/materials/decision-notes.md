# Decision Notes: partition-table-creation

完成狀態：Step 5。

用途：整理分表建立 flow 的 owner decision、取捨與面試 framing。

## 決策題 1：SQL template 建表 vs migration framework

現況：

- schema 來源是 `initBiTable/*.sql` 與 `initGameTable/*.sql`。
- job 只做 `CREATE TABLE IF NOT EXISTS`，不是 Flyway / Liquibase 類 migration。

優點：

- 對大量日期 / 月份分表很直覺。
- 不需要為每個 channel DB 寫 migration history。
- 重跑基本安全。

代價：

- 已存在表不會自動改 schema。
- schema drift 只能靠人工或另外 SOP。
- template 檔名錯誤可能靜默變成錯表名。

Owner 判斷：

- 若只是 rollover，template 建表可以接受。
- 若要管理 schema evolution，需要 migration / drift check / repair SOP。

## 決策題 2：只建下一期 vs 可指定補建

現況：

- `day` 建下一天。
- `month` 建下一月。
- 沒看到指定日期 / 月份補建入口。

優點：

- 日常排程簡單。
- 不容易誤建大量歷史表。

代價：

- job 停一天後，不一定能補回缺失表。
- 新 channel 開通時，歷史所需分表可能不完整。
- incident repair 需要人工 SQL 或臨時改 code / config。

Owner 判斷：

- production 需要最小補建工具：指定 DB、template、date / month，dry-run 後執行。

## 決策題 3：錯誤只記 log vs fail task

現況：

- `checkAndCreateTable()` catch exception 後記 log。
- `checkTablePartition()` 也 catch exception。

優點：

- 一張表失敗不會阻斷其他表或其他 channel。
- 可降低單點 schema 問題造成全局停擺。

代價：

- job 可能看起來完成，但實際有 partial failure。
- 下游 writer 到缺失表時才爆。
- 排查要翻 log，缺少 summary。

Owner 判斷：

- 可以保留 continue-on-error，但最後必須彙總 failed table list，並讓 task status / alert 知道本輪非全成功。

## 決策題 4：多 channel DB 建表

現況：

- game table 會查 active channel list，對每個 `db_name` 建表。

優點：

- 新增 active channel 後，理論上會自動納入分表建立。
- 避免每個 channel 手動建表。

代價：

- channel config 若錯或漏，該 DB 不會建表。
- channel 很多時，timeout / DB 慢查會放大。
- 單個 channel failure 可能不明顯。

Owner 判斷：

- 需要 per channel result 與 expected table count。
- 新 channel onboarding 要有建表 verification。

## Step 4 面試 decision framing

面試時應把這條定位成 reliability / owner decision，不要包成「我做了一個 schema migration 平台」：

- table rollover job 的價值是讓 writer / batch / report 跨日跨月前有表可寫可查。
- `CREATE TABLE IF NOT EXISTS` 是存在性 idempotency，不是 schema evolution。
- 多 channel DB 的 owner 風險是 partial success 與缺少 per channel summary。
- template 檔名是 contract，未知格式應 fail fast。
- production 補強重點是 dry-run expected list、failed summary、alert、backfill 與 schema drift check。

## Step 4 後仍不可升級的點

- 不可寫 Nick 真實開發這條 flow，因 direct path 未見 Nick / `10gt12nc` commit。
- 不可說 production 已驗證啟用，因 main / k3s config 都 disabled。
- 不可說這條是完整 schema migration platform。
- 不可寫改善 rollover 正確率、事故數或建表時間 X%。

## Step 5 claim decision

最終處理：

- 保留為 code-backed 面試 case。
- 不更新正式履歷 / 自傳。
- 不把 `game_job` 既有 daily summary / GSC backup 的真實開發 evidence 擴張到本 flow。

理由：

- direct path 未見 Nick / `10gt12nc` commit。
- `origin/k3s` prod cron 對齊 commit 由 Arnold 提交，且 `createTableEnable=false`。
- 沒有本人確認或 production incident evidence。
- 本 flow 的價值是 owner 思維補充，不是履歷主成果。
