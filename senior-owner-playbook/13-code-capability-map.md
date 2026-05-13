# 本地專案能力地圖

這份是根據 `/Users/nick/Git` 下主要專案做的高階掃描結果，用來建立 `projects/` 開工前的規範。

這不是 flow analysis，也不是履歷證據。它只回答：

> 本地 code 裡大致有哪些能力線，可以支撐 Senior / Platform Backend / Owner / Lead-Architect 的哪些準備方向？

## 掃描範圍

已掃：

- `/Users/nick/Git/iwin`
- `/Users/nick/Git/ugsoft`
- `/Users/nick/Git/antplay`
- `/Users/nick/Git/DevOps/primestar`

只做高階能力掃描，不展開單一 flow，不產生專案 case。

## 本地 Code 已有的能力線

### iwin

本地 code / workspace 顯示 iwin 具備以下能力線：

- `payment`：金流核心、三方支付、入金 / 出金、callback、MQ、timer、provider request。
- `payment-thirdparty-simulator`：支付 / 提現 simulator、notify / callback、簽章、callback log。
- `game_api` / `game_job`：遊戲 API、job、排程、報表 / 資料搬移線索。
- `iwin_gameserver`：多模組 game server、slots center / gate / dbproxy / game log / games 等分層。
- `third_games_api`：第三方遊戲 provider / 交易 / 玩家資料線索。
- `k3s-deploy`：K3s deployment、kustomization、service rollout、phase deployment。
- `app_bi` / `bi_share`：BI、報表、跨 DB / 遊戲資料查詢與匯出。

對標能力：

- Senior Backend：金流 callback、game job、third-party provider、資料排查。
- Platform Backend：payment / game / BI / third-games 多專案串接。
- System Owner：金流訂單、callback、job、BI projection、遊戲狀態。
- Lead / Architect 候選：K3s rollout、observability、跨服務邊界與 release risk。

### ugsoft

本地 code / workspace 顯示 ugsoft 具備以下能力線：

- `ugsoft-connector-api`：provider adapter、transfer、callback、request log、circuit breaker、冪等線索。
- `ugsoft-admin-api`：後台 API、報表、RabbitMQ、DLQ、quota、bet record、request log。
- `ugsoft-admin-web`：後台 UI、角色路由、營運操作入口。
- `ugsoft-workspace`：系統架構、contract、handoff、decision log、connector/admin 關聯文件。
- `official-web-v3`：前台官網 / i18n / static release。

對標能力：

- Senior Backend：transfer idempotency、request log、bet record、quota、報表。
- Platform Backend：admin-api -> MQ -> connector-api cache / runtime 關聯。
- System Owner：transfer reference、provider callback、request log traceability、quota monitoring。
- Lead / Architect 候選：provider contract、circuit breaker、decision log、handoff template。

### antplay

本地 code 顯示 antplay 具備以下能力線：

- `antplay-slot-game-api`：slot game API、bet / settlement / rollback、wallet / provider、request log 線索。
- `antplay-slot-game-job`：settled bets、Kafka / job / retry 類能力線。
- `antplay-slot-admin-api`：admin reporting、schedule job、monitor alert、營運後台 API。
- `antplay-slot-admin`：後台前端、RBAC / route / operation UI 線索。
- `antplay-push`：Kafka listener、WebSocket push、Redis / error handler。
- `antplay-tool-transfer-web`：transfer 測試 / sandbox 工具線索。
- `math-core` 與大量 `*-math`：slot math、RTP、reel strip、round result、simulation / validation。
- `official-web` / `official-web-v2`：網站 / static release。

對標能力：

- Senior Backend：bet settlement、rollback、Kafka consumer、admin reporting。
- Platform Backend：game-api / game-job / admin-api / push / math module 串接。
- System Owner：round id、transaction id、settlement truth、request log / report projection。
- Lead / Architect 候選：math runtime contract、push reliability、admin operation boundary。

### DevOps / primestar

本地 code 顯示 DevOps / primestar 具備以下能力線：

- `antplay-api-deploy` / `antplay-web-deploy`：Dockerfile、GitLab CI。
- `antplay-docker-deploys`：Docker Swarm / stack / runtime config。
- `ci-template`：CI template / release provenance。
- `ci-test`：GitLab CI / release pipeline。
- `kafka`：Kafka docker-compose / KRaft 基礎。
- `openobserve`：OpenObserve docker-compose / observability 基礎。

對標能力：

- Senior Backend：理解部署與 runtime 影響。
- Platform Backend：CI / release / log / Kafka 基礎設施。
- System Owner：rollout、rollback、incident trace、runtime config。
- Lead / Architect 候選：release risk、observability、deployment topology。

## 本地 Code 支撐最強的 5 條準備線

1. 金流 / callback / payment order：iwin `payment` + simulator。
2. transfer wallet / provider adapter / idempotency：ugsoft `connector-api`。
3. bet / settlement / rollback：antplay `slot-game-api` + `slot-game-job`。
4. MQ / Kafka / async reliability：antplay push / game-job、ugsoft RabbitMQ、DevOps Kafka。
5. rollout / observability / runtime：iwin K3s、DevOps OpenObserve / Docker / CI。

## 本地 Code 較弱或需外部補強的能力線

以下不是說本地完全沒有，而是目前高階掃描中沒有足夠完整證據可直接當履歷主張：

- Transactional Outbox / CDC / Transaction log tailing。
- Saga orchestration / choreography 的完整落地。
- SLO / error budget 制度。
- 全鏈路 tracing 規範與 trace id 強制傳遞。
- 完整 incident management / postmortem 流程。
- 正式架構決策紀錄 ADR。
- 正式 capacity planning / load testing / performance budget。
- 正式 team lead / people management 證據。

這些可以作為學習與面試延伸，但在沒有本地 evidence 前，不能寫成 Nick 已做過。

## 外部案例參考標注規則

之後如果參考網路案例，必須標成：

```text
來源類型：外部案例 / 非本地 code evidence
用途：補強理解 / 面試延伸 / 改善方案參考
不得用於：履歷主張 Nick 已實作
```

已查到的外部參考：

- Microservices.io Idempotent Consumer：說明 at-least-once delivery 會造成重複消費，consumer 需要記錄已處理 message id 或讓業務實體具備去重能力。來源：https://microservices.io/patterns/communication-style/idempotent-consumer.html
- Microservices.io Transactional Outbox：用 DB outbox 在同一 transaction 內保存要送出的 message，再由 relay 發送，避免 DB 更新與 message broker 發送不一致；relay 仍可能重送，所以 consumer 仍要 idempotent。來源：https://microservices.io/patterns/data/transactional-outbox
- AWS Well-Architected Reliability Pillar：可靠性強調 workload 能正確且一致地執行預期功能，並包含 resilient architecture、change management、failure recovery。來源：https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/welcome.html
- Google SRE SLO：SLO / SLI 用來定義服務哪些行為重要、如何測量、未達標時如何反應；常見 SLI 包含 availability、latency、throughput、error rate。來源：https://sre.google/sre-book/service-level-objectives/

## 安全注意

高階掃描時已發現部分專案配置檔可能含敏感設定或環境資訊。

因此未來 `projects/**/evidence.md`：

- 可以列檔案路徑。
- 可以描述「有 secret/config/env 類風險」。
- 不可以摘錄 token、password、private key、internal IP、production URL、客戶資料。
- 不可以把敏感內容 commit 到 `nick-vault`。

## 結論

本地專案足以支撐 Senior / Platform Backend / Owner 的主要準備方向。

Lead / Architect 候選能力可以準備，但目前應以「候選能力 / 思維」呈現，不應包裝成正式職責。

下一步不是開始寫大量專案 case，而是先把 `projects/` 的規範固定好，確保每個 case 都能：

- 有 code evidence
- 有 claim boundary
- 有外部參考標注
- 不外洩敏感資訊
- 不誇大成正式 owner / architect
