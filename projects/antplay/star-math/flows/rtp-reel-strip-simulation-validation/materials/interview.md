# Interview - RTP / 輪帶模擬與驗證

## 1. 30 秒摘要

> 我參與過 AntPlay slot math module 的 RTP / reel strip simulation validation。這類 flow 不是線上交易 API，但會影響長期派彩正確性。我會看 target / tolerance、symbol ratio、candidate reel strip、simulation rounds，並拆 Base RTP、free trigger、Free RTP、Jackpot hit rate，確認模擬走的是實際 `math.spin` path。

## 2. 3 分鐘版本

> Slot 遊戲的輪帶調整不能只看單次中獎結果，因為一條輪帶會同時影響一般局 RTP、免費局觸發率、免費局倍率和 jackpot hit rate。以 `sph-math` 為例，流程會先在 `RTPConstants` 定義目標與 tolerance，再由 `Ratio_RTP_*` 定義各符號比例，`GenerateByRatio` 產候選輪帶，`Simulate` 把候選輪帶塞到 `RTP_REEL_STRIP_OPTIMIZER`，再用真實 `P21008SlotMath` 的 `spin` 跑大量 rounds。最後把 Base RTP、FG trigger、Free RTP、JP hit rate 和 target 比對，不在範圍內就重試。
>
> 我在看這類 flow 時，會特別注意三件事。第一，validation path 是否走真實 runtime code，不要 simulation 和 production 分叉。第二，不能只看總 RTP，要拆 base、free、trigger 和特殊事件。第三，state 要乾淨，例如 `spn-math` 有 `lastSymbols`，如果 helper 沒 reset，就會污染驗證結果。
>
> 所以這個經驗我不會說成我是完整 RTP 策略 owner，但可以說我參與過 slot math module 的維護與驗證，理解 high-risk domain logic 怎麼透過 target、tolerance、simulation 和 runtime path 一致性降低上線風險。

## 3. Senior 追問

### Q1. 為什麼總 RTP 接近還不夠？

因為總 RTP 可能被不同組成抵銷。Base RTP、Free trigger、Free RTP、Jackpot hit rate 都會影響玩家體感與長期派彩風險。總 RTP pass，但 free trigger 偏太高或 jackpot hit rate 偏離，仍可能出問題。

### Q2. simulation 怎麼確保和 production 一致？

我會檢查 simulation 是否走真實 `math.spin`，並確認 `flagRTP` 有把候選輪帶導到 `RTP_REEL_STRIP_OPTIMIZER`。如果 validation 另外手寫一套派彩邏輯，它就不能代表 production。

### Q3. 隨機模擬結果不穩怎麼辦？

先看 rounds / attempts 是否足夠，再看是否需要固定 seed、保存 candidate reel strip 和 run output。若要作 release gate，不能只看一次 pass；要能重跑、比對、追溯。

### Q4. `lastSymbols` 這種 state 有什麼風險？

它會讓上一局盤面影響下一次 spin，尤其是 free game / re-spin 類 feature。若 debug helper 或 simulation 沒有正確 reset / carry，就會把驗證結果污染。

### Q5. 這可以放履歷嗎？

可以放在 grouped `*-math` bullet，保守說參與 slot math module RTP / reel strip / simulation validation 維護。不能寫成主導完整 RTP 策略或完整遊戲數學模型。

## 4. Lead / Architect 延伸追問

- 若要把這套 validation 做成正式 pipeline，你會保存哪些 artifacts？
- 要如何避免 simulation 過度依賴某次 random result？
- GDD target、code constants、production reel strip 之間如何做 traceability？
- 若 production 發現 RTP 偏差，你會怎麼回查是輪帶、feature state、runtime routing 還是統計問題？
- 這種 math module 和一般 payment consistency 的相同點 / 不同點是什麼？

## 5. 保守回答

如果被問「你是不是 RTP owner？」：

> 我不會把自己說成完整 RTP 策略 owner。我的 evidence 是參與多個 slot math module 的維護與驗證，能讀懂 RTP target、輪帶、simulation loop、runtime path 和 failure window，並能把這些風險整理成可驗證的 release gate。
