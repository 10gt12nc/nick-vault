# Buy Free / Scatter / RTP_3 Result Contract Career Interview

## 0. 定位

- Flow: `buy-free-scatter-rtp3-result-contract`
- Status: Step 5 / 單條 flow claim gate 已完成
- 證據層級: `真實開發過 + code-backed` / `專案存在 / code-backed`
- 主 evidence: `spn-math` Nick / `10gt12nc` commits + `antplay-slot-game-api` runtime caller 補讀

## 1. 可以怎麼講

30 秒版本：

> 我參與過 slot math module 的 buy free / scatter / RTP_3 類維護。這類 flow 不是只改免費局倍數，而是 game-api 要判斷 buy free、依 math config 找 odds，math module 要走 `RTP_3` 輪帶，最後把 Base round、free rounds、free total win 組成 `RoundResult` / `FreeBetResult` 給前端、bet record 和後續統計使用。

3 分鐘版本：

> 以 `spn-math` 為例，買免費局會從 game-api 的 `boughtFreeSpin` 判斷開始。上游會檢查 `freeSpinType`，從 math config 的 `BoughtFreeSpinTypeBO` 找 odds，然後呼叫 `SlotMathFacade.freeSpinBet`。進到 math module 後，`SPNMathFactory#boughtFreeSpinBet` 會建立 `InputData`，把 `flagRTP` 設成 `RTP_3`，讓 `AbstractSlotMath#init` 選到 buy free 專用輪帶。
>
> 真正要注意的是 result contract 和扣款語意。`GameFacade` 會在 beforeBet 前把買免費局 odds 乘進 bet amount；Base round 觸發免費局後，factory 會逐次跑 free spin，把 free rounds 包成 `FreeBetResult`，再把 free total win、free scatter pay、free spin count 放回 `RoundResult`。上游 `GameFlowFacade#afterBet` 也會補 `isBoughtFreeSpin`、`boughtFreeSpinOdds`、`boughtFreeSpinType`，並讓 result JSON 裡的 totalBet 語意能看出這筆是 buy free。如果 odds、RTP_3 routing、free result 結構或 totalBet 語意其中一層錯，前端顯示、bet record、wallet 或 darkpool 統計就可能對不上。

5 分鐘版本：

> 我會把這條 flow 拆成三個契約來看。第一個是 pricing contract：上游 request 帶 `buyFreeSpin` 和 `freeSpinType`，game-api 必須先確認這個 type 是這款遊戲允許的，再從 math config 拿 odds，並把 bet amount 乘上 odds。第二個是 runtime routing contract：一般 bet 走 normal bet，buy free 必須走 `SlotMathFacade.freeSpinBet`，在 `SPNMathFactory` 內把 `flagRTP` 設成 `RTP_3`，讓 `AbstractSlotMath#init` 選 buy free reel strip。第三個是 result contract：math result 要把 Base round、free rounds、freeTotalWin、freeScatterPay、freeBetResults 組好，game-api afterBet 還要補 buy free metadata，讓前端、bet record、客服查詢與統計知道這筆不是一般 bet。
>
> 這裡我會特別盯兩個 failure window。第一個是 `lastSymbols`，因為 SPN 會把 scatter / wild 狀態保存再回寫；若 helper loop 或 debug flow 沒 reset，可能把上一輪盤面狀態帶到下一輪。第二個是 totalBet 語意，上游實際扣款已乘 odds，result JSON 也要能反映 buy free odds，否則玩家扣款、前端顯示和後台紀錄會產生認知落差。

## 2. Senior 能力點

- Contract thinking: buy free 同時是 pricing contract、runtime routing contract、result JSON contract。
- State awareness: SPN 的 `lastSymbols` 會影響 scatter / wild 固定與 re-spin，測試 helper 要 reset。
- Runtime alignment: game-api `freeSpinBet` 必須真的落到 math module `RTP_3`。
- Money-like correctness: buy free odds 影響 bet amount、result totalBet、darkpool 統計。
- Troubleshooting path: 先查 request / type / odds，再查 `freeSpinBet` routing，再查 `RoundResult` / `FreeBetResult` 與 result JSON。
- Claim boundary: 可說參與維護與驗證，不說完整 buy free 商業設計 owner。

## 3. 面試問答

Q: 如果玩家說買免費局扣款或結果不對，你會怎麼查？

A: 我會先確認 request 是否 `boughtFreeSpin=true`、`freeSpinType` 是否屬於這款 game，再確認 math config 對應 odds。接著看 `getBetResult` 是否真的走 `SlotMathFacade.freeSpinBet`，math input 是否 `flagRTP=RTP_3`。最後比對 `RoundResult` / `FreeBetResult`、`GameFlowFacade#afterBet` 補的 `isBoughtFreeSpin` / odds / type，以及 bet record result JSON。

Q: 為什麼 `lastSymbols` 是這條 flow 的高風險點？

A: SPN 的 scatter / wild 不是每次 spin 都完全獨立，它會把指定 symbol state 保存到 `lastSymbols`，後續再回寫到當前盤面。這能支援 feature 規則，但也代表 debug / loop helper 若沒 reset，會讓上一輪狀態污染下一輪。Nick 的相關 commit 有直接修 `lastSymbols` reset，這是這條 flow 很好的 failure window evidence。

Q: 這可以怎麼連到 Senior Backend，而不是只像遊戲 math？

A: 我不會只講輪帶和符號，而是講跨 module contract：game-api 定價與扣款、math module runtime routing、result JSON 與後續紀錄必須一致。這跟後端常見的 payment / order / settlement 一樣，核心是「不同層的金額語意和狀態語意不能漂移」。

## 4. 可安全履歷 bullet

只建議併入 `*-math` grouped bullet：

> 參與 AntPlay 多個 slot math module 維護與驗證，處理 RTP / reel strip、buy free / scatter、debug bet、fixedMultiBet、jackpot / symbol、currency 與 result contract 類調整。

更保守版本：

> 參與 slot math module 維護，協助 buy free / scatter / RTP_3 與 free game result contract 類功能驗證。

## 5. 不可誇大

- 不說「我設計完整 buy free 商業模型」。
- 不說「我主導完整 RTP / 遊戲數學策略」。
- 不說「我負責 wallet / settlement / darkpool 全鏈路」。
- 不說「所有 `*-math` repo 的 buy free 都完整深掃」。
- 不說改善倍率或 RTP 數字，除非後續補正式 validation report / ticket。

## 6. Step 5 Claim Gate

本 flow 已完成 Step 5。它最適合包成「slot math feature 的跨層 contract consistency」案例，而不是單純「我改了 RTP_3 輪帶」。

### 6.1 可放履歷

可以，但只建議併入 `*-math` grouped bullet，不拆成 standalone buy free 成果：

> 參與 AntPlay 多個 slot math module 維護與驗證，處理 RTP / reel strip、buy free / scatter、debug bet、fixedMultiBet、jackpot / symbol、currency 與 result contract 類調整。

證據層級：`真實開發過 + code-backed`。強 evidence 來自 `spn-math` Nick / `10gt12nc` direct commits，集中在 RTP_3、buyFreeWinInfo、`HAVE_3_SCATTER_WIN`、`lastSymbols` reset。

### 6.2 可面試講

可以作正式面試 case，主軸是：

- buy free 的 pricing / routing / result contract consistency。
- `RTP_3` 在本 flow 是 buy free routing key。
- `lastSymbols` 是跨 spin state failure window。
- `RoundResult` / `FreeBetResult` 與 game-api afterBet result JSON 要對齊。

### 6.3 不更新 05 / 08

本輪不直接更新 `05-resume-master-zh.md` / `08-application-autobiography-zh.md`。原因是：這是單條 flow Step 5；正式履歷 / 自傳仍以 `*-math` project-level contribution consolidation 或 rolling resume package 為準。本 flow 只作 future 回填 evidence。

### 6.4 不可誇大

- 不說「我設計完整 buy free 商業模型」。
- 不說「我主導完整 RTP / 遊戲數學策略」。
- 不說「我負責 wallet / settlement / darkpool 全鏈路」。
- 不說「所有 `*-math` repo 的 buy free 都完整深掃」。
- 不說改善倍率或 RTP 數字，除非後續補正式 validation report / ticket。

下一步：

```text
antplay *-math jackpot-symbol-hit-and-prize-scaling Step 3
```
