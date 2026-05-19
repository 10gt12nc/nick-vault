# Senior / Owner Flow Inventory

用途: 這份是 flow dashboard，用來決定下一步做哪條 flow。

它不是研究報告，不放完整分析。完整分析放在:

```text
projects/{domain}/{project}/flows/{flow-name}/flow.md
```

## 使用規則

- 每次只做一條 flow。
- 已完成 Step 5 只代表單條 flow 完成，不代表整個 project 完成。
- 履歷欄位只表示「能不能考慮」，不代表已寫進 `05-resume-master-zh.md`。
- 證據欄位要分清楚：`真實開發過`、`專案存在 / code-backed`、`分析素材 / learning-only`、`外部案例 / non-local`、`待確認`。
- 沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認，不寫主導、獨立完成、改善百分比。
- 只看到後台 / 前端 / BI 入口的 flow，優先當面試分析素材，不急著寫履歷。
- Source repo 清單看 `projects/source-repo-inventory.md`；真正做 flow 前仍要重讀 code branch / log / path-specific history。

## 跨 repo 優先排序

用途：當 Nick 只問「下一個整理哪個 project / repo」時，先用這份排序判斷；真正開工前仍必須做該 repo 的 Step 1 / Step 2，不能把本表當 code evidence。

評估目標：Senior Java Backend / Platform Backend / System Owner 的履歷與面試素材價值。優先看 production flow、money correctness、wallet / bet / settlement、idempotency、retry / compensation、reconciliation、observability、rollout / rollback。workspace、官網、前端、mock、simulator 預設只當輔助入口。

| 排名 | Project / repo | 初步判斷 |
| --- | --- | --- |
| 1 | `math-core` / `*-math` | 差異化最高；若有遊戲數學、RTP、派彩、模擬驗證，是最稀缺素材 |
| 2 | `payment` | 金流 callback、訂單狀態、MQ retry、上分 / 退款、對帳，Backend 最高價值主線 |
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
| 24 | `bi_share` | 可能偏 BI / share 報表，需 Step 1 確認 |
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
| 38 | `iwin-workspace` | workspace 索引，不當 flow 主題 |
| 39 | `ugsoft-workspace` | workspace 索引，不當 flow 主題 |
| 40 | `math-workspace` | workspace 索引，不當 flow 主題 |

使用提醒：

- 若目標是最快產出 Senior Backend 履歷素材，payment Top 5 flow 都已完成到 Step 5，且 `iwin payment contribution claim consolidation` 已於 2026-05-19 完成；下一步回到 `game_api coupon-redeem-credit-grant Step 5`。
- 2026-05-19 補充：Nick 已明確確認 `payment` 實際開發很多，且已完成 project-level consolidation。`payment` 可保守寫「參與多個第三方金流 provider 對接與維護、provider callback / sign / response parsing bugfix、payment / withdraw order consistency 修正」，但不得寫成主導完整金流或全部 provider owner。
- 若目標是差異化面試題，下一個新 domain 可先做 `math-core` / `*-math` Step 1。
- 若目標是 Platform / System Owner，`openobserve`、`kafka`、`k3s-deploy`、`antplay-api-deploy` 可往前，但必須和實際 production flow / incident / rollout evidence 串起來。

## 狀態定義

| 狀態 | 意義 |
| --- | --- |
| `未開始` | 只有候選題，尚未做 Step 1 |
| `Step 1` | 已找 candidate flows |
| `Step 2` | 已做 flow ranking |
| `Step 3` | 已有單條 flow 深挖 |
| `Step 4` | 已轉面試 case |
| `Step 5` | 已檢查履歷 / 自傳是否更新 |
| `暫停` | 目前 evidence 不足或價值不夠，先不做 |

## 目前進度

| Domain | Project | Flow | 中文名稱 | 價值 | 狀態 | 證據層級 | 履歷 | 下一步 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| iwin | app_bi | `point-control-admin-operation` | 單點控制 / 營運控制操作 | 中 | Step 5 | 專案存在 / code-backed；Nick 貢獻依三層 claim gate 判斷 | 否 | 已收斂 |
| iwin | app_bi | `admin-config-redis-sync` | 後台設定同步 Redis | 中 | Step 5 | 專案存在 / code-backed；Nick 貢獻依三層 claim gate 判斷 | 否 | 已收斂 |
| iwin | app_bi | `daily-game-record-summary` | 每日遊戲資料彙總 | 中 | Step 5 | 專案存在 / code-backed；Nick 貢獻依三層 claim gate 判斷 | 否 | 已收斂 |
| iwin | app_bi | `game-round-record-query` | 遊戲局紀錄查詢 | 中 | Step 5 | app_bi 專案存在 / iwin_gameserver 有 Nick commit 線索 | 否 | 已收斂 |
| iwin | game_api | `coupon-redeem-credit-grant` | 優惠券兌換上分 / 打碼要求 | 高 | Step 4 | 專案存在 / code-backed；Nick 貢獻依三層 claim gate 判斷 | 否 | 下一步 Step 5 |
| iwin | payment | `contribution-claim-consolidation` | payment 實際開發貢獻收斂 | 高 | 已完成 | 本人確認 + 真實開發過 + code-backed | 是，保守更新 | 回到 game_api Step 5 |
| iwin | payment | `payment-provider-callback` | 金流 provider callback | 高 | Step 5 | 單條 flow code-backed；project-level 有多 provider callback / sign 維護 evidence | 併入 payment project bullet | 已收斂 |
| iwin | payment | `withdrawal-auto-review-refund` | 玩家提款、自動審核 / 自動出款與失敗退款 | 高 | Step 5 | 單條 flow code-backed；withdraw insert / null-safety 有有限維護 evidence | 不單獨寫自動出款 owner | 已收斂 |
| iwin | payment | `payment-order-provider-request` | 充值建單與 provider request | 高 | Step 5 | 部分真實開發過：多 provider request / callback / query / withdraw evidence；整體金流 owner 不誇大 | 是，保守更新 | 已收斂 |
| iwin | payment | `manual-order-review-repair` | 人工審核 / 補單 / 訂單修復 | 中高 | Step 5 | code-backed；不單獨升級人工修復 owner | 否，作面試素材 | 已收斂 |
| iwin | payment | `payment-channel-config-selection` | 支付列表 / 商戶設定選擇 | 中 | Step 5 | code-backed；不單獨升級支付設定 owner | 否，作面試素材 | 已收斂 |
| iwin | third_games_api | `gsc-transfer-bet-settle-rollback` | GSC transfer 投注 / 派彩 / rollback | 高 | Step 4 | 專案存在 / code-backed；Nick 貢獻依三層 claim gate 判斷 | 否 | 待 game_api Step 5 後再排 |
| iwin | game_job | `daily-game-data-summary` | 每日遊戲資料彙總 | 中高 | Step 4 | 專案存在 / code-backed；Nick 貢獻依三層 claim gate 判斷 | 否 | queue 第 2 |
| iwin | iwin_gameserver | `third-party-transfer-in-out` | 第三方遊戲投派整合 / 投注派彩退款 | 高 | Step 5 | 專案存在 / code-backed；Nick 貢獻依三層 claim gate 判斷 | 否 | 已收斂，待回 ranking |
| iwin | iwin_gameserver | `center-http-deposit-withdraw` | center_http 上分 / 下分 | 高 | Step 2 | 專案存在 / code-backed；Nick 貢獻依三層 claim gate 判斷 | 否 | queue 第 3 |
| iwin | k3s-deploy | `gameserver-phased-rollout` | gameserver phase rollout / rollback | 中高 | Step 4 | 專案存在 / code-backed；Nick 貢獻依三層 claim gate 判斷 | 否 | 待 game_api Step 5 後再排 |

## 下一步推薦

目前只推薦一件事:

```text
iwin game_api coupon-redeem-credit-grant Step 5
```

原因:

- `payment` project-level contribution consolidation 已完成，履歷 / 自傳已保守同步。
- `game_api coupon-redeem-credit-grant` 已完成 Step 4，下一步應按 KB 做 Step 5 claim gate。
- 這會判定 coupon 上分 flow 是否能更新履歷 / 自傳，並避免跳到新 flow。

## 近期候選 Queue

這裡只放近期最值得排隊的 flow，不把所有舊清單塞滿。

| 優先 | Domain | Project | Flow | 中文名稱 | 為什麼值得做 | 起手式 |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | iwin | game_api | `coupon-redeem-credit-grant` | 優惠券兌換上分 / 打碼要求 | 已完成 Step 4，應收 Step 5 判定，不跳新 flow | `iwin game_api coupon-redeem-credit-grant Step 5` |
| 2 | iwin | game_job | `daily-game-data-summary` | 每日遊戲資料彙總 | 已完成 Step 4，下一步做履歷 / claim gate | `iwin game_job daily-game-data-summary Step 5` |
| 3 | iwin | iwin_gameserver | `center-http-deposit-withdraw` | center_http 上分 / 下分 | money correctness / center wallet mutation / idempotency | 待 game_api Step 5 後再排 Step 3 |
| 4 | iwin | game_api / game_job | `settled-bets-kafka` | Settled bets Kafka | MQ reliability / settlement / audit | `game_api Step 1` 或 `game_job Step 1` |
| 5 | iwin | payment | `contribution-claim-consolidation` | payment 實際開發貢獻收斂 | 已完成；保留為 claim evidence，不再排下一步 | 已完成 |
| 6 | antplay | antplay-slot-game-api | `antplay-bet-settle-rollback` | Antplay 投注 / 結算 / rollback | 高交易遊戲 flow、rollback、交易一致性 | `antplay-slot-game-api Step 1` |
| 7 | ugsoft | ugsoft-connector-api | `ug-adapter-provider-gateway` | UG Adapter provider gateway | provider integration / request log / adapter contract | `ugsoft-connector-api Step 1` |
| 8 | DevOps | primestar | `observability-pipeline` | OpenObserve / Fluent Bit 觀測性 pipeline | production troubleshooting / logs / observability | `DevOps Step 1` |

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
