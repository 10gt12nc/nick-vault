# transfer-wallet-money-in-out Evidence

日期: 2026-05-21

## 1. Source Repo 狀態

| 項目 | 狀態 |
| --- | --- |
| source path | `/Users/nick/Git/antplay/antplay-slot-game-api` |
| local branch | `develop` |
| local HEAD | `079aa66` |
| local `origin/develop` | `079aa66` |
| ahead / behind | `0 / 0` |
| source working tree | clean |
| remote refs | 前一輪已嘗試 fetch 並失敗；依 KB 不反覆重試 |
| 最新性判斷 | 未確認最新遠端；本 Step 依本地 refs / 本地 working tree 保守分析 |

## 2. 掃描範圍

Vault:

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `projects/antplay/antplay-slot-game-api/README.md`
- `step1-candidate-flows.md`
- `step2-flow-comparison.md`
- `slot-bet-settle-rollback` flow package

Source:

- `src/main/java/com/ps/domain/api/game/controller/TransferBalanceController.java`
- `src/main/java/com/ps/domain/api/game/facade/TransferBalanceFacade.java`
- `src/main/java/com/ps/domain/manage/service/TransferBalanceService.java`
- `src/main/java/com/ps/domain/constant/TransferRedis.java`
- `src/main/java/com/ps/domain/player/data/entity/TransferPlayerWallet.java`
- `src/main/java/com/ps/domain/player/data/entity/TransferPlayerWalletTransaction.java`
- `src/main/java/com/ps/domain/player/data/entity/TransferOrderLookup.java`
- `AgentApiFacade` / `GameFlowFacade` / `GameFacade` transfer wallet 相關片段

## 3. Code Evidence

| Code path | Evidence | 判斷 |
| --- | --- | --- |
| `TransferBalanceController#accountBalance` | 驗簽後從 Redis balance hash 回餘額 | 查餘額路徑已確認 |
| `TransferBalanceController#transferIn` | 驗簽、寫 request log、init wallet、Redis short lock、transaction、lookup、wallet + Redis | transfer-in 主路徑已確認 |
| `TransferBalanceController#transferOut` | 驗簽、防重、讀 Redis balance、餘額檢查、transaction、lookup、扣 wallet | transfer-out 主路徑已確認 |
| `TransferBalanceController#transferOutAll` | 讀 DB 全額、transaction、lookup、扣全額 | full withdraw 主路徑已確認 |
| `TransferBalanceController#transaction` | `transferReferenceId` -> lookup -> transaction | 查單路徑已確認 |
| `TransferBalanceFacade#verifySign` | digest key + sign string | API auth 邊界已確認 |
| `TransferBalanceService#isTransferReferenceIdExist` | Redis key `antplay:Transfer:ReferenceLock:{reference}`，TTL 3 秒 | 短鎖防重，不是完整 idempotency |
| `TransferBalanceService#insertTransferPlayerWalletTransaction` | 寫 `pt_transfer_player_wallet_transaction` | transaction source 已確認 |
| `TransferBalanceService#insertOrderLookup` | 寫 `ag_transfer_order_lookup` | 查單索引已確認 |
| `TransferBalanceService#updatePlayerWallet` | DB rows=1 後 Redis increment | DB + Redis dual-write 已確認 |

## 4. Commit / History Evidence

| Commit | Author | Evidence | Claim 判斷 |
| --- | --- | --- | --- |
| `67039e6` | Derek | 建立 transfer wallet API 初版，新增 controller / facade / service / DTO / entity | code context；不是 Nick direct |
| `0733906` | Derek | transfer wallet 金額寫 DB 同時寫 Redis；將 Redis 更新收斂進 `updatePlayerWallet` | code context |
| `0781f68` | Derek | lookup 由 traceId 改為存 `transferReferenceId` | code context，重要 idempotency / query 語意 |
| `54078fe` | `10gt12nc` | `updatePlayerWallet` 改回 boolean、檢查 rows、支撐 wallet changed flag / deadlock 補償 | Nick direct evidence |
| `718a207` | `10gt12nc` | db partition v2，影響 transfer wallet / request log / transaction table path | Nick direct evidence |
| `99c63ff` | `10gt12nc` | `ag_transfer_player_wallet` 相關 TODO / table path | Nick direct evidence |
| `aaddfdc` | `10gt12nc` | `ag_transfer_order_lookup` 相關 TODO / table path | Nick direct evidence |
| `3531f42` | `10gt12nc` | `pt_transfer_player_wallet_transaction` 分表 / table creator 相關 | Nick direct evidence |
| `eb2573a` | `10gt12nc` | `pt_transfer_request_log` 分表 / table creator 相關 | Nick direct evidence |
| `41cd5ae` | eliot | transfer-out 防負數補強 | context only |

## 5. 已確認

- transfer API 初版不是 Nick direct author；Step 3 不能直接寫「Nick 主導 transfer wallet」。
- Nick / `10gt12nc` 有 transfer wallet 後續分表 / schema route / wallet update failure handling 相關 direct evidence。
- `transferReferenceId` 同時進 request log、transaction、lookup，是外部交易 idempotency / 查詢的核心 key。
- Redis short lock TTL 是 3 秒，適合擋短時間重送，但不能單獨代表完整 idempotency。
- `updatePlayerWallet` 目前先更新 DB，再 Redis increment；DB update rows != 1 時回 false，不做 Redis。

## 6. 待確認

- DB 是否有 unique key 保護 `agentId + account + transferReferenceId + pt_day`。
- `pt_transfer_player_wallet_transaction` 是否真的有 PENDING / FAILED 寫入路徑。
- transfer-out 是否有 DB conditional update / row lock 防併發負數；目前主 SQL 未看到 `balance >= ?` 條件。
- DB 成功但 Redis 失敗後是否有 repair / reconciliation job。
- external caller retry policy 與 timeout 行為。
- live migration / schema route 實際部署狀態。

## 7. Evidence Level

- `transfer-wallet-money-in-out` flow: 專案存在 / code-backed。
- Nick direct evidence: `54078fe`、`718a207`、`99c63ff`、`aaddfdc`、`3531f42`、`eb2573a`。
- 非 Nick direct 但重要 context: `67039e6`、`0733906`、`0781f68`、`41cd5ae`。
- 履歷結論: 待 Step 5 claim gate，不在 Step 3 直接升級。
