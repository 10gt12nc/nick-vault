# third-party-transfer-in-out：保守面試素材

更新時間：2026-05-15
對應 Step：Step 3 初版
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 一句話版本

我分析過 iwin gameserver 的第三方遊戲投派整合 flow，重點不是只看 HTTP 入口，而是追投注 / 派彩 / 退款如何進入 gameserver、如何改玩家錢包、如何送戰績與打碼 log，以及中間有哪些 consistency 和 idempotency 風險。

## 可講的技術重點

- 這條 flow 橫跨 `slots-center`、`slots-games/slots-game-common`、`slots-game-log`，不是單一 Controller。
- gameserver 以 `HttpService` 接收 `PGTRANSFERINOUT`、`ANTPLAYTRANSFERINOUT`、`GSC_BET`、`GSC_SETTLE`、`GSC_REFUND` 等 command。
- 實際錢包變更在 `PlayerData.modifyAndGetCoinPG/AP/GSC`，再送 currency log、在線通知、戰績 log 與打碼 log。
- 目前 Step 3 看到的順序是錢包成功後先回 HTTP OK，後續才送戰績與打碼 log，因此有 wallet/log consistency window。
- `transactionId` / `betId` 有被帶進 log，但 gameserver 端防重未確認，是面試時可以延伸討論的 owner 風險。

## 面試回答草稿

如果被問「你會怎麼看第三方遊戲投派 flow？」可以回答：

> 我會先把它切成 adapter、gameserver wallet、log server 三段。adapter 負責把第三方 callback 轉成 gameserver command；gameserver 的核心是確認玩家、進 per-account queue、修改 wallet；log server 則處理戰績與打碼資料。真正的風險不是 HTTP parse，而是 wallet update、HTTP response、log write 不是同一個 transaction，所以要追防重 key、重試語意、reconciliation 與 observability。

如果被問「這裡最容易出什麼問題？」可以回答：

> 最大問題是 timeout / retry 和 duplicate callback。如果 gameserver 已經改了玩家餘額，但 response 或 log 後續失敗，上游可能 retry。若錢包變更前沒有用 `transactionId` 或 `betId` 做 idempotency guard，就可能 double apply。就算 log 層有 serial id，也不能直接保證 wallet 層安全。

## 目前不能講

- 不能說我主導這條 flow。
- 不能說我修過這條 production bug。
- 不能說已確認防重完整。
- 不能說有改善百分比或 owner 權限。

## Step 4 待補

- 收斂成 2 分鐘版本與 5 分鐘版本。
- 補常見追問：timeout、duplicate callback、settle 早於 bet、refund 重送、log 補償。
- 將 `flow.md` 的 owner decision 轉成面試可講語氣。
