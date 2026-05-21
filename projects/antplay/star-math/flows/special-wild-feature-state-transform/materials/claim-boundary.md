# Claim Boundary

## 1. 結論

本 flow 可強化 `*-math` grouped 履歷 bullet，但不單獨新增一條正式履歷 claim。

建議狀態:

- `sfm-math`: 可作 `真實開發過 + code-backed` 的 feature state transform 面試素材。
- `slc-math`: 只作 `專案存在 / code-backed` 對照素材。
- `05` / `08`: 本輪不直接更新。

## 2. 可放履歷

若要補一句，只能併入 grouped bullet:

> 參與 AntPlay 多個 slot math module 維護與驗證，處理 RTP / reel strip、debug bet、fixedMultiBet、buy free、jackpot / symbol、currency 與特殊 feature result contract 類調整。

更保守的做法是暫不新增文字，沿用既有 grouped bullet，讓本 flow 作面試補充。

## 3. 可面試講

- 讀懂 SFM Special Wild 的 parent / child transform、`extraData`、free game type routing。
- 可講 feature state marker 為什麼要在算分前收斂成一般 wild。
- 可講 `acac921 找不到父 wild 前端卡` 的風險：missing parent 不應 fallback 到有效 reel。
- 可用 `slc` LuckyClover 對照 single-round transform 與 cross-free-spin sticky state。

## 4. 不可誇大

- 不說主導 SFM Special Wild 完整設計。
- 不說主導完整 slot math model / RTP / certification。
- 不說 Nick 主開發 LuckyClover。
- 不說完整前端動畫 owner。
- 不寫 production incident 或改善百分比，除非後續補 ticket / issue / 本人確認。

## 5. Evidence 使用方式

強 evidence:

- `sfm-math` path-specific Nick / `10gt12nc` commits。
- `acac921 找不到父 wild 前端卡`。
- `412ad07 特殊符號 間隔`。
- `15ba947 初始盤 originalSymbols`。

補充 evidence:

- `slc-math` LuckyClover code path。
- `math-core` symbol constants。

待補 evidence:

- 前端消費 `extraData` 的實際 code。
- GDD / QA / certification 文件。
- production issue / ticket 或 Nick 本人確認 incident context。

## 6. Step 5 預判

若後續進 Step 5，初步預判是:

- 不單獨更新 `05` / `08`。
- 將本 flow 保留為 `*-math` grouped bullet 的支撐 evidence。
- 面試稿可放在 `04-interview-casebook.md`，但要標明不是完整 math owner claim。
