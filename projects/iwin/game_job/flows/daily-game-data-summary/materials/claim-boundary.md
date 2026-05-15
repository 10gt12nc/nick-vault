# daily-game-data-summary Claim Boundary

更新時間：2026-05-15
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 目前結論

這條 flow 目前只能作為 code-backed 分析素材，不可作為 Nick 正式履歷成果。

原因：

- 沒有 Nick 本人 MR。
- 沒有 Nick 本人 ticket。
- 沒有 Nick 本人 commit attribution。
- 沒有 production issue 佐證 Nick 參與排查。
- 沒有 Nick 本人確認。

## 可以安全說

在學習 / 面試分析情境：

- 我分析過一條遊戲每日資料彙總 batch projection。
- 這條 flow 從 `log_reel` 投注明細分表彙總到 BI projection。
- 我會從 time window、delete + insert、retention、new player state、backup cleanup 角度檢查 correctness。
- 這條 flow 可用來練習 Senior Backend 對 batch consistency 的分析。

## 暫時不能說

正式履歷 / 自傳 / 面試成果敘述中，不能說：

- 我主導每日遊戲資料彙總。
- 我設計 game_job 批次架構。
- 我修正 PG / Antplay 時區問題。
- 我負責遊戲資料 BI pipeline。
- 我建立 retention / new player 報表。
- 我改善查詢效能或資料正確率 X%。
- 我是這條 flow 的 owner。

## 需要補的 evidence

要升級成 `真實開發過`，至少需要其中一類：

- Nick 本人 MR / PR。
- Nick 本人 commit。
- Nick 對應 ticket 或 issue。
- Nick 本人 production incident / debug 記錄。
- Nick 口頭或文字確認具體參與範圍。

要寫成較強履歷 claim，還需要：

- 問題背景：為什麼要改。
- 具體責任：Nick 做了哪一段。
- 技術決策：Nick 如何取捨。
- 結果：正確率、延遲、報表穩定性、排查時間或維運成本的可驗證改善。

## 未來可升級說法

若只補到「Nick 參與排查 / 維護」：

> 參與 BI 批次報表資料流維護，協助檢查遊戲每日彙總 projection 的資料日邊界、重跑一致性與下游查詢正確性。

若補到「Nick 實作部分修正」：

> 參與遊戲每日資料彙總批次維護，針對平台資料日、重跑邊界與報表 projection 一致性進行修正與驗證。

若補到「Nick 主導」且有明確 evidence：

> 主導遊戲每日資料彙總批次的可靠性改善，收斂跨平台資料日定義、重跑安全與 BI projection 對帳流程。

最後一句目前不能使用。

## Evidence 標籤

| Claim | 目前標籤 | 原因 |
| --- | --- | --- |
| `game_job` 有每日遊戲資料彙總 job | 專案存在 / code-backed | code、mapper、config、commit history 可佐證 |
| PG / Antplay 時區曾修正 | 專案存在 / code-backed | path-specific commit 可佐證 |
| Iwin job 有 backup / cleanup | 專案存在 / code-backed | code、mapper、commit 可佐證 |
| Nick 參與此 flow | 待確認 | 缺本人 evidence |
| Nick 可把此成果放正式履歷 | 待確認 | 需本人參與與結果 evidence |
| 用此 flow 練習 batch correctness 面試 | 分析素材 / learning-only | 可作學習，不等同本人貢獻 |
