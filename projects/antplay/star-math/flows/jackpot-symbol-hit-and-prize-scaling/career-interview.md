# Jackpot Symbol Hit / Prize Scaling Career Interview

## Evidence Level

- 證據層級: `真實開發過 + code-backed` / `專案存在 / code-backed`
- 掃描深度: Step 4 / 面試 case 已完成
- 主要 evidence: `sph-math` JP commits、`sdt-math` JP / fixedMultiBet / debug commits、`math-core` JackpotReward contract、`antplay-slot-game-api` SDT callback registrar / jackpot service。
- 限制: 本 Step 只補到 SDT runtime callback 與 jackpot service path；未完整深掃所有 jackpot game registration、wallet / settlement / pool 對帳。

## 可面試講的版本

我在 AntPlay slot math module 裡整理過 jackpot hit 和 prize scaling 這類 flow。這種功能表面上是遊戲特色，實際上很接近 money correctness：盤面或 wild 觸發後，要決定 jackpot type、查 jackpot balance，再依玩家當前下注和最大下注比例縮放，最後把 jackpot amount 放進 result contract。

以 `sph-math` 為例，盤面上 701~704 類 JP symbol 達到三顆就 hit 對應 jackpot；以 `sdt-math` / `slc-math` 為例，先由 wild 觸發，再用 `JackpotFlip` 決定 Min / Minor / Major / Grand 和前端翻牌表演。金額不是 math module 自己憑空產生，而是用 `agentId|jackpotType|currency` 查 balance，再用 `nowBet / maxBet` 做縮放。

這裡我會特別看幾個風險：`fixedMultiBet` 有沒有進 jackpot scaling、`jackpotRewardList` 有沒有跟 `betTotalWin` 一起回傳、balance callback 失敗時是歸零還是要 fail fast、以及 701~704 的 symbol / jackpot type 是否跨 module 和 provider 一致。Step 4 補讀到 game-api 之後，也能把 runtime side 串進來：SDT 會註冊 balance callback，runtime 會從 result 累加 jackpot amount、做 force respin 判斷，最後按 jackpot type 分組扣池與記錄。

## 30 秒版本

我參與過 slot math module 的 jackpot / symbol / prize scaling 類維護。重點不是只判斷有沒有中獎，而是把 jackpot type、balance callback、下注倍率縮放、取整規則、force respin 與 result contract 對齊，避免 `jackpotRewardList`、總贏分與獎池扣減不一致。

## STAR 草稿

Situation: slot game jackpot flow 需要在 math module 中判斷命中、決定獎種、查獎池 balance，並把結果回傳給 runtime / front-end。

Task: 保守理解與整理 jackpot hit / prize scaling 的 code path，確認哪些部分可以作為履歷與面試素材，哪些不能誇大成完整 jackpot platform owner。

Action:

- 讀 `sph-math` 的 701~704 jackpot symbol hit path。
- 對照 `sdt-math` / `slc-math` 的 wild trigger、`JackpotFlip` 與 `fixedMultiBet` scaling。
- 追 `OperatorService#getJackpotBalance` 的 callback contract 與 payload 格式。
- 補讀 `antplay-slot-game-api` 的 SDT registrar、`JackpotService#getResultJackpotAmountCentUnit`、force respin 與 jackpot record / balance reduce path。
- 檢查 `math-core` 的 `AbstractBetResult.JackpotReward`，確認 reward list 如何作為回傳 contract。
- 把風險整理成 hit condition、balance source、maxBet / nowBet、rounding、reward list / total win consistency。

Result: 可形成一個 backend 面試能理解的 case：slot math jackpot 不是單純調機率，而是 high-risk result contract；要用 evidence 講清楚 money-like correctness、runtime consistency、force respin 與 claim boundary。

## 可放履歷

只建議併入 grouped `*-math` bullet：

> 參與 AntPlay 多個 slot math module 維護與驗證，處理 RTP / reel strip、debug bet、fixedMultiBet、buy free / purchasable free spin、jackpot / symbol、currency 與模擬驗證調整。

## 可面試講

- Jackpot symbol / wild trigger 如何轉成 jackpot type。
- `agentId|jackpotType|currency` payload 如何作為 balance lookup contract。
- `lineBet` / `fixedMultiBet` / max bet 如何影響 jackpot amount。
- `jackpotRewardList` 和 `betTotalWin` 要一致，否則 result contract 會破。
- game-api 如何從 `jackpotRewardList` 累加 jackpot amount，並做 force respin / balance reduce / record。
- debug helper 為什麼要能強制 Min / Minor / Major / Grand。

## 不可誇大

- 不說主導完整 jackpot pool。
- 不說負責 wallet 入帳 / settlement。
- 不說設計 jackpot 機率策略。
- 不說完整掌握所有 `*-math` jackpot module。
- 不說所有 jackpot game runtime callback 都已完整驗證；本 Step 只補到 SDT registrar 與 jackpot service path。

## Step 5 待補

- 單條 flow claim gate：確認這條是否只併入 `*-math` grouped bullet。
- 檢查是否回填 project-level contribution consolidation，仍避免單獨寫 jackpot owner。
- 標明 SDT runtime caller 已補，SPH / SLC runtime registration 未確認。
