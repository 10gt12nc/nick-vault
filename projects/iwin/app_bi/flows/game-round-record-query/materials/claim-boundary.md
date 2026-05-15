# game-round-record-query - Claim Boundary

更新時間：2026-05-15
完成狀態：Step 5 已完成
證據層級：app_bi 查詢端為專案存在 / code-backed；iwin_gameserver writer 有 Nick commit 線索；正式履歷不更新

## 可說

可在學習 / 面試分析中說：

- 我分析過 `app_bi` 的遊戲局紀錄查詢 flow。
- 我知道後台如何依玩家 ID、牌局號、`serial_id` 與日期查 `log_reel` 每日分表。
- 我能把 app_bi 查詢端往下追到 iwin_gameserver 的 log writer 線索。
- 我能說明這類查詢在玩家申訴排查中的價值與限制。
- 我能指出 `log_reel` 是 troubleshooting projection，不等於完整 wallet / provider truth。

## 只能保守說

如果面試官問是否做過，只能保守回答：

> 我目前把它當作專案 flow 分析素材。從 code 來看，這是 app_bi 的後台查詢入口，並且可以追到 iwin_gameserver 的戰績 log 寫入線索。若要放進正式履歷或說成我實際負責，還需要補 Nick 本人 MR、ticket、commit、production issue 或本人確認。

## 不能說

不能說：

- 我主導遊戲局紀錄查詢。
- 我負責玩家申訴系統。
- 我建立 `log_reel` pipeline。
- 我設計遊戲結算 / wallet correctness。
- 我改善過查詢效能。
- 我修過 `serial_id` update 或 Antplay / GSC log bug。

## 可進履歷條件

以下任一條補到 evidence 後，才可能考慮寫入正式履歷：

- Nick 本人 MR / commit 與本 flow 直接相關。
- Nick 本人 ticket / issue 與玩家申訴、戰績查詢、log 修正直接相關。
- Nick 本人處理過 production incident，且可保守描述。
- Nick 明確確認自己維護過此 flow。

即使補到 evidence，也要保守寫，不用「主導」「owner」「架構師」這類字。

## Step 4 面試邊界補充

Step 4 已轉成面試 case，但仍不改變 claim 層級：

- 面試可說「我分析過 / 我會這樣排查」。
- 不說「我實際主導 / 我實際修過」。
- 不說「我設計完整玩家申訴系統」。
- 不說「我負責 wallet correctness」。

## Step 5 判定

正式履歷 / 自傳不更新。

原因：

- `app_bi` 查詢端 path-specific log 未看到 Nick / `10gt12nc` author。
- `iwin_gameserver` log writer / Antplay-GSC 戰績相關 path 有 `10gt12nc` commit 線索，但這屬於後端 writer / provider integration evidence，不應混入 `app_bi` 查詢頁 claim。
- 本 flow 可作面試分析素材；若要升級履歷，應另做 `iwin_gameserver` / provider log writer flow，並追重要 commit diff、ticket / issue 與 production context。

下一步不再做本 flow；回到更高價值後端 flow。
