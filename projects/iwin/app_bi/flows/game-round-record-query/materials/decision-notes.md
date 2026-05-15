# game-round-record-query - Decision Notes

更新時間：2026-05-15
完成狀態：Step 4 已完成
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 1. 查詢 log 不等於交易 truth

本 flow 最大的硬底子是：要分清楚 troubleshooting log、projection 與 source of truth。

`app_bi` 查的是 `log_reel_{date}`。這張表對排查很重要，但它不是完整 wallet ledger。面試或 production 排查時，不能只憑一筆戰績 log 就判定玩家餘額一定正確。

更穩的排查順序：

```text
玩家申訴
-> 查 log_reel 戰績
-> 對 currency / wallet log
-> 對 provider transaction / callback
-> 對遊戲結算狀態
-> 判斷是顯示問題、log 延遲、settle update miss、還是交易 truth 問題
```

## 2. 每日分表的 trade-off

優點：

- 寫入資料量大時，依日期拆表比較容易保留與清理。
- 查近日期資料時，掃描範圍可控。
- 批次 job 與維護工具容易按日期操作。

代價：

- 查詢端要 loop 多張表。
- 跨日排序與分頁複雜。
- 高頁數查詢可能變慢。
- 日期欄位與 table date 不一致時容易漏查。

本 flow 的 app_bi 查詢端採用逐日查表、收集資料、PHP 切分頁。這對後台排查可用，但不是高併發查詢模型。

## 3. `serial_id` update 模型

Antplay / GSC 類 flow evidence 顯示：

```text
bet -> batchInsertLogReel
settle / refund -> batchUpdateLogReel by serial_id
```

這代表 `serial_id` 是很關鍵的 idempotency / correlation key。

Owner 追問：

- `serial_id` 是否全域唯一？
- 是否要加 game id / channel / date 條件？
- update 找不到 row 時怎麼發現？
- bet insert 重複時怎麼處理？
- settle 早於 bet 到達時怎麼處理？

目前只能標為待確認，不直接判定 bug。

## 4. 查詢條件與效能

已確認 app_bi 查詢條件含：

```text
date_format(game_start_time, '%Y-%m-%d %H:%i:%s') BETWEEN startTime AND endTime
```

風險：

- 對 indexed column 包 function，可能讓 index 使用變差。
- 長時間範圍會逐日掃多張表。
- 沒玩家 ID 時預設 log index，可能查不到其他 channel。

可改善方向：

- 時間條件改成直接 range compare。
- 限制最大日期區間。
- 查 `serial_id` / `roundid` 時要求 channel 或 player id。
- 若要跨 channel 查，應明確做跨 shard search，而不是暗中預設。

## 5. 面試可問的技術點

Senior：

- 你怎麼判斷一筆玩家申訴是 log 查不到、交易沒發生、還是資料延遲？
- 如果 `serial_id` update miss，要怎麼發現與補償？
- 每日分表查詢慢，你會怎麼改善？
- 這種後台查詢可以當 source of truth 嗎？

Lead / Architect：

- 戰績 log、wallet ledger、provider transaction 三者的資料契約怎麼定？
- 如何設計 replay / reconciliation？
- 如何讓客服查詢不壓垮 log DB？
- 如何設計 observability，讓 log pipeline failure 可以被及時發現？
