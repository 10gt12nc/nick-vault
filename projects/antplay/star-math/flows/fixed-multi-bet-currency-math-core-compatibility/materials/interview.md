# Interview Q&A - fixedMultiBet / Currency / Math-Core Compatibility

## Q1. 這條 flow 解決什麼問題？

它解決 slot math module 在多幣別與固定倍數玩法下的金額語意一致性。下注不只是 `lineBet`，還可能要乘 currency multiplier 與 fixedMultiBet；如果 core contract、debug bet、totalBet、jackpot、result 欄位沒有一致傳遞，就會出現測試和正式下注不同、前端顯示和後端計算不同、或 jackpot 派彩比例錯誤。

## Q2. 為什麼不是直接把所有 module 都強制改掉？

`*-math` repo 很多，強制一次改介面風險高。core 用 default fallback 可以先保持相容，讓舊 module 不壞；真正需要 fixedMultiBet / currency 的 module 再逐步 override。缺點是需要逐 module 驗證，不能只看 core 有新 method 就以為全部支援。

## Q3. fixedMultiBet 應該出現在哪些地方？

至少要出現在 inputData、totalBet 計算、需要按 bet 比例縮放的 jackpot、debug / test input，以及前端或 caller 需要核對的 result 欄位。只放在 VO 或只放在 totalBet 都不完整。

## Q4. currency multiplier 用 ThreadLocal 的風險是什麼？

優點是不用一路傳參數；風險是 thread reuse 或某些入口忘記 set 時，可能沿用錯誤 multiplier。所以 normal bet、free spin、debug bet、init config 這些入口都要檢查初始化邏輯。

## Q5. 這和 DB transaction 有關嗎？

math module 本身不是 DB transaction boundary，不負責扣款、補償或 rollback。但它的 result 是上游交易的前置依據，所以它有 money-like correctness：totalBet、totalWin、jackpot reward 的語意必須一致且可重算。

## Q6. 你會怎麼驗證？

我會先用固定 RNG 驗證 deterministic result，再針對不同 currency multiplier、不同 fixedMultiBet、不同 agent override、normal/freeSpin/debug path 比較 totalBet、totalWin、jackpot reward 與 result 欄位。最後再確認 init config 與前端可見欄位能對上。

## Q7. 可以放履歷嗎？

可以保守放「參與 slot math core / 多個 math module 維護，處理 fixedMultiBet、currency、debug bet、totalBet / jackpot scaling 相容性」。不能寫成「主導完整 slot math 平台」或「負責完整 RTP 策略」。
