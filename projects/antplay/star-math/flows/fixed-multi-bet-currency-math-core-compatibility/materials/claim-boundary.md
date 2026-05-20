# Claim Boundary - fixedMultiBet / Currency / Math-Core Compatibility

## 可放履歷

- 參與 AntPlay slot math core 與多個 slot math module 維護。
- 處理 fixedMultiBet、currency、debug bet、totalBet / jackpot scaling 相容性與驗證問題。
- 具備把遊戲數學模組轉成 backend contract / money-like correctness 來檢查的能力。

## 可面試講

- `math-core` default fallback 如何在多 module 環境中降低改版風險。
- `sdt` / `slc` 如何把 fixedMultiBet 帶入 totalBet 與 jackpot scaling。
- `sfm` 如何呈現 currency multiplier 與 math-core compatibility 的差異。
- 為什麼 debug bet 也必須支援 currency / fixedMultiBet。
- 為什麼這類純計算 module 仍然要以 money correctness 看待。

## 不能誇大

- 不說主導完整遊戲數學模型。
- 不說負責全部 `*-math` repo。
- 不說完整 RTP 策略 / simulator / certification owner。
- 不說所有 module 都已完整支援 fixedMultiBet / currency。
- 不說 math module 負責錢包 transaction rollback。

## 待補 evidence

- 上游 game-api / runtime 呼叫端。
- 實際 release / issue / MR context。
- test result 或 simulation output。
- 其他 high-evidence `*-math` module 的相同 pattern。

## Step 4 面試邊界

面試時可以把這條 flow 定位成「多 module math contract 相容與 money-like correctness」，但要主動說明：

- 這是單條 flow 的 Step 4 面試 case，不是全 `*-math` final consolidation。
- 已深掃代表 path：`math-core`、`sdt-math`、`sfm-math`、`slc-math`。
- 未深掃上游 game-api caller 與全部 71 個 `*-math` repo。
- 沒有 production incident / ticket evidence 時，不主動包裝成事故處理案例。
