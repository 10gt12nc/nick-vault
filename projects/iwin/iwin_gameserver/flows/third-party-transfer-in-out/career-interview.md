# third-party-transfer-in-out：保守面試素材

更新時間：2026-05-15
對應 Step：Step 4 面試案例
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 面試定位

這份不是正式履歷，也不是 Nick 已實作 claim。它是 code-backed 的 Senior / Owner 面試分析素材：用一條真實存在的 gameserver money flow，練習怎麼談 transaction boundary、idempotency、retry / compensation、reconciliation 與 observability。

可用語氣：

- 「我分析過一條第三方遊戲投派整合 flow。」
- 「我會把風險拆成 wallet correctness、idempotency、log consistency。」
- 「如果我是 owner，我會優先補防重與 reconciliation。」

避免語氣：

- 「我主導了這條 flow。」
- 「我修好了重複扣款。」
- 「我設計了整套遊戲錢包架構。」

## 30 秒版本

我分析過 iwin gameserver 的第三方遊戲投派整合 flow，重點不是只看 HTTP 入口，而是追投注 / 派彩 / 退款如何進入 gameserver、如何改玩家錢包、如何送戰績與打碼 log，以及中間有哪些 consistency 和 idempotency 風險。

這條 flow 最值得談的是：錢包成功後會先回 HTTP OK，後續才送戰績與打碼 log，所以要定義 OK 語意、防重 key、重試與對帳補償。

## 2 分鐘版本

這條 flow 是第三方遊戲投注 / 派彩 / 退款進 gameserver 的 runtime money flow。上游 adapter 先把 provider callback 轉成 gameserver command，例如 `PGTRANSFERINOUT`、`ANTPLAYTRANSFERINOUT`、`GSC_BET`、`GSC_SETTLE`、`GSC_REFUND`。gameserver 在 `slots-center` 解析 command，查玩家資料，再把實際 money job 放進以 account 為 key 的 game pool。

job 裡的核心是 `modifyCoin()`，它會呼叫 `PlayerData.modifyAndGetCoinPG/AP/GSC` 去更新玩家餘額，並推 currency log。成功後 job 會先回 HTTP OK，接著才做在線玩家通知、戰績 log、打碼 log。這代表整條 flow 不是單一 ACID transaction，而是 wallet mutation 加上一串 downstream side effect。

所以我會把風險拆成三類：第一是 wallet correctness，不能重複扣加；第二是 idempotency，timeout 或 duplicate callback 要用 `transactionId` / `betId` 在 wallet mutation 前防重；第三是 reconciliation，因為 log server 批次 insert / update `log_reel`，和 gameserver wallet update 不在同一個 transaction。

## 5 分鐘版本

我會先把系統切成三段：upstream adapter、gameserver wallet、log server。

Adapter 的責任是把不同 provider 的 callback 轉成 gameserver command。PG / Antplay 比較像投派整合，一筆 command 帶 `addMoney`、`betCoin`、`validBetCoin`、`spinCurrency`、`transactionId`、`betId`；GSC 則比較偏 split path，有 bet、settle、refund 不同 command。

Gameserver 的責任是處理 source of truth。`HttpService` 收到 command 後，不應該只看成一個 HTTP API，因為真正的狀態變更在 `PlayerData`。wrapper job 先查玩家，再放入 `CenterWorld.addGamePool(accountId, job)`。實際 job 檢查玩家狀態與餘額，接著呼叫 `modifyAndGetCoin*` 修改 `coins`，同步更新玩家投注 / 派彩統計，並推 currency log。

Log server 的責任是戰績與報表 projection。`GamePlayer.sendReelToLog*` 會組 `LogReelData`，包含目前餘額、下注、派彩、上一筆餘額與 `serial_id`。GSC / 舊 Antplay 的 bet 會 insert，settle / refund 會 update；PG / AP 投派整合多走一般 `REEL_NORMAL`。

我看到的 owner 風險是 response ordering。現有 Step 3 掃描確認：wallet mutation 成功後就回 HTTP OK，戰績 / 打碼 log 是後面的 side effect。這不一定錯，但 contract 要講清楚：OK 是代表 wallet applied，不是代表所有 downstream projection 都完成。否則上游 timeout retry、log server 延遲、settle update 失敗，都可能造成查帳困難。

如果要改善，我不會只加 log。第一步會確認 provider 的唯一鍵語意，決定 `provider + event type + transactionId/betId` 的 idempotency key，且 guard 必須在 wallet mutation 前。第二步會建立 reconciliation，至少能比對 provider statement、adapter request、currency log、log_reel。第三步才評估 outbox，把 wallet success 後要送的 log event 可靠落地，避免 OK 後 side effect 靠記憶體流程硬撐。

## 可講的技術重點

- 這條 flow 橫跨 `slots-center`、`slots-games/slots-game-common`、`slots-game-log`，不是單一 Controller。
- gameserver 以 `HttpService` 接收 `PGTRANSFERINOUT`、`ANTPLAYTRANSFERINOUT`、`GSC_BET`、`GSC_SETTLE`、`GSC_REFUND` 等 command。
- 實際錢包變更在 `PlayerData.modifyAndGetCoinPG/AP/GSC`，再送 currency log、在線通知、戰績 log 與打碼 log。
- 目前 Step 3 看到的順序是錢包成功後先回 HTTP OK，後續才送戰績與打碼 log，因此有 wallet/log consistency window。
- `transactionId` / `betId` 有被帶進 log，但 gameserver 端防重未確認，是面試時可以延伸討論的 owner 風險。

## STAR 版本

Situation：

- 第三方遊戲投注 / 派彩 / 退款會影響玩家餘額與報表，錯誤會造成玩家錢包、戰績、打碼統計不一致。

Task：

- 不是只看 HTTP 入口，而是追 gameserver 內部 wallet mutation、log dispatch、response ordering 與補償邊界。

Action：

- 先從 `HttpService` command routing 定位入口。
- 再追 wrapper 查玩家與 `CenterWorld.addGamePool(accountId, job)`。
- 追 `*TransferInOutJob.sendMoneyChange2Center()` 的實際順序。
- 對照 `PlayerData.modifyAndGetCoin*`、`GamePlayer.sendReelToLog*` 與 `slots-game-log` insert / update job。
- 最後整理 idempotency、failure window、reconciliation 與不可誇大的 claim boundary。

Result：

- 形成一個可面試討論的 owner analysis case：OK response 語意、wallet 防重、log projection 補償與 reconciliation 是核心風險。
- 目前仍只屬於 `專案存在 / code-backed` 與 `分析素材 / learning-only`，不能寫成 Nick 主導成果。

## 面試回答草稿

如果被問「你會怎麼看第三方遊戲投派 flow？」可以回答：

> 我會先把它切成 adapter、gameserver wallet、log server 三段。adapter 負責把第三方 callback 轉成 gameserver command；gameserver 的核心是確認玩家、進 per-account queue、修改 wallet；log server 則處理戰績與打碼資料。真正的風險不是 HTTP parse，而是 wallet update、HTTP response、log write 不是同一個 transaction，所以要追防重 key、重試語意、reconciliation 與 observability。

如果被問「這裡最容易出什麼問題？」可以回答：

> 最大問題是 timeout / retry 和 duplicate callback。如果 gameserver 已經改了玩家餘額，但 response 或 log 後續失敗，上游可能 retry。若錢包變更前沒有用 `transactionId` 或 `betId` 做 idempotency guard，就可能 double apply。就算 log 層有 serial id，也不能直接保證 wallet 層安全。

如果被問「你會怎麼設計 idempotency？」可以回答：

> 我會先確認 provider spec 裡哪個欄位是全域唯一，不能只看欄位名。實作上會在 wallet mutation 前建立 processed transaction record，key 至少包含 provider、event type 和 provider transaction id。retry 時如果 key 已存在，就回既有結果；如果先改 wallet 再補紀錄，中間 crash 還是會留下 double apply 風險。

如果被問「為什麼要 reconciliation？」可以回答：

> 因為這條 flow 橫跨 wallet runtime state、currency log、reel log、bet record，且 log 是 downstream projection。即使每段都有 log，沒有 reconciliation 就很難回答哪筆 provider transaction 已完成、哪筆只有 wallet applied、哪筆 log 缺失。對 money flow 來說，能查出差異和能補償跟即時寫成功一樣重要。

## 反問面試官

可以用這些問題展現 owner 視角：

- 你們第三方 provider callback 的 retry contract 是 at-least-once 還是 exactly-once by id？
- wallet mutation 的 idempotency guard 放在應用層、DB unique key，還是 ledger service？
- HTTP OK 代表 wallet applied，還是所有 downstream projection 都完成？
- log / report 不一致時，是否有 daily reconciliation 或人工補單工具？
- provider bet / settle / refund 是否可能 out-of-order？

## 目前不能講

- 不能說我主導這條 flow。
- 不能說我修過這條 production bug。
- 不能說已確認防重完整。
- 不能說有改善百分比或 owner 權限。

## Step 5 待補

- 判斷是否能形成履歷 bullet。
- 若沒有 Nick 本人 evidence，正式履歷應暫不放，或只保留「分析 / 熟悉」類面試素材。
- 若 Nick 補 MR / ticket / commit / production issue，再重新評估是否能標成 `真實開發過`。
