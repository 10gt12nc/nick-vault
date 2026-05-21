# Buy Free / Scatter / RTP_3 Result Contract Career Interview

## 0. 定位

- Flow: `buy-free-scatter-rtp3-result-contract`
- Status: Step 3 / 深掃完成，待 Step 4 轉正式面試 case
- 證據層級: `真實開發過 + code-backed` / `專案存在 / code-backed`
- 主 evidence: `spn-math` Nick / `10gt12nc` commits + `antplay-slot-game-api` runtime caller 補讀

## 1. 可以怎麼講

30 秒版本：

> 我參與過 slot math module 的 buy free / scatter / RTP_3 類維護。這類 flow 不是只改免費局倍數，而是 game-api 要判斷 buy free、依 math config 找 odds，math module 要走 `RTP_3` 輪帶，最後把 Base round、free rounds、free total win 組成 `RoundResult` / `FreeBetResult` 給前端和 bet record。

3 分鐘版本：

> 以 `spn-math` 為例，買免費局會從 game-api 的 `boughtFreeSpin` 判斷開始。上游會檢查 `freeSpinType`，從 math config 的 `BoughtFreeSpinTypeBO` 找 odds，然後呼叫 `SlotMathFacade.freeSpinBet`。進到 math module 後，`SPNMathFactory#boughtFreeSpinBet` 會建立 `InputData`，把 `flagRTP` 設成 `RTP_3`，讓 `AbstractSlotMath#init` 選到 buy free 專用輪帶。
>
> 真正要注意的是 result contract。Base round 觸發免費局後，factory 會逐次跑 free spin，把 free rounds 包成 `FreeBetResult`，再把 free total win、free scatter pay、free spin count 放回 `RoundResult`。上游 `GameFlowFacade#afterBet` 也會補 `isBoughtFreeSpin`、`boughtFreeSpinOdds`、`boughtFreeSpinType`。如果 odds、RTP_3 routing、free result 結構或上游 totalBet 語意其中一層錯，前端顯示、bet record、wallet 或 darkpool 統計就可能對不上。

## 2. Senior 能力點

- Contract thinking: buy free 同時是 pricing contract、runtime routing contract、result JSON contract。
- State awareness: SPN 的 `lastSymbols` 會影響 scatter / wild 固定與 re-spin，測試 helper 要 reset。
- Runtime alignment: game-api `freeSpinBet` 必須真的落到 math module `RTP_3`。
- Money-like correctness: buy free odds 影響 bet amount、result totalBet、darkpool 統計。
- Claim boundary: 可說參與維護與驗證，不說完整 buy free 商業設計 owner。

## 3. 可安全履歷 bullet

只建議併入 `*-math` grouped bullet：

> 參與 AntPlay 多個 slot math module 維護與驗證，處理 RTP / reel strip、buy free / scatter、debug bet、fixedMultiBet、jackpot / symbol、currency 與 result contract 類調整。

更保守版本：

> 參與 slot math module 維護，協助 buy free / scatter / RTP_3 與 free game result contract 類功能驗證。

## 4. 不可誇大

- 不說「我設計完整 buy free 商業模型」。
- 不說「我主導完整 RTP / 遊戲數學策略」。
- 不說「我負責 wallet / settlement / darkpool 全鏈路」。
- 不說「所有 `*-math` repo 的 buy free 都完整深掃」。
- 不說改善倍率或 RTP 數字，除非後續補正式 validation report / ticket。

## 5. Step 4 準備

Step 4 應把本 flow 轉成面試 case，重點放：

- buy free 不是單一 math function，而是跨 game-api / math module / result JSON 的契約。
- `RTP_3` routing、odds、scatter state、`RoundResult` / `FreeBetResult` 是核心。
- failure window 以 odds 錯、RTP routing 錯、result contract 錯、`lastSymbols` 污染為主。

下一步：

```text
antplay *-math buy-free-scatter-rtp3-result-contract Step 4
```
