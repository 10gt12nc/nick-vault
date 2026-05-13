# Senior / Owner Flow Inventory

這份是重新整理後的選題地圖。它不是完整學習包，而是用來決定下一條要深讀什麼。

## 第一優先：最能對標 Senior / Owner

### Money / Wallet / Settlement

- `payment-provider-callback`｜金流 provider callback
- `payment-order-provider-request`｜金流訂單與 provider request
- `withdrawal-auto-review-refund`｜提現自動審核與退款
- `transfer-wallet-transfer-in-out`｜Transfer wallet 轉入 / 轉出
- `transfer-wallet-ledger`｜Transfer wallet ledger
- `provider-transfer-in-out`｜Provider 轉入 / 轉出
- `bet-settlement`｜下注 / 派彩 / 結算
- `game-round-settlement`｜遊戲局結算
- `antplay-bet-settle-rollback`｜Antplay 投注 / 結算 / rollback

### Async / Reliability / Audit

- `settled-bets-kafka`｜Settled bets Kafka
- `bet-record-request-log-mq`｜Bet record / request log MQ
- `request-log-monitor-alert-mq`｜Request log / Monitor alert RabbitMQ
- `transfer-compensation-retry`｜Transfer 補償 retry
- `request-log-traceability`｜Request log 追蹤
- `request-log-reconciliation-tooling`｜Request log 對帳工具
- `scheduled-bi-aggregation`｜排程 BI 聚合

### Platform / Runtime / Observability

- `k3s-migration-track`｜K3s migration track
- `dev-cluster-foundation`｜Dev K3s 叢集基礎
- `iwin-service-rollout`｜iwin 服務 rollout
- `gameserver-phased-rollout`｜GameServer 分階段 rollout
- `docker-swarm-runtime`｜Docker Swarm runtime
- `observability-pipeline`｜OpenObserve / Fluent Bit 觀測性 pipeline
- `ci-template-release-provenance`｜CI template / release provenance

## 第二優先：平台控制面與營運能力

### Backoffice / RBAC / Operation

- `admin-rbac-operations`｜後台 RBAC 操作
- `admin-reporting-reconciliation`｜後台報表對帳
- `admin-reporting-pipeline`｜後台報表 pipeline
- `role-routing-permission-ui`｜角色路由 / 權限 UI
- `quartz-job-control`｜Quartz job 控制
- `quota-monitoring-alerting`｜額度監控與告警
- `provider-whiteip-superagent-sync`｜Provider 白名單 IP / SuperAgent Redis 同步
- `config-cache-refresh`｜設定 cache refresh

### Game / Provider Integration

- `third-party-game-login`｜第三方遊戲登入
- `third-party-transaction-sync`｜第三方交易同步
- `provider-record-mongo-log`｜Provider 紀錄 Mongo log
- `gsc-pg-seamless-wallet`｜GSC / PG seamless wallet
- `oneapi-pg-wallet`｜OneAPI PG wallet
- `antplay-launch-transfer-callback`｜Antplay launch / transfer / callback
- `derplay-launch-transfer-callback`｜Derplay launch / transfer / callback
- `ug-adapter-provider-gateway`｜UG Adapter provider gateway

## 第三優先：專案深度與補充案例

### iwin

- `app-bi-report-export`｜BI 報表查詢與匯出
- `daily-game-record-summary`｜每日遊戲紀錄彙總
- `first-recharge-award-review`｜首充獎金審核
- `merchant-channel-redis-sync`｜商戶 / 渠道 Redis 同步
- `payment-order-status-repair`｜金流訂單狀態修正
- `point-control-operations`｜點控營運操作
- `third-game-provider-reporting`｜第三方遊戲商報表
- `coupon-redemption-admin-reporting`｜兌換碼領取與後台報表
- `invitation-roulette-reward`｜邀請轉盤獎勵
- `ip-whitelist-blacklist-access-control`｜IP 白名單 / 黑名單存取控制
- `login-registration-cps-token`｜登入註冊與 CPS token
- `partner-api-wallet-order`｜Partner API 錢包訂單

### Antplay

- `agent-secret-and-signature`｜Agent secret 與簽章
- `auth-token-white-ip-maintenance`｜Auth token / 白名單 / 維護模式
- `bet-record-partition-retry-jobs`｜注單分表與 retry job
- `free-round-voucher-lifecycle`｜Free round voucher 生命週期
- `math-loader-runtime`｜Math runtime 載入
- `rtp-cache-runtime-control`｜RTP cache runtime 控制
- `activity-accumulated-bet-voucher`｜活動累積投注 voucher
- `big-win-push-user`｜Big win 推送玩家
- `jackpot-balance-push-user`｜Jackpot 餘額推送玩家
- `proxy-user-report-agent-player`｜Proxy user / agent / player 報表
- `settle-pool-monitoring`｜Settle pool 監控
- `push-user-websocket`｜push_user WebSocket
- `websocket-handshake-response-encryption`｜WebSocket handshake 與 response 加密
- `telegram-deploy-command`｜Telegram deploy command
- `activity-conf-common-service`｜活動設定共用 service
- `bet-voucher-common-service`｜Bet voucher 共用 service
- `slot-math-interface-contract`｜SlotMath 計算介面契約
- `gdd-to-math-code-flow`｜GDD 到 math code flow
- `reel-strip-rtp-validation`｜輪帶與 RTP 驗證

### UGSoft

- `bet-record-daily-partitioning`｜Bet record / request log 每日分表
- `bet-record-sync-reporting`｜Bet record 同步與報表
- `serial-sync-id-store`｜Serial ID store 同步
- `backoffice-config-management`｜後台設定管理
- `bet-record-reporting-ui`｜Bet record / 報表 UI
- `player-management-ui`｜玩家管理 UI
- `whitelist-upgrade-tools-ui`｜白名單 / 升級工具 UI
- `i18n-static-site-release`｜i18n 靜態網站發版
- `runbook-release-handoff`｜Runbook / release handoff

