# Special Wild Feature State Transform Career / Interview

## 0. 定位

- Flow: `special-wild-feature-state-transform`
- Step: Step 5 claim gate
- 證據層級: `sfm-math` 真實開發過 + code-backed；`slc-math` code-backed 補充
- 是否更新 05 / 08: 否。本 flow 只強化 `*-math` grouped bullet，不單獨新增履歷句。

## 1. 可放履歷口徑

不建議單獨寫一條「Special Wild」。可併入既有 `*-math` grouped bullet:

> 參與 AntPlay 多個 slot math module 維護與驗證，處理 RTP / reel strip、debug bet、fixedMultiBet、buy free、jackpot / symbol、currency 與特殊 feature result contract 類調整。

若空間有限，維持原本 grouped bullet 即可，不需要為本 flow 額外加字。

## 2. 30 秒說法

我在 AntPlay 的 slot math module 裡有接觸過特殊 feature 的狀態轉換。以 SFM 的 Special Wild 為例，它不是單純把某個 symbol 改成 wild，而是先在 math module 內建立 parent / child family 狀態，產生前端動畫需要的 `extraData`，最後再把盤面收斂成一般 wild 給算分。這類 flow 的重點是讓算分結果、前端顯示和後續 free game state 一致，不能讓中間 marker 或錯誤 fallback 漏到對外 contract。

## 3. 3 分鐘 STAR 版本

情境:

Slot game 裡有一些特殊 wild feature，會在盤面上觸發 parent wild，並把其他位置轉成 child wild。這會同時影響玩家看到的動畫、實際算分盤面，以及 free game 的狀態選擇。

任務:

我要確認這類 feature transform 的 code path，知道哪些狀態是只存在於 math module 內部，哪些欄位會變成對外 result contract，並把 bug 風險收斂在正確邊界。

行動:

- 讀 `AbstractSlotMath#initTable` / `changeWildState`，確認 base game / free game 會選不同 special wild type。
- 拆 `markSpecialWilds`、`handleSpecialWildType`、`addExtraDataForWilds`，確認 parent / child family 如何產生。
- 確認 transform 後會用 `revertSpecialWildsToNormal` 收斂成一般 wild，避免中間 marker 進入算分。
- 追到 `extraData.launchIndex` / `transformIndex` / `gameType`，確認前端動畫與 free game routing 都依賴這份 contract。
- 從 `找不到父 wild 前端卡` 的修正看到，錯誤 fallback 不能把找不到 parent 當成 reel 0，否則會造成前端卡住或顯示錯誤。

結果:

這條 flow 讓我能把 slot math feature 翻成後端可理解的 contract 問題：內部 state 可以複雜，但對外的 scoring result、display payload、free game state 必須一致，而且 fallback / debug replay 要能支撐線上排查。

## 4. Senior / Lead 版回答

我會把這條 Special Wild 當成 result contract consistency 的問題來講。SFM 的特殊 wild 會先在 math module 內建立 parent / child family state，這是內部 domain state；但算分前必須收斂回一般 `W=50`，這是 scoring contract；同時前端還需要 `extraData.launchIndex` / `transformIndex` 來做動畫，free game routing 又會讀 `extraData[0].gameType`，所以它不是單純的 symbol transform。

這類 flow 最怕的是三個結果不一致：玩家看到的盤面、實際算分盤面、下一段 free game state。像 `找不到父 wild 前端卡` 這個 direct evidence，就反映 fallback 值不能亂給。找不到 parent 時如果默默當成 reel 0，系統會產生一個看似合法但實際錯誤的 display contract；改成 `-1` 並 skip，雖然保守，但可以避免把 unknown 變成錯誤有效值。

所以我在面試會強調：slot math feature 不是只看中獎，而是要把 internal marker、scoring symbol、display payload、debug replay 邊界切清楚。這也是我把遊戲數學 feature 轉成後端 contract / state consistency 來理解的方式。

## 5. 可面試講

- Special Wild 不是直接改 `symbols`，而是先經過 `wildState` / family marker，再收斂回一般 wild。
- `extraData` 在這裡是 result contract，支援前端動畫與 free game routing。
- `originalSymbols` / `symbols` / `extraData` 是 debug 這類 feature 的三個關鍵視角。
- `acac921` 類修正可以講 fallback 值的風險：找不到 parent 時要顯式 skip，不應默默落到有效位置。
- `slc-math` LuckyClover 可作比較：同樣是 feature state transform，但它是跨 free spin 的 sticky tracker。

## 6. Lead / Architect 追問準備

### Q1. 為什麼這不是單純前端動畫資料？

因為 `extraData` 同時牽涉 display contract 和 free game routing。前端需要 launch / transform indexes，但 factory 也會依 `gameType` 進下一段 free game state；所以它不是可有可無的 log，而是跨 math result、front-end、state transition 的 contract。

### Q2. 如果 `symbols` 和 `extraData` 不一致，最壞會怎樣？

玩家看到的動畫、實際算分和客服查詢會對不上。金額可能未必立刻錯，但玩家體感、debug replay 和 dispute 排查會變困難。若 free game type 也錯，還會走錯後續 state。

### Q3. 為什麼 missing parent 不應 fallback 到 reel 0？

reel 0 是有效 domain value，unknown 不能用有效值表示。這會讓錯誤狀態變成合法 transform，造成前端卡住或錯位。用 `-1` 並 skip 比較保守，也讓錯誤邊界清楚。

### Q4. 這和 `slc` LuckyClover 有什麼 owner decision 差異？

SFM 是單局 transform，最後收斂回 `W=50`；SLC LuckyClover 是跨 free spin 的 sticky board state，state lifetime 更長。前者重點是 marker 不外漏，後者重點是 tracker snapshot / state owner / replay。

### Q5. 如果要補 production-grade observability，你會補什麼？

至少要能用 round id 查到 `originalSymbols`、final `symbols`、`extraData`、game state、RNG / debug input 或 result snapshot。若有前端卡住類問題，要能比對 math index mapping 與前端 animation mapping。

## 7. 不可誇大

- 不說主導完整 Special Wild feature 設計。
- 不說主導完整 SFM 遊戲數學模型。
- 不說修復 production incident，除非 Nick 後續補 ticket / issue / 本人確認。
- 不說 Nick 主開發 LuckyClover；目前 `slc` 只作 code-backed 對照。
- 不寫改善百分比、RTP 數字或 certification 結論。

## 8. Step 5 Claim Gate

結論: 本 flow 可面試講、可強化 `*-math` grouped bullet，但不單獨更新正式履歷。

可放履歷:

- 只沿用或微調既有 grouped bullet。
- 不新增「Special Wild owner」或「完整 feature 設計」獨立條目。

可面試講:

- SFM Special Wild 的 parent / child transform。
- `extraData` 作為 front-end display contract 與 free game routing input。
- `acac921 找不到父 wild 前端卡` 代表 unknown fallback 不能用有效 reel 值。
- `slc` LuckyClover 作 state lifetime 對照，但不寫 Nick 主開發。

不更新 `05` / `08`:

- 原因是這是單條 flow Step 5，不是 project-level final consolidation。
- `*-math` rolling consolidation 已有 grouped bullet；本 flow 只作後續回填 evidence。

## 9. 下一步

後續 `antplay-slot-game-api`、`antplay-slot-game-job`、`antplay-slot-admin-api` 與 AntPlay system map v1 也已收斂；目前沒有預設下一步。
