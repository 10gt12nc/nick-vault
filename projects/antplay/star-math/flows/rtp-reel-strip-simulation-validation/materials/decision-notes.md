# Decision Notes - RTP / 輪帶模擬與驗證

## 1. 為什麼不能只看總 RTP

總 RTP 可能看似接近，但分布可能不健康：

- Base RTP 過高、Free RTP 過低，玩家體感不同。
- Free trigger 太低，免費局很少進。
- Free trigger 太高，長期派彩可能被放大。
- Jackpot hit rate 偏離，會影響特殊獎池期望。
- Buy Free path 的 expected RTP 和一般自然觸發 path 不同。

因此 Step 3 掃到的 code 不是只算 `totalWin / totalBet`，而是拆成 Base RTP、FG trigger、Free RTP、SPH Jackpot hit rate。

## 2. 為什麼 simulation 要走 `math.spin`

如果 validation 另外手寫一套中獎計算，它可能和 production runtime 分叉。這條 flow 比較好的地方是：

- `Simulate` 建立真實 `P21008SlotMath`。
- 透過 `flagRTP="RTP_REEL_STRIP_OPTIMIZER"` 讓 `AbstractSlotMath#init` 選候選輪帶。
- 統計結果來自 `SlotBetResult` / `RoundResult`。

這表示 validation 和 runtime domain code 有對齊，而不是只在 optimizer 裡估公式。

## 3. Target / Tolerance 的 owner decision

`RTPConstants` 裡的 target / lower bound / upper bound 是 owner decision，不只是常數：

- Base RTP tolerance 放太寬，production payout 可能偏離。
- FG trigger tolerance 太寬，Free RTP 會被放大或縮小。
- Free RTP upper bound 在部分註解中明確要求嚴格，避免超過目標。
- Jackpot hit rate 是超低機率事件，rounds 不足時波動會很大。

面試時不要講「我調到過就好」，要講「target、tolerance、rounds、attempts 都是 release risk decision」。

## 4. Randomness / Reproducibility

本輪讀到 `GenerateByRatio` 以 `new Random()` 產生候選輪帶，未看到固定 seed。這不代表錯，但代表：

- 適合探索候選輪帶。
- 若要作正式 QA / certification evidence，最好固定 seed 或保存候選輪帶與 run output。
- 若每次重跑結果差距大，要判斷是 sample size 不足、輪帶不穩，還是 target / ratio 有問題。

## 5. State Reset

`spn-math` 有 `lastSymbols` state，且 commit message 出現「重置 lastSymbols」。這說明 simulation / debug helper 不能只看單次 spin；某些 feature 會把上一局 symbols 帶入下一次免費局或 re-spin。

Owner 觀點：

- 進入 helper method 前是否 reset。
- BG 到 FG 是否正確 carry。
- 重跑 validation 時是否殘留上一次 state。
- 對外 result 是否也要包含足夠 state 讓前端復現。

## 6. Release Gate 建議

若要把這條 flow 變成更硬的 production-ready evidence，建議至少保留：

- target source: 對應 GDD / math spec 的版本。
- run config: rounds、attempts、lineBet、expectRTP、seed 或候選輪帶。
- output summary: Base RTP、FG trigger、Free RTP、JP hit rate、是否 pass。
- accepted reel strip: 對應 production `SlotReelStripTable` 的 diff。
- reviewer note: 為什麼接受這組 tolerance。

本輪未補這些外部文件，因此 claim 保持「參與維護與驗證」，不升級成完整 owner。
