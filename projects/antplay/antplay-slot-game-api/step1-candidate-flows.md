# antplay-slot-game-api Step 1 Candidate Flows

日期: 2026-05-21

## 0. 閱讀定位

本檔是 `antplay-slot-game-api` 的 Flow Track Step 1，用來找出值得進 Step 2 比較的 production flows。這不是 class summary，也不是履歷最終結論。

掃描深度: Level 1 flow scan。

證據層級:

- Project-level: 真實開發過 + code-backed。既有 contribution consolidation 已確認 Nick / `10gt12nc` 在 game init、bet / settle / rollback、transfer wallet、bet record / request log 分表、RabbitMQ request log、Quartz 補通知、RTP / dark pool / player control 有大量 direct commits。
- Flow-level: 目前是候選 flow 盤點。每條 flow 是否能放履歷，仍要等 Step 2 / Step 3 / Step 4 / Step 5 逐條補 evidence 與 claim boundary。

## 1. 本輪重讀範圍

Vault / KB:

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `projects/antplay/README.md`
- `projects/antplay/antplay-slot-game-api/README.md`
- `projects/antplay/antplay-slot-game-api/contribution-claim-consolidation.md`

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
- path-specific commit log / branch list / author summary

## 2. Source Repo 狀態

| 項目 | 狀態 |
| --- | --- |
| source path | `/Users/nick/Git/antplay/antplay-slot-game-api` |
| local branch | `develop` |
| local HEAD | `079aa66` |
| local `origin/develop` | `079aa66` |
| ahead / behind | `0 / 0` |
| source working tree | clean |
| remote refs | `git fetch --all --prune` 失敗一次；依 KB 不反覆重試 |
| 最新性判斷 | 未確認最新遠端；本 Step 依本地 refs / 本地 working tree 保守分析 |
| Java 掃描量 | `src/main/java/com/ps/domain` 約 238 個 Java files |
| author 線索 | `10gt12nc` 在 all refs author summary 約 151 commits，`nick` 約 3 commits |

注意: source remote 是內網環境，vault 不記錄內網 URL / IP / sensitive remote detail。

## 3. Module / Service 邊界

| Layer | 主要 code path | 本輪觀察 |
| --- | --- | --- |
| Game API entry | `GameController` | `/game/init`、`/game/bet`、`/game/voucher-bet`、活動投注與 free spin 入口 |
| Runtime orchestration | `GameFacade`、`GameFlowFacade` | auth / game / currency validation、bet id、RTP / dark pool / player control、下注前扣款、bet record 狀態、結算、rollback |
| Agent / provider callback | `AgentApiFacade` | single wallet 對外 callback、transfer wallet 本地 wallet 更新、request log MQ |
| Transfer wallet | `TransferBalanceController`、`TransferBalanceFacade`、`TransferBalanceService` | transfer in / out / balance、signature、duplicate reference、transaction、Redis + DB balance |
| Bet record / schema | `BetRecordManageService`、`UseSchema`、`SchemaRouteAspect` | bet record 建單、狀態流轉、schema route、分表 / 分庫邊界 |
| Async / request log | `RabbitMQConfig`、`RabbitMqService`、`RequestLogMessageDto` | external API request log 改成 RabbitMQ 非同步送出 |
| Runtime control | `RtpCache`、`DarkpoolService`、`PlayerControlService`、`JackpotService`、`SlotMathFacade` | 影響遊戲結果與 runtime decision，但不等於完整 math owner |

## 4. 候選 Flow 排序

| Rank | Flow | 中文名稱 | Senior Backend 價值 | Nick evidence | Step 3 成本 | 履歷用途 |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | `slot-bet-settle-rollback` | Slot 下注 / 開獎 / 結算 / rollback | 很高 | project-level direct commits + code-backed；單條 path 待 Step 3 細分 | 高 | 可成為 game runtime / betting-settlement 主面試題，Step 5 後再決定履歷說法 |
| 2 | `transfer-wallet-money-in-out` | Transfer wallet 轉入 / 轉出 / 餘額同步 | 很高 | transfer wallet / deadlock / transaction 相關 direct commits 線索 | 高 | 可補 wallet consistency / idempotency / Redis + DB dual-write 面試素材 |
| 3 | `request-log-rabbitmq-async` | Agent API request log RabbitMQ 非同步化 | 中高 | #774 類 request log MQ direct commits 線索 | 中 | 可補 async log / audit / reliability 素材；不寫完整 MQ architecture owner |
| 4 | `bet-record-sharding-schema-route` | Bet record / request log / transfer transaction 分表與 schema routing | 中高 | db partition / `@UseSchema` / table creator direct commits 線索 | 中高 | 可補 high-traffic table / schema route / query risk 素材 |
| 5 | `runtime-rtp-darkpool-player-control` | RTP / dark pool / player control runtime decision | 中高 | RTP / dark pool / player control direct commits 線索 | 高 | 可和 `*-math` 串成 runtime + math 邊界；不得寫完整 RTP 策略 owner |
| 6 | `auth-token-whiteip-game-code-guard` | auth token / white IP / game code guard | 中 | auth token / white IP 相關 direct commits 線索 | 中 | 可作安全入口補充，不優先於交易 / wallet flow |

## 5. Candidate Flow Detail

### 5.1 `slot-bet-settle-rollback`

白話定位: 玩家打 slot 時，Game API 從驗證、下注、扣款、產生遊戲結果、寫 bet record、派彩、失敗 rollback 到通知 provider 的主流程。

主要 code path:

- `GameController#/game/bet`
- `GameFacade#bet`
- `GameFlowFacade#getBeforeBetMoney`
- `GameFlowFacade#beforeBet`
- `GameFlowFacade#afterBet`
- `GameFlowFacade#settle`
- `GameFlowFacade#cancel`
- `AgentApiFacade#bet`
- `AgentApiFacade#betSettle`
- `AgentApiFacade#betRollback`
- `BetRecordManageService#setStatusDeal`
- `BetRecordManageService#setStatusResult`
- `BetRecordManageService#setStatusCancel`

為什麼值得做:

- 是 `antplay-slot-game-api` 最核心的 production money flow。
- 同時碰到 balance correctness、bet record state transition、external provider callback、rollback、deadlock / compensation、RTP / jackpot / dark pool / player control。
- 最適合練 Senior Backend 面試的 consistency / failure window / owner decision。

待 Step 2 / Step 3 確認:

- single wallet 與 transfer wallet 在同一條 bet flow 的責任差異。
- `GameFacade#bet` catch deadlock / data exception 時的補償實際狀態。
- `AgentApiFacade#betSettle` transfer wallet branch 的 transaction boundary。
- 後續 Arnold / Eliot 修改 rollback / cancel / job 行為後，Nick direct evidence 與 final code 行為的邊界。

### 5.2 `transfer-wallet-money-in-out`

白話定位: transfer wallet 模式下，平台透過 Game API 做玩家錢包餘額查詢、轉入、轉出、全額轉出，並同步 DB wallet、Redis wallet、transaction 與 request log。

主要 code path:

- `TransferBalanceController#/public/transfer/balance`
- `TransferBalanceController#/public/transfer/transfer-in`
- `TransferBalanceController#/public/transfer/transfer-out`
- `TransferBalanceController#/public/transfer/transfer-out-all`
- `TransferBalanceFacade`
- `TransferBalanceService`
- `TransferPlayerWalletTransaction`
- `TransferOrderLookup`
- `GameFlowFacade#getBeforeBetMoney`
- `AgentApiFacade#betSettle`

為什麼值得做:

- 是 wallet consistency 主題，比單純 CRUD 更接近 Senior Backend。
- 有 signature、duplicate `transferReferenceId`、transaction status、DB + Redis balance、request log、deadlock compensation 等風險。
- 可和 bet / settle flow 串成「下注前扣款、派彩後加回、轉入轉出」完整 wallet 邊界。

待 Step 2 / Step 3 確認:

- transfer-out / transfer-out-all 是否和 transfer-in 有不同 idempotency / locking 模型。
- Redis wallet 初始化、DB wallet 更新、transaction 寫入的順序與失敗窗。
- transaction status `SUCCESS / FAILED` 與 duplicate order 的回應語意。

### 5.3 `request-log-rabbitmq-async`

白話定位: 對外 agent API request / response log 不同步阻塞主流程，而是包成 message 丟到 RabbitMQ，讓 log 寫入與主交易流程解耦。

主要 code path:

- `AgentApiFacade#execute`
- `AgentApiFacade#sendRequestLogMq`
- `RabbitMQConfig`
- `RabbitMqService`
- `RequestLogMessageDto`

為什麼值得做:

- 能講清楚「request log 是 audit / observability，不是交易 source of truth」。
- 有 MQ failure、message duplication、consumer delay、主流程成功但 log 失敗等 owner decision。
- scope 比 bet / wallet 小，適合做中型面試 case。

待 Step 2 / Step 3 確認:

- consumer / listener 是否在同 repo 或 `antplay-slot-game-job`。
- RabbitMQ send failure 是否會影響 main flow。
- 是否有 retry / DLQ / fallback；若沒有，要保守標示。

### 5.4 `bet-record-sharding-schema-route`

白話定位: 高流量 bet record、request log、transfer transaction 依日期 / agent / schema 做分表或 route，避免單表過大，同時維持查詢與寫入正確性。

主要 code path:

- `UseSchema`
- `SchemaRouteAspect`
- `BetRecordManageService`
- `BetRecordTableCreator`
- `RequestLogTableCreator`
- `TransferPlayerWalletTransactionTableCreator`
- `ShardingTableName`

為什麼值得做:

- 這是高流量資料治理題，可講 schema route、AOP、table creation、query window、migration risk。
- 和 bet record / request log / transfer wallet 都有關，適合作為 Step 2 比較後的支線或獨立 flow。

待 Step 2 / Step 3 確認:

- schema route 如何從 method args / agentId / table name 判斷。
- nested schema switch 的保護是否足夠。
- table creator 與 runtime write path 的時序。

### 5.5 `runtime-rtp-darkpool-player-control`

白話定位: 每次 bet 時，runtime 會決定 target RTP、jackpot RTP、dark pool、player control 等參數，再交給 math layer 算結果。

主要 code path:

- `GameFacade#bet`
- `RtpCache`
- `DarkpoolService`
- `PlayerControlService`
- `JackpotService`
- `SlotMathFacade`

為什麼值得做:

- 可以把 `antplay-slot-game-api` 與 `math-core` / `*-math` 的 evidence 串起來。
- 是遊戲 runtime decision path，不只是資料 CRUD。
- 適合回答「backend runtime 如何把營運策略、玩家控制、math result contract 串起來」。

待 Step 2 / Step 3 確認:

- 不可誇大成完整 RTP 策略 owner。
- 要分清楚 game-api runtime decision、math-core contract、各 game math module 三層責任。
- Step 3 前最好先選一個最清楚的遊戲或 RTP path，不要平均掃全部 runtime control。

## 6. 暫不優先 Flow

| Flow | 原因 |
| --- | --- |
| `auth-token-whiteip-game-code-guard` | 有安全入口價值，但比 bet / wallet / async log 的履歷與面試價值低 |
| `voucher-bet-free-spin-activity` | 可能有產品複雜度，但 Step 1 觀察下不如主 bet / wallet 線核心 |
| `quartz-notify-compensation` | 可能更適合和 `antplay-slot-game-job` 一起看，避免在 game-api Step 1 就拆太碎 |

## 7. 本輪未掃 / 待確認

- 未做 Level 3 逐檔逐行 / 每個 commit diff 深掃。
- 未掃 live DB schema / migration 執行結果。
- 未確認最新遠端 refs；remote fetch 失敗一次後依 KB 停止重試。
- 未完整追 `antplay-slot-game-job` 是否有 request log consumer / compensation job。
- 未把任何單條 candidate flow 升級為履歷 claim。
- 未更新 `05-resume-master-zh.md` / `08-application-autobiography-zh.md`；Step 1 只作 Flow Track 選題。

## 8. Step 1 結論

本 repo 的 Flow Track 第一批最值得比較的是:

1. `slot-bet-settle-rollback`
2. `transfer-wallet-money-in-out`
3. `request-log-rabbitmq-async`
4. `bet-record-sharding-schema-route`
5. `runtime-rtp-darkpool-player-control`

歷史建議：當時下一步應做 Step 2，比較這 5 條 candidate flows 的面試價值、Nick direct evidence、掃描成本、履歷風險，並決定第一條進 Step 3 的代表 flow。

2026-06-05 檢查：本 project 的 Step 2、五條代表 flows Step 5、project-level contribution claim consolidation refresh 與後續 rolling resume package 都已完成；本檔只保留 Step 1 candidate 歷史，不再作下一步 prompt。
