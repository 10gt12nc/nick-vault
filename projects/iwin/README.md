# iwin projects

本資料夾整理 iwin 相關 repo 的 Senior / Owner 學習資料。長期主軸是 production flow，不做 class summary，也不把後台入口包裝成完整後端成果。

## 讀檔順序

1. [app_bi](app_bi/README.md)：PHP / ThinkPHP 後台與 BI / control plane，目前已有多條 flow。
2. [iwin_gameserver](iwin_gameserver/README.md)：Java 遊戲伺服器，已建立 Step 1 候選 flow。

## 共用邊界

已確認：

- iwin 來源 repo 位置以 [projects/source-repo-inventory.md](../source-repo-inventory.md) 為索引。
- `app_bi` 偏後台 / BI / control plane。
- `iwin_gameserver` 偏遊戲 runtime、center_http、玩家錢包、投注 / 派彩 / 退款、DB proxy 與 log writer。

待確認：

- Nick 本人實際參與過哪些 repo / flow。
- 哪些 flow 有 Nick 本人 MR / ticket / commit / production issue / 本人確認。

履歷邊界：

- 沒有 Nick 本人 evidence 前，所有 iwin flow 預設只能標為 `專案存在 / code-backed` 或 `分析素材 / learning-only`。
- 不更新正式履歷 / 自傳，除非單條 flow 已完成足夠深掃且 Nick 明確要求。
