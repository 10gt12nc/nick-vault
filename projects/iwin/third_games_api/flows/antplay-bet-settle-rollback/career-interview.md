# Antplay bet / settle / rollback Career Interview

## 定位

完成狀態：Step 5 已完成，單條 flow claim gate 已收斂；`gsc-seamless-withdraw-deposit-cancel` 也已完成 Step 5，下一步回必做收口 `k3s-deploy gameserver-phased-rollout Step 5`。

證據層級：`專案存在 / code-backed`、`分析素材 / learning-only`。目前不更新正式履歷 / 自傳。

## 面試主軸

這條 flow 可以包成「第三方遊戲舊三段式 bet / settle / rollback 的 state transition 與 wallet idempotency boundary」。

它最適合拿來展示三件事：

- 你能把 provider adapter 和真正 wallet source of truth 分開。
- 你能看出 `bet -> settle / rollback` 的前置狀態與 retry 風險。
- 你不會把 Mongo audit / transaction evidence 誤講成已經 money-safe。

## 30 秒說法

```text
我分析過 Antplay 舊版三段式 bet / settle / rollback flow。third_games_api 會做 MD5 驗簽、Redis game / center routing，然後分別送 gameserver 的 ANTPLAY_BET、ANTPLAY_SETTLE、ANTPLAY_REFUND。真正改錢在 gameserver，adapter Mongo 只是 step evidence。這條 flow 的重點是 gameserver 成功但 Mongo 沒寫時，provider retry 可能造成重複扣款、派彩或退款，所以 idempotency guard 應該靠近 wallet mutation boundary。
```

## 90 秒說法

```text
Antplay 舊 flow 是三個 endpoint：bet 扣款、settle 派彩、rollback 退款。Adapter 先驗 MD5 sign，再用 Redis 找 provider game 對應的 internal gameId 和玩家 center 對應的 center_http。bet 會先查餘額，再送 ANTPLAY_BET；settle / rollback 會先查 Mongo 是否已有 step 1，再回查原始 bet，分別送 ANTPLAY_SETTLE 或 ANTPLAY_REFUND。

我會特別把責任邊界拆開：third_games_api 是 adapter，Mongo third_log / third_transaction 是 audit / evidence；真正的 money commit 在 iwin_gameserver 的 AntplaySettledJob 和 modifyAndGetCoinAntplay。

風險在寫入順序。adapter 是 gameserver 成功後才寫 Mongo。如果 gameserver 已改錢但 Mongo insert 失敗或 adapter timeout，下次 provider retry 查不到 step evidence，可能再次送 gameserver。這種 flow 的改善方向不是只補更多 log，而是要讓 wallet mutation boundary 有 provider + betId + action 的 idempotency guard，或先落 durable request state。
```

## 3 分鐘說法

Antplay 舊版對接是典型三段式 seamless wallet flow。Provider 不會一次送完整投注結果，而是分成 `/antplay/bet`、`/antplay/settle`、`/antplay/rollback` 三個 API。

`bet` 進來時，adapter 會驗 `merchantId + account + game + betId + bet + timestamp + key` 的 MD5 sign，將 bet 金額乘內部倍率，透過 Redis 找 internal game id，再查玩家餘額。如果餘額足夠，就送 gameserver `ANTPLAY_BET` 扣款。gameserver 成功後，adapter 才寫 `third_log_antplay` type 3 與 `third_transaction_antplay` step 1。

`settle` 和 `rollback` 都依賴這個 step 1。`settle` 會驗包含 win fields 的 sign，查 step 1 存在後，從 Mongo 讀原始 bet 和 betTime，再送 `ANTPLAY_SETTLE` 派彩。`rollback` 會驗不含金額的 sign，同樣查 step 1 和原始 bet，再送 `ANTPLAY_REFUND` 退款。gameserver 裡面會轉成 `AntplaySettledJob`，最後呼叫 `modifyAndGetCoinAntplay` 修改玩家餘額，並產生 currency log、reel log、打碼等 side effect。

我會把這條 flow 的風險分成兩層。第一層是 state transition：settle / rollback 必須有 bet step 1，否則不知道要派彩或退多少；但如果只查 step 1，不查 step 2 / step 3，就可能擋不住重複 settle 或重複 refund。第二層是 transaction boundary：現在 adapter 是下游 gameserver 成功後才寫 Mongo evidence，所以 Mongo duplicate guard 並不等於 wallet-safe idempotency。

如果我要做 owner 改善，我會先確認 provider retry contract，確定 `betId`、provider transaction id、sign 在 retry 時是否穩定。接著把 idempotency key 定義成 provider + betId + action / step，放到 wallet mutation 前；或先落 durable request state，讓 retry 可以回同一筆已處理結果。最後再補 reconciliation，把 provider statement、adapter Mongo、gameserver currency log、reel log 對起來。

這條 case 我會保守講成 code-backed 分析，不會說是我主導開發。它的價值是可以展示我能從 adapter 追到真正 money boundary，並指出 retry、audit、wallet mutation 之間的風險。

## STAR 講法

Situation：第三方遊戲 provider 的 bet / settle / rollback 會直接影響玩家餘額，而且 provider retry、timeout、out-of-order 都可能發生。

Task：需要拆清楚 adapter、Mongo evidence、gameserver wallet mutation 的責任，避免把「有寫 log」誤當成「交易已安全」。

Action：我沿 `AntplayController` 的 `/bet`、`/settle`、`/rollback` 往下追 MD5 sign、Redis game mapping / center routing、Mongo step 1 / 2 / 3、gameserver `ANTPLAY_BET / SETTLE / REFUND`、`AntplaySettledJob`、`modifyAndGetCoinAntplay` 與 log side effect。

Result：整理出這條 flow 的核心 failure window：gameserver 改錢成功後，adapter 才寫 Mongo。若 Mongo 寫失敗或 adapter timeout，provider retry 可能再次進入 wallet mutation。改善方向是把 idempotency guard 放到 wallet mutation boundary，或先建立 durable request state，再做 audit / reconciliation。

## 常見追問

### 為什麼 Mongo step 1 不夠？

因為 step 1 是 gameserver 扣款成功後才寫。如果扣款成功但 step 1 沒寫，adapter 後續看不到 evidence，retry bet 可能再扣一次，settle / rollback 也可能因找不到 step 1 而失敗。

### settle / rollback 為什麼也有 double apply 風險？

目前 Step 3 看到 adapter 主要檢查 step 1 是否存在，沒有在送 gameserver 前明確檢查 step 2 / step 3 是否已完成。若 gameserver 已派彩或退款，但 Mongo step 2 / 3 寫失敗，provider retry 可能再次派彩或退款。

### retry key 應該用 sign、betId 還是 transactionId？

要先看 provider contract。sign 可能包含 timestamp，若每次 retry timestamp 不同，sign 不適合當唯一 idempotency key。比較穩的是 provider + betId + action / step，若 provider 有穩定 transaction id，再納入 transaction id。

### rollback 是不是只要把 bet 加回去？

不是。rollback 要看目前狀態。如果 bet 尚未成功，退款會多加錢；如果 settle 已成功，rollback 和 settle 是否互斥要看 provider contract。rollback 需要狀態機，不只是反向打一筆加款。

### adapter Mongo 是帳本嗎？

不是。它是 provider callback audit / transaction evidence。真正錢包狀態在 gameserver wallet / currency log。面試時要把 audit、projection、wallet source of truth 分清楚。

### 如果要補一件事，你會先補哪裡？

先補 wallet mutation 前的 idempotency guard。因為只補 adapter log 或補查詢，不一定能避免已改錢但 audit 未寫時的重入。接著補 durable request state 與 reconciliation。

### 新 `/bet_settle-new` 代表什麼？

它可能代表後續從舊三段式往整合式投派演進，但 Step 4 不能直接宣稱它已取代 production 舊 flow。面試可以說「我會把它當成後續演進線索，還需要確認 production route 和 provider contract」。

## 可反問面試官

- 你們 provider callback 的 idempotency key 是 provider 保證，還是 wallet service 自己保證？
- bet / settle / rollback 是三段式狀態機，還是 provider 會送整合式 transfer？
- wallet mutation、adapter audit、reel / currency log 之間有沒有 reconciliation job？
- 如果 provider retry 來了，你們回同一筆交易結果，還是回即時餘額？

## 可講 / 不可講

可講：

- 分析過 Antplay 三段式 bet / settle / rollback。
- 能拆 adapter 驗簽、Redis routing、Mongo step evidence、gameserver wallet mutation。
- 能指出 gameserver success -> Mongo insert fail 的 failure window。
- 能說明為什麼 idempotency guard 應靠近 wallet boundary。

不可講：

- Nick 開發 Antplay adapter。
- Nick 主導 `third_games_api`。
- 已確認 production 仍走舊三段式 endpoint。
- 已建立完整 exactly-once / reconciliation。
- 已修復 Antplay production 錯帳。

## Step 5 claim gate

結論：本 flow 可以面試講，不新增正式履歷 bullet。

原因：

- `third_games_api` Antplay adapter path 只重新確認到 Nick / `10gt12nc` 兩筆測試 / diagnostic commit，沒有補到 production adapter direct evidence。
- 下游 `iwin_gameserver` Antplay path 有 Nick / `10gt12nc` direct commits，但 project attribution 屬於 `iwin_gameserver`。
- 本 case 的價值在於 code-backed 系統分析：三段式 state transition、Mongo step evidence、gameserver wallet boundary、retry / rollback failure window。
- `05-resume-master-zh.md` / `08-application-autobiography-zh.md` 本輪不更新。

最穩的開場：

```text
這條是我做過 code-backed 深度分析的 flow，不會把它包裝成我主導開發。它的價值在於可以講清楚三段式 provider callback、adapter audit、gameserver wallet mutation、retry / rollback failure window 的邊界。
```

若面試官問「這是你做的嗎？」：

```text
third_games_api 的 Antplay adapter 本身我目前不會宣稱是我開發；我會把它當成分析過的第三方遊戲三段式 callback case。比較強的本人 direct evidence 在下游 iwin_gameserver 的 Antplay / GSC / PG 投派整合與 money job，這部分我會另外用 iwin_gameserver 的 project claim 來講。
```

## 履歷建議

目前不建議新增 `third_games_api` standalone 履歷 bullet。

可作為面試補充：

```text
分析過第三方遊戲 provider 三段式 bet / settle / rollback flow，能拆解 adapter 驗簽、Redis routing、gameserver wallet mutation、Mongo audit evidence 與 retry / rollback failure window。
```

這句只能作面試素材，不應直接放正式履歷主成果。

## 下一步

```text
iwin k3s-deploy gameserver-phased-rollout Step 5
```
