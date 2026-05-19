# third-party-record-mongo-backup decision-notes

完成狀態：Step 4。

用途：整理這條 Mongo retention flow 的 owner decision、取捨與面試 framing。

## Step 4 判斷

這條 flow 的核心不是「排程清資料」，而是「跨 collection copy-then-delete 是否能安全重跑」。

目前 code 順序是先 insert backup，再 delete origin。這個順序偏安全，因為失敗時比較可能留下 duplicate，而不是直接丟資料。但它還缺少 idempotent write、batch reconciliation 與 retention policy 的明確 decision。

`10gt12nc` 相關 diff 顯示 GSC job 曾從一次查出所有符合條件的資料，改成 `while` loop + `limit(BATCH_SIZE)` 分批查詢、逐批 insert backup、逐批依 `_id` delete origin；後續又調整 batch size。這可作為 Step 4 面試素材，但是否能寫成正式履歷要等 Step 5。

## Owner decision 1：資料丟失 vs duplicate

建議立場：

- retention / audit 類資料，寧可 duplicate，不可丟資料。
- 若 duplicate 會污染查詢，查詢端或 backup job 必須有 `_id` / source id 去重。

落地要求：

- backup collection 保留原 `_id` 或保留 source id。
- backup write 使用 duplicate-safe 行為。
- delete origin 前確認 backup write 成功。

## Owner decision 2：batch reconciliation

目前只 log backup count 與 delete count，沒有看到持久化 reconciliation。

建議至少紀錄：

- job run id
- source collection
- backup collection
- threshold date
- fetched count
- copied count
- deleted count
- remaining old count
- failed reason

這能支援事故後回答：哪一天哪一批資料有沒有真的搬完。

## Owner decision 3：retention policy

目前 hard-code：

- active 14 天。
- backup 30 天。

這需要和以下需求對齊：

- provider dispute 可追溯多久。
- 客服查詢會查 active 還是 backup。
- audit / compliance 是否允許 30 天後 hard delete。
- storage 成本和查詢成本。

若沒有明確需求，不能把 14 / 30 天當成理所當然。

## Owner decision 4：concurrency

`@DisallowConcurrentExecution` 只保守確認單 scheduler 同 job 不重疊。

仍需確認：

- Quartz cluster locking。
- 多 instance 部署。
- manual endpoint 是否在 production 開放。
- manual endpoint 和 Quartz 同時跑時的行為。

若不能確認，建議加 distributed lock 或以 DB / Redis lock 限制同 collection 同時只有一個 backup run。

## Owner decision 5：時間欄位語意

retention 依賴 `createdTime`，所以要確認：

- `createdTime` 是 provider event time 還是寫入時間。
- 補單 / 延遲資料是否可能帶很舊的 `createdTime`。
- 時區與 Mongo date type 是否固定。

`a6268cb` 曾修正 ISODate，表示時間型別不是小問題。

## Owner decision 6：batch size

GSC history 有 batch size 調整：

- `d11b1f4`：從一次查全量改成分批查詢，初始 batch size 1000。
- `bf92773`：GSC log / transaction batch size 調到 10000。
- `b993395`：GSC transaction 後續調到 2500。

面試時可以說 batch size 是 trade-off，不是越大越好：

- 太大：單次 Mongo query / insert / delete 壓力高，memory 與 lock / IO 風險提高。
- 太小：loop 次數變多，job duration 變長，log / network round trip 變多。
- transaction 類資料和 log 類資料可能需要不同 batch size，因為文件大小、索引、寫入量與查詢需求不同。

合理 owner decision 是先用 production metrics 校準：每批耗時、delete count、remaining old docs、job duration、Mongo CPU / IO、錯誤率，而不是只憑感覺調整。

## Owner decision 7：面試 framing

這條 case 不要講成「我會 Mongo 備份」。更好的 framing：

```text
我會把批次 retention 當成資料生命週期與 partial failure 題。
我關注 copy-before-delete、idempotent retry、delete count reconciliation、retention policy 與查詢端邊界。
```

這比背 class 名稱更像 Senior / Owner。

## Step 5 前不可升級的點

- 不可把 `10gt12nc` 的兩個 GSC commit 擴大成完整 Antplay / GSC backup owner。
- 不可說 production 已驗證，因 main config GSC disabled，production enable 仍待部署 config 確認。
- 不可寫改善 X%，因沒有 metrics。
- 不可說完整 audit retention policy 由 Nick 設計。
