# Interview - Buy Free / Scatter / RTP_3 Result Contract

## 0. Step 4 定位

- Flow: `buy-free-scatter-rtp3-result-contract`
- Status: Step 5 / 正式面試 case + 單條 flow claim gate 已完成
- 證據層級: `真實開發過 + code-backed` / `專案存在 / code-backed`

本檔是 Step 4 面試稿細節。主讀文件仍是同層 `career-interview.md`；本檔放更完整的追問、排查順序與回答邊界。

Step 5 已確認：本 case 可作 `*-math` grouped 履歷 bullet 的強化 evidence，但不獨立寫成完整 buy free owner / RTP owner / wallet owner。

## 1. 30 秒摘要

> 我參與過 slot math module 的 buy free / scatter / RTP_3 類維護。它不是單純讓玩家買免費局，而是 game-api 要用 buy free odds 算成本，math module 要用 `RTP_3` 選買免費局輪帶，scatter / `lastSymbols` 會影響免費局 state，最後還要把 `RoundResult` / `FreeBetResult` 組成前端和 bet record 可理解的 result contract。

## 2. 3 分鐘版本草稿

> 以 `spn-math` 為例，buy free flow 從上游 `GameFacade` 的 `boughtFreeSpin` 判斷開始。若玩家買免費局，game-api 會先確認 `freeSpinType` 是否存在於該遊戲 math config 的 `BoughtFreeSpinTypeBO`，並拿 odds 算成本。之後不走 normal bet，而是呼叫 `SlotMathFacade.freeSpinBet`。
>
> 進入 math module 後，`SPNOperatorService#freeSpinBet` 會委派到 `SPNMathFactory#boughtFreeSpinBet`。這裡會建立 `InputData`，把 `flagRTP` 設成 `RTP_3`。`AbstractSlotMath#init` 看到 `RTP_3` 就會選 `stripTableContainer_buyFreeGame`，也就是 `SlotReelStripTable.RTP_3`。所以 `RTP_3` 在這條 flow 裡不只是數字標籤，而是 buy free runtime routing。
>
> 我會特別注意 result contract。Base round 觸發 free game 後，factory 會逐次跑 free spin，累加 free total win，並把每次 free round 包成 `FreeBetResult`。上游 `GameFlowFacade#afterBet` 還會補 `isBoughtFreeSpin`、`boughtFreeSpinOdds`、`boughtFreeSpinType`。如果 odds、`RTP_3` routing、free result 結構或 totalBet 語意不一致，玩家扣款、前端顯示、bet record 或 darkpool 統計都可能對不上。

## 2.1 5 分鐘版本

> 這條 case 我會先用 backend contract 的角度講。第一層是 pricing：`GameFacade` 會讀 request 的 `buyFreeSpin` 與 `freeSpinType`，先 validate type 是否允許，再從 math config 找 `BoughtFreeSpinTypeBO` odds。這個 odds 不只是顯示用，而是會影響 beforeBet 前的 bet amount。
>
> 第二層是 runtime routing：buy free 不走 normal bet，而是 `SlotMathFacade.freeSpinBet`。到 `spn-math` 後，`SPNMathFactory#boughtFreeSpinBet` 會把 `InputData.flagRTP` 設成 `RTP_3`。`AbstractSlotMath#init` 根據 `RTP_3` 選 `stripTableContainer_buyFreeGame`，所以如果這層 routing 錯，買免費局就可能跑到一般局輪帶。
>
> 第三層是 result contract：Base round 若進免費局，factory 會用同一個 `flagRTP` 跑 free game，累加 free total win，並把每次免費局包成 `FreeBetResult`。`GameFlowFacade#afterBet` 再把 buy free metadata 寫到 result JSON。這樣前端展示、bet record、客服查詢和後續統計才能知道這筆是 buy free，而不是一般 spin。
>
> 我會補充一個具體 failure：SPN 有 `lastSymbols` 保存 scatter / wild state 的機制，Nick 的相關 commit 有處理 reset。這種 state 如果在 helper loop 或 debug flow 裡沒有清乾淨，結果會看起來像 RTP 或輪帶問題，但根因其實是上一輪盤面狀態污染。

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

### Q6. 為什麼可以把這條講成 backend case？

因為它不是只調 symbol 或 reel strip。它跨了上游 validation / pricing、math module routing、result JSON schema、bet record 與 darkpool 統計語意。這和 payment / order 類 flow 一樣，核心是不同 layer 對「這筆 bet 是什麼、扣多少、結果怎麼記」要一致。

### Q7. 如果 odds 在 beforeBet 和 afterBet 各乘一次，會不會有風險？

要分清楚語意。`GameFacade` 在 beforeBet 前乘 odds 是實際 bet amount / wallet 語意；`GameFlowFacade#afterBet` 補 result JSON 與 `betResult.totalBet` 是展示與紀錄語意。風險在於兩邊如果不同源或邏輯漂移，就可能出現扣款正確但 result JSON 顯示錯，或後台統計重複乘 / 漏乘。Owner 應該要求同一份 odds config、schema test 與 bet record snapshot 對齊。

### Q8. Step 4 後還缺什麼才能變成履歷強 claim？

缺 MR / ticket / validation report / production issue，或更完整地追 wallet mutation、bet record schema、front-end display、darkpool 報表如何使用 buy free metadata。現在可以作 code-backed 面試 case，但不升級成完整 buy free owner。

## 4. Lead / Architect 追問方向

- odds 應放 math config 還是營運配置？
- result JSON 是否需要 schema version？
- buy free totalBet 是在 beforeBet 還是 afterBet 調整？如何避免雙乘或漏乘？
- `RTP_3` 是否要納入 automated validation pipeline？
- normal bet / buy free 的 darkpool 統計要怎麼分開？
- `lastSymbols` 這種跨 spin state 是否應該被包成明確 state object，避免 static / helper loop 污染？
- 買免費局支援新增 type 時，是否要有 config completeness check 與 snapshot tests？

## 5. 面試避免踩雷

- 不要說自己設計完整 buy free 商業模型。
- 不要說自己主導完整 wallet / settlement。
- 不要只講 class 名稱，要講 contract 邊界。
- 不要保證 RTP 或 odds 數字正確，除非補正式 validation evidence。

## 6. Step 5 Claim Gate 結論

本 flow 已完成正式面試 case與單條 flow claim gate。最推薦的講法是：

> 我把 buy free 拆成 pricing、routing、result contract 三層來看，重點不是單一 RTP_3 表，而是 odds、`RTP_3`、`lastSymbols`、`RoundResult` / `FreeBetResult` 和 result JSON 要一致。

Step 5 判定：

- 可作面試案例：可以。
- 可放履歷：只併入 `*-math` grouped bullet。
- 不更新 05 / 08：本輪不直接更新，後續由 project-level consolidation / rolling resume package 回填。
- 不可誇大：不寫成完整 buy free / RTP / wallet / darkpool owner。

後續 Rank 4 與 Rank 5 已完成到 Step 5；目前沒有預設下一步。

```text
antplay *-math jackpot-symbol-hit-and-prize-scaling Step 4
```
