# third-party-transfer-in-out Interview Notes

更新時間：2026-05-15
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 30 秒摘要

我分析過 iwin gameserver 的第三方遊戲投派整合 flow。它的核心不是 HTTP routing，而是 provider callback 進來後，gameserver 如何改玩家錢包、回 response、送戰績 log 和打碼 log。最值得討論的是 transaction boundary：目前 wallet 成功後會先回 OK，後續 log 是 side effect，所以要特別處理 idempotency、retry、reconciliation。

## 2 分鐘說法草稿

這條 flow 是第三方遊戲投派整合。外部平台 callback 先進 upstream adapter，再轉成 gameserver 的 center_http command。gameserver 在 `slots-center` 解析 command 後查玩家，把 job 放進 per-account game pool，實際 job 修改 `PlayerData` 的餘額，然後回 HTTP OK，再通知在線玩家、送戰績 log、更新打碼紀錄。

我會把它的風險分成三類：第一是 wallet correctness，因為投注 / 派彩 / 退款會直接改玩家餘額；第二是 idempotency，因為 timeout 或 duplicate callback 可能重送；第三是 observability / reconciliation，因為 wallet update 和 log server 寫 `log_reel` 不是同一個 transaction。

Step 3 掃描時看到 `transactionId` 和 `betId` 都有被傳下去，但沒有在 gameserver wallet mutation 前看到明確防重查詢，所以這是 owner 角度要優先確認的點。

## 5 分鐘深講版

我會把這條 flow 拆成四個視角。

第一是入口轉換。第三方 provider callback 不直接動 gameserver wallet，而是由 `third_games_api` adapter 轉成 gameserver command。PG / Antplay 偏整合式 transfer in/out；GSC 則是 bet、settle、refund split path。

第二是 gameserver wallet。`HttpService` 解析 command 後，wrapper 先查玩家，再把 job 丟進 `CenterWorld.addGamePool(accountId, job)`。實際 money job 呼叫 `modifyCoin()`，再進 `PlayerData.modifyAndGetCoinPG/AP/GSC` 改玩家 `coins`，並推 currency log。

第三是 projection。錢包成功後，job 會通知在線玩家 / 子遊戲，並透過 `GamePlayer.sendReelToLog*` 送戰績資料到 `slots-game-log`。GSC / 舊 Antplay 有 bet insert、settle / refund update 的差異，PG / AP 投派整合比較像一般戰績。

第四是 owner 風險。這不是一個單 DB transaction；HTTP OK 在 wallet mutation 後送出，log 和打碼紀錄在後面。因此我會優先確認防重 key、重試 contract、out-of-order event、log 補償與 reconciliation，而不是只看 API 有沒有成功回傳。

## 常見追問

### 如果 gameserver 已扣款但 response timeout 怎麼辦？

保守回答：

> 要看防重在哪一層。如果 retry 以同一 `transactionId` 或 `betId` 進來，gameserver 應該在錢包變更前查是否已處理，然後回傳既有結果。只靠 log layer 的 serial id 不夠，因為 wallet 已經先改了。

### 如果 log_reel 寫失敗怎麼辦？

保守回答：

> 現有順序是 wallet 成功後先回 OK，log 是後續 side effect。所以需要 reconciliation 或 outbox。短期可用 provider statement、currency log、adapter request、log_reel 做差異檢查；長期可以把待送 log event 可靠落地，再由 worker retry。

### 為什麼不是把所有東西包在一個 DB transaction？

保守回答：

> 這條 flow 橫跨 gameserver runtime state、log server cache 批次寫入、online notify 與 adapter response，不是單一 DB 寫入。硬包一個 transaction 可能會讓 latency、可用性和模組耦合變差。比較實際的是定義 source of truth、防重 key、event/outbox 與補償策略。

### `betId` 和 `transactionId` 哪個該當 idempotency key？

保守回答：

> 不能靠猜，要看 provider spec。`betId` 常像一筆投注 / 局號，`transactionId` 可能是 provider transaction。應先確認唯一範圍與重送規則，再決定 key。若 bet / settle / refund 是不同事件，key 可能要包含 provider、event type、transaction id。

### 如果 settle 比 bet 早到怎麼辦？

保守回答：

> Step 3 沒確認 provider 是否可能 out-of-order，所以不能說系統已處理。owner 角度我會要求事件狀態機：bet 未存在時 settle 要暫存、拒絕並可重試，或建立 pending record。不能讓 settle update silently fail，否則 wallet、log_reel 和 provider 對帳會分岔。

### 如果 refund 重送怎麼辦？

保守回答：

> refund 本質上也是 money mutation，不能因為是補償就放鬆防重。應該用 refund transaction id 或原 bet id 加 refund type 做 idempotency guard。否則同一筆 refund 重送會重複加回玩家餘額。

### 如果玩家餘額不足，這算系統錯還是業務拒絕？

保守回答：

> 對 bet 來說餘額不足通常是業務拒絕，不應該進 wallet mutation。對 settle / refund 這類加錢事件，餘額不足不是問題。面試時我會把 operation type 分開看，不能用同一個 error semantics 處理所有事件。

### 如果要你改善第一版，你會先做什麼？

保守回答：

> 第一優先是 transaction idempotency，因為 duplicate callback 直接影響錢包。第二是明確 OK response contract。第三是 reconciliation dashboard 或報表，先能找出 wallet / log / provider 的差異。outbox 可以是下一階段，視現有 dbproxy / log server 架構成本決定。

## Senior 能力點

- 能從 HTTP 入口往下追到 wallet source of truth。
- 能區分 wallet state 與 log projection。
- 能指出 OK response 不等於 downstream 全部完成。
- 能把 duplicate callback、timeout retry、out-of-order event 轉成 idempotency / state machine 設計問題。
- 能保守標示未確認 evidence，不把 analysis 誇大成已實作成果。

## Lead / Architect 延伸追問

| 追問 | 應答方向 |
| --- | --- |
| 這條 flow 要不要抽成 wallet service？ | 先看現有 gameserver source of truth 與 latency / consistency 需求；不能只為抽象而拆服務 |
| outbox 放哪裡？ | 應靠近 wallet mutation 的可靠儲存；否則無法保證 wallet success 後 event 不丟 |
| 防重表 key 怎麼設？ | provider + event type + provider transaction id；必要時加 account / gameId，取決於 provider spec |
| log_reel update 找不到 bet 怎麼辦？ | 應有 pending / retry / reconciliation，不應 silent success |
| 如何驗證改善有效？ | duplicate callback test、timeout retry test、settle-before-bet test、wallet-log reconciliation report |

## 不能回答成這樣

- 「我做過這個 flow」：目前沒有本人 evidence。
- 「這套已經完全防重」：本輪沒有確認。
- 「我把一致性問題修好了」：沒有 commit / MR / production issue evidence。
- 「這是我主導的遊戲平台整合」：過度 claim。

## 練習提示

Nick 練習時可以先照這個順序講：

1. 先講這是 money flow，不是 Controller flow。
2. 再講三段：adapter、gameserver wallet、log server。
3. 只選一個核心風險深講：OK response after wallet mutation before log side effects。
4. 用 owner 語氣收斂：防重、reconciliation、outbox。
5. 最後補邊界：目前是 code-backed analysis，未宣稱本人主導。
