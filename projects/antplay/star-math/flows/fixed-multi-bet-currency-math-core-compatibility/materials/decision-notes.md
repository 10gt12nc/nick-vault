# Decision Notes - fixedMultiBet / Currency / Math-Core Compatibility

## Decision 1: Core Contract 用 Default Fallback

`math-core` 在 `SlotMath` / `SlotMathOperatorService` 提供帶 `currency` / `fixedMultiBet` 的 default method。這個決策偏向 backward compatibility：舊 module 不 override 也能 compile / run。

Trade-off:

- 好處: 不會一次改破大量 `*-math` repo。
- 代價: caller 看到新簽名，不代表每個 module 都完整套用 fixedMultiBet / currency。
- Owner 判斷: 必須 module-by-module 驗證，不能只看 core interface。

## Decision 2: Currency Multiplier 放在 Module OperatorService

`sdt` / `sfm` 使用 OperatorService 的 `ThreadLocal<Integer>` 保存 currency multiplier。

Trade-off:

- 好處: 不需要把 multiplier 參數一路塞進每個計算 helper。
- 代價: thread reuse / async path 要小心殘留；每個入口都要確保先 set。
- Owner 判斷: debug / normal / freeSpin 入口都應該對 currency 初始化行為一致。

## Decision 3: fixedMultiBet 不是只改 totalBet

`sdt` / `slc` 顯示 fixedMultiBet 需要同時進入:

- inputData
- resultTemp
- way game totalBet
- jackpot multiplier
- 前端 / debug 可見結果

Owner 判斷: 只改 `getTotalBet` 不夠，因為 jackpot / result / debug 路徑仍可能失真。

## Decision 4: Result Base 沒有統一 fixedMultiBet / currency

`AbstractBetResult` 沒有共用 fixedMultiBet / currency 欄位；`sdt` 自己補了 `SlotBetResult.fixedMultiBet`。

Trade-off:

- 好處: 不強迫所有 module result schema 變更。
- 代價: 前端 / caller 對不同 module 的 result 欄位一致性較弱。
- Owner 判斷: 若上游需要跨遊戲統一顯示或對帳，應再評估是否要提升到 core result contract。

## Decision 5: Step 3 只採代表 module

本輪不平均掃 71 個 `*-math` repo，選 `sdt`、`sfm`、`slc` 配合 `math-core` 作代表。

原因:

- Step 2 已選此 flow 為第一順位。
- `sdt` 提供 fixedMultiBet 最完整 path。
- `sfm` 提供 math-core compatibility 修正 evidence。
- `slc` 提供 jackpot scaling 旁證。

這符合 KB 規則：不平均整理所有功能，優先抓 production-like flow 與高差異化 evidence。
