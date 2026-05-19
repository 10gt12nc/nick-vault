# daily-game-data-summary Claim Boundary

更新時間：2026-05-19
證據層級：真實開發過 + code-backed

## 目前結論

這條 flow 可保守作為 Nick 正式履歷成果。

原因：

- `game_job` daily summary 相關 path 有多筆 Nick / `10gt12nc` commits。
- `#247` commits 涵蓋每日遊戲資料彙總主體、select + insert、欄位 / 計算、備份 / 清理。
- 後續 commits 涵蓋新增玩家 / 留存、PG / Antplay 與 Iwin job 拆分、PG / Antplay 時區窗口修正。
- 這足以支撐「參與 / 開發 / 維護每日遊戲資料彙總 batch / BI projection」。
- 但不支撐「主導完整 BI pipeline」、「完整 game_job owner」、「負責上游 gameserver 到 app_bi 全鏈路」或「改善 X%」。

## 可以安全說

在履歷 / 面試情境：

- 我參與過一條遊戲每日資料彙總 batch projection。
- 這條 flow 從 `log_reel` 投注明細分表彙總到 BI projection。
- 我參與處理過資料日 / 時區窗口、delete + insert、retention、new player state、backup cleanup 等 batch correctness 邊界。
- 這條 flow 可用來練習 Senior Backend 對 batch consistency 的分析。

## 暫時不能說

正式履歷 / 自傳 / 面試成果敘述中，不能說：

- 我主導每日遊戲資料彙總。
- 我設計 game_job 批次架構。
- 我主導修正 PG / Antplay production 時區事故。
- 我負責遊戲資料 BI pipeline。
- 我建立 retention / new player 報表。
- 我改善查詢效能或資料正確率 X%。
- 我是這條 flow 的 owner。

## 需要補的 evidence

已足以升級成 `真實開發過`。若要升級成更強 claim，仍需要：

- 問題背景：為什麼要改。
- 具體責任：Nick 做了哪一段。
- 技術決策：Nick 如何取捨。
- 結果：正確率、延遲、報表穩定性、排查時間或維運成本的可驗證改善。
- production issue / ticket / MR review context。

## 可用說法

保守正式履歷：

> 參與 BI 批次報表資料流維護，協助檢查遊戲每日彙總 projection 的資料日邊界、重跑一致性與下游查詢正確性。

面試可講：

> 參與遊戲每日資料彙總批次維護，針對平台資料日、重跑邊界與報表 projection 一致性進行修正與驗證。

目前不能使用：

> 主導遊戲每日資料彙總批次的可靠性改善，收斂跨平台資料日定義、重跑安全與 BI projection 對帳流程。

## Evidence 標籤

| Claim | 目前標籤 | 原因 |
| --- | --- | --- |
| `game_job` 有每日遊戲資料彙總 job | 專案存在 / code-backed | code、mapper、config、commit history 可佐證 |
| PG / Antplay 時區曾修正 | 專案存在 / code-backed | path-specific commit 可佐證 |
| Iwin job 有 backup / cleanup | 專案存在 / code-backed | code、mapper、commit 可佐證 |
| Nick 參與此 flow | 真實開發過 | `10gt12nc` 有 daily summary path-specific commits |
| Nick 可把此成果放正式履歷 | 真實開發過 + code-backed | 可用參與 / 維護口徑，不寫主導 |
| 用此 flow 練習 batch correctness 面試 | 真實開發過 + 分析素材 | 可講實作經驗與 owner 分析，但改善建議需標分析素材 |

## 履歷 claim 分層（2026-05-18 KB 對齊）

- 可放履歷 evidence：真實開發過。Nick / `10gt12nc` 有 daily summary path-specific commits，可保守寫法為「參與每日遊戲資料彙總 batch / BI projection 開發與維護」；已由 `contribution-claim-consolidation.md` 收斂。
- 可面試講：code-backed / 實作過 + 分析過。可用 daily game data summary 說明 batch projection、delete-insert 重跑、一致性、時區分表、backup / cleanup 與報表正確性。
- 不可誇大：不得寫成 Nick 主導完整 game_job BI projection、完整資料平台 owner、負責上游 gameserver 到 app_bi 全鏈路或有未驗證量化改善。
