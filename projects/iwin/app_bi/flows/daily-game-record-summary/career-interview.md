# Career / Interview - app_bi daily-game-record-summary

更新時間：2026-05-15
完成狀態：Step 3 附帶初步邊界；Step 4 尚未正式完成
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 目前定位

這條 flow 目前只能當作「報表 projection / 批次資料一致性」學習與面試分析素材。

已確認 code 有：

- app_bi 後台查詢每日遊戲資料彙總。
- game_job 產生 `log_game_daily_record` 個體與彙總資料。
- job 會依平台切時間窗、計算 summary、留存與金額欄位。

尚未確認：

- Nick 本人 MR / ticket / commit / production issue。
- Nick 是否負責 producer job 或後台報表。
- 正式環境補跑、對帳、告警與 owner 流程。

## 可用於面試的保守說法

```text
我有把每日遊戲資料彙總這條報表 flow 拆過。這類報表不能只看後台查詢，要往上追 producer job，確認原始戰績 log、每日 projection、summary table 和後台 pivot 查詢的邊界。真正的風險通常不是 API 回不回資料，而是 job 沒跑、時區切錯、重跑半成品、金額倍率錯或留存窗口還沒成熟，導致營運看到的數字不可信。
```

## 不能放進正式履歷的說法

- 主導每日遊戲資料彙總。
- 負責 game_job 報表批次。
- 設計資料平台。
- 改善報表準確率或效能百分比。
- 建立完整對帳 / 補跑 / 告警機制。

## Step 4 待整理

Step 4 應正式產出：

- 3 分鐘面試講法。
- Senior 追問與回答。
- Lead / Architect 追問。
- 報表 projection 與交易 truth source 的邊界。
- 不能誇大的履歷邊界。
