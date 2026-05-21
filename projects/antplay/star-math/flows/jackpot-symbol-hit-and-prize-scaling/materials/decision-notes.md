# Decision Notes - Jackpot Symbol Hit / Prize Scaling

## 1. 為什麼這條值得做

- Jackpot prize scaling 很接近 money correctness，適合 Senior Backend 面試語言。
- `sph-math` 與 `sdt-math` 都有 Nick / `10gt12nc` direct commits。
- 能把 slot math domain 翻成 contract consistency、balance source、scaling、rounding、observability。

## 2. 為什麼 Step 3 先不深掃 game-api

本 Step 的目標是建立 math side flow 初版。runtime caller / `registerJackpotBalance` 屬於 Step 4 failure / consistency 補強項；若現在展開 game-api，會把單條 flow 範圍拉太大，且容易混入 jackpot platform owner 的誇大口徑。

## 3. 核心 owner decision

- Math module 是否應該直接依賴 jackpot pool？本 Step 判斷：不應宣稱 math 是 pool owner；math 只負責 hit / scaling / result contract。
- Balance callback unavailable 要不要回 0？本地 test 可以回 0；production 需要告警 / fail policy，Step 4 待補。
- SDT / SLC scaling 應用 `lineBet * fixedMultiBet`，這比只看 `lineBet` 更符合多倍數下注模型。
- Result contract 必須同時有 `jackpotRewardList` 與 total win aggregation，避免 display / settlement drift。

## 4. Step 4 建議補強方向

- 追 `registerJackpotBalance` 在 runtime / game-api 的呼叫位置。
- 檢查 jackpot amount 是否可能在 BG / FG 被漏加或重複加。
- 把 balance callback 失敗、rounding、type mapping 寫成面試問答。
- 補 `jackpotRewardList` / `betTotalWin` contract consistency 的 failure case。
