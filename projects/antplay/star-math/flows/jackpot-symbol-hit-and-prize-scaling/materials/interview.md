# Interview Notes - Jackpot Symbol Hit / Prize Scaling

## 1. 面試官可能問：Jackpot flow 最怕什麼錯？

最怕不是單純沒中獎，而是中獎後 result contract 不一致。比如 jackpot type 對錯、balance callback 查錯幣別、`fixedMultiBet` 沒進縮放、或 `jackpotRewardList` 有金額但 `betTotalWin` 沒加。這些都會造成玩家看到的獎金、前端顯示和後端結算不一致。

## 2. 面試官可能問：math module 負責 jackpot pool 嗎？

不應該誇大成 pool owner。math module 在這條 flow 裡負責 hit 判斷、獎種、下注比例縮放與 result contract；真正 jackpot pool balance、入帳與對帳應該在 runtime / wallet / provider / settlement 層。

## 3. 面試官可能問：為什麼 `fixedMultiBet` 重要？

因為同一個 lineBet 在不同 fixedMultiBet 下代表不同實際下注規模。如果 jackpot amount 用 max bet scaling，SDT / SLC 這種多倍數輪帶就不能只看 lineBet；需要用 `lineBet * fixedMultiBet` 對 `maxLineBet * maxFixedMultiBet`。

## 4. 面試官可能問：balance callback 掛了怎麼辦？

本地 test path 會回 0，但 production 不能只默默歸零。Step 4 補讀到 SDT runtime caller 會把 payload 解析後交給 `JackpotService#getBalance`；如果 payload 解析失敗會回 null，math side 最終可能走 0。面試時我會說這裡需要明確 policy：是 fail fast、force respin、阻擋派彩、告警，還是進人工 reconciliation，不能只靠 log。

## 5. 面試官可能問：你怎麼測 jackpot？

可以用 debug helper 強制 Min / Minor / Major / Grand，例如 SDT 的 `haveMinJackpot`、`haveMinorJackpot`、`haveMajorJackpot`、`haveGrandJackpot`，並固定 RNG / 提高 WILD_RATE 來讓 case 可重現。測試不只看有沒有 jackpot，也要看 reward list、total win、jackpot type、前端翻牌 deck 是否一致。

## 6. 面試表述邊界

可以說：

- 我參與過 slot math module jackpot / symbol / prize scaling 類維護與驗證。
- 我能說清楚 hit condition、balance callback、fixedMultiBet scaling、rounding 與 result contract。
- 我能說清楚 runtime 如何從 result 取 `jackpotRewardList`，做 force respin、扣池與 record。
- 這條已完成 Step 5 claim gate，只併入 `*-math` grouped 履歷 bullet，不單獨包裝成 jackpot 平台 owner。

不要說：

- 我主導 jackpot 平台。
- 我負責 jackpot pool / wallet / settlement。
- 我設計完整 jackpot 機率模型。
- 我直接開發 game-api jackpot service / 全部 jackpot runtime callback。

## 7. 3 分鐘講法

我會把 jackpot flow 當成 result contract consistency 來講。Math side 先判斷 jackpot hit：SPH 是盤面上 701 到 704 的 JP symbol 達到三顆；SDT / SLC 則是 FW 觸發後用 JackpotFlip 決定 Min / Minor / Major / Grand。中獎後 math 會用 `agentId|jackpotType|currency` 查 balance，並依當前下注和最大下注比例縮放，SDT / SLC 這裡一定要把 `fixedMultiBet` 算進去。

Step 4 補讀 runtime 後，我會再接著講：game-api 會在啟動後把 SDT 的 jackpot balance callback 註冊進 math service；拿到 math result 後，`JackpotService` 會從 `jackpotRewardList` 累加 jackpot amount，做 force respin 檢查，最後依 jackpot type 分組扣減 balance、寫 jackpot record。

所以這條不是「有中獎就給錢」而已。真正風險在 type mapping、currency、單位、rounding、fixedMultiBet、reward list / total win 是否一致，以及 force respin 例外時的策略。我的 claim 邊界是：可以講 jackpot / symbol / scaling / result contract 維護與驗證，不講自己主導 jackpot pool 或 wallet settlement。

## 8. Lead / Architect 追問

Q: 如果 `registerJackpotBalance` 沒註冊會怎樣？

A: 本地 test 會回 0，但 production 應該要有明確告警和 policy。因為這不是一般 feature degrade，而是獎池金額 source 不存在，應評估是否 fail fast、force respin 或阻擋派彩。

Q: 為什麼 `jackpotRewardList` 重要？

A: 它不只是前端顯示欄位。runtime 會用它累加 jackpot amount、依 jackpot type 分組、做 force respin 和扣池 / record。如果 list 和 `betTotalWin` 不一致，就會造成顯示、派彩和獎池記錄 drift。

Q: 為什麼 force respin 需要小心？

A: 它是在 jackpot amount 超過 dark-pool maxWin 或 type balance 時阻擋結果的最後一道關卡。但如果檢查例外被 catch 後回 false，流程會偏向放行，所以需要告警、metric 和人工追查。

Q: 如何驗證 Min / Minor / Major / Grand？

A: 不能只靠隨機 hit。SDT / SLC debug helper 會提高 WILD_RATE、覆寫機率，讓指定 jackpot type 可重現，再檢查 `jackpotShow`、`jackpotType`、`jackpotRewardList`、`betTotalWin` 和 runtime record。
