# 學習路線

## 原則

不要一次讀完全部。每次只選一條 flow，做完整學習包，再進下一條。

完成一條 flow 的標準不是「文件很多」，而是你可以用自己的話講：

- 業務問題
- code 路徑
- 資料流
- 失敗情境
- owner 取捨
- 技術選型差異
- 為什麼這樣設計，以及別的方案代價
- 面試講法
- 履歷保守寫法

輔助層只在需要時補：

- 大專案 / 子專案地圖：不知道 repo 關係或 flow 入口時才補。
- `decision-notes.md`：flow 讀懂後，針對這條 flow 補技術硬底子。
- 職涯能力矩陣：每完成 1-2 條 flow 後，用來檢查缺口。

不要先把所有輔助層讀完。每天真正推進的仍然是一條 production flow。

## 第 1 階段：錢與一致性

目標：能講清楚 money correctness。

1. `payment-provider-callback`
2. `transfer-wallet-transfer-in-out`
3. `bet-settlement`
4. `game-round-settlement`
5. `antplay-bet-settle-rollback`

讀完後要能回答：

- 怎麼防止重複 callback / 重複結算。
- timeout 不能直接視為失敗時怎麼辦。
- 哪裡是 source of truth。
- DB 成功但 MQ 失敗怎麼辦。
- 報表和結算真相要怎麼切開。

## 第 2 階段：非同步與可恢復

目標：能講清楚 retry、補償、對帳。

1. `settled-bets-kafka`
2. `transfer-compensation-retry`
3. `request-log-monitor-alert-mq`
4. `bet-record-request-log-mq`
5. `scheduled-bi-aggregation`

讀完後要能回答：

- retry-safe 是什麼。
- DLT / retry / compensation 差在哪。
- request log 是 audit 還是 source of truth。
- 如何設計可重跑 batch。
- 如何讓營運查得到狀態。

## 第 3 階段：平台與部署

目標：能講清楚服務如何穩定上線。

1. `k3s-migration-track`
2. `iwin-service-rollout`
3. `gameserver-phased-rollout`
4. `observability-pipeline`
5. `ci-template-release-provenance`

讀完後要能回答：

- rollout 風險怎麼控。
- probe / resource / config / secret 怎麼看。
- log 與 trace 怎麼串回 transaction。
- Docker Swarm 到 K3s 的差異。
- CI template 如何保留 release provenance。

## 第 4 階段：控制面與 Lead 思維

目標：能把後台、權限、營運工具視為 control plane。

1. `admin-rbac-operations`
2. `admin-reporting-reconciliation`
3. `quota-monitoring-alerting`
4. `provider-whiteip-superagent-sync`
5. `quartz-job-control`

讀完後要能回答：

- 後台操作怎麼避免誤傷 production。
- 權限、audit log、操作紀錄怎麼設計。
- Redis rebuild 怎麼避免 stale data。
- manual trigger 怎麼做到可追蹤。
- 哪些操作需要雙人覆核或風險提示。
