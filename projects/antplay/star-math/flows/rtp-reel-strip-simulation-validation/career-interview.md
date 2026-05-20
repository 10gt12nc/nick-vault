# RTP / 輪帶模擬與驗證 Career Interview

## 0. 定位

- Flow: `rtp-reel-strip-simulation-validation`
- Status: Step 3 完成後的初版面試素材
- 證據層級: `真實開發過 + code-backed` / `專案存在 / code-backed`
- 來源: `sph-math`、`spn-math` 本地 code / git history
- 注意: 這不是 Step 4 正式面試稿；Step 4 會再收斂成完整 30 秒 / 3 分鐘 / 追問版本。

## 1. 可以怎麼講

30 秒版本：

> 我參與過 AntPlay 多個 slot math module 的維護與驗證，其中一類是 RTP / reel strip simulation。這類不是線上交易 API，但會影響長期派彩正確性。我會看 RTP target、symbol ratio、candidate reel strip、simulation rounds、Base RTP、free trigger、Free RTP 和 Jackpot hit rate，並確認 simulation 走的是實際 `math.spin` path，而不是另外手寫一套驗證邏輯。

2 分鐘版本：

> Slot math 的輪帶調整不能只看單次結果，因為一個 symbol ratio 或 scatter plan 會同時影響 Base RTP、免費局觸發率、Free RTP、buy free 體感和 Jackpot hit rate。以 `sph-math` 為例，模擬流程會從 `RTPConstants` 讀目標與 tolerance，再從 `Ratio_RTP_*` 產候選輪帶，放到 `RTP_REEL_STRIP_OPTIMIZER`，用 `P21008SlotMath` 的 `spin` 跑大量 rounds。結果會和目標區間比對，不在範圍內就繼續嘗試下一組。這讓我面對高風險 domain logic 時，不只看功能能不能跑，而是會拆 source of truth、runtime path、統計指標與 release gate。

## 2. Senior 能力點

- Domain correctness: RTP / reel strip / free trigger / jackpot hit rate 都屬於長期派彩正確性。
- Runtime path consistency: simulation 不能和 production math engine 分叉。
- State awareness: SPN 的 `lastSymbols` 顯示 feature state 會跨 spin 影響驗證，必須 reset / carry 正確。
- Validation design: 不只看總 RTP，要拆 Base / Free / trigger / special event。
- Claim boundary: 能講參與維護與驗證，不誇大成完整 RTP 策略 owner。

## 3. 可安全履歷 bullet

可併入 `*-math` grouped bullet，不建議單獨獨立成一條完整成果：

> 參與 AntPlay 多個 slot math module 維護與驗證，處理 RTP / reel strip、simulation validation、buy free / scatter、jackpot / symbol 與 fixedMultiBet / currency 類調整。

更保守版本：

> 參與 slot math module 維護，協助 RTP / reel strip 模擬驗證、debug bet 與跨 module result contract 調整。

## 4. 不可誇大

- 不說「我設計 RTP」。
- 不說「我主導遊戲數學模型」。
- 不說「我負責 certification」。
- 不說「我完整負責全部 `*-math` repo」。
- 不說「我保證改善多少 RTP / hit rate」，除非後續補 GDD / validation report / metric evidence。

## 5. 待 Step 4 收斂

Step 4 要把這條整理成：

- 30 秒摘要。
- 3 分鐘正式面試 case。
- 5 個 Senior 追問。
- Lead / Architect 追問。
- 面對「你不是 math owner 吧？」的保守回答。
