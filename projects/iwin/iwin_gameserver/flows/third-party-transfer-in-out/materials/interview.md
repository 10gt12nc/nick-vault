# third-party-transfer-in-out Interview Notes

更新時間：2026-05-15
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 2 分鐘說法草稿

這條 flow 是第三方遊戲投派整合。外部平台 callback 先進 upstream adapter，再轉成 gameserver 的 center_http command。gameserver 在 `slots-center` 解析 command 後查玩家，把 job 放進 per-account game pool，實際 job 修改 `PlayerData` 的餘額，然後回 HTTP OK，再通知在線玩家、送戰績 log、更新打碼紀錄。

我會把它的風險分成三類：第一是 wallet correctness，因為投注 / 派彩 / 退款會直接改玩家餘額；第二是 idempotency，因為 timeout 或 duplicate callback 可能重送；第三是 observability / reconciliation，因為 wallet update 和 log server 寫 `log_reel` 不是同一個 transaction。

Step 3 掃描時看到 `transactionId` 和 `betId` 都有被傳下去，但沒有在 gameserver wallet mutation 前看到明確防重查詢，所以這是 owner 角度要優先確認的點。

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

## 不能回答成這樣

- 「我做過這個 flow」：目前沒有本人 evidence。
- 「這套已經完全防重」：本輪沒有確認。
- 「我把一致性問題修好了」：沒有 commit / MR / production issue evidence。
- 「這是我主導的遊戲平台整合」：過度 claim。
