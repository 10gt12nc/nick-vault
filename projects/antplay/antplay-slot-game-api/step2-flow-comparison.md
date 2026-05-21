# antplay-slot-game-api Step 2 Flow Comparison

日期: 2026-05-21

## 0. 閱讀定位

本檔是 `antplay-slot-game-api` 的 Flow Track Step 2。目的不是深挖單條 flow，而是比較 Step 1 找出的候選 flows，決定第一條進 Step 3 的代表 flow。

掃描深度: Level 1.5 / Step 2 flow comparison。已補讀 route、facade、service、path-specific history，但尚未做單條 flow Level 2 深掃。

既有文件狀態:

| 文件 | 狀態 | 判斷 |
| --- | --- | --- |
| `README.md` | 可沿用 | 已標示 contribution consolidation 與 Step 1 狀態 |
| `contribution-claim-consolidation.md` | 可沿用 | rolling / scoped 邊界清楚，沒有把整個 repo 誇大成完整 owner |
| `step1-candidate-flows.md` | 可沿用 | 有 source 狀態、候選 flow、未掃範圍與下一步 |

## 1. 本輪重讀範圍

Vault:

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `projects/antplay/README.md`
- `projects/antplay/antplay-slot-game-api/README.md`
- `projects/antplay/antplay-slot-game-api/contribution-claim-consolidation.md`
- `projects/antplay/antplay-slot-game-api/step1-candidate-flows.md`

Source repo:

- `/Users/nick/Git/antplay/antplay-slot-game-api`
- `GameController`
- `GameFacade`
- `GameFlowFacade`
- `AgentApiFacade`
- `TransferBalanceController`
- `TransferBalanceFacade`
- `BetRecordManageService`
- `UseSchema`
- `SchemaRouteAspect`
- `RabbitMQConfig`
- `RabbitMqService`
- path-specific commit log for game runtime / transfer wallet / schema route / RabbitMQ request log

## 2. Source Repo 最新性

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

注意: 不記錄內網 remote URL / IP / sensitive detail。

## 3. Step 2 評分標準

| 指標 | 說明 |
| --- | --- |
| Money correctness | 是否碰到玩家金額、下注扣款、派彩、退款、wallet balance |
| State transition | 是否有清楚且可講的狀態流轉，例如 bet record / transaction status |
| Failure window | 是否能講清楚失敗窗、retry、compensation、rollback |
| Nick evidence | 是否有 Nick / `10gt12nc` direct commits 或 path-specific history |
| Interview value | 是否能轉成 Senior Backend / System Owner 面試案例 |
| Scope control | Step 3 是否能控制成單條 flow，不會平均掃完整 repo |
| Resume risk | 是否容易誇大；風險越低越適合先做 |

## 4. 候選 Flow 比較

| Rank | Flow | Money correctness | State / consistency | Nick evidence | Step 3 可控性 | 履歷 / 面試價值 | 結論 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | `slot-bet-settle-rollback` | 很高 | 很高 | 高，game runtime / deadlock / request log / partition / RTP 相關 direct commits 線索 | 中高，需要鎖定 `/game/bet` 主線 | 很高 | 第一條進 Step 3 |
| 2 | `transfer-wallet-money-in-out` | 很高 | 很高 | 高，transfer wallet / deadlock / transaction / order lookup direct commits 線索 | 中，transfer-in/out/all 範圍較大 | 很高 | 第二順位，適合接在 bet 主線後補 wallet 邊界 |
| 3 | `request-log-rabbitmq-async` | 低 | 中 | 高，#774 request log MQ direct commits 線索 | 高，scope 小 | 中高 | 好做，但履歷主力不如 money flow |
| 4 | `bet-record-sharding-schema-route` | 中 | 高 | 高，#167 / db partition / `@UseSchema` direct commits 線索 | 中低，跨多 table / AOP / job | 中高 | 技術硬，但宜等 bet 主線先定位 bet record source of truth |
| 5 | `runtime-rtp-darkpool-player-control` | 中 | 中 | 中高，RTP / dark pool / player control direct commits 線索 | 中低，容易跨到 math / operation policy | 高但誇大風險較高 | 暫不第一條，等 game-api 主線與 `*-math` 邊界更清楚 |
| 6 | `auth-token-whiteip-game-code-guard` | 低 | 中 | 中高，auth token / white IP direct commits 線索 | 高 | 中 | 可作補充安全 case，不是第一批主交易題 |

## 5. 第一條代表 Flow 決策

第一條進 Step 3:

```text
slot-bet-settle-rollback
```

中文名稱: Slot 下注 / 開獎 / 結算 / rollback。

為什麼先做:

- 它是 `antplay-slot-game-api` 的主交易路徑。`/game/bet` 會串起下注前金額檢查 / 扣款、bet record 狀態、遊戲結果、settle、rollback、provider callback 與 request log。
- 它能自然帶到 transfer wallet branch，但不會把 transfer-in / transfer-out 全部吞進同一個 Step 3。
- 它能把 `GameFacade`、`GameFlowFacade`、`AgentApiFacade`、`BetRecordManageService` 的責任邊界講清楚，是後續 wallet、MQ、分表、RTP flow 的共同底座。
- 面試價值最高：money correctness、state transition、idempotency、failure window、compensation、observability 都能在同一條 flow 裡講。
- 履歷風險可控：Step 3 先做 code-backed 深掃，Step 5 才決定可不可以升級成 flow-level claim；不直接改 05 / 08。

## 6. Step 3 Scope

`slot-bet-settle-rollback Step 3` 建議掃描範圍:

| 範圍 | 納入方式 |
| --- | --- |
| `/game/bet` entry | 主入口，必掃 |
| `GameFacade#bet` | 主 orchestration，必掃 |
| `GameFlowFacade#getBeforeBetMoney` | 下注前金額與 single wallet / transfer wallet 分支，必掃 |
| `GameFlowFacade#beforeBet` | bet record DEAL 狀態與 voucher 失敗處理，必掃 |
| `GameFlowFacade#afterBet` | 結果落地、settle 前後狀態，必掃 |
| `GameFlowFacade#settle` | provider / transfer wallet 派彩，必掃 |
| `GameFlowFacade#cancel` | rollback / cancel 狀態，必掃 |
| `AgentApiFacade#betSettle` | single wallet callback / transfer wallet 本地加錢分支，必掃 |
| `AgentApiFacade#betRollback` | provider rollback，必掃 |
| `BetRecordManageService#setStatusDeal / setStatusResult / setStatusCancel` | bet record state transition，必掃 |
| `TransferBalanceService` / transfer wallet model | 只掃 bet / settle 需要的 wallet mutation，不展開 transfer-in/out/all |
| `AgentApiFacade#sendRequestLogMq` | 只作 observability / audit boundary，不展開完整 MQ consumer |
| RTP / dark pool / player control | 只掃 `/game/bet` 內直接影響結果的 decision point，不展開完整 math policy |
| `antplay-slot-game-job` | 暫不納入；若 Step 3 發現 rollback / notify job 必須下游補證據，再標成待補 |

Step 3 不做:

- 不平均掃所有 controller / facade。
- 不把 transfer wallet transfer-in / transfer-out 作為同一條 flow 的主體。
- 不把 RabbitMQ request log 做成完整 MQ architecture。
- 不把 RTP / dark pool / player control 誇大成完整遊戲數學或風控策略。
- 不更新 05 / 08；最多在 Step 5 後回填 project consolidation。

## 7. 後續 Candidate Queue

| 順位 | Flow | 何時做 |
| --- | --- | --- |
| 2 | `transfer-wallet-money-in-out` | Step 3 已完成 / 2026-05-21；下一步做 Step 4 轉正式面試 case |
| 3 | `request-log-rabbitmq-async` | 要補 async / observability / audit case 時做；scope 較小 |
| 4 | `bet-record-sharding-schema-route` | bet 主線清楚後，再回頭看 bet record / request log / transfer transaction 分表 |
| 5 | `runtime-rtp-darkpool-player-control` | game-api 與 `*-math` 邊界更清楚後，再做 runtime decision / math contract |
| 6 | `auth-token-whiteip-game-code-guard` | 若需要 API access-control 面試題，再補 |

## 8. Claim Boundary

本 Step 2 可以說:

- `antplay-slot-game-api` 第一條代表 flow 選定為 `slot-bet-settle-rollback`。
- 選它是因為它最能代表 game API runtime 的主交易路徑，且能帶到 money correctness、state transition、rollback、transfer wallet branch 與 provider callback。
- 其他 candidate flows 保留為後續候選，不代表已完整深掃。

本 Step 2 不能說:

- 不能說 `slot-bet-settle-rollback` 已完成深掃。
- 不能說 Nick 主導完整 bet / settle / rollback。
- 不能說完整 wallet / ledger / reconciliation owner。
- 不能說完整 RabbitMQ / Kafka architecture owner。
- 不能把 Step 2 結論直接寫入 05 / 08。

## 9. Step 2 結論

2026-05-21 更新: `slot-bet-settle-rollback` 已完成 Step 5，Rank 2 `transfer-wallet-money-in-out` 已完成 Step 3。Flow Track 下一步應繼續同一條 flow Step 4:

```text
antplay antplay-slot-game-api transfer-wallet-money-in-out Step 4
```

預期產出:

- `projects/antplay/antplay-slot-game-api/flows/slot-bet-settle-rollback/flow.md`
- `projects/antplay/antplay-slot-game-api/flows/slot-bet-settle-rollback/career-interview.md`
- `projects/antplay/antplay-slot-game-api/flows/slot-bet-settle-rollback/materials/evidence.md`
- `projects/antplay/antplay-slot-game-api/flows/slot-bet-settle-rollback/materials/interview.md`
- `projects/antplay/antplay-slot-game-api/flows/slot-bet-settle-rollback/materials/claim-boundary.md`
- 視需要補 `materials/decision-notes.md`
