# Interview Notes - Jackpot Symbol Hit / Prize Scaling

## 1. 面試官可能問：Jackpot flow 最怕什麼錯？

最怕不是單純沒中獎，而是中獎後 result contract 不一致。比如 jackpot type 對錯、balance callback 查錯幣別、`fixedMultiBet` 沒進縮放、或 `jackpotRewardList` 有金額但 `betTotalWin` 沒加。這些都會造成玩家看到的獎金、前端顯示和後端結算不一致。

## 2. 面試官可能問：math module 負責 jackpot pool 嗎？

不應該誇大成 pool owner。math module 在這條 flow 裡負責 hit 判斷、獎種、下注比例縮放與 result contract；真正 jackpot pool balance、入帳與對帳應該在 runtime / wallet / provider / settlement 層。

## 3. 面試官可能問：為什麼 `fixedMultiBet` 重要？

因為同一個 lineBet 在不同 fixedMultiBet 下代表不同實際下注規模。如果 jackpot amount 用 max bet scaling，SDT / SLC 這種多倍數輪帶就不能只看 lineBet；需要用 `lineBet * fixedMultiBet` 對 `maxLineBet * maxFixedMultiBet`。

## 4. 面試官可能問：balance callback 掛了怎麼辦？

本地 test path 會回 0，但 production 不能只默默歸零。比較合理的 owner decision 是：要有明確告警、錯誤分類與 caller policy，決定這輪是否 fail fast、是否阻擋派彩、或是否進人工 reconciliation。本 Step 尚未深掃 production caller，所以不能宣稱已解完。

## 5. 面試官可能問：你怎麼測 jackpot？

可以用 debug helper 強制 Min / Minor / Major / Grand，例如 SDT 的 `haveMinJackpot`、`haveMinorJackpot`、`haveMajorJackpot`、`haveGrandJackpot`，並固定 RNG / 提高 WILD_RATE 來讓 case 可重現。測試不只看有沒有 jackpot，也要看 reward list、total win、jackpot type、前端翻牌 deck 是否一致。

## 6. 面試表述邊界

可以說：

- 我參與過 slot math module jackpot / symbol / prize scaling 類維護與驗證。
- 我能說清楚 hit condition、balance callback、fixedMultiBet scaling、rounding 與 result contract。

不要說：

- 我主導 jackpot 平台。
- 我負責 jackpot pool / wallet / settlement。
- 我設計完整 jackpot 機率模型。
