# Jackpot Symbol Hit / Prize Scaling Career Interview

## Evidence Level

- 證據層級: `真實開發過 + code-backed` / `專案存在 / code-backed`
- 掃描深度: Step 3 / Level 2 Flow 深掃
- 主要 evidence: `sph-math` JP commits、`sdt-math` JP / fixedMultiBet / debug commits、`math-core` JackpotReward contract。
- 限制: 本 Step 未深掃 game-api runtime caller、jackpot provider、wallet / settlement / pool 對帳。

## 可面試講的版本

我在 AntPlay slot math module 裡整理過 jackpot hit 和 prize scaling 這類 flow。這種功能表面上是遊戲特色，實際上很接近 money correctness：盤面或 wild 觸發後，要決定 jackpot type、查 jackpot balance，再依玩家當前下注和最大下注比例縮放，最後把 jackpot amount 放進 result contract。

以 `sph-math` 為例，盤面上 701~704 類 JP symbol 達到三顆就 hit 對應 jackpot；以 `sdt-math` / `slc-math` 為例，先由 wild 觸發，再用 `JackpotFlip` 決定 Min / Minor / Major / Grand 和前端翻牌表演。金額不是 math module 自己憑空產生，而是用 `agentId|jackpotType|currency` 查 balance，再用 `nowBet / maxBet` 做縮放。

這裡我會特別看幾個風險：`fixedMultiBet` 有沒有進 jackpot scaling、`jackpotRewardList` 有沒有跟 `betTotalWin` 一起回傳、balance callback 失敗時是歸零還是要 fail fast、以及 701~704 的 symbol / jackpot type 是否跨 module 和 provider 一致。這些問題如果沒處理好，就會變成前端顯示、玩家贏分與後端結算不一致。

## 30 秒版本

我參與過 slot math module 的 jackpot / symbol / prize scaling 類維護。重點不是只判斷有沒有中獎，而是把 jackpot type、balance callback、下注倍率縮放、取整規則與 result contract 對齊，避免 `jackpotRewardList` 和總贏分不一致。

## STAR 草稿

Situation: slot game jackpot flow 需要在 math module 中判斷命中、決定獎種、查獎池 balance，並把結果回傳給 runtime / front-end。

Task: 保守理解與整理 jackpot hit / prize scaling 的 code path，確認哪些部分可以作為履歷與面試素材，哪些不能誇大成完整 jackpot platform owner。

Action:

- 讀 `sph-math` 的 701~704 jackpot symbol hit path。
- 對照 `sdt-math` / `slc-math` 的 wild trigger、`JackpotFlip` 與 `fixedMultiBet` scaling。
- 追 `OperatorService#getJackpotBalance` 的 callback contract 與 payload 格式。
- 檢查 `math-core` 的 `AbstractBetResult.JackpotReward`，確認 reward list 如何作為回傳 contract。
- 把風險整理成 hit condition、balance source、maxBet / nowBet、rounding、reward list / total win consistency。

Result: 可形成一個 backend 面試能理解的 case：slot math jackpot 不是單純調機率，而是 high-risk result contract；要用 evidence 講清楚 money-like correctness 和 boundary。

## 可放履歷

只建議併入 grouped `*-math` bullet：

> 參與 AntPlay 多個 slot math module 維護與驗證，處理 RTP / reel strip、debug bet、fixedMultiBet、buy free / purchasable free spin、jackpot / symbol、currency 與模擬驗證調整。

## 可面試講

- Jackpot symbol / wild trigger 如何轉成 jackpot type。
- `agentId|jackpotType|currency` payload 如何作為 balance lookup contract。
- `lineBet` / `fixedMultiBet` / max bet 如何影響 jackpot amount。
- `jackpotRewardList` 和 `betTotalWin` 要一致，否則 result contract 會破。
- debug helper 為什麼要能強制 Min / Minor / Major / Grand。

## 不可誇大

- 不說主導完整 jackpot pool。
- 不說負責 wallet 入帳 / settlement。
- 不說設計 jackpot 機率策略。
- 不說完整掌握所有 `*-math` jackpot module。
- 不說 production provider callback 已完整驗證；本 Step 只掃到 math side contract。

## Step 4 待補

- 補 runtime caller / function registration evidence。
- 補 jackpot reward list 在 free game / base game 是否會漏加或重複加的風險分析。
- 補面試問答：balance callback 失敗怎麼辦、rounding 怎麼對齊、如何測 Min / Minor / Major / Grand。
