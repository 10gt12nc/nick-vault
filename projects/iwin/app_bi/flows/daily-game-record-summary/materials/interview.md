# Interview - app_bi daily-game-record-summary

更新時間：2026-05-15
完成狀態：Step 4 尚未完成；本檔先放 Step 3 初稿
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 3 分鐘講法草稿

```text
我看過一條每日遊戲資料彙總 flow。它表面上是後台報表查詢，但我不會只停在 controller 查表，而是往 producer 追。這條 flow 的資料來源是遊戲戰績日表，game_job 會依平台和時間窗整理成 log_game_daily_record，先寫 type=0 個體資料，再聚合成 type=1 summary，app_bi 後台再查 type=1 顯示玩家數、投注、盈虧、殺率和留存。

這類 flow 的重點不是 API 能不能回資料，而是報表數字能不能信。風險包含 job 沒跑、時區切錯、重跑 delete 後失敗、summary 重複被 SUM 加總、金額單位換算錯、留存窗口未成熟。Senior / Owner 角度會希望看到 job 狀態、最後成功時間、row count 對帳、補跑審計與 source log 到 summary 的一致性檢查。
```

## Senior 追問

Q：這條 flow 的 source of truth 是哪裡？

A：比較接近 source log 的是 `slots_logv1.log_reel_{date}`。`log_game_daily_record type=0` 是中間 projection，`type=1` 是後台查詢用 summary。app_bi 報表本身不是交易 truth source。

Q：如果 job 跑一半失敗會怎樣？

A：目前 code evidence 顯示 job 會先 delete 當日平台資料，再寫 type=0、聚合 type=1、寫留存。若中途失敗，可能留下空資料或部分資料。這需要補 job status、重跑流程、row count 檢查或 staging table。

Q：為什麼時區是風險？

A：Antplay、PG、Iwin 的資料日界線不同，game_job history 也有 PG 從 GMT+1 改 GMT+8、Antplay 改 GMT+0 的修正。報表日期看似同一天，但實際抓取時間窗不同，切錯會造成整天資料錯位。

## Lead / Architect 追問

Q：你會怎麼讓這份報表更可靠？

A：我會先補 observability，而不是先重構。至少要有每個日期 / 平台的 job run status、開始結束時間、row count、錯誤訊息、最後成功時間。再補 source log 到 type=0 到 type=1 的對帳檢查。若重跑常見，會考慮 staging table 或先寫新版本再切換，避免 delete 後半成品。

Q：這能不能寫進履歷？

A：目前不能寫成個人主導成果。沒有 Nick 本人 MR / ticket / commit / production issue 前，只能當作分析素材或面試時說明自己的 flow reading / production risk thinking。
