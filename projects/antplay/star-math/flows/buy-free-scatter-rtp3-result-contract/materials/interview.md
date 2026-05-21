# Interview - Buy Free / Scatter / RTP_3 Result Contract

## 0. Step 3 定位

- Flow: `buy-free-scatter-rtp3-result-contract`
- Status: Step 3 / 深掃完成，待 Step 4 正式整理
- 證據層級: `真實開發過 + code-backed` / `專案存在 / code-backed`

## 1. 30 秒摘要

> 我參與過 slot math module 的 buy free / scatter / RTP_3 類維護。它不是單純讓玩家買免費局，而是 game-api 要用 buy free odds 算成本，math module 要用 `RTP_3` 選買免費局輪帶，scatter / `lastSymbols` 會影響免費局 state，最後還要把 `RoundResult` / `FreeBetResult` 組成前端和 bet record 可理解的 result contract。

## 2. 3 分鐘版本草稿

> 以 `spn-math` 為例，buy free flow 從上游 `GameFacade` 的 `boughtFreeSpin` 判斷開始。若玩家買免費局，game-api 會先確認 `freeSpinType` 是否存在於該遊戲 math config 的 `BoughtFreeSpinTypeBO`，並拿 odds 算成本。之後不走 normal bet，而是呼叫 `SlotMathFacade.freeSpinBet`。
>
> 進入 math module 後，`SPNOperatorService#freeSpinBet` 會委派到 `SPNMathFactory#boughtFreeSpinBet`。這裡會建立 `InputData`，把 `flagRTP` 設成 `RTP_3`。`AbstractSlotMath#init` 看到 `RTP_3` 就會選 `stripTableContainer_buyFreeGame`，也就是 `SlotReelStripTable.RTP_3`。所以 `RTP_3` 在這條 flow 裡不只是數字標籤，而是 buy free runtime routing。
>
> 我會特別注意 result contract。Base round 觸發 free game 後，factory 會逐次跑 free spin，累加 free total win，並把每次 free round 包成 `FreeBetResult`。上游 `GameFlowFacade#afterBet` 還會補 `isBoughtFreeSpin`、`boughtFreeSpinOdds`、`boughtFreeSpinType`。如果 odds、`RTP_3` routing、free result 結構或 totalBet 語意不一致，玩家扣款、前端顯示、bet record 或 darkpool 統計都可能對不上。

## 3. Senior 追問

### Q1. 為什麼 buy free 不是只看 `boughtFreeSpinBet`？

因為它跨上游定價、math routing、result JSON。只看 math function 會漏掉 odds / totalBet；只看 game-api 會漏掉 `RTP_3` 輪帶與 scatter state。

### Q2. `RTP_3` 的風險是什麼？

`RTP_3` 是 buy free 專用 routing。若 input 沒設 `RTP_3`，或 config 沒把 `stripTableContainer_buyFreeGame` 對到正確輪帶，買免費局就可能跑一般局輪帶。

### Q3. 為什麼 `lastSymbols` 重要？

SPN 的 scatter / wild state 會跨 spin 保存與回寫。debug 或 loop helper 若不 reset，可能把上一輪 state 帶到下一輪，導致 free trigger 或 result 不穩。

### Q4. result contract 要看哪些欄位？

至少看 `isBoughtFreeSpin`、`boughtFreeSpinOdds`、`boughtFreeSpinType`、`totalBet`、`betTotalWin`、`RoundResult.freeTotalWin`、`freeScatterPay`、`freeBetResults`。

### Q5. 如果玩家說買免費局結果不對，你怎麼查？

先查 request 是否 `boughtFreeSpin=true` 與 type，接著查 odds 是否匹配 math config，再查 `freeSpinBet` 是否走 `RTP_3`，最後比對 `RoundResult` / `FreeBetResult` 與 bet record result JSON。

## 4. Lead / Architect 追問方向

- odds 應放 math config 還是營運配置？
- result JSON 是否需要 schema version？
- buy free totalBet 是在 beforeBet 還是 afterBet 調整？如何避免雙乘或漏乘？
- `RTP_3` 是否要納入 automated validation pipeline？
- normal bet / buy free 的 darkpool 統計要怎麼分開？

## 5. 面試避免踩雷

- 不要說自己設計完整 buy free 商業模型。
- 不要說自己主導完整 wallet / settlement。
- 不要只講 class 名稱，要講 contract 邊界。
- 不要保證 RTP 或 odds 數字正確，除非補正式 validation evidence。
