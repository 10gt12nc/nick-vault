# Career / Interview - app_bi daily-game-record-summary

更新時間：2026-05-15
完成狀態：Step 4 已完成；Step 5 尚未完成
證據層級：分析素材 / learning-only；code 功能為專案存在 / code-backed；Nick 貢獻待確認

## 結論

這條 flow 可以當面試分析案例，不更新正式履歷 / 自傳。

可講成：

```text
我分析過一條每日遊戲資料彙總的 reporting flow。它表面上是 app_bi 後台查詢，但實際要往上追 game_job producer，拆出 source log、type=0 projection、type=1 summary 與後台 pivot 查詢。我會從批次重跑、時區切分、資料成熟度、金額單位與補跑對帳角度看風險。
```

不能講成：

```text
我主導每日遊戲資料彙總。
我負責 game_job 報表批次。
我設計資料平台或報表架構。
我建立完整補跑 / 對帳 / 告警機制。
```

原因：

- 目前確認 code 功能存在，但 Nick 個人貢獻待確認。
- 這條是報表 projection / learning case，不是交易 source of truth。
- 沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認前，不放正式履歷。

## 已確認 code

- app_bi 後台查詢每日遊戲資料彙總。
- game_job 產生 `log_game_daily_record` 個體與彙總資料。
- job 會依平台切時間窗、計算 summary、留存與金額欄位。
- app_bi 曾有 SQL group、SUM pivot、平台選擇、金額換算、2 日留存與生成時間提示等修改。
- game_job 曾有跨時區、欄位長度、不同 DB、計算、新增人數 / 留存與備份等修改。

## 面試 30 秒版

我分析過一條每日遊戲資料彙總 flow。它不是單純後台查表，因為後台看到的數字是 game_job 每天從遊戲戰績日表整理出來的 projection。資料會先寫成 `log_game_daily_record type=0` 個體層，再聚合成 `type=1` summary，app_bi 後台才查 summary 顯示玩家數、投注、盈虧、殺率和留存。這條的重點是報表數字是否可信，例如 job 沒跑、時區切錯、重跑半成品、金額單位錯或留存窗口未成熟。

## 面試 3 分鐘版

這條 flow 我會定位成 reporting projection，而不是交易主流程。

後台入口在 app_bi 的每日遊戲資料彙總頁，使用者選日期和平台後，前端呼叫 `Payment::DailyGameDataSummary()`。這個 controller 查的是 `log_game_daily_record type = 1`，再用 `SUM(CASE WHEN sub_type = ... THEN value1 ELSE 0 END)` 把多種 summary row pivot 成一列報表。

但我不會只停在 app_bi controller，因為報表數字真正的風險在 producer。往上追到 `game_job` 後，可以看到 Antplay / PG 和 Iwin 分別有 daily job。job 會先刪除當日平台舊資料，再從 `slots_logv1.log_reel_{date}` 查原始戰績，寫入 `type = 0` 個體資料，再聚合出 `type = 1` summary，最後計算新增玩家和 1 / 2 / 3 / 7 日留存。

這裡的 production 風險主要有幾個。

第一是批次重跑的一致性。delete + insert 如果中途失敗，可能造成當日資料空掉或只完成一半。

第二是時區與資料日。Antplay、PG、Iwin 的資料窗口不同，game_job history 也有時區修正紀錄，所以日期看起來同一天，實際抓取範圍可能不同。

第三是報表 projection 被誤當 source of truth。`log_game_daily_record type=1` 是 summary，不是原始交易真相。報表錯不一定代表交易錯，但會影響營運判斷。

第四是 observability。UI 有固定生成時間提示，但我沒有看到報表直接顯示 job 最後成功時間、row count、補跑狀態或 source log 到 summary 的對帳結果。

如果我是 owner，我會先補 job run status、last success time、row count / amount 對帳、補跑審計，再考慮 staging table 或版本化 summary，避免重跑時留下半成品。

## 可展示能力

- 從後台報表入口追到 producer job，而不是只看 controller。
- 分辨 source log、projection、summary report。
- 看懂 batch job 的重跑、partial failure 與資料成熟度風險。
- 能把時區、金額單位、留存窗口放進 production 風險討論。
- 能保守區分 code-backed 功能與 Nick 個人 claim。

## 履歷候選句

目前不建議寫進正式履歷。

如果未來 Nick 補到本人 evidence，才可考慮非常保守版本：

```text
參與每日遊戲資料彙總 / 報表相關功能維護，協助釐清遊戲戰績來源、批次彙總、報表查詢與資料一致性邊界，提升報表數字排查與補跑風險判斷能力。
```

目前證據層級不足，不放入：

- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

## Claim Boundary

可以說：

- 我分析過每日遊戲資料彙總的 reporting flow。
- 我能從 source log、type=0 projection、type=1 summary、app_bi 查詢端拆資料流。
- 我能指出批次重跑、時區切分、金額倍率、留存窗口、job observability 與補跑對帳風險。

必須保守說：

- 這是 code-backed 分析素材。
- Nick 個人實作貢獻待確認。
- 目前不寫進正式履歷。

尚未確認：

- Nick 本人 MR / ticket / commit / production issue。
- Nick 是否維護過 producer job 或後台報表。
- 正式環境補跑、對帳、告警與 owner 流程。

不能說：

- 主導每日遊戲資料彙總。
- 負責 game_job 報表批次。
- 設計資料平台。
- 改善報表準確率或效能百分比。
- 建立完整對帳 / 補跑 / 告警機制。

## 詳細附錄

- 主研究報告：`flow.md`
- 證據附錄：`materials/evidence.md`
- 技術決策附錄：`materials/decision-notes.md`
- 面試稿附錄：`materials/interview.md`
- 履歷邊界附錄：`materials/claim-boundary.md`
