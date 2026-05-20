# Interview Q&A - fixedMultiBet / Currency / Math-Core Compatibility

## Step 4 Goal

本檔是 Step 4 面試稿細節。主讀文件仍是同層 `career-interview.md`；本檔放更完整的追問與答法。

證據層級：`真實開發過 + code-backed`。回答時要保守：講參與維護、相容調整、驗證與風險判斷，不講完整 slot math owner。

## Opening Pitch

我會把這個案例定位成「多 module math contract 相容與 money-like correctness」。

當時 slot math module 很多，下注金額除了 `lineBet`，還會受到 currency multiplier 和 fixedMultiBet 影響。這種改動如果只在某一層加欄位，很容易讓 totalBet、debug bet、jackpot scaling 或前端 result 對不起來。所以重點是讓 core contract、module input、operator service、math calculation、result output 使用同一組下注上下文。

這不是 DB transaction，但它影響上游扣款和派彩依據，所以我會用交易正確性的標準去看它。

## Interview Flow

### 1. 先講背景

- `*-math` module 很多，不能一次改破。
- core contract 需要支援 currency / fixedMultiBet，但也要保留舊 module 相容。
- 某些 module 需要把 fixedMultiBet 用在 totalBet、jackpot、debug 和前端 result。

### 2. 再講做法

- `math-core` 提供新 method 與 debug VO 欄位。
- module factory 建 inputData。
- operator service 管 currency multiplier / override。
- `AbstractSlotMath` 計算 totalBet / win / jackpot。
- result 補可核對欄位。

### 3. 最後講風險與取捨

- default fallback 降低 rollout 風險，但造成支援程度不一致。
- ThreadLocal 降低傳參成本，但要注意 thread reuse。
- fixedMultiBet 不能只看 totalBet，要看 jackpot / debug / result。
- math module 不做 rollback，但輸出要支撐上游交易。

## Common Questions

### Q1. 這個 flow 最核心的 bug risk 是什麼？

不是 code 跑不起來，而是金額語意不一致。例如前端顯示 fixedMultiBet 已生效，totalBet 也乘了，但 jackpot multiplier 沒乘；或 debugBet 沒帶 currency，測出來的 result 和正式下注不同。這種問題通常不會立刻 compile fail，但會造成對帳或測試判斷錯誤。

### Q2. 你怎麼確認這不是單純加欄位？

我會追欄位從 caller contract 到 module input，再到 calculation 和 result output。只加 `DebugBetVO.fixedMultiBet` 不代表成功；要看 `getTotalBet`、jackpot scaling、debug path、前端 result 欄位是否都吃到同一組上下文。

### Q3. 為什麼這條可以展現 Senior backend 能力？

因為它不是單一 class 改動，而是跨 core contract、多 module 相容、測試/debug、金額語意、rollout trade-off。Senior 不只要會改到能跑，還要知道哪些 module 會被影響、哪些舊路徑會 fallback、哪些結果會被上游交易依賴。

### Q4. 你有主導全部 math module 嗎？

不能這樣講。比較準確是：我參與 slot math core 與多個 math module 的維護與相容調整，有 code-backed evidence；這條 flow 深掃了 `math-core`、`sdt-math`、`sfm-math`、`slc-math` 的代表 path。全部 71 個 `*-math` repo 沒有逐檔逐行全掃，所以不講全部 owner。

### Q5. 如果面試官追問 production incident，你怎麼回答？

如果沒有具體 incident evidence，不要硬編。我會說這份整理是 code-backed 的 flow analysis 與實際維護 evidence，未補 production issue / MR context。可以講「這類問題的 production 風險會是 totalBet、jackpot、debug 或 result 不一致」，但不宣稱曾處理某個 production incident。

### Q6. 如果要求你現在設計更好的版本，你會怎麼做？

我會先定義 compatibility matrix，而不是直接大改：

- core method 是否支援 currency / fixedMultiBet。
- module 是否 override。
- factory 是否建 inputData。
- calculation 是否同步 totalBet / jackpot。
- result 是否能讓 caller 核對。
- tests 是否覆蓋 normal / freeSpin / debug。

若跨遊戲 caller 真的需要統一核對，才考慮把 currency / fixedMultiBet 提升到 core result；否則先避免擴大 core schema。

## 強回答句

- 「這裡的重點不是欄位，而是同一筆 spin 的下注上下文要一致。」
- 「default fallback 是 rollout 安全網，不是完整支援的證明。」
- 「math module 沒有 DB transaction，但它輸出的 amount 會進入上游交易語意，所以仍然要用 money correctness 來檢查。」
- 「我會用 compatibility checklist 管多 module 落地程度，而不是假設 interface 一改所有 module 都完成。」

## 弱回答要避免

- 「就是加 fixedMultiBet 欄位。」
- 「所有 math module 都支援了。」
- 「我負責整個 slot math 平台。」
- 「這就是交易 rollback flow。」
- 「ThreadLocal 沒風險。」

## Evidence-backed Claims

| Claim | Evidence level | 面試用法 |
| --- | --- | --- |
| `math-core` 有 debugBet / fixedMultiBet / currency contract 調整 | 真實開發過 + code-backed | 可講 core contract compatibility |
| `sdt-math` 有 fixedMultiBet 給前端、currency / debug bet 修正 | 真實開發過 + code-backed | 可講完整代表 module |
| `sfm-math` 有 math-core compatibility 修正 | 真實開發過 + code-backed | 可講 module 落地差異 |
| `slc-math` 可作 jackpot scaling 旁證 | code-backed + direct commits | 可講相同 pattern 旁證 |
| 上游 game-api 如何呼叫 | 待確認 | 不能講已深掃 |
| production incident / ticket | 待確認 | 不能硬編 |

## 最後收束

面試最後可以這樣收：

> 這個案例我會保守定位成參與 slot math core / module 相容維護。它對我的價值是讓我更清楚 backend contract 改版不只是 method signature，而是要看上下游金額語意、debug path、結果可核對性，以及多 module 漸進 rollout 的風險。
