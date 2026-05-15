# game-round-record-query - Interview Notes

更新時間：2026-05-15
完成狀態：Step 3 初版；Step 4 尚未完成
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 30 秒版

我分析過遊戲局紀錄查詢 flow。這類後台功能主要用於玩家申訴與 production troubleshooting，會依玩家 ID、牌局號、第三方交易單號與日期區間查 `log_reel` 每日分表，再把 `common_data` 解析成遊戲可讀紀錄。重點是它是戰績 log / projection，不是完整交易 truth；真正判斷金額正確性還要對 wallet、currency log、provider record 與 settlement flow。

## 目前可答的 Senior 追問

### 查不到局怎麼辦？

先不直接判定玩家沒玩。要排除：

- 查詢日期是否跨日。
- `game_start_time` 與 table date 是否一致。
- 玩家 channel / log DB index 是否選對。
- 是否只用 `serial_id` 或 `roundid` 導致查預設 log index。
- writer 是否有延遲或失敗。

### 查到局但金額不對怎麼辦？

先區分：

- `spin_currency`：下注。
- `win_currency`：派彩。
- `curr_currency` / `last_user_currency`：前後餘額。
- `common_data`：遊戲顯示細節。

再對照 wallet / currency log / provider transaction，不把 `log_reel` 當唯一真相。

### 為什麼 `serial_id` 很重要？

第三方遊戲通常會把 provider transaction / bet id 放到 `serial_id`。這讓後台可以用外部單號查內部戰績，也讓 bet / settle / refund 可以關聯同一筆交易。

風險是：如果 `serial_id` 不是全域唯一，update 或查詢就應該加更多限定條件，例如 game id、channel 或日期。

## Step 4 待補

Step 4 需要把這份初版整理成完整面試 case：

- 3 分鐘版。
- STAR / production issue 版，但不能說成真實發生。
- Senior / Lead / Architect 追問與答法。
- 不可誇大的回覆邊界。

下一步：

```text
app_bi game-round-record-query Step 4
```
