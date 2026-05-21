# transfer-wallet-money-in-out Claim Boundary

日期: 2026-05-21

## 1. Step 4 Claim 狀態

本 flow 目前是 Step 4。

可以作為 code-backed 面試素材，但還不能單獨升級成履歷 claim。原因是:

- transfer wallet API 初版主要 commit author 是 Derek。
- Nick / `10gt12nc` direct evidence 目前集中在後續分表 / schema route / `updatePlayerWallet` deadlock 補償改造。
- Step 5 尚未完整追所有 branch / diff / final behavior。

## 2. 可以說

- 我理解並整理過 AntPlay `antplay-slot-game-api` transfer wallet 的轉入 / 轉出 / 全轉出流程。
- 這條 flow 涉及 signature、`transferReferenceId` 防重、transaction record、order lookup、DB wallet、Redis balance 與 request log。
- Nick / `10gt12nc` 有 transfer wallet 相關後續維護 evidence，例如 `updatePlayerWallet` rows check / boolean return、分表 / schema route 相關 commits。

## 3. 暫時只能面試講

- transfer wallet 的 idempotency / consistency 設計風險。
- Redis short lock 和 transaction table 的責任差異。
- DB + Redis dual-write failure window。
- transfer-out 併發與 negative balance 防護。
- lookup table 如何支撐單筆交易查詢。

## 4. 不能說

- 不能說 Nick 主導 transfer wallet 初版。
- 不能說 Nick 完整設計 wallet / ledger / reconciliation。
- 不能說已做完整 exactly-once。
- 不能說所有 failure compensation / repair 已完整落地。
- 不能把 Step 4 面試素材直接寫進 `05 / 08`。

## 5. Step 5 要補的 claim gate

Step 5 需要再追:

- `10gt12nc` 在 transfer wallet path 的全部 direct commits。
- `54078fe` 最終行為在 `develop` 是否仍有效。
- 分表 commits 對 transfer wallet 實際 runtime 的影響。
- 是否有 branch / MR / ticket 可證明 Nick 實際負責範圍。
- 與 project-level consolidation 的邊界: 可放履歷、可面試講、不可誇大。

## 6. 下一步

```text
antplay antplay-slot-game-api transfer-wallet-money-in-out Step 5
```
