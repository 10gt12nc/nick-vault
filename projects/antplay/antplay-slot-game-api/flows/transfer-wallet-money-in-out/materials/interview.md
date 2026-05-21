# transfer-wallet-money-in-out Interview Notes

日期: 2026-05-21

## 1. Step 4 定位

本檔是 `transfer-wallet-money-in-out` 的 Step 4 面試稿。主報告在 `flow.md`；履歷邊界看 `career-interview.md` 與 `materials/claim-boundary.md`。

證據層級: code-backed。Nick / `10gt12nc` direct evidence 主要是後續分表 / schema route / `updatePlayerWallet` deadlock 補償，不是 transfer wallet API 初版 owner。

## 2. 30 秒版

Transfer wallet 的重點不是 API 長什麼樣，而是 money state 怎麼保持一致。這條 flow 會驗簽、用 `transferReferenceId` 防重和查單、寫 transaction / lookup，再更新 DB wallet 與 Redis balance。最大的風險是 transaction、lookup、DB、Redis、request log 不是同一個 atomic unit，所以要清楚定義 DB source of truth、Redis cache、idempotency 和 repair / reconciliation。

## 3. 90 秒版

Transfer wallet 我會拆成四層。第一層是入口安全：sign verify、sub-agent mapping。第二層是冪等：Redis 3 秒 short lock 擋短時間重送，但真正要靠 `transferReferenceId` 查 transaction。第三層是狀態：transaction 記錄 before / after balance，lookup 負責把 reference id 對到分表 transaction。第四層是 money mutation：更新 DB wallet，再同步 Redis balance。

風險在於這些不是一個 transaction boundary。比如 transaction 已經寫 SUCCESS，wallet update 失敗，查單會顯示成功但餘額沒對上；DB update 成功但 Redis increment 失敗，玩家看到的 hot balance 可能不準；transfer-out 若只是先讀 balance 再扣，併發下可能扣成負數。改善方向是 unique key、PENDING 狀態、conditional update、repair job 和 reconciliation。

## 4. 3 分鐘版

這條 flow 是外部平台對 Game API 做 transfer wallet 操作，包含 balance、transfer-in、transfer-out、transfer-out-all 和 get-single-transaction。入口先驗證 request body 和 sign，取得 agent digest key，用固定 sign string 驗簽。接著處理 subAgentId，轉成實際寫入資料的 replaceAgentId，並先寫 request log。

transfer-in 會確保玩家 wallet record 和 Redis balance field 存在；transfer-out 則先檢查餘額。接著進入防重。系統會用 Redis `ReferenceLock` 對 `transferReferenceId` 做 3 秒短鎖，降低連續重送；再查 transaction table，如果同 reference 已經 SUCCESS，就回原本結果；如果 FAILED，就回交易失敗。

真正改錢時，系統會產生內部 transactionId，寫入 `pt_transfer_player_wallet_transaction`，再寫 `ag_transfer_order_lookup`，讓後續查單可以從 external reference 找到分表 transaction row。最後更新 `ag_transfer_player_wallet`，並同步 Redis balance，成功後補 request log response。

面試時我會強調這條 flow 的三個 owner decision。第一，Redis short lock 不等於完整 idempotency，長期唯一性應該靠 DB constraint 和 transaction status。第二，DB 是 source of truth，Redis 是 cache，所以 dual-write 需要 repair / reconciliation。第三，transfer-out 要避免併發扣款，最好在 DB 層做 conditional update 或 lock，而不是只靠 Java 先讀 balance。

## 5. STAR

| 欄位 | 內容 |
| --- | --- |
| Situation | Transfer wallet 模式下，平台會頻繁呼叫轉入、轉出、查餘額與查單 API，任何重複或不一致都可能造成金額錯誤。 |
| Task | 梳理 API 到 DB / Redis 的完整資料流，找出冪等、狀態、餘額同步與 audit 的邊界。 |
| Action | 追 controller / facade / service / entity / Redis key / path-specific commits，拆出 `transferReferenceId`、transaction、lookup、wallet DB、Redis balance 的責任。 |
| Result | 產出可面試的 money correctness case，能回答防重、DB / Redis source of truth、failure window、併發扣款與 owner repair。 |

## 6. Failure Scenarios

| Scenario | 風險 | 改善方向 |
| --- | --- | --- |
| 同一 reference 連續重送 | 短時間可能被 Redis 擋，TTL 後仍可能進 DB path | DB unique key + transaction status |
| transaction SUCCESS 後 wallet update 失敗 | 查單成功但餘額未更新 | PENDING -> SUCCESS / FAILED，repair job |
| DB 成功 Redis 失敗 | hot balance 不準 | DB source of truth + Redis rebuild / reconciliation |
| transfer-out 併發 | 不同 reference 同時扣款可能越扣越低 | `WHERE balance >= amount`、row lock、ledger |
| lookup 成功但 transaction / wallet 不一致 | 查單 audit 和 money state 不一致 | 狀態機 + repair |
| request log 失敗 | 排查困難，但不應阻斷 money flow | 非阻塞 audit + 補寫 / 告警 |

## 7. 常見追問

| 問題 | 回答 |
| --- | --- |
| 這是不是完整 idempotency？ | 不是。現況看到 Redis short lock + transaction 查詢；完整性要看 DB unique key / constraint，Step 3 未確認。 |
| 為什麼 transaction 先 SUCCESS 是風險？ | 因為後面 wallet update / Redis update 仍可能失敗，SUCCESS 會讓查單語意早於 money mutation。 |
| 你會怎麼改？ | 先寫 PENDING，wallet DB update 成功後改 SUCCESS；失敗改 FAILED；Redis 用 repair 對齊。 |
| Redis 和 DB 不一致怎麼辦？ | 以 DB wallet / transaction 為準，Redis 可重建；要有監控與 reconciliation。 |
| transfer-out-all 有什麼特別風險？ | 它讀 DB 全額再扣，讀與扣之間若有併發變動，需要 lock / conditional update。 |
| request log 是交易 source of truth 嗎？ | 不是。它是 audit / observability，不應反向決定 money state。 |

## 8. 面試不可踩線

- 不說 Nick 主導 transfer wallet 初版。
- 不說已經實作完整 reconciliation。
- 不說 exactly-once。
- 不說 Redis lock 就能完整防重。
- 不說 `transfer-wallet-money-in-out` 可單獨代表完整 transfer wallet owner；Step 5 只允許回填 project-level consolidation。
