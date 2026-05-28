# Senior / Owner Flow Inventory

用途: 這份是 flow dashboard，用來決定下一步做哪條 flow。

它不是研究報告，不放完整分析。完整分析放在:

```text
projects/{domain}/{project}/flows/{flow-name}/flow.md
```

## 使用規則

- 每次只做一條 flow。
- 如果 Nick 要的是待辦事項、缺口清單、KB 維護或優先順序，本檔只用來列候選與排序；AI 不得因為本檔出現 queue 就自動執行 Step 4 / Step 5，必須等 Nick 明確下 Step 或 consolidation。
- 已完成 Step 5 只代表單條 flow 完成，不代表整個 project 完成。
- 履歷欄位只表示「能不能考慮」，不代表已寫進 `05-resume-master-zh.md`。
- 證據欄位要分清楚：`真實開發過`、`專案存在 / code-backed`、`分析素材 / learning-only`、`外部案例 / non-local`、`待確認`。
- 沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認，不寫主導、獨立完成、改善百分比。
- 只看到後台 / 前端 / BI 入口的 flow，優先當面試分析素材，不急著寫履歷。
- Source repo 清單看 `projects/source-repo-inventory.md`；真正做 flow 前仍要重讀 code branch / log / path-specific history。

## 跨 repo 優先排序

用途：當 Nick 只問「下一個整理哪個 project / repo」時，先用這份排序判斷；真正開工前仍必須做該 repo 的 Step 1 / Step 2，不能把本表當 code evidence。

評估目標：Senior Java Backend / Platform Backend / System Owner 的履歷與面試素材價值。優先看 production flow、provider integration、wallet / bet / settlement、idempotency、retry / compensation、reconciliation 邊界、observability、rollout / rollback。workspace、官網、前端、mock、simulator 預設只當輔助入口。

| 排名 | Project / repo | 初步判斷 |
| --- | --- | --- |
| 1 | `math-core` / `*-math` | 差異化最高；若有遊戲數學、RTP、派彩、模擬驗證，是最稀缺素材 |
| 2 | `payment` | 第三方金流 provider request / callback / query / withdraw、訂單狀態、MQ retry、上分 / 退款與查單 / 人工補償邊界；高價值但不可包裝成完整 wallet / ledger owner |
| 3 | `iwin_gameserver` | 遊戲 runtime / wallet source of truth，可追投注、派彩、退款、錢包一致性 |
| 4 | `third_games_api` | 第三方遊戲 seamless wallet、bet / settle / rollback，交易邊界清楚 |
| 5 | `antplay-slot-game-api` | 遊戲 API 主線，可能接下注、遊戲啟動、玩家狀態與錢包 |
| 6 | `game_api` | 玩家端 / partner API orchestration，有 coupon 上分、partner deposit / withdraw、agent bonus |
| 7 | `antplay-slot-game-job` | 遊戲批次、報表、結算、projection / replay 可能性高 |
| 8 | `game_job` | BI projection、daily summary、備份、重跑安全與資料一致性 |
| 9 | `openobserve` | Observability / log / tracing / metrics，適合 System Owner 故障定位素材 |
| 10 | `kafka` | Event-driven consistency、consumer lag、retention、retry / DLQ 題材 |
| 11 | `antplay-slot-admin-api` | 後台 API；若有設定、風控、人工修正，可支援 owner story |
| 12 | `k3s-deploy` | K8s / Kustomize rollout、config / secret、probe、rollback |
| 13 | `ugsoft-connector-api` | 可能是 external integration / connector API；若有重試、冪等、同步，可升級 |
| 14 | `antplay-api-deploy` | API deploy 主線，適合補 rollout / rollback / config 管理 |
| 15 | `antplay-push-grpc` | gRPC realtime / push pipeline；若有 delivery / retry / backpressure 可升級 |
| 16 | `ugsoft-admin-api` | 後台 API；價值取決於是否只是 CRUD，或有審核 / 修正 / 風控 |
| 17 | `app_bi` | 已整理完；後台 / BI / control plane，輔助追後端，不當主線 |
| 18 | `antplay-push` | push runtime，需確認是否只是 wrapper |
| 19 | `antplay-bot` | bot / automation / ops tooling，通常輔助 |
| 20 | `antplay-tg-notify` | 通知 / 告警，能補 observability，但不是主線 |
| 21 | `buffer-id` | 若是 ID generator，可當 platform 小亮點，需確認 |
| 22 | `ci-template` | CI 標準化素材，中等偏輔助 |
| 23 | `antplay-docker-deploys` | Docker deploy / env 管理，價值看 production 接近度 |
| 24 | `bi_share` | 已做 rolling / scoped negative consolidation；偏 legacy Laravel 分享 / 佣金 / BI 報表，未見 Nick direct commits，不當正式履歷主線 |
| 25 | `shareinstall-back` | 安裝歸因 / tracking / reward 可能有價值，但目前未知 |
| 26 | `payment-thirdparty-simulator` | 支援 payment 測試，不當主專案 |
| 27 | `platform-mock` | mock / contract 支援，不當主線 |
| 28 | `antplay-slot-lib` | 未知共用庫；若含 game state SDK 再升級 |
| 29 | `antplay-tool-transfer-web` | 工具 web；除非牽涉 money repair，否則偏輔助 |
| 30 | `ugsoft-admin-web` | 後台前端，作入口，不當主線 |
| 31 | `antplay-slot-admin` | 後台前端，作入口，不當主線 |
| 32 | `antplay-web-deploy` | Web deploy，通常低於 API deploy |
| 33 | `official-web-v3` | 官網，履歷價值低 |
| 34 | `official-web-v2` | 官網舊版，履歷價值低 |
| 35 | `official-web` | 官網舊版，履歷價值低 |
| 36 | `iwin_client_unity` | Client 端，對 Backend 主線較低 |
| 37 | `ci-test` | sandbox / 測試 repo 可能性高 |
| 38 | `iwin-workspace` | workspace 索引，不當 flow 主題；contribution consolidation 已完成，只作 KB / cross-repo reconstruction supporting evidence |
| 39 | `ugsoft-workspace` | workspace 索引，不當 flow 主題 |
| 40 | `math-workspace` | workspace 索引，不當 flow 主題 |

使用提醒：

- 若目標是最快產出 Senior Backend 履歷素材，依最新 KB 先檢查 Step 2 定義的本批代表 flows 是否都完成。`payment`、`game_job`、`game_api` 已完成 project-level consolidation，其中 `payment` 已於 2026-05-20 重新覆核並補入 GoldenPay direct evidence，不需要因新規則重做。`game_api` 正式履歷只採 coupon 保守 claim，partner / agent bonus 只作 code-backed 面試素材。
- 2026-05-26 code-first claim audit：`payment` 確認有 Nick / `10gt12nc` 多 provider 對接與維護 evidence，定位要收斂為 provider / 商戶 integration、payment / withdraw order consistency、查單與人工補償邊界；不得把 `payment` 單獨寫成完整 money correctness、wallet、ledger、reconciliation 或完整金流 owner。2026-05-20 補充仍有效：GoldenPay 可列入多 provider evidence，但不得寫成主導完整金流、全部 provider owner 或 GoldenPay production owner。
- 2026-05-20 補充：`iwin_gameserver` 已完成 rolling / scoped contribution consolidation。Nick / `10gt12nc` 在 Antplay / GSC / PG 第三方 provider 投派整合、gameserver money job、`GamePlayer` log dispatch 與 log reel path 有 direct commits；可保守寫「參與第三方遊戲 provider 投派整合與 gameserver 錢包 / 投注流水串接」，不得寫成完整 gameserver owner、完整 wallet owner、完整上分 / 下分 owner 或完整 idempotency / reconciliation owner。
- 2026-05-20 補充：`iwin-workspace` 已完成 rolling / scoped contribution consolidation。Nick / `10gt12nc` 有大量 KB / docs / environment index / tool direct commits，但 repo 本身不是 production service，不新增 standalone 正式履歷主成果；只作工作方法、cross-repo system reconstruction 與 knowledge governance supporting evidence。
- 2026-05-27 補充：`ugsoft-admin-api` 已完成 contribution consolidation refresh。Nick / `10gt12nc` 在 login / JWT / RBAC、商戶 / provider 白名單、超級代理、報表查詢、風控監控、RabbitMQ request log / bet record 與 Quartz / report job 有大量 direct commits；可保守寫「參與 UGSoft 後台 API / control plane 與非同步資料處理開發維護」，本次 refresh 已把 `connect-bet-record-mq-ingestion Step 5`、`request-log-rabbitmq-admin-consumer Step 5` 與 `game-api-provider-white-ip-control-plane Step 5` 回填 project-level claim。不得寫成完整 UG 平台、完整 provider gateway、完整 wallet / money flow、完整 RabbitMQ architecture owner 或完整 access-control platform owner。目前本 project 已收斂，沒有預設下一步。
- 2026-05-20 補充：`ugsoft-connector-api` 已完成 rolling contribution consolidation。Nick / `10gt12nc` 在 AntPlay / DerPlay provider adapter、login / balance / transfer in-out / bet-settle / callback、request / bet record MQ、transfer wallet compensation、分表與 circuit breaker code-backed reliability 有 direct commits / code evidence；可保守寫「參與 UGSoft provider connector / gateway 開發維護」，不得寫成完整 connector architecture owner、全部 provider owner、完整 wallet / ledger / reconciliation owner 或 exactly-once / outbox owner。
- 2026-05-25 補充：`antplay-slot-game-job` 已完成 contribution consolidation refresh。五條代表 flows 均已 Step 5；Nick / `10gt12nc` 在 Kafka consumer / Quartz job、代理玩家報表 projection、big-win notification、report currency / key 修正與 db partition / job config 有 direct commits，activity accumulated bet 有 Nick merge + code-backed evidence，settle pool / darkpool 只作 analysis-first。可保守寫「參與 AntPlay slot job / event processing 開發維護」，不得寫成完整 Kafka event platform、完整 reward platform、完整 settle pool / risk / jackpot owner、完整 DB sharding / schema routing owner 或完整 BI / report platform owner。
- 2026-05-21 補充：`math-core` 與 `*-math` 已完成 contribution consolidation；`*-math` 五條代表 flows 已全部 Step 5 並完成 refreshed grouped claim。Nick / `10gt12nc` 在 `math-core` 有 slot math contract / debugBet / RTP / symbol direct commits；71 個 `*-math` repo 中有 49 個有 direct commits，強 evidence 是 `sph-math`、`spn-math`、`sfm-math`、`setl-math`、`sdt-math`、`slc-math`。可保守寫「參與 AntPlay slot math core / math module 維護與驗證」，包含 SlotMath contract、debug bet、fixedMultiBet、currency、RTP / reel strip、buy free、jackpot / symbol、特殊 feature result contract 與 simulation validation；不得寫成完整遊戲數學模型、全部 math module、完整 RTP 策略、完整 simulator / certification owner、完整 jackpot pool 或單一遊戲 feature owner。
- 2026-05-20 補充：`math-workspace` 已完成 rolling consolidation，只作 cross-math KB / validation workflow supporting evidence；`platform-mock` 只有局部 failure injection commits，只作 provider failure testing supporting evidence；`buffer-id` 未見 Nick direct commits，只作 learning-only。
- 若目標是差異化面試題，`*-math fixed-multi-bet-currency-math-core-compatibility` Step 5 已完成，`rtp-reel-strip-simulation-validation` Step 5 已完成，`buy-free-scatter-rtp3-result-contract` Step 5 已完成，`jackpot-symbol-hit-and-prize-scaling` Step 5 已完成，`special-wild-feature-state-transform` Step 5 已完成；`*-math contribution claim consolidation` refresh 也已完成。`antplay-slot-game-api slot-bet-settle-rollback Step 5`、`transfer-wallet-money-in-out Step 5`、`request-log-rabbitmq-async Step 5`、`bet-record-sharding-schema-route Step 5`、`runtime-rtp-darkpool-player-control Step 5`、project-level contribution claim consolidation refresh、2026-05-27 `rolling resume package`、`104 投遞欄位檢查`、`04 / 面試 case 對齊檢查` 與三條主力 case 口說稿已完成；`antplay-slot-game-job` 五條代表 flows Step 5 與 contribution consolidation refresh 也已完成，`ugsoft-admin-api` / `ugsoft-connector-api` contribution refresh 已回填 05 / 08 / 04 / 17。`iwin iwin_gameserver center-http-deposit-withdraw Step 5`、`game-spin-settlement-log-reel Step 5`、`bet-target-set-query Step 5`、`third_games_api gsc-transfer-bet-settle-rollback Step 5`、`third_games_api oneapi-wallet-bet-result Step 5`、`third_games_api antplay-bet-settle-rollback Step 5`、`third_games_api gsc-seamless-withdraw-deposit-cancel Step 5` 與 `k3s-deploy gameserver-phased-rollout Step 5` 也已完成。面試口說練習目前先暫停；除非 Nick 明確說「開始練 / 模擬面試 / 我先講一版」，AI 不得主動進入問答式實戰練習。
- 若目標是 Platform / System Owner，`openobserve`、`kafka`、`k3s-deploy`、`antplay-api-deploy` 可往前，但必須和實際 production flow / incident / rollout evidence 串起來。

## 狀態定義

| 狀態 | 意義 |
| --- | --- |
| `未開始` | 只有候選題，尚未做 Step 1 |
| `Step 1` | 已找 candidate flows |
| `Step 2` | 已做 flow ranking |
| `Step 3` | 已有單條 flow 深挖 |
| `Step 4` | 已轉面試 case |
| `Step 5` | 已完成單條 flow claim gate；正式履歷 / 自傳仍以 project contribution consolidation 為準 |
| `暫停` | 目前 evidence 不足或價值不夠，先不做 |

## 目前進度

| Domain | Project | Flow | 中文名稱 | 價值 | 狀態 | 證據層級 | 履歷 | 下一步 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| iwin | app_bi | `point-control-admin-operation` | 單點控制 / 營運控制操作 | 中 | Step 5 | 專案存在 / code-backed；Nick 貢獻依三層 claim gate 判斷 | 否 | 已收斂 |
| iwin | app_bi | `admin-config-redis-sync` | 後台設定同步 Redis | 中 | Step 5 | 專案存在 / code-backed；Nick 貢獻依三層 claim gate 判斷 | 否 | 已收斂 |
| iwin | app_bi | `daily-game-record-summary` | 每日遊戲資料彙總 | 中 | Step 5 | 專案存在 / code-backed；Nick 貢獻依三層 claim gate 判斷 | 否 | 已收斂 |
| iwin | app_bi | `game-round-record-query` | 遊戲局紀錄查詢 | 中 | Step 5 | app_bi 專案存在 / iwin_gameserver 有 Nick commit 線索 | 否 | 已收斂 |
| iwin | app_bi | `contribution-claim-consolidation` | app_bi rolling / scoped negative 收口 | 中 | 已完成 | 專案存在 / code-backed；Nick app_bi direct contribution 未確認 | 否，不放正式履歷主成果 | 已收斂 |
| iwin | game_api | `coupon-redeem-credit-grant` | 優惠券兌換上分 / 打碼要求 | 高 | Step 5 | 真實開發過 + code-backed；`10gt12nc` 有 game_api / iwin_gameserver coupon commits | 可作 flow evidence；不代表完整 project | 已收斂 |
| iwin | game_api | `partner-deposit-withdraw-bill` | Partner API 上分 / 下分 / 查單 | 很高 | Step 5 | 專案存在 / code-backed；未見 Nick direct path evidence | 否，先作面試素材 | 已收斂 |
| iwin | game_api | `agent-bonus-receive-transfer` | 代理佣金領取 / 轉帳 | 高 | Step 5 | 專案存在 / code-backed；目前未見 Nick direct path evidence | 否，先作面試素材 | 已收斂 |
| iwin | game_api | `contribution-claim-consolidation` | game_api 實際開發貢獻收斂 | 高 | 已完成 / 2026-05-20 已覆核 | 部分真實開發過 + code-backed；coupon direct evidence；partner / agent bonus interview-only | 是，保守使用 coupon | 履歷 claim 已收斂，不因本輪重做 |
| iwin | payment | `contribution-claim-consolidation` | payment 實際開發貢獻收斂 | 高 | 已完成 / 2026-05-20 已覆核 | 本人確認 + 真實開發過 + code-backed；GoldenPay direct evidence 已補入 | 是，保守更新 | 履歷 claim 已收斂；不是全 payment project 完成 |
| iwin | payment | `payment-provider-callback` | 金流 provider callback | 高 | Step 5 | 單條 flow code-backed；project-level 有多 provider callback / sign 維護 evidence | 併入 payment project bullet | 已收斂 |
| iwin | payment | `withdrawal-auto-review-refund` | 玩家提款、自動審核 / 自動出款與失敗退款 | 高 | Step 5 | 單條 flow code-backed；withdraw insert / null-safety 有有限維護 evidence | 不單獨寫自動出款 owner | 已收斂 |
| iwin | payment | `payment-order-provider-request` | 充值建單與 provider request | 高 | Step 5 | 部分真實開發過：多 provider request / callback / query / withdraw evidence；整體金流 owner 不誇大 | 是，保守更新 | 已收斂 |
| iwin | payment | `manual-order-review-repair` | 人工審核 / 補單 / 訂單修復 | 中高 | Step 5 | code-backed；不單獨升級人工修復 owner | 否，作面試素材 | 已收斂 |
| iwin | payment | `payment-channel-config-selection` | 支付列表 / 商戶設定選擇 | 中 | Step 5 | code-backed；不單獨升級支付設定 owner | 否，作面試素材 | 已收斂 |
| iwin | third_games_api | `gsc-transfer-bet-settle-rollback` | GSC transfer 投注 / 派彩 / rollback | 高 | Step 5 | 專案存在 / code-backed；rolling consolidation 後仍不作 Nick 正式成果 | 否，作面試素材 | 已收斂；下一條是 OneAPI |
| iwin | third_games_api | `oneapi-wallet-bet-result` | OneAPI / PG bet_result 投派 callback | 高 | Step 5 | 專案存在 / code-backed；下游 PGTransferInOut direct evidence 歸屬 iwin_gameserver | 否，作面試素材 | 已收斂；下一條是 Antplay |
| iwin | third_games_api | `antplay-bet-settle-rollback` | Antplay 投注 / 結算 / rollback 三段式流程 | 高 | Step 5 | 專案存在 / code-backed；下游 Antplay gameserver direct evidence 歸屬 iwin_gameserver | 否，作正式面試素材 | 已收斂；下一條是 GSC split |
| iwin | third_games_api | `gsc-seamless-withdraw-deposit-cancel` | GSC split withdraw / deposit / rollback / cancel | 中高 | Step 5 | 專案存在 / code-backed；split endpoint production status 待確認；未見 Nick direct path evidence | 否，作正式面試素材 | 已收斂 |
| iwin | third_games_api | `contribution-claim-consolidation` | third_games_api rolling / scoped 收口 | 中高 | 已完成 / 2026-05-20 | 專案存在 / code-backed；本 repo 只有局部測試 / merge 線索；下游 gameserver direct evidence 已歸入 iwin_gameserver | 否，不放 standalone 正式履歷主成果 | 已收斂 |
| iwin | game_job | `daily-game-data-summary` | 每日遊戲資料彙總 | 中高 | Step 5 | 真實開發過 + code-backed；`10gt12nc` 有 daily summary / 時區 / 留存 / 備份相關 commits | 是，併入 game_job project bullet | 已收斂 |
| iwin | game_job | `third-party-record-mongo-backup` | 第三方遊戲紀錄 Mongo 備份與清理 | 中高 | Step 5 | 局部真實開發過 + code-backed；`10gt12nc` 有 GSC 分批查詢 / batch size 調整 commits | 是，併入 game_job project bullet | 已收斂 |
| iwin | game_job | `coin-flow-batch-projection` | 金幣流水清算 / 玩家行為投影 | 高 | Step 5 | 專案存在 / code-backed；目前未見 Nick direct path evidence | 否，先作面試素材 | 已收斂 |
| iwin | game_job | `online-payment-data-cleaning` | 充值 / 提現資料清洗與每日經濟資料 | 中 | Step 5 | 專案存在 / code-backed；目前未見 Nick direct path evidence | 否，先作面試素材 | 已收斂 |
| iwin | game_job | `partition-table-creation` | 每日 / 每月分表建立 | 中低 | Step 5 | 專案存在 / code-backed；目前未見 Nick direct path evidence | 否，先作面試素材 | 已收斂 |
| iwin | game_job | `contribution-claim-consolidation` | game_job 實際開發貢獻收斂 | 高 | 已完成 / 2026-05-20 已覆核 | 部分真實開發過 + code-backed；daily summary + GSC backup direct evidence | 是，保守更新 | 履歷 claim 已收斂，不因本輪重做 |
| iwin | iwin_gameserver | `third-party-transfer-in-out` | 第三方遊戲投派整合 / 投注派彩退款 | 高 | Step 5 | 部分真實開發過 + code-backed；Antplay / GSC / PG gameserver money job / log projection direct commits | 是，併入 iwin_gameserver project bullet | 已收斂 |
| iwin | iwin_gameserver | `center-http-deposit-withdraw` | center_http 上分 / 下分 | 高 | Step 5 | 專案存在 / code-backed；已完成正式面試 case 與 claim gate；Nick direct path evidence 不足 | 否，保留 interview-only | 已收斂 |
| iwin | iwin_gameserver | `game-spin-settlement-log-reel` | 遊戲 spin / 結算 / 投注流水 | 高 | Step 5 | 一般 Game40 spin 為 code-backed；provider log reel / 投派整合為部分真實開發過 + code-backed | 否，不單獨放一般 slot spin；provider log reel 回填既有 project claim | 已收斂 |
| iwin | iwin_gameserver | `bet-target-set-query` | 打碼目標設定與查詢 | 中高 | Step 5 | 分層 claim；coupon 打碼入口是真實開發過 + code-backed，完整打碼系統為 code-backed interview-only | 不新增獨立履歷 bullet；作 coupon supporting evidence | 已收斂 |
| iwin | iwin_gameserver | `contribution-claim-consolidation` | iwin_gameserver 實際開發貢獻收斂 | 高 | 已完成 / 2026-05-22 已回填 game-spin Step 5 | 部分真實開發過 + code-backed；第三方 provider 投派整合 direct evidence；center-http 與一般 Game40 spin 仍 interview-only | 是，保守使用 third-party provider 投派整合 | 已收斂；bet-target Step 5 已回填 |
| iwin | iwin-workspace | `contribution-claim-consolidation` | workspace / KB / docs / environment index 收口 | 低 | 已完成 / 2026-05-20 | 真實做過 KB / docs / workspace 維護；不是業務 service 開發 | 否，不放 standalone 正式履歷主成果 | 已收斂；只作 supporting evidence |
| iwin | k3s-deploy | `gameserver-phased-rollout` | gameserver phase rollout / rollback | 中高 | Step 5 | 專案存在 / code-backed；未見 Nick direct commit，維持 interview-only | 否 | 已收斂 |
| iwin | bi_share | `contribution-claim-consolidation` | bi_share rolling / scoped negative 收口 | 中低 | 已完成 | 專案存在 / code-backed；Nick bi_share direct contribution 未確認 | 否，不放正式履歷主成果 | 已收斂；若要深挖先做 Step 1 |
| ugsoft | ugsoft-admin-api | `contribution-claim-consolidation` | 後台 API / control plane / RabbitMQ 非同步資料處理收口 | 中高 | refreshed / 2026-05-27 | 真實開發過 + code-backed；Nick / `10gt12nc` 有大量 direct commits，本批三條代表 flow 已 Step 5 | 是，保守補入後台 API / async data processing / white IP control plane | 已收斂；不寫完整 UG 平台 / provider gateway / RabbitMQ / access-control owner |
| ugsoft | ugsoft-admin-api | `step1-candidate-flows` | 後台 control plane / RabbitMQ / Quartz 候選 flow 盤點 | 中高 | Step 1 / 2026-05-27 | 真實開發過 + code-backed；local `main` 落後 `origin/main` 42 commits，依 local code + fetched remote log / snapshots 判斷；`arnold` 只作主管 context | 否，先作 Flow Track 選題 | Step 2 已完成 |
| ugsoft | ugsoft-admin-api | `step2-flow-comparison` | admin-api 本批代表 flow 比較 | 中高 | Step 2 / 2026-05-27 | 真實開發過 + code-backed；已選 `connect-bet-record-mq-ingestion`、`request-log-rabbitmq-admin-consumer`、`game-api-provider-white-ip-control-plane` | 否，先作 Flow Track 排序 | 第一條 `connect-bet-record-mq-ingestion Step 5`、第二條 `request-log-rabbitmq-admin-consumer Step 5`、第三條 `game-api-provider-white-ip-control-plane Step 5` 已完成 |
| ugsoft | ugsoft-admin-api | `connect-bet-record-mq-ingestion` | Connector BetRecord MQ 入庫與 quota update | 高 | Step 5 / 2026-05-27 | 真實開發過 + code-backed；Nick / `10gt12nc` 有 BetRecord MQ 初版、consumer 調整、mapper query、currency default direct evidence；quota monitoring / id generation / amount normalization 為 `arnold` current behavior | 可回填 project-level RabbitMQ / BetRecord async evidence；不直接更新 05 / 08 | 已收斂；第二條 `request-log-rabbitmq-admin-consumer Step 5` 已完成 |
| ugsoft | ugsoft-admin-api | `request-log-rabbitmq-admin-consumer` | RequestLog RabbitMQ 非同步入庫 | 高 | Step 5 / 2026-05-27 | 真實開發過 + code-backed；Nick / `10gt12nc` 有 RequestLog RabbitMQ 非同步化、listener / mapper / processor direct evidence；`arnold` request-log key / query 修正只作主管 context | 可回填 project-level RabbitMQ / request log async evidence；不直接更新 05 / 08 | 已收斂；第三條 `game-api-provider-white-ip-control-plane Step 5` 已完成 |
| ugsoft | ugsoft-admin-api | `game-api-provider-white-ip-control-plane` | Game API / provider white IP 後台控制面 | 中高 | Step 5 / 2026-05-27 | 真實開發過 + code-backed；Nick / `10gt12nc` 有 Game API white IP 與 provider white IP CRUD direct evidence；`arnold` provider fanout reload / global scope 只作主管 context；connector reload / filter 為下游 code-backed context | 已回填 project-level white IP control plane / runtime access-control supporting evidence；不直接更新 05 / 08 | 已收斂；admin-api contribution refresh 已完成 |
| ugsoft | ugsoft-connector-api | `contribution-claim-consolidation` | provider connector / gateway / transfer wallet / MQ 收口 | 高 | refreshed / 2026-05-27 | 真實開發過 + code-backed；Nick / `10gt12nc` 有大量 direct commits，三條代表 flow 已 Step 5 | 是，保守補入 provider connector / transfer wallet / callback / request-bet-record MQ / job sync | 已收斂；不寫完整 gateway / wallet / reconciliation owner |
| ugsoft | ugsoft-connector-api | `transfer-wallet-in-out-query` | Transfer wallet in / out / out-all / 查單 | 高 | Step 5 / 2026-05-27 | 真實開發過 + code-backed 混合；adapter transfer / DerPlay 查單為 Nick direct evidence，transaction facade / Redis guard 為團隊 context | 可回填 project-level provider connector / transfer wallet evidence；不單獨寫完整 owner | 已完成；下一條 `provider-callback-bet-settle-to-mq` |
| ugsoft | ugsoft-connector-api | `provider-callback-bet-settle-to-mq` | Provider callback / bet-settle / MQ 入庫 | 高 | Step 5 / 2026-05-27 | 真實開發過 + code-backed 混合；callback 寫 MQ、admin consumer、currency / pt_day 有 Nick direct evidence，最新 whitelist / subAgent / amount scaling 為團隊 context | 可回填 project-level provider connector / callback / MQ evidence；不單獨寫完整 owner | 已完成；第三條 `request-bet-record-mq-sync` Step 5 已完成 |
| ugsoft | ugsoft-connector-api | `request-bet-record-mq-sync` | Provider bet record job sync / late data 補資料到 MQ | 中高 | Step 5 / 2026-05-27 | 真實開發過 + code-backed 混合；BetRecord MQ、job、跨日 pt_day、currency 修正有 Nick direct evidence，Redis watermark / 最新 sync behavior 多為團隊 context | 已回填 project-level job-driven bet record sync / MQ evidence；不單獨寫完整 reconciliation / outbox owner | 已收斂；connector refresh 已完成 |
| ugsoft | ugsoft-workspace | `contribution-claim-consolidation` | workspace / docs / harness / runbook 收口 | 低 | 已完成 / 2026-05-20 | supporting evidence；主要 author 為 `arnold`，Nick 已確認是主管帳號，不是 Nick direct evidence | 否，不放 standalone 正式履歷主成果 | 已收斂；不作預設下一步 |
| antplay | antplay-slot-admin-api | `step1-candidate-flows` | 後台 API / 商戶控制面 / 風控監控 / RabbitMQ 非同步候選 flows | 中高 | Step 1 / 2026-05-28 | 真實開發過 + code-backed；Nick / `10gt12nc` 有大量 direct commits；fetch 失敗，依本地 refs / 本地工作樹判斷 | 否，Step 1 不直接更新 05 / 08；既有 consolidation 已保守補入後台 API / risk ops / async data processing | Step 2 已完成 |
| antplay | antplay-slot-admin-api | `step2-flow-comparison` | 選定 RequestLog RabbitMQ admin consumer 作第一條代表 flow，Game API 白名單作第二條 | 中高 | Step 2 / 2026-05-28 | 真實開發過 + code-backed；Rank 1 有 `#774` RequestLog MQ direct commits，Rank 2 有 `#682` / `#684` Game API 白名單 direct commits，fetch 失敗，依本地 refs / 本地工作樹判斷 | 否，Step 2 不直接更新 05 / 08；待單條 flow Step 5 或 contribution refresh | Rank 1 Step 5 已完成；Rank 2 Step 3 已完成；下一步可選 `game-api-whitelist-sync Step 4` |
| antplay | antplay-slot-admin-api | `request-log-rabbitmq-admin-consumer` | RequestLog RabbitMQ admin consumer / audit log 入庫 | 中高 | Step 5 / 2026-05-28 | 真實開發過 + code-backed；admin-api `#774` listener / mapper / processor direct evidence，upstream game-api `#774` producer direct evidence；fetch 失敗，依本地 refs / 本地工作樹判斷 | 不直接更新 05 / 08；可回填 project-level supporting evidence / 面試 case | 已完成；Rank 2 `game-api-whitelist-sync Step 3` 已完成 |
| antplay | antplay-slot-admin-api | `game-api-whitelist-sync` | Game API 白名單控制面 / DB + Redis 同步 | 中高 | Step 3 / 2026-05-28 | 真實開發過 + code-backed；admin-api `#682` 白名單 control plane direct evidence，game-api `#684` runtime `WhiteIpFilter` direct evidence；fetch 失敗，依本地 refs / 本地工作樹判斷 | 暫不直接更新 05 / 08；先作後台 control plane / runtime access-control 面試素材 | 下一步 `game-api-whitelist-sync Step 4` |
| antplay | antplay-slot-game-api | `contribution-claim-consolidation` | 遊戲 API runtime / 下注結算 / 轉帳錢包 / RabbitMQ request log 收口 | 高 | 已完成 / refreshed / 2026-05-21；05 / 08 已回填 | 真實開發過 + code-backed；Nick / `10gt12nc` 有大量 direct commits；五條代表 flow 均已 Step 5 | 是，保守補入 game runtime / betting-settlement / transfer wallet / async log / high-traffic table governance / runtime decision；仍不寫完整 RTP / math / wallet / sharding owner | 已收斂；沒有預設下一步 |
| antplay | antplay-slot-game-api | `step1-candidate-flows` | slot bet / transfer wallet / request log / sharding / RTP runtime 候選 flow 盤點 | 高 | Step 1 / 2026-05-21 | 真實開發過 + code-backed；source fetch 失敗，依本地 refs / 本地 working tree | 否，先作 Flow Track 選題；不直接更新 05 / 08 | 已收斂；Step 2 已完成 |
| antplay | antplay-slot-game-api | `step2-flow-comparison` | 選定 slot bet / settle / rollback 作第一條代表 flow | 高 | Step 2 / 2026-05-21 | 真實開發過 + code-backed；source fetch 失敗，依本地 refs / 本地 working tree | 否，先作 Flow Track 排序；不直接更新 05 / 08 | 已收斂；Rank 1 Step 3 已完成 |
| antplay | antplay-slot-game-api | `slot-bet-settle-rollback` | Slot 下注 / 開獎 / 結算 / rollback | 很高 | Step 5 / 2026-05-21 | 真實開發過 + code-backed；`10gt12nc` 有 deadlock / transfer wallet / request log MQ direct commits；source fetch 失敗，依本地 refs / 本地 working tree | 是，併入 project-level claim；不單獨寫完整 owner | 已完成；後續 `transfer-wallet-money-in-out Step 5` 已完成 |
| antplay | antplay-slot-game-api | `transfer-wallet-money-in-out` | Transfer wallet 轉入 / 轉出 / 全額轉出 / 查單 | 很高 | Step 5 / 2026-05-21 | 真實開發過 + code-backed；`10gt12nc` 有分表 / schema route / transaction lookup / `updatePlayerWallet` rows check direct commits，API 初版多為他人 commits；source fetch 失敗，依本地 refs / 本地 working tree | 是，回填 project-level consolidation；不單獨寫完整 transfer wallet owner | 已完成；後續 `request-log-rabbitmq-async Step 5` 已完成 |
| antplay | antplay-slot-game-api | `request-log-rabbitmq-async` | Request log RabbitMQ 非同步化 | 中高 | Step 5 / 2026-05-21 | 真實開發過 + code-backed；`10gt12nc` 有 #774 producer / consumer direct commits；source fetch 失敗，依本地 refs / 本地 working tree；routing key 最終格式修正屬他人 context | 是，回填 project-level async audit / observability claim；不單獨寫完整 RabbitMQ owner | 已完成；後續 `bet-record-sharding-schema-route Step 5` 已完成 |
| antplay | antplay-slot-game-api | `bet-record-sharding-schema-route` | Bet record / request log / transfer transaction 分表與 schema routing | 中高 | Step 5 / 2026-05-21 | 真實開發過 + code-backed；`10gt12nc` 有 #167 分表、db partition v2、`@UseSchema`、table creator 與 bet record query fix direct commits；source fetch 失敗，依本地 refs / 本地 working tree；logical-to-physical live route 待確認 | 是，回填 project-level high-traffic table governance；不單獨寫完整 sharding owner | 已完成；後續 `runtime-rtp-darkpool-player-control Step 5` 已完成 |
| antplay | antplay-slot-game-api | `runtime-rtp-darkpool-player-control` | RTP / dark pool / player control runtime decision | 中高 | Step 5 / 2026-05-21 | 真實開發過 + code-backed；`10gt12nc` 有 `GameFacade#bet` respin loop、dark pool `maxWin`、timeout refund branch、player control setData caller、deadlock / afterBet failure window direct evidence；source fetch 失敗，依本地 refs / 本地 working tree；math module / consumer / reconciliation 未掃 | 是，回填 project-level runtime decision claim；不單獨寫完整 RTP / math / jackpot owner | 已完成；project consolidation refresh 已完成 |
| antplay | antplay-slot-game-job | `contribution-claim-consolidation` | Kafka / Quartz job / 報表 projection / big-win notification / activity / partition 收口 | 中高 | refreshed / 2026-05-25；05 / 08 / 17 已回填 | 真實開發過 + code-backed；五條代表 flows 已 Step 5；settle pool 為 analysis-first，不作 direct claim | 是，保守補入 job / event processing / report projection / summary / notification / activity supporting / partition；已由 rolling resume package 回填 | 已收斂；練習暫停，等 Nick 明確要求 |
| antplay | antplay-slot-game-job | `step1-candidate-flows` | proxy report / activity voucher / big-win / settle pool 候選 flow 盤點 | 中高 | Step 1 / 2026-05-22 | 真實開發過 + code-backed；source fetch 失敗，依本地 refs / 本地 working tree；flow-level claim 待 Step 2+ | 否，先作 Flow Track 選題；不直接更新 05 / 08 | 已收斂；Step 2 與五條代表 flows 已完成 |
| antplay | antplay-slot-game-job | `step2-flow-comparison` | 選定 proxy-user-data-report-projection 作第一條代表 flow | 高 | Step 2 / 2026-05-22 | 真實開發過 + code-backed；source fetch 失敗，依本地 refs / 本地 working tree；`#386` / `#590` / `#702` direct evidence 最集中 | 否，先作 Flow Track 排序；不直接更新 05 / 08 | 已收斂；五條代表 flows 與 consolidation refresh 已完成 |
| antplay | antplay-slot-game-job | `proxy-user-data-report-projection` | 代理玩家報表 Kafka projection / Quartz summary | 高 | Step 5 / 2026-05-25 | 真實開發過 + code-backed；`#386` / `#590` / `#702` / `fix ag_report_player` direct evidence；Kafka config 有基本 retry / dead-letter-topic evidence；source fetch 失敗，依本地 refs / 本地 working tree | 是，回填 project-level report projection / Quartz summary evidence；不單獨更新 05 / 08 | 已完成；Rank 2 Step 5 已完成 |
| antplay | antplay-slot-game-job | `activity-accumulated-bet-voucher` | 活動累計投注送 voucher / free spin | 中高 | Step 5 / 2026-05-25 | 專案存在 / code-backed + Nick merge evidence；`BetVoucherService` 下游 implementation / DB unique key 未在本 repo；implementation 主要 Gill，後續 Arnold / Eliot context；source fetch 失敗，依本地 refs / 本地 working tree | 否，只作 reward correctness 面試素材與 project-level supporting evidence；不單獨更新 05 / 08 | 已完成；Rank 3 Step 5 已完成 |
| antplay | antplay-slot-game-job | `big-win-notification` | 中大獎通知 / push user message | 中高 | Step 5 / 2026-05-25 | 真實開發過 + code-backed；`#303` direct evidence；current behavior 後續 Gill / Arnold / Eliot 修改；source fetch 失敗，依本地 refs / 本地 working tree；下游未見 `_id` dedupe / `fullPlayerName` filtering | 是，作 project-level supporting evidence；不單獨更新 05 / 08 | 已完成；Rank 4 / Rank 5 Step 5 皆已完成 |
| antplay | antplay-slot-game-job | `settle-pool-monitor-darkpool-sync` | settle pool / dark pool Redis DB sync | 中高 | Step 5 / 2026-05-25 | 專案存在 / code-backed / analysis-first；current implementation 主要 Arnold / Eliot，未找到 Nick direct evidence；source fetch 失敗，依本地 refs / 本地 working tree；Nick 命名 branch 未見此 path | 否，只作面試分析素材；不單獨更新 05 / 08 | 已完成；Rank 5 Step 3 已完成 |
| antplay | antplay-slot-game-job | `db-partition-job-report-routing` | DB partition / report schema routing | 中高 | Step 5 / 2026-05-25 | 真實開發過 + code-backed；`b754dae db_partition v2`、`38b74bd fix`、`6866866 fix ag_report_player` direct evidence；current `@UseSchema` framework 多人後續脈絡；G3 data source mapping 未確認；source fetch 失敗，依本地 refs / 本地 working tree | 作 project-level supporting evidence；不單獨更新 05 / 08 | 已完成；consolidation refresh 已完成 |
| antplay | math-core | `contribution-claim-consolidation` | slot math core contract / debugBet / RTP / symbol 收口 | 高 | 已完成 / 2026-05-20 | 真實開發過 + code-backed；Nick / `10gt12nc` 有 direct commits | 是，保守補入 slot math core / contract / debug tooling | 已收斂；Flow Track Step 1 只作可選加強 |
| antplay | `*-math` | `contribution-claim-consolidation` | 多個 slot math module 維護與驗證收口 | 高 | 已完成 / refreshed grouped / 2026-05-21 | 真實開發過 + code-backed；49 / 71 repo 有 direct commits，6 個強 evidence module，五條代表 flow 已全部 Step 5 | 是，保守補入 slot math core / math module / RTP / reel strip / debug / fixedMultiBet / buy free / jackpot / feature result contract | 已收斂；後續已完成 antplay game-api 五條代表 flows |
| antplay | `*-math` | `step1-candidate-flows` | fixedMultiBet / RTP / buy free / jackpot 候選 flow 盤點 | 高 | Step 1 / 2026-05-20 | 真實開發過 + code-backed；依本地 refs，部分 remote 未確認最新 | 否，先作 Flow Track 選題 | 已收斂 |
| antplay | `*-math` | `step2-flow-comparison` | fixedMultiBet / currency / math-core compatibility 第一順位 | 高 | Step 2 / 2026-05-20 | 真實開發過 + code-backed；依本地 refs，部分 remote 未確認最新 | 否，先作 Flow Track 排序 | 已收斂；Rank 1 Step 5 已完成 |
| antplay | `*-math` | `fixed-multi-bet-currency-math-core-compatibility` | fixedMultiBet / currency / math-core 相容 flow | 高 | Step 5 / 2026-05-20 | 真實開發過 + code-backed；`math-core`、`sdt-math`、`sfm-math`、`slc-math` 代表 path | 是，作 `*-math` grouped 履歷 bullet 強化 evidence；不單獨寫完整 owner | 已完成；本批五條代表 flows 已全部 Step 5 |
| antplay | `*-math` | `rtp-reel-strip-simulation-validation` | RTP / 輪帶模擬與驗證 | 高 | Step 5 / 2026-05-20 | 真實開發過 + code-backed；`sph-math` 主樣本、`spn-math` 對照 path | 併入 `*-math` grouped bullet；不單獨寫完整 RTP / math owner；不直接改 05 / 08 | 已完成；本批五條代表 flows 已全部 Step 5 |
| antplay | `*-math` | `buy-free-scatter-rtp3-result-contract` | Buy Free / Scatter / RTP_3 結果契約 | 高 | Step 5 / 2026-05-21 | 真實開發過 + code-backed；`spn-math` 主樣本、`sph-math` 對照、game-api caller 補讀 | 只併入 `*-math` grouped bullet；不單獨寫 buy free owner | 已完成；本批五條代表 flows 已全部 Step 5 |
| antplay | `*-math` | `jackpot-symbol-hit-and-prize-scaling` | Jackpot symbol hit / prize scaling | 高 | Step 5 / 2026-05-21 | 真實開發過 + code-backed；`sph-math` JP symbol hit、`sdt-math` fixedMultiBet jackpot scaling、`math-core` JackpotReward contract、game-api SDT callback context | 只併入 `*-math` grouped bullet；不單獨寫 jackpot owner；不直接更新 05 / 08 | 已完成；Rank 5 Step 3 已完成 |
| antplay | `*-math` | `special-wild-feature-state-transform` | Special Wild / symbol state transform | 中 | Step 5 / 2026-05-21 | 真實開發過 + code-backed；`sfm-math` Special Wild parent / child transform 與 `acac921` direct bugfix，`slc-math` LuckyClover 只作 code-backed 對照 | 只併入 `*-math` grouped bullet；不單獨寫 feature owner；不直接更新 05 / 08 | 已完成；本批 `*-math` flows 全部 Step 5 |
| antplay | math-workspace | `contribution-claim-consolidation` | math KB / validation workflow supporting 收口 | 低 | 已完成 / 2026-05-20 | 真實做過 KB / docs / workspace 維護；不是 runtime service | 否，不放 standalone 主成果 | 已收斂；回 math source repo |
| antplay | platform-mock | `contribution-claim-consolidation` | provider mock / failure injection supporting 收口 | 中低 | 已完成 / 2026-05-20 | 局部真實開發過；failure injection commits | 否，作 testing supporting evidence | 已收斂；不優先 |
| antplay | buffer-id | `contribution-claim-consolidation` | ID generator learning-only 收口 | 低 | 已完成 / 2026-05-20 | 未見 Nick direct commits | 否，只作 learning-only | 已收斂；不優先 |

## 目前狀態

目前不自動推薦新的 Flow Track、互動練習或架構補強。狀態是:

```text
通用投遞包可用；沒有預設下一步；可以自由提問或彈性指定
```

原因:

- `math-core` / `*-math` 已經補出強 career evidence，且 `*-math` 五條代表 flows 已全部 Step 5，project-level contribution claim consolidation refresh 已完成。
- `*-math` 後續不該再平均掃 71 repo；除非 Nick 要 Level 3 final，否則先把已收斂的交易主線與 math 素材回填履歷更有近期價值。
- `antplay-slot-game-api` 與 `antplay-slot-game-job` 已完成 refreshed contribution consolidation；代表 flows Step 5、05 / 08 / 04 / 17 rolling resume package、104 投遞欄位檢查、`04 / 面試 case 對齊檢查` 與三條主力 case 口說稿都已回填。
- `third_games_api oneapi-wallet-bet-result Step 5`、`third_games_api antplay-bet-settle-rollback Step 5`、`third_games_api gsc-seamless-withdraw-deposit-cancel Step 5` 與 `k3s-deploy gameserver-phased-rollout Step 5` 已完成；2026-05-27 `rolling resume package` 已回填最新 interview-only 邊界、AntPlay job refresh 與 UGSoft admin / connector refresh。
- 下一步不再慣性推 flow；沒有特定 JD 時，面試口說稿可保留，但練習先暫停，等 Nick 明確要求「開始練 / 模擬面試 / 我先講一版」才進入互動問答。有實際 JD 時，才客製 `08` 與談薪說法 `17`。若 Nick 要補架構視角，可選 `iwin system map v1`；這不是投遞前必做。若 Nick 只是想聊方向、薪資、缺口、學習、焦慮或任一 flow，可以直接問，不需要先選固定下一步。

## 近期候選 Queue

這裡只放近期最值得排隊的 flow，不把所有舊清單塞滿。

注意：queue 不是無限必做清單。對標 Senior / Platform Backend 時，優先完成 `必做收口`，再視需要補 2 個非 iwin project 代表 flow；完成後應停止大規模整理，轉為投遞、面試練習與依職缺補洞。低價值 repo、官網、前端、workspace、mock、全部 legacy / 全部 math repo 平均深掃，預設不列為必做。

| 優先 | Domain | Project | Flow | 中文名稱 | 為什麼值得做 | 起手式 |
| --- | --- | --- | --- | --- | --- | --- |
| done | iwin | k3s-deploy | `gameserver-phased-rollout` | gameserver phase rollout / rollback | 已完成 Step 5；維持 interview-only，不更新正式履歷 | 已收斂 |
| 2 | iwin | game_api | `contribution claim consolidation` | game_api project-level 履歷 claim 收口 | 已完成；正式履歷只採 coupon 保守 claim，partner / agent bonus 只作面試素材 | 已完成 |
| 3 | iwin | game_job | `contribution-claim-consolidation` | game_job 實際開發貢獻收斂 | 已完成；保留為 claim evidence，不因新規則重做 | 已完成 |
| 4 | iwin | third_games_api | `contribution-claim-consolidation` | third_games_api rolling / scoped 履歷 claim 收口 | 已完成；不新增 standalone 正式履歷主成果，下游 direct evidence 已由 iwin_gameserver 收口 | 已完成 |
| done | iwin | third_games_api | `gsc-seamless-withdraw-deposit-cancel` | GSC seamless withdraw / deposit / rollback / cancel | Step 5 已完成；維持 interview-only，不更新正式履歷 | 已收斂 |
| 6 | iwin | payment | `contribution-claim-consolidation` | payment 實際開發貢獻收斂 | 已完成並於 2026-05-20 重新覆核；GoldenPay direct evidence 已補入；保留為 claim evidence，不因新規則重做 | 已完成 |
| 7 | antplay | antplay-slot-game-api | `contribution claim consolidation` | 五條代表 flow Step 5 後的 project-level claim refresh | 已完成 refreshed consolidation，且已回填 05 / 08；game runtime、betting-settlement、transfer wallet、async log、schema routing、runtime decision 可保守使用 | 已完成 |
| done | antplay | antplay-slot-game-job | `contribution claim consolidation` | 五條代表 flow Step 5 後的 project-level claim refresh | 已完成 refreshed consolidation；Kafka / Quartz / report projection / big-win / activity supporting / partition 可保守使用，settle pool analysis-only；05 / 08 / 17 已回填 | 已完成 |
| 9 | antplay | `*-math` | `contribution claim consolidation` | `*-math` 五條代表 flow 收口後的 claim refresh | 已完成；保留為 refreshed grouped claim evidence，後續除非 Nick 指定 Level 3 final，不再搶下一步 | 已完成 |
| done | ugsoft | ugsoft-connector-api | `contribution claim consolidation refresh` | UGSoft connector 三條代表 flow 後的 project-level claim refresh | 已完成 refreshed consolidation；三條代表 flow `transfer-wallet-in-out-query`、`provider-callback-bet-settle-to-mq`、`request-bet-record-mq-sync` 均已 Step 5 並回填 project-level claim | 已收斂 |
| 11 | DevOps | primestar | `observability-pipeline` | OpenObserve / Fluent Bit 觀測性 pipeline | production troubleshooting / logs / observability | `DevOps Step 1` |

Career Track 補充：`iwin iwin_gameserver contribution claim consolidation` 已完成 rolling / scoped 履歷風險收斂。`center-http-deposit-withdraw` 與 `game-spin-settlement-log-reel` 已完成 Step 5；若後續新增 gameserver flow，要回填校正 project-level claim。

## Domain Backlog

以下是候選，不代表已確認 code evidence。做之前必須重新深掃 source repo。

### iwin

核心後端候選:

- `payment-provider-callback`｜金流 provider callback
- `payment-order-provider-request`｜金流訂單與 provider request
- `withdrawal-auto-review-refund`｜提現自動審核與退款
- `transfer-wallet-transfer-in-out`｜Transfer wallet 轉入 / 轉出
- `transfer-wallet-ledger`｜Transfer wallet ledger
- `provider-transfer-in-out`｜Provider 轉入 / 轉出
- `settled-bets-kafka`｜Settled bets Kafka
- `daily-game-data-summary`｜每日遊戲資料彙總
- `third-party-record-mongo-backup`｜第三方遊戲紀錄 Mongo 備份與清理
- `coin-flow-batch-projection`｜金幣流水清算 / 遊戲行為投影
- `online-payment-data-cleaning`｜充值 / 提現資料清洗與每日經濟資料
- `game-round-settlement`｜遊戲局結算
- `third-party-game-login`｜第三方遊戲登入
- `third-party-transaction-sync`｜第三方交易同步

後台 / BI / control plane 候選:

- `admin-config-redis-sync`｜後台設定同步 Redis
- `point-control-admin-operation`｜單點控制 / 營運控制操作
- `payment-order-status-repair`｜金流訂單狀態修正
- `daily-game-record-summary`｜每日遊戲紀錄彙總
- `game-round-record-query`｜遊戲局紀錄查詢
- `app-bi-report-export`｜BI 報表查詢與匯出
- `admin-rbac-permission-check`｜後台 RBAC / 權限判斷

### Antplay

核心後端候選:

- `antplay-bet-settle-rollback`｜Antplay 投注 / 結算 / rollback
- `antplay-launch-transfer-callback`｜Antplay launch / transfer / callback
- `bet-record-partition-retry-jobs`｜注單分表與 retry job
- `free-round-voucher-lifecycle`｜Free round voucher 生命週期
- `activity-accumulated-bet-voucher`｜活動累積投注 voucher
- `activity-conf-common-service`｜活動設定共用 service
- `bet-voucher-common-service`｜Bet voucher 共用 service

Math / runtime 候選:

- `slot-math-interface-contract`｜SlotMath 計算介面契約
- `gdd-to-math-code-flow`｜GDD 到 math code flow
- `reel-strip-rtp-validation`｜輪帶與 RTP 驗證
- `math-loader-runtime`｜Math runtime 載入
- `rtp-cache-runtime-control`｜RTP cache runtime 控制

Push / notify / platform 候選:

- `big-win-push-user`｜Big win 推送玩家
- `jackpot-balance-push-user`｜Jackpot 餘額推送玩家
- `push-user-websocket`｜push_user WebSocket
- `websocket-handshake-response-encryption`｜WebSocket handshake 與 response 加密
- `telegram-deploy-command`｜Telegram deploy command

### UGSoft

核心後端候選:

- `ug-adapter-provider-gateway`｜UG Adapter provider gateway
- `bet-record-daily-partitioning`｜Bet record / request log 每日分表
- `bet-record-sync-reporting`｜Bet record 同步與報表
- `serial-sync-id-store`｜Serial ID store 同步
- `backoffice-config-management`｜後台設定管理

前端 / 後台候選:

- `bet-record-reporting-ui`｜Bet record / 報表 UI
- `player-management-ui`｜玩家管理 UI
- `whitelist-upgrade-tools-ui`｜白名單 / 升級工具 UI
- `i18n-static-site-release`｜i18n 靜態網站發版

### DevOps / primestar

候選:

- `observability-pipeline`｜OpenObserve / Fluent Bit 觀測性 pipeline
- `ci-template-release-provenance`｜CI template / release provenance
- `antplay-api-deploy-flow`｜Antplay API deploy flow
- `antplay-web-deploy-flow`｜Antplay Web deploy flow
- `kafka-local-runtime`｜Kafka local / dev runtime

邊界:

- DevOps 不是目前履歷主戰場。
- 只補 Senior Backend 需要的 deployment、observability、release risk、Kafka 運維理解。

## 履歷更新規則

正式履歷 / 自傳目前先不動:

- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

只有同時符合以下條件才考慮更新:

1. flow 已完成 Step 4 或 Step 5。
2. `claim-boundary.md` 判定可以安全寫。
3. 有 Nick 本人 MR / ticket / commit / production issue / 本人確認。
4. 說法能保守呈現，不寫成主導或全權 owner。

否則只留在 flow 內當面試分析素材。
