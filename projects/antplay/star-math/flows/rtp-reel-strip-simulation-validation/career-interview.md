# RTP / 輪帶模擬與驗證 Career Interview

## 0. 定位

- Flow: `rtp-reel-strip-simulation-validation`
- Status: Step 5 / 單條 flow claim gate 已完成
- 證據層級: `真實開發過 + code-backed` / `專案存在 / code-backed`
- 來源: `sph-math`、`spn-math` 本地 code / git history
- 注意: 這是單條 flow 的 claim gate；正式履歷仍只併入 `*-math` grouped bullet，不獨立寫成 RTP owner。

## 1. 可以怎麼講

30 秒版本：

> 我參與過 AntPlay 多個 slot math module 的維護與驗證，其中一類是 RTP / reel strip simulation validation。這類不是線上交易 API，但會影響長期派彩正確性。我會看 target / tolerance、symbol ratio、candidate reel strip、simulation rounds，並拆 Base RTP、free trigger、Free RTP 和 Jackpot hit rate，確認 simulation 走的是實際 `math.spin` path。

3 分鐘版本：

> Slot math 的輪帶調整不能只看單次結果，因為一個 symbol ratio 或 scatter plan 會同時影響 Base RTP、免費局觸發率、Free RTP、buy free 體感和 Jackpot hit rate。以 `sph-math` 為例，模擬流程會從 `RTPConstants` 讀目標與 tolerance，再從 `Ratio_RTP_*` 產候選輪帶，放到 `RTP_REEL_STRIP_OPTIMIZER`，用 `P21008SlotMath` 的 `spin` 跑大量 rounds。結果會拆成 Base RTP、FG trigger、Free RTP、JP hit rate 和目標區間比對，不在範圍內就繼續嘗試下一組。
>
> 我會特別注意 validation path 是否走真實 runtime code、是否只看總 RTP、feature state 有沒有 reset，例如 `spn-math` 的 `lastSymbols`。所以我不把自己說成完整 RTP 策略 owner，但可以說我參與過 slot math module 的維護與驗證，能把高風險 domain logic 的 source of truth、runtime path、統計指標與 release gate 講清楚。

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

## 5. Step 5 Claim Gate

### 5.1 判定

這條 flow 可以強化既有 `*-math` grouped 履歷 bullet，也可以作為面試時的 high-risk domain validation case。它不應獨立寫成「RTP owner」或「完整遊戲數學模型 owner」。

| Claim | 判定 | 安全說法 |
| --- | --- | --- |
| 履歷主成果 | 可用，但只併入 grouped bullet | 參與 AntPlay 多個 slot math module 維護與驗證，處理 RTP / reel strip、simulation validation、buy free / scatter、jackpot / symbol 與 fixedMultiBet / currency 類調整 |
| 面試案例 | 可用，且很有差異化 | 我參與 / 維護 / 驗證過 slot math module 的 RTP / reel strip simulation validation，能把 target、tolerance、runtime path、state reset 與 release gate 講清楚 |
| 個人 ownership | 保守 | 我不是完整 RTP 策略 owner，但有 direct commits 與 code-backed analysis 支撐我參與過維護與驗證 |
| 05 / 08 更新 | 本輪不直接更新 | 後續若重整履歷，可把本 flow 作為 `*-math` grouped bullet 的 evidence |

### 5.2 可放履歷

建議只放在 `*-math` grouped bullet，不單獨拆成一條：

> 參與 AntPlay 多個 slot math module 維護與驗證，處理 RTP / reel strip、simulation validation、buy free / scatter、jackpot / symbol 與 fixedMultiBet / currency 類調整。

若職缺偏遊戲後端 / 遊戲平台，可在面試或自傳短補：

> 參與 slot math module RTP / reel strip 模擬驗證與 high-risk domain logic 檢查，協助確認 Base RTP、Free trigger、Free RTP、Jackpot hit rate 與 runtime math path 一致性。

### 5.3 不可寫

- 不寫「我設計 RTP」。
- 不寫「我主導完整遊戲數學模型」。
- 不寫「我負責 certification」。
- 不寫「我建立完整 simulator platform」。
- 不寫「我完整負責全部 `*-math` repo」。
- 不寫改善百分比，除非後續補 GDD、validation report、ticket 或監控 evidence。

### 5.4 後續狀態

`fixed-multi-bet-currency-math-core-compatibility` 與本 flow 都已完成 Step 5。後續 Rank 3 到 Rank 5、`*-math` contribution claim consolidation refresh 與 rolling resume package 也已完成；目前沒有預設下一步。
