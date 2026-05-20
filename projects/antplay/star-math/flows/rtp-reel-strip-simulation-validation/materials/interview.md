# Interview - RTP / 輪帶模擬與驗證

## 0. Step 4 定位

- Flow: `rtp-reel-strip-simulation-validation`
- Status: Step 5 / 正式面試 case + 單條 flow claim gate 已完成
- 證據層級: `真實開發過 + code-backed` / `專案存在 / code-backed`
- 主要 evidence: `sph-math` RTP / JP / simulation commits，`spn-math` RTP_3 / buy free / `lastSymbols` commits
- Claim boundary: 可說參與 slot math module 維護與驗證；不說完整 RTP 策略 owner、完整遊戲數學模型 owner 或 certification owner

這份是面試可直接講的版本。若面試官不是遊戲產業背景，先用「高風險 domain logic 的 validation」來講；若對方懂遊戲，才展開 RTP / reel strip / free trigger / jackpot hit rate。

Step 5 已確認：本 case 可作 `*-math` grouped 履歷 bullet 的強化 evidence，但不獨立寫成完整 RTP owner / game math model owner / certification owner。

## 1. 30 秒摘要

> 我參與過 AntPlay 多個 slot math module 的維護與驗證，其中一類是 RTP / reel strip simulation validation。這不是線上 API，但會影響長期派彩正確性。我會看 target / tolerance、symbol ratio、candidate reel strip、simulation rounds，並拆 Base RTP、free trigger、Free RTP、Jackpot hit rate，確認模擬走的是實際 `math.spin` path，而不是另外手寫一套不一致的驗證邏輯。

## 2. 3 分鐘版本

> 我在 AntPlay 的 slot math module 維護裡，看過一類很典型的驗證 flow：RTP / reel strip simulation validation。Slot 遊戲的輪帶調整不能只看單次中獎結果，因為一條輪帶會同時影響一般局 RTP、免費局觸發率、免費局倍率、buy free 體感和 jackpot hit rate。這些不一定是線上交易 API，但它們會影響 production payout correctness。
>
> 以 `sph-math` 為例，流程會先在 `RTPConstants` 定義目標與 tolerance，例如 Base RTP、free trigger、Free RTP 和 Jackpot hit rate；再由 `Ratio_RTP_*` 定義各符號比例，`GenerateByRatio` 產候選輪帶。接著 `Simulate` 把候選輪帶塞到 `RTP_REEL_STRIP_OPTIMIZER`，用 `flagRTP` 讓 `AbstractSlotMath#init` 選到這組候選輪帶，最後用真實 `P21008SlotMath#spin` 跑大量 rounds。模擬結果會拆開看 Base RTP、FG trigger、Free RTP、JP hit rate，和 target / tolerance 比對，不在範圍內就重試下一組 candidate。
>
> 我在看這類 flow 時，會特別注意三件事。第一，validation path 是否走真實 runtime code，不要 simulation 和 production 分叉。第二，不能只看總 RTP，要拆 base、free、trigger、jackpot 這種組成指標。第三，state 要乾淨，例如 `spn-math` 有 `lastSymbols`，如果 helper 沒 reset 或 carry 錯，就會污染驗證結果。
>
> 所以這個經驗我不會說成我是完整 RTP 策略 owner；比較準確的說法是，我參與過 slot math module 的維護與驗證，理解高風險 domain logic 怎麼透過 target、tolerance、simulation、runtime path 一致性和 release gate 降低上線風險。

## 3. STAR 版本

### Situation

AntPlay 有多個 slot math module。每個遊戲的輪帶、符號比例、RTP、免費局觸發和 jackpot hit 都會影響長期派彩。如果只看單次 spin 或只看總 RTP，很容易漏掉分布偏差。

### Task

在維護 / 驗證 slot math module 時，需要確認候選 reel strip 是否符合目標 RTP 與遊戲體感，並避免 simulation 和 production runtime path 分叉。

### Action

- 讀 `RTPConstants` 確認 Base RTP、Free RTP、FG trigger、Jackpot hit rate 與 tolerance。
- 讀 `Ratio_RTP_*` 與 `GenerateByRatio` 確認候選輪帶如何按比例生成，scatter / special symbol 如何避免過度集中。
- 讀 `Simulate` 確認 candidate reels 會被放入 `RTP_REEL_STRIP_OPTIMIZER`。
- 讀 `AbstractSlotMath#init` 確認 `flagRTP` 會選到 optimizer table。
- 讀 `ProcessGameSpin` / `P21008SlotMath#spin` 確認 simulation 走真實 math runtime path。
- 對照 `spn-math` 的 `lastSymbols`，確認 feature state reset / carry 是 validation 風險點。

### Result

形成一套可面試講清楚的 validation case：不是只調輪帶數字，而是把 source of truth、runtime path、統計指標、state reset、sample size、release artifact 與 claim boundary 串起來。履歷上只保守併入 `*-math` grouped bullet，不誇大成完整 RTP owner。

## 4. Senior 追問

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

### Q6. 如果 simulation pass，上線後 RTP 還是偏，可能原因是什麼？

我會先拆四類：target / tolerance 是否對 GDD；accepted reel strip 是否真的放進 production table；runtime 是否選到同一個 RTP / game state；最後才看 sample size、randomness、feature state 或 jackpot 低機率事件造成的統計波動。

### Q7. 這和 payment consistency 有什麼相同與不同？

相同點是都要找 source of truth、state transition、failure window 和 audit evidence。不同點是 payment 是短期交易一致性，會有 DB transaction、idempotency、callback retry；slot math validation 是長期統計正確性，重點是 sample size、runtime path 一致、target / tolerance 和可重跑 artifact。

## 5. Lead / Architect 延伸追問

- 若要把這套 validation 做成正式 pipeline，你會保存哪些 artifacts？
- 要如何避免 simulation 過度依賴某次 random result？
- GDD target、code constants、production reel strip 之間如何做 traceability？
- 若 production 發現 RTP 偏差，你會怎麼回查是輪帶、feature state、runtime routing 還是統計問題？
- 這種 math module 和一般 payment consistency 的相同點 / 不同點是什麼？

### Lead 追問回答方向

如果要把它升級成正式 pipeline，我會保存：

- target source: GDD / math spec 版本。
- run config: rounds、attempts、lineBet、expectRTP、seed 或 candidate reel strip。
- output summary: Base RTP、FG trigger、Free RTP、JP hit rate、pass / fail。
- accepted artifact: 要進 production `SlotReelStripTable` 的候選輪帶 diff。
- reviewer note: 為什麼接受這組 tolerance。

我也會避免把一次 simulation pass 當作絕對正確。正式 release gate 至少要能重跑、能比對、能追溯，必要時固定 seed 或保留輸出快照。

## 6. 保守回答

如果被問「你是不是 RTP owner？」：

> 我不會把自己說成完整 RTP 策略 owner。我的 evidence 是參與多個 slot math module 的維護與驗證，能讀懂 RTP target、輪帶、simulation loop、runtime path 和 failure window，並能把這些風險整理成可驗證的 release gate。

如果被問「你有沒有真的開發過？」：

> `sph-math` 和 `spn-math` 有 Nick / `10gt12nc` 的 direct commits，包含 RTP / reel strip / simulation / JP / scatter / buy free 相關調整。我的履歷會保守寫參與多個 slot math module 維護與驗證，不會寫成我主導全部 math module。

## 7. 面試時避免踩雷

- 不要講「我設計 RTP」。
- 不要講「我負責全部遊戲數學」。
- 不要講「我做 certification」。
- 不要講改善百分比，除非後續補正式 validation report。
- 不要陷入只背 class 名稱；要回到 source of truth、runtime path、指標拆分、state reset、release gate。

## 8. 一句話收尾

> 這個 case 的重點不是我會調輪帶，而是我知道高風險 domain logic 不能只靠單次測試，要用可重跑的 simulation、拆分指標、runtime path 一致性和清楚的 claim boundary 來控風險。
