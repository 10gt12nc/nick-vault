# Career Interview - fixedMultiBet / Currency / Math-Core Compatibility

## Step 5 Reading Position

- Project: `antplay *-math`
- Flow: `fixed-multi-bet-currency-math-core-compatibility`
- Step: Step 5
- Evidence level: `真實開發過 + code-backed`
- Interview theme: slot math contract compatibility、money-like correctness、multi-module rollout trade-off
- Boundary: 可講參與維護與相容調整；不可講主導完整 slot math 平台、完整 RTP 策略或全部 `*-math` module。

這份 Step 5 的用途，是把 Step 3/4 的 code-backed 面試素材收斂成單條 flow claim gate。主軸不是「我加了幾個欄位」，而是「多個 slot math module 之間，怎麼讓下注金額語意、debug、jackpot、前端結果一致，又不一次改壞大量 module」。

## 30 秒回答

我參與過 AntPlay slot math core 與多個 math module 維護，其中一個代表案例是 fixedMultiBet / currency 相容。這類調整表面上是加參數，但實際上要確保 core contract、debug bet、module input、totalBet、jackpot scaling 和結果欄位都用同一組下注上下文，否則前端顯示、測試工具、派彩或 jackpot 會出現金額語意不一致。

## 2 分鐘回答

AntPlay 的 slot math module 很多，不可能一次強制所有 module 都改新介面，所以 `math-core` 採 default fallback：新的 `currency` / `fixedMultiBet` method 先保持舊 module 可編譯、可運行，再由有需求的 module 逐步 override。

到 module 端，像 `sdt` / `slc` 會在 factory 建立 inputData，把 `currency`、`fixedMultiBet`、RTP flag、debug RNG 這些上下文帶進一次 spin；operator service 會處理 agent override 和 currency multiplier；`AbstractSlotMath` 再用同一組上下文計算 totalBet、派彩和 jackpot multiplier。這裡最怕的不是 compile error，而是金額語意錯：例如 totalBet 有乘 fixedMultiBet，但 jackpot 沒乘；或 debug bet 沒帶 currency，導致測試和正式下注不同。

我會把這類 flow 當成 money-like correctness 來看。math module 本身沒有 DB transaction，也不做 rollback；但它的輸出會被上游用來扣款、派彩、對帳，所以它必須可重算、可驗證，而且同一筆 spin 的金額上下文必須一致。

## 5 分鐘深講版

這個案例可以拆成三個層次講。

第一層是 core contract。`math-core` 加了帶 `currency` / `fixedMultiBet` 的入口，但用 default method fallback 到舊方法。這是很典型的多 module rollout trade-off：保留 backward compatibility，降低一次改破大量遊戲 module 的風險；代價是不能只看 core 有新 method 就宣稱所有 module 都完整支援。

第二層是 module implementation。`sdt` 是比較完整的代表：factory 把 fixedMultiBet 和 currency 放進 inputData，operator service 用 currency multiplier，`AbstractSlotMath` 在 way game totalBet 裡乘 fixedMultiBet，jackpot 也用 `lineBet * fixedMultiBet` 和最大 bet 做比例縮放，最後 `SlotBetResult` 也補 fixedMultiBet 給前端核對。`slc` 可作 jackpot scaling 旁證，`sfm` 則顯示不是每個 module 的落地程度都一樣，主要是 currency multiplier 與 math-core compatibility。

第三層是 owner risk。這不是資料庫交易，但它是交易前置計算。風險不在於 code 會不會跑，而在於同一筆 bet 的 `lineBet`、currency multiplier、fixedMultiBet、totalBet、totalWin、jackpot reward 和 debug output 是否一致。只要有一層漏掉，就可能導致前端、測試、上游扣款或 jackpot 顯示的語意對不上。

## Senior 面試追問

### Q1. 為什麼 core 要用 default fallback？

因為 `*-math` module 數量很多，核心介面一改就可能影響很多遊戲。default fallback 讓舊 module 可以先保持可運行，新 module 或需求明確的 module 再 override。這是風險比較低的漸進 rollout。

要補一句 trade-off：fallback 也會造成假象，caller 可能以為 fixedMultiBet 生效，但實際 module 沒 override 就仍走舊算法，所以一定要逐 module 驗證。

### Q2. fixedMultiBet 只乘 totalBet 就好了嗎？

不夠。fixedMultiBet 至少要進 inputData、resultTemp、totalBet、jackpot multiplier、debug/test input，以及前端或 caller 可核對的 result 欄位。只乘 totalBet 會讓 jackpot、debug output 或前端顯示仍可能和實際下注語意不一致。

### Q3. currency multiplier 用 ThreadLocal 有什麼風險？

好處是不用一路傳參數，module 內很多 helper 可以讀同一個上下文。風險是同一個 thread 被 reuse 時，如果入口沒有正確 set 或 clear，就可能用到上一筆的 multiplier。所以 normal bet、free spin、debug bet、init config 等入口都要一致初始化，最好也有測試覆蓋不同 currency。

### Q4. 這算 transaction consistency 嗎？

不是 DB transaction consistency。math module 沒有扣款、commit、rollback；它是 pure calculation / contract consistency。但因為它的 result 會被上游交易拿來扣款、派彩、對帳，所以它仍然是 money-like correctness。面試時我會把責任邊界講清楚：交易補償在上游 wallet / game-api，math module 負責輸出可信、可重算、語意一致的結果。

### Q5. 如果要驗證，你會怎麼做？

我會用固定 RNG 做 deterministic 測試，組幾組 currency multiplier、fixedMultiBet、agent override、normal/freeSpin/debug path，比較 totalBet、totalWin、jackpot reward、result 欄位和前端需要的值是否一致。也會特別測 fallback module，確認它是「保守不支援」而不是誤以為支援。

## Lead / Architect 追問

### Q1. 你會把 fixedMultiBet / currency 提升到 core result contract 嗎？

我會先看上游是否需要跨遊戲統一顯示、對帳或 debug。如果只是個別遊戲前端需要，module 自己補欄位風險較低；如果上游 runtime / admin / report 都需要統一核對，才值得把 fixedMultiBet / currency 提升到 `AbstractBetResult` 或共用 result contract。否則 core contract 變大會增加所有 module 的改版成本。

### Q2. 你怎麼避免所有 module 落地程度不一致？

我不會只靠 interface。會建立 module compatibility checklist：入口是否 override、debugBet 是否帶參數、InputData 是否保存、totalBet 是否乘正確倍數、jackpot 是否同步、result 是否可核對、test 是否覆蓋。高風險 module 先做，低風險 module 保留 fallback 但明確標示支援程度。

### Q3. 如果 production 發現 jackpot 金額比例錯，你會先看哪裡？

我會先確認同一筆 spin 的 lineBet、fixedMultiBet、currency、maxBet / nowBet、jackpot balance payload 是否一致，再看 totalBet 和 jackpot multiplier 是否使用同一個下注上下文。接著用固定 RNG 或 debug bet 重現，確認是 input 漏帶、currency multiplier 錯、module fallback，還是 jackpot scaling 公式不一致。

## Step 5 Claim Gate

結論：可放履歷，但只能作為 `*-math` grouped 經驗的一部分，不單獨寫成完整 math platform owner。

## 可用履歷句

參與 AntPlay slot math core 與多個 slot math module 維護，處理 fixedMultiBet、currency、debug bet、totalBet / jackpot scaling 等相容性與驗證問題，確保遊戲數學輸出與上游下注金額語意一致。

## 不建議寫的履歷句

- 主導 AntPlay 完整 slot math 平台。
- 負責全部 `*-math` module 與完整 RTP 策略。
- 設計完整遊戲數學模型與 certification 流程。
- 負責錢包交易 rollback / reconciliation。

## 面試講法收斂

這個 case 最適合拿來展現三種能力：

- Backend contract sense: core interface 變更如何保持相容。
- Money correctness sense: 即使不是 DB transaction，也知道金額語意要一致。
- Owner trade-off: 不一次改破大量 module，而是用代表 module + checklist 漸進落地。

## 下一步

Step 5 已完成。`rtp-reel-strip-simulation-validation`、`buy-free-scatter-rtp3-result-contract`、`jackpot-symbol-hit-and-prize-scaling`、`special-wild-feature-state-transform` 也已完成到 Step 5；`*-math` contribution claim consolidation refresh 已完成。

後續 rolling resume package 也已完成；目前沒有預設下一步。
