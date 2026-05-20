# Career Interview - fixedMultiBet / Currency / Math-Core Compatibility

## Evidence Level

- `真實開發過 + code-backed`
- 依據: Nick / `10gt12nc` 在 `math-core`、`sdt-math`、`sfm-math`、`slc-math` 有相關 direct commits；本輪逐行讀過代表 code path。
- 限制: 部分內網遠端未確認最新狀態；這份先作 Step 3 單條 flow 面試素材，不直接更新 05/08。

## 30 秒說法

我參與過 AntPlay slot math core 與多個 math module 的維護，其中一條比較有代表性的 flow 是 fixedMultiBet / currency 相容。這不是單純加欄位，而是要讓 core interface、debug bet、module input、totalBet、jackpot scaling 和前端結果欄位一致，避免「下注額顯示、派彩、jackpot 或測試工具」用到不同倍數。

## 2 分鐘說法

這個 flow 的背景是 slot math module 很多，不可能一次要求所有 module 同步重構，所以 core contract 用 default method 保持相容；新 module 或有需求的 module 再 override currency / fixedMultiBet 版本。

實作上，`math-core` 先提供帶 currency / fixedMultiBet 的入口與 debug VO。到 module 端，像 `sdt` / `slc` 會在 factory 建 inputData，帶入 currency、fixedMultiBet、RTP flag、debug RNG；operator service 負責 currency multiplier 和 agent override；`AbstractSlotMath` 再用同一組上下文算 totalBet、win 與 jackpot multiplier。這裡要避免的不是 compile error，而是金額語意錯誤：例如 totalBet 有乘 fixedMultiBet，但 jackpot 沒乘，或 debug bet 沒帶 currency，測試就會失真。

我會把這種 flow 當成 money-like correctness 來看，雖然 math module 本身不處理 DB transaction，但它提供上游下注扣款、派彩與對帳需要相信的計算結果。

## 可放履歷句

參與 AntPlay slot math core 與多個 slot math module 維護，處理 fixedMultiBet、currency、debug bet、totalBet / jackpot scaling 等相容性與驗證問題。

## 可追問重點

- 為什麼 core 要用 default fallback，而不是一次改破所有 module？
- fixedMultiBet 應該乘在哪些地方？
- currency multiplier 用 ThreadLocal 有什麼風險？
- debug bet 如果沒有帶 currency / fixedMultiBet，會造成什麼問題？
- 這種 flow 和真正的錢包交易 transaction boundary 差在哪？

## 保守邊界

這個案例可以講「參與維護與相容調整」，不能講「主導完整 slot math 平台」、「設計完整 RTP 策略」、「負責全部 math module certification」。
