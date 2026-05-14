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
| iwin | app_bi | `point-control-admin-operation` | 單點控制 / 營運控制操作 | 中 | Step 5 | 專案存在 / Nick 貢獻待確認 | 否 | 回到 app_bi ranking，選下一條 |
| iwin | app_bi | `admin-config-redis-sync` | 後台設定同步 Redis | 中 | Step 5 | 專案存在 / Nick 貢獻待確認 | 否 | 回到 app_bi ranking，選下一條 |
| iwin | app_bi | `daily-game-record-summary` | 每日遊戲資料彙總 | 中 | Step 2 | 專案存在 / Nick 貢獻待確認 | 否 | `app_bi daily-game-record-summary Step 3` |

## 下一步推薦

目前只推薦一件事:

```text
app_bi daily-game-record-summary Step 3
```

原因:

- `app_bi` Step 1 / Step 2 已於 2026-05-14 重整乾淨。
- `point-control-admin-operation` 與 `admin-config-redis-sync` 都已完成 Step 5。
- 同 project 下一條未完成 flow 是 `daily-game-record-summary`。
- 預期不更新履歷；先把報表 projection、truth source、資料延遲與補跑邊界釐清。

## 近期候選 Queue

這裡只放近期最值得排隊的 flow，不把所有舊清單塞滿。

| 優先 | Domain | Project | Flow | 中文名稱 | 為什麼值得做 | 起手式 |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | iwin | payment | `payment-provider-callback` | 金流 provider callback | money correctness / callback / idempotency / reconciliation | `payment Step 1` |
| 2 | iwin | payment | `payment-order-provider-request` | 金流訂單與 provider request | 訂單狀態、provider contract、timeout / retry | `payment Step 1` |
| 3 | iwin | game_api / game_job | `settled-bets-kafka` | Settled bets Kafka | MQ reliability / settlement / audit | `game_api Step 1` 或 `game_job Step 1` |
| 4 | iwin | iwin_gameserver | `game-round-settlement` | 遊戲局結算 | state transition / bet settlement / rollback | `iwin_gameserver Step 1` |
| 5 | antplay | antplay-slot-game-api | `antplay-bet-settle-rollback` | Antplay 投注 / 結算 / rollback | 高交易遊戲 flow、rollback、交易一致性 | `antplay-slot-game-api Step 1` |
| 6 | ugsoft | ugsoft-connector-api | `ug-adapter-provider-gateway` | UG Adapter provider gateway | provider integration / request log / adapter contract | `ugsoft-connector-api Step 1` |
| 7 | DevOps | primestar | `observability-pipeline` | OpenObserve / Fluent Bit 觀測性 pipeline | production troubleshooting / logs / observability | `DevOps Step 1` |

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
