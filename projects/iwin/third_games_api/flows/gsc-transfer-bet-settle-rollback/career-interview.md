# GSC transfer bet / settle / rollback Career Interview

證據層級：`分析素材 / learning-only`、`專案存在 / code-backed`。

目前沒有 Nick 本人參與 evidence，因此本文件只提供保守面試素材，不直接更新正式履歷 master 或自傳。

## 30 秒說法

我追過一條第三方遊戲 seamless wallet transfer flow：provider callback 進 `third_games_api` 後，adapter 會驗簽、檢查玩家與幣別、用 Redis 找 gameserver routing，再呼叫 gameserver 做實際錢包異動，最後把 callback 與 transaction evidence 寫進 Mongo。

這條 flow 的重點不是 controller 本身，而是外部 provider retry、內部 wallet source of truth、Mongo audit、rollback 語意與 idempotency 的邊界。尤其 `ROLLBACK` 分支目前 code 顯示沒有呼叫 gameserver mutation，只回算 balance 並寫 Mongo，所以正式講法必須保守，不能把它說成完整補償交易。

## 3 分鐘說法

這個 flow 我會從 transaction boundary 切入。`third_games_api` 是 provider adapter，不是錢包 source of truth。它接 GSC `/api/seamless/transfer`，做 request validation、signature check、currency check、player lookup，然後解析 transaction 內容，計算本次 `addMoney`。非 rollback 的情境會透過 Redis cached platform config 找到 gameserver `center_http`，送 `PGTRANSFERINOUT` command 給 gameserver。

gameserver 端再由 `HttpService` dispatch 到 `HttpPGTransferInOut` 與 `PGTransferInOutJob`。真正餘額異動發生在 gameserver job 內，會檢查玩家與餘額，再呼叫 player wallet method，之後回 HTTP OK，並推 game coin log、reel log、bet log。

我認為這條 flow 的 owner 風險有三個：第一，provider retry 時要有以 `transactionId` / `betId` 為核心的冪等，不應只靠 adapter 的 Mongo。第二，gameserver wallet 成功但 adapter Mongo insert 失敗會造成外部 response、內部錢包、audit evidence 的不一致窗口。第三，`ROLLBACK` 在目前 code 裡不是實際 wallet rollback，這需要 provider spec 或 production evidence 再確認，不然很容易在面試或文件裡誇大。

## 可用履歷素材草稿

以下只能在 Nick 確認本人參與後再改成正式履歷句；目前不建議直接貼到 resume：

- 分析第三方遊戲 seamless wallet transfer flow，釐清 provider callback、gameserver wallet mutation、Mongo audit 與 retry / idempotency 邊界。
- 盤點 GSC transfer callback 的 money correctness 風險，包含 rollback 語意、gameserver 成功但 audit 寫入失敗、provider 重送與報表投影一致性。

更保守、可放在面試自我準備而非正式履歷的說法：

- 曾針對第三方遊戲下注派彩 callback 做 code-level 追蹤，能說明 adapter 層與 wallet 層的責任切分，以及 owner 要如何設計對帳與補償。

## 可說

- `third_games_api` 在這條 flow 裡是 adapter / orchestration 層。
- gameserver 才是 wallet mutation 的主要落點。
- Mongo evidence 對 audit / reconciliation 有價值，但不等於已確認的唯一帳本。
- `ROLLBACK` 分支目前 code-backed 行為是「不呼叫 gameserver mutation」。
- provider retry 安全性要追到底層 wallet mutation 是否冪等。

## 不可說

- 不可說 Nick 主導 GSC 串接。
- 不可說 Nick 設計或修復此 flow。
- 不可說此 flow 已經 exactly-once。
- 不可說 rollback 已完整補償 wallet。
- 不可說 Mongo insert 與 gameserver wallet mutation 是同一個 transaction。

## 面試提醒

被問到「那你怎麼修」時，建議回答方向：

1. 先定義 source of truth：wallet ledger 在 gameserver。
2. 再定義 idempotency key：provider transaction id / wager code。
3. 把 external response 與 internal projection 拆開：wallet mutation 成功後若 audit 寫入失敗，要有 repair / reconciliation，不應靠 provider 盲目重送。
4. 對 rollback 語意先查 spec，再決定是 reversal transaction、query response，或特殊 validation branch。
