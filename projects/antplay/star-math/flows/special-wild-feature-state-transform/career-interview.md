# Special Wild Feature State Transform Career / Interview

## 0. 定位

- Flow: `special-wild-feature-state-transform`
- Step: Step 3 後的履歷 / 面試素材初版
- 證據層級: `sfm-math` 真實開發過 + code-backed；`slc-math` code-backed 補充
- 是否更新 05 / 08: 否。本 flow 只強化 `*-math` grouped bullet，不單獨新增履歷句。

## 1. 可放履歷口徑

不建議單獨寫一條「Special Wild」。可併入既有 `*-math` grouped bullet:

> 參與 AntPlay 多個 slot math module 維護與驗證，處理 RTP / reel strip、debug bet、fixedMultiBet、buy free、jackpot / symbol、currency 與特殊 feature result contract 類調整。

若空間有限，維持原本 grouped bullet 即可，不需要為本 flow 額外加字。

## 2. 30 秒說法

我在 AntPlay 的 slot math module 裡有接觸過特殊 feature 的狀態轉換。以 SFM 的 Special Wild 為例，它不是單純把某個 symbol 改成 wild，而是先在 math module 內建立 parent / child family 狀態，產生前端動畫需要的 `extraData`，最後再把盤面收斂成一般 wild 給算分。這類 flow 的重點是讓算分結果、前端顯示和後續 free game state 一致，不能讓中間 marker 或錯誤 fallback 漏到對外 contract。

## 3. 3 分鐘 STAR 初稿

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

## 4. 可面試講

- Special Wild 不是直接改 `symbols`，而是先經過 `wildState` / family marker，再收斂回一般 wild。
- `extraData` 在這裡是 result contract，支援前端動畫與 free game routing。
- `originalSymbols` / `symbols` / `extraData` 是 debug 這類 feature 的三個關鍵視角。
- `acac921` 類修正可以講 fallback 值的風險：找不到 parent 時要顯式 skip，不應默默落到有效位置。
- `slc-math` LuckyClover 可作比較：同樣是 feature state transform，但它是跨 free spin 的 sticky tracker。

## 5. 不可誇大

- 不說主導完整 Special Wild feature 設計。
- 不說主導完整 SFM 遊戲數學模型。
- 不說修復 production incident，除非 Nick 後續補 ticket / issue / 本人確認。
- 不說 Nick 主開發 LuckyClover；目前 `slc` 只作 code-backed 對照。
- 不寫改善百分比、RTP 數字或 certification 結論。

## 6. 下一步

```text
antplay *-math special-wild-feature-state-transform Step 4
```
