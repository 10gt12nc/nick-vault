# Decision Notes - app_bi daily-game-record-summary

更新時間：2026-05-15
完成狀態：Step 3 技術決策附錄
證據層級：專案存在 / code-backed；外部通用判斷未補網路案例

## 這條 flow 的硬底子

這不是 CRUD 題，而是 batch reporting / projection 題。

核心觀念：

```text
source log != projection != report
```

`log_reel` 是比較接近 source log 的資料；`log_game_daily_record type=0` 是中間 projection；`type=1` 是後台查詢用 summary。

## 技術取捨

| 設計 | 好處 | 代價 |
| --- | --- | --- |
| 先寫 type=0，再聚合 type=1 | 可以保留個體層與 summary 層，方便重算 | 表內同時混個體 / 彙總，查詢要嚴格帶 type |
| 重跑前先 delete 當日平台資料 | 避免重跑重複加總 | delete 後失敗會留下空窗 |
| app_bi 查 type=1 pivot | 查詢端簡單，前端好顯示 | 重複 summary 會被 SUM 放大 |
| 平台分 job / 分時間窗 | 符合不同平台對帳日界線 | 時區設定錯會整天錯位 |
| 靜態 UI 提示生成時間 | 使用者知道大概何時看資料 | 沒有真正 last success timestamp |

## Owner 應該追的設計問題

1. Job 失敗時，有沒有 alert？
2. 重跑是否一定 idempotent？
3. delete + insert 是否有 transaction 或 staging table？
4. 查詢端是否能知道資料生成時間與 job 狀態？
5. source log、type=0、type=1 是否有 row count / checksum / 金額總和檢查？
6. 留存資料是否要等窗口成熟後才顯示？
7. Excel 匯出是目前頁面還是全量查詢結果？

## 可改善方向

保守改善，不代表目前 code 已有：

- 加一張 `daily_job_run_status` 或同等狀態表，記錄日期、平台、開始時間、結束時間、status、row count、錯誤訊息。
- producer 使用 staging table：先寫新資料，驗證成功後 swap / replace，降低 delete 後空窗。
- app_bi 顯示最後成功生成時間，而不是只顯示固定時間提示。
- 補 row count / sum amount 對帳：source log vs type=0 vs type=1。
- 補平台時間窗 test case，避免 GMT 設定回歸。

## 不展開的部分

- 不展開 Kafka / MQ，因為本 flow 未看到 MQ evidence。
- 不展開 Redis consistency，因為查詢端與 producer 未看到 Redis。
- 不展開即時交易一致性，因為本 flow 是報表 projection。
