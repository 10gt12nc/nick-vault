# daily-game-data-summary Career / Interview Notes

更新時間：2026-05-15
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 使用邊界

這份文件只整理面試可用的保守說法。沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認前，不能寫成正式履歷成果，也不能說「我主導」、「我設計」、「我修正」。

可用定位：

- code-backed 的 batch projection 分析案例。
- Senior Backend / Platform Backend 面試中，用來說明如何看資料一致性、重跑安全與時區邊界。
- 若面試官追問「你會怎麼檢查一條報表批次」，可以用本 flow 的結構回答，但要避免暗示本人實作。

## 30 秒說法

我研究過一條遊戲每日資料彙總批次。它從投注 log 日分表彙總玩家每日投注、贏分、新增玩家與留存，寫入 BI 查詢用 projection。這類 flow 的重點不是單純 SQL group by，而是資料日定義、跨時區窗口、重跑刪除邊界、輔助狀態像新增玩家表是否與主 projection 一致，以及下游報表是否可能讀到半成品。

## 3 分鐘說法

這條 flow 有兩組 job：PG / Antplay 一組，Iwin 一組。PG / Antplay 因為平台資料日與時區不同，會查當日與前一日的 `log_reel` 日分表，依 `platform:uid` 合併投注次數、投注額與贏分；Iwin 則以自己的資料日跑彙總。

批次先刪除指定日期與平台的 `log_game_daily_record`，再寫入 type=0 personal data，接著從 type=0 彙總 type=1 summary，最後計算新增玩家與留存。下游 `app_bi` 查 type=1，把不同 `sub_type` pivot 成報表欄位。

Senior 角度我會先看幾個風險：第一，delete + insert 重跑會有短暫空窗，下游是否可能讀到不完整資料；第二，新增玩家表是跨日累積狀態，重跑舊日期時不一定和主 projection 同步；第三，PG / Antplay 時區窗口曾有修正紀錄，代表資料日邊界很敏感；第四，Iwin 的 backup insert 與 delete 如果不是同一 transaction，需要 rows count reconciliation 或 duplicate 防護。

## 可安全使用的技術點

- Batch projection：原始 log 與 BI 查詢 projection 分離。
- Replay / re-run：主表用 delete + insert 重建指定日期資料。
- Timezone boundary：PG / Antplay 有不同查詢窗口，且 path history 有時區修正 commit。
- Retention calculation：以 type=0 uid set 計算新增玩家與 N 日留存。
- Reporting consistency：下游只查 type=1 summary，需注意 type=0 / type=1 產生順序與半成品風險。
- Data retention：Iwin job 對舊 personal / summary 做 backup 後清理。

## 不能安全使用的說法

- 我主導每日遊戲資料彙總。
- 我設計 game_job BI projection 架構。
- 我修正 PG / Antplay 時區 bug。
- 我負責 app_bi 每日遊戲資料報表。
- 我保證這條 flow production 完全啟用且沒有一致性問題。

## 面試問答

### Q：這條批次的 source of truth 是什麼？

A：對這條 flow 而言，主要輸入是 `slots_logv1.log_reel_{yyyy_m_d}` 這類投注明細日分表；`log_game_daily_record` 是 BI projection，不應倒過來當成交易 source of truth。

### Q：如果 job 跑到一半失敗怎麼辦？

A：主表採 delete + insert，若刪除後失敗，下游可能看到缺資料；若 type=0 已寫但 type=1 失敗，下游 summary 也可能不完整。比較穩的做法是 staging table 或完成標記，讓 BI 只讀已完成資料日。

### Q：重跑是否 idempotent？

A：主 projection 大致可透過刪除指定日期 / 平台後重建來達成重跑，但新增玩家表與 backup 表是累積狀態，不能直接假設完全 idempotent。這兩塊需要額外檢查 unique key、transaction、rows count 與補償流程。

### Q：為什麼時區是風險？

A：PG / Antplay 不是單純查自然日，而是不同平台有不同資料窗口，且窗口會跨前一日分表。只要起訖時間錯一小時，就可能造成某些投注被算到錯誤資料日，進而影響投注額、輸贏與留存。

### Q：你會怎麼改善？

A：我會優先加完成標記與每步 rows count，讓下游只讀完成資料日；再把平台資料日窗口集中成可測試配置；針對新增玩家表與 backup 流程補 transaction 或 reconciliation；最後建立重跑 runbook，明確說明重跑前後要比對哪些 count 與金額。

## 履歷使用建議

目前不建議寫入正式履歷。

若未來補到 Nick 本人 evidence，可考慮保守寫成：

> 參與 BI 批次報表資料流維護，協助檢查遊戲每日彙總 projection 的資料日邊界、重跑一致性與下游報表查詢正確性。

這句仍需要 Nick 本人參與 evidence 才能使用。
