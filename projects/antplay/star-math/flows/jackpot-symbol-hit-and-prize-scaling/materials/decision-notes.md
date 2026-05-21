# Decision Notes - Jackpot Symbol Hit / Prize Scaling

## 1. 為什麼這條值得做

- Jackpot prize scaling 很接近 money correctness，適合 Senior Backend 面試語言。
- `sph-math` 與 `sdt-math` 都有 Nick / `10gt12nc` direct commits。
- 能把 slot math domain 翻成 contract consistency、balance source、scaling、rounding、observability。

## 2. Step 4 補讀 game-api 後的邊界

Step 4 已補讀 `antplay-slot-game-api` 的 SDT callback registrar、`JackpotService` 與 `GameFacade` jackpot path。這讓面試 case 可以從 math side 延伸到 runtime consistency：balance callback、result jackpot amount、force respin、balance reduce 與 jackpot record。

邊界仍要保守：

- 只確認 SDT registrar，不能說所有 jackpot game registration 都完整確認。
- game-api jackpot service path 本 Step 是 `專案存在 / code-backed` context，未掃到 Nick direct path commits，不升級成 Nick 主導 jackpot platform。
- wallet / settlement / jackpot pool owner 仍不可 claim。

## 3. 核心 owner decision

- Math module 是否應該直接依賴 jackpot pool？本 Step 判斷：不應宣稱 math 是 pool owner；math 只負責 hit / scaling / result contract。
- Balance callback unavailable 要不要回 0？本地 test 可以 0；production 若 callback 解析失敗或 balance 取不到，要有告警 / fail policy / force respin policy。
- SDT / SLC scaling 應用 `lineBet * fixedMultiBet`，這比只看 `lineBet` 更符合多倍數下注模型。
- Result contract 必須同時有 `jackpotRewardList` 與 total win aggregation，避免 display / settlement drift。
- `JackpotService#isForceRespin` 例外時回 false 是可靠性 trade-off：避免下注流程被 jackpot check 例外打斷，但可能放行高風險結果，面試可拿來談 observability 與 policy。
- `setData` 用 `betId` 去重 jackpot record，避免同一局重複扣池 / 記錄；這是 result side idempotency 的核心。

## 4. Step 4 面試收斂

這條 case 面試時不要講成「我做 jackpot 平台」，而是講：

- Math module result contract 怎麼和 runtime jackpot service 對齊。
- `fixedMultiBet` / `lineBet` / max bet scaling 為什麼是 money correctness。
- `jackpotRewardList` 是 runtime 後續 force respin、扣池與 record 的 source。
- 失敗時要看 callback parsing、balance source、unit conversion、rounding、duplicate betId 與 observability。

## 5. Step 5 Claim Gate

- 單條 flow claim gate 已完成：本 flow 只強化 `*-math` grouped bullet，不單獨變成 jackpot owner 履歷 bullet。
- SDT runtime evidence 已回填為 code-backed context；仍不標成 Nick direct jackpot service 開發。
- 不更新 05 / 08；若後續 project-level consolidation 或 rolling resume package 重包 `*-math`，再吸收本 Step 5 結論。
- 下一步回 Step 2 ranking，若繼續 `*-math`，做 `special-wild-feature-state-transform Step 3`。
