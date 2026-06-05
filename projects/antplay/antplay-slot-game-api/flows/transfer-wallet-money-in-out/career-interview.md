# transfer-wallet-money-in-out Career / Interview

日期: 2026-05-21

## 0. 定位

- Flow: `transfer-wallet-money-in-out`
- 狀態: Step 5 已完成
- 證據層級: 真實開發過 + code-backed；Nick / `10gt12nc` 有後續分表 / schema route / transaction / lookup table path / `updatePlayerWallet` rows check direct evidence，但 transfer API 初版主要不是 Nick
- 履歷狀態: 可回填 `antplay-slot-game-api` project-level consolidation；不直接單獨寫入 05 / 08，不寫成完整 transfer wallet owner

## 1. 30 秒講法

Transfer wallet 不是單純加減 balance。這條 flow 入口會先驗簽與轉換 sub-agent，接著用 `transferReferenceId` 做防重與查單 key，再寫 transaction、lookup、DB wallet 與 Redis hot balance。真正的風險在於這些資料不是同一個 atomic unit，所以面試重點是 idempotency、DB / Redis source of truth、併發扣款，以及失敗後怎麼 repair / reconciliation。

## 2. 90 秒講法

我會把 transfer wallet 拆成四層看。第一層是入口安全：固定 sign string、digest key 驗簽、sub-agent 轉成實際寫入的 agent。第二層是防重：短時間用 Redis `ReferenceLock` 擋連續重送，但它只有 3 秒，所以不能當完整 idempotency，長期還要靠 `transferReferenceId` 查 transaction。第三層是 money state：先寫 `pt_transfer_player_wallet_transaction` 和 `ag_transfer_order_lookup`，再更新 `ag_transfer_player_wallet`，最後同步 Redis balance。第四層是 audit：request log 記錄 request / response。

這裡最大的 owner 問題是 failure window。比如 transaction 已經寫 `SUCCESS`，但 wallet update 或 Redis increment 失敗，查單會看到成功，餘額卻可能沒對齊；transfer-out 先讀 balance 再扣款，也要考慮不同 reference 併發扣成負數。保守改善方向會是 DB unique key、conditional update、PENDING -> SUCCESS / FAILED 狀態流、repair job，以及明確宣告 DB 是 source of truth、Redis 是 cache。

## 3. 3 分鐘完整講法

這條 flow 是 AntPlay slot game-api 的 transfer wallet 入口，處理 balance、transfer-in、transfer-out、transfer-out-all 和 get-single-transaction。它不是遊戲下注本身，而是外部平台把玩家錢轉進 / 轉出遊戲錢包的 money flow。

正常流程是：Controller 先做 validation 和 sign verify，根據 subAgent 取得 replaceAgentId，先寫一筆 request log。transfer-in 會確保 player wallet 和 Redis field 存在；transfer-out 會先檢查 balance。接著系統用 `transferReferenceId` 做防重：第一層是 Redis 3 秒短鎖，第二層是查 transaction table，如果同 reference 已經 SUCCESS，就回原本的 before / after balance；如果是 FAILED，就回交易失敗。

真正進入 money mutation 時，系統會先產生內部 `transactionId`，寫 `pt_transfer_player_wallet_transaction`，記錄 amount、before balance、after balance、status；再寫 `ag_transfer_order_lookup`，讓之後用 `transferReferenceId` 查單時，可以找到正確的 transaction table 和 row；最後才更新 `ag_transfer_player_wallet`，並同步 Redis balance。

Senior 角度我會追三個重點。第一是 idempotency：Redis short lock 只能擋連續重送，不能保證跨時間、跨日或 Redis 遺失時的唯一性，所以 DB unique key / transaction 查詢語意要補強。第二是 consistency：transaction、lookup、wallet DB、Redis、request log 不是同一個 transaction boundary，任何一步失敗都可能造成查單、餘額或 audit 不一致。第三是併發扣款：transfer-out 如果只是先讀 balance 再扣 DB，應該再確認 DB conditional update 或 row lock，避免不同 reference 同時扣成負數。

這條 flow 我不會誇大成我主導完整 transfer wallet。比較保守、可面試的說法是：我能拆解 transfer wallet money flow 的防重、狀態、DB / Redis 一致性和 failure window，也能提出 owner 層的修復方向。

## 4. STAR

| 欄位 | 內容 |
| --- | --- |
| Situation | Game API 需要支援 transfer wallet 模式，外部平台會呼叫轉入、轉出、全額轉出與查單 API。 |
| Task | 釐清一筆 transfer request 從驗簽、防重、transaction、lookup 到 DB / Redis wallet 更新的資料邊界。 |
| Action | 追 `TransferBalanceController`、`TransferBalanceFacade`、`TransferBalanceService`、transaction / lookup entity、Redis key 與 path-specific commits，整理出 source of truth、idempotency 與 failure window。 |
| Result | 形成可面試的 money correctness case：能說清楚 Redis short lock 不是完整冪等、DB / Redis dual-write 風險、transfer-out 併發扣款風險，以及 owner 改善方向。 |

## 5. Failure Scenarios

| Scenario | 面試回答重點 |
| --- | --- |
| 同一 `transferReferenceId` 連續重送 | Redis short lock 可擋短時間重送，但 TTL 只有 3 秒；完整冪等應靠 DB unique key / transaction status 查詢。 |
| transaction 已寫 SUCCESS，wallet update 失敗 | 這是最重要 failure window；應避免先 SUCCESS，或改成 PENDING -> SUCCESS / FAILED，並有 repair job。 |
| DB wallet 更新成功，Redis increment 失敗 | DB 應是 source of truth，Redis 是 cache；需要對帳 / repair 將 Redis 對齊 DB。 |
| transfer-out 併發扣款 | 先讀 balance 再扣有 race；應用 DB conditional update、row lock 或 ledger model 防負數。 |
| lookup 寫成功但 transaction / wallet 不一致 | 查單會有誤導風險；lookup、transaction、wallet 狀態需要一致的狀態機或補償。 |
| request log 寫失敗 | log 是 audit / observability，不應 rollback money flow，但要能補寫與告警。 |

## 6. Senior 追問

| 問題 | 回答 |
| --- | --- |
| Redis 3 秒 lock 夠不夠？ | 不夠。它只能降低短時間重送，不能保證長期 idempotency；完整做法要 DB unique key、transaction status 與查單語意。 |
| `transferReferenceId` 和 `transactionId` 差在哪？ | `transferReferenceId` 是外部平台傳入的冪等 / 查單 key，`transactionId` 是系統內部交易識別。 |
| DB 和 Redis 誰是 source of truth？ | 應以 DB wallet / transaction record 為 source of truth；Redis 是 hot balance cache，異常時要 repair。 |
| 為什麼需要 `ag_transfer_order_lookup`？ | 因為 transaction 有分表 / 日期路由，lookup 可以用 reference id 找到實際 transaction table / row，避免掃表。 |
| transfer-out 怎麼避免負數？ | 不能只靠 Java 先讀 balance；要看 DB 條件更新、row lock 或 ledger model。 |
| request log 失敗要不要 rollback？ | 不應該因 audit log 失敗 rollback money flow，但要有補寫、告警和追查能力。 |

## 7. Lead / Architect 追問

| 問題 | 回答方向 |
| --- | --- |
| 如果讓你重設計，你會改哪裡？ | transaction 先 PENDING，wallet DB conditional update 成功後改 SUCCESS；失敗改 FAILED，並用 unique key 約束 external reference。 |
| 會不會用 outbox？ | 如果需要跨 DB / Redis / MQ 或 audit log 保證，會用 outbox / repair worker；但不能把現況誇成已有 outbox。 |
| Redis cache 壞掉怎麼恢復？ | 以 DB wallet / transaction replay 或定時 reconciliation 重建 Redis hash。 |
| 跨日重送怎麼辦？ | 要定義 `transferReferenceId` 的全域唯一範圍；若只查當日 transaction，跨日重送會是風險。 |
| 分表後怎麼查交易？ | lookup table 存 reference、transaction table id、data day table；查單先查 lookup 再查 transaction。 |

## 8. 履歷保守 bullet 候選

可回填 project-level consolidation；若 05 / 08 之後重整，可併入 AntPlay game-api runtime bullet:

- 參與 AntPlay slot game API 的 transfer wallet 相關維護與分析，整理轉入 / 轉出、transaction record、order lookup、DB / Redis balance 同步與防重邊界。
- 參與 transfer wallet 分表 / schema route / transaction lookup 與 wallet update failure-window 維護，能說明 `transferReferenceId`、transaction record、DB wallet、Redis hot balance 的一致性邊界。

不能單獨寫成完整 transfer wallet owner；建議只併入「game runtime / betting settlement / transfer wallet / async log」這種 project-level bullet。

## 9. 不可誇大

- 不寫主導 transfer wallet 初版。
- 不寫完整 wallet / ledger / reconciliation owner。
- 不寫已完成 exactly-once。
- 不寫所有 failure compensation 都已落地。
- 不把 code-backed 分析素材直接升級成真實開發過，除非 Step 5 確認 direct path evidence。

## 10. 下一步

後續 rolling resume package 已完成；目前沒有預設下一步。
