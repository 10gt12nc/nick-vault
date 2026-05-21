# Decision Notes - Buy Free / Scatter / RTP_3 Result Contract

## 1. 為什麼這條不是純 math tuning

Buy free 同時碰到三種契約：

- Pricing: `BoughtFreeSpinTypeBO` 的 odds 決定買免費局成本。
- Runtime routing: `flagRTP=RTP_3` 決定使用買免費局輪帶。
- Result contract: `RoundResult` / `FreeBetResult` / result JSON 決定前端、後台與 bet record 怎麼看這筆結果。

若只看 `SlotReelStripTable.RTP_3`，會漏掉上游扣款與下游 result JSON；若只看 game-api `boughtFreeSpin`，又會漏掉 math module 的 scatter / state。

## 2. `RTP_3` 的 owner decision

`RTP_3` 在本 flow 裡不是一般 RTP variant，而是 buy free routing key。

Owner 要決定：

- `RTP_3` 是否只服務 buy free。
- debug / simulation 能否共用 `RTP_3`，以及共用時如何避免誤用。
- `RTP_3` Base / Free game table 是否都要一起 validation。
- result JSON 是否要標明 `flagRTP` / buy free type，讓排查能回推 routing。

## 3. `lastSymbols` 的 state 風險

SPN flow 有 scatter / wild 固定與回寫機制：

- Base game 第一次看到 scatter 會存進 `lastSymbols`。
- 第二次 spin 可能把 `lastSymbols` 回寫到 symbols。
- Free game 也會把 scatter / wild state 保存到下一轉。

因此 debug / loop helper 如果不 reset `lastSymbols`，結果會受上一輪污染。`d9beaa4` 的 commit message 明確提到重置 `lastSymbols`，這是可面試講的 failure window。

## 4. Result Contract 風險

`RoundResult` 與 `FreeBetResult` 的語意要穩：

- `roundTotalWin`: base round + free total win。
- `normalWin`: base win。
- `freeScatterPay`: scatter pay。
- `freeSpin`: 本 round 增加免費轉次數。
- `freeTotalWin`: free scatter pay + free round total win。
- `freeBetResults`: 免費局每次 spin 的 round list。

若欄位語意混亂，前端和 bet record 可能都能顯示，但客服 / 對帳會很難回推。

## 5. 建議補強

- 建立 buy free result snapshot tests：固定 rng / type / odds，比對 result JSON。
- 增加 config check：每個支持 buy free 的 game 都要有對應 `BoughtFreeSpinTypeBO`。
- 將 `RTP_3` routing、odds、free result schema 放進 release checklist。
- 若要上升為正式履歷強 claim，需補 MR / ticket / validation report / production issue。

## 6. beforeBet / afterBet totalBet 語意

Step 4 補讀後，這條 flow 最值得面試講的是 totalBet 語意被拆在兩段：

- `GameFacade` 在 beforeBet 前找到 buy free odds，將 bet amount 乘上 odds。這比較接近實際扣款 / wallet amount 語意。
- `GameFlowFacade#afterBet` 再把 `betResult.totalBet`、`isBoughtFreeSpin`、`boughtFreeSpinOdds`、`boughtFreeSpinType` 寫進 result JSON。這比較接近展示 / bet record / 後續查詢語意。

Owner decision:

- odds 必須同源，避免 beforeBet 與 afterBet 使用不同 config。
- result JSON 裡的 totalBet 要定義清楚：是一般 line bet、buy free 後成本，還是給前端顯示的 total cost。
- 需要 snapshot test 驗證同一筆 buy free request 在 wallet amount、bet result、bet record JSON 中的 totalBet 語意一致。

## 7. darkpool / statistics 分流

`GameFacade` caller path 已看到 buy free 會走 free spin total bet / total win 統計入口，normal bet 則走一般統計入口。這代表 buy free 不只是前端 feature，也會影響後台長期 RTP / darkpool 觀測。

本 Step 未深掃 darkpool service implementation，因此不能宣稱統計完整正確。可面試講的是「caller 已有 normal / buy free 分流，Owner 需要確認下游 service 與報表也維持分流語意」。

## 8. Step 4 面試主線

最推薦的講法不是「我改 `RTP_3`」，而是：

```text
buy free = pricing contract + runtime routing contract + result contract
```

這條 flow 能展示的 Senior / Owner 能力:

- 看到 feature 跨 module contract，而不是只看單一 class。
- 能把遊戲 math 的 RTP / scatter / free game state，翻成後端熟悉的金額語意、狀態語意與 result schema。
- 能指出 failure window：odds 漂移、RTP routing 錯、`lastSymbols` 污染、result JSON 與 bet record 語意不一致。
- 能保守界定 claim：可面試講 code-backed flow，不誇大成完整 buy free / wallet / RTP owner。
