# transfer-wallet-money-in-out Career / Interview

日期: 2026-05-21

## 0. 定位

- Flow: `transfer-wallet-money-in-out`
- 狀態: Step 3 已完成，待 Step 4 轉正式面試 case
- 證據層級: code-backed；Nick / `10gt12nc` 有後續分表 / schema route / `updatePlayerWallet` deadlock 補償 direct evidence，但 transfer API 初版主要不是 Nick
- 履歷狀態: 暫不直接寫入 05 / 08；等 Step 5 claim gate 後再決定是否回填 project-level consolidation

## 1. 可面試講

可以講成一個 money correctness case:

> Transfer wallet 不是單純加減 balance。它要同時處理外部 request 簽名、`transferReferenceId` 防重、transaction record、order lookup、DB wallet、Redis balance、request log，以及轉出時的餘額檢查。真正的風險在於每一步不是同一個 atomic unit，所以要清楚知道哪個資料是 source of truth，以及失敗後如何補償或對帳。

## 2. 目前可用 30 秒說法

我會把 transfer wallet 拆成四層看：入口驗簽與 sub-agent mapping、防重與 transaction state、DB / Redis balance 更新、最後是 request log / lookup audit。這條 flow 的重點不是 API 長什麼樣，而是 `transferReferenceId` 如何避免重複入帳、transaction 和 wallet balance 的寫入順序，以及 DB 成功但 Redis 失敗時要怎麼界定 source of truth。

## 3. 可用技術點

- API sign string 與 digest key 驗證。
- `transferReferenceId` vs internal `transactionId`。
- Redis 3 秒短鎖只能擋短時間重送，不等於完整 idempotency。
- `pt_transfer_player_wallet_transaction` 記錄 before / after balance。
- `ag_transfer_order_lookup` 讓查單不用掃所有日表。
- DB balance 與 Redis hot balance dual-write failure window。
- transfer-out 讀 balance 後再扣款，併發下需要 DB 條件更新 / row lock / unique key 配合。

## 4. 履歷保守 bullet 候選

目前只作候選，不直接寫入正式履歷:

- 參與 AntPlay slot game API 的 transfer wallet 相關維護，理解並整理轉入 / 轉出、transaction record、order lookup、DB / Redis balance 同步與防重邊界。

Step 5 若補強 Nick direct evidence 後，可考慮併入 project-level bullet；不能單獨寫成完整 transfer wallet owner。

## 5. 不可誇大

- 不寫主導 transfer wallet 初版。
- 不寫完整 wallet / ledger / reconciliation owner。
- 不寫已完成 exactly-once。
- 不寫所有 failure compensation 都已落地。
- 不把 code-backed 分析素材直接升級成真實開發過，除非 Step 5 確認 direct path evidence。

## 6. Step 4 應補

- 30 秒 / 90 秒 / 3 分鐘講法。
- STAR: 背景、問題、分析、改善方向。
- 面試官追問: idempotency、transaction boundary、Redis / DB consistency、negative balance、防重 TTL。
- Owner 改善方向: DB unique key、conditional update、outbox / repair job、request log 不阻塞主流程、reconciliation report。
