# Decision Notes：gameserver-phased-rollout

## 1. Phase rollout vs apply all

選擇：phase1 stateless -> phase2 center -> phase3 gate -> phase4 games。

原因：

- gameserver runtime 有 ZK registration / watch dependency。
- center、gate、game 不是完全獨立服務；啟動時會連其他 server type。
- 一次 apply all 可能讓 pod 都是 Running，但 runtime dependency 尚未 ready。

代價：

- 部署變慢。
- 需要人工或 pipeline gate。
- rollback 也要理解 phase 邊界，不能只看單一 Deployment。

## 2. Recreate vs RollingUpdate

選擇：Deployment 使用 `Recreate`。

目前判斷：

- runtime 以 server id / appid / apptype 註冊 ZK node。
- `BaseServer.checkServerPort()` 會查 registration 是否重複。
- `gen.sh` 註解與 runtime code 共同支持「避免雙跑 / 雙註冊」的設計背景。

代價：

- 服務更新時可能短暫不可用。
- 需要靠 phase、replica 或 traffic drain 策略降低玩家感知。
- 若 production 要零停機，需要更多 runtime-aware 設計，不是把 strategy 改成 RollingUpdate 就好。

## 3. ZK discovery vs Kubernetes Service

選擇：gameserver 內部服務發現主要沿用 ZK znode。

好處：

- 貼合 legacy runtime 的 NetNode / server id / server type 語意。
- app code 已有 watch path、connect pool、reconnect 流程。

代價：

- Kubernetes readiness 不等於 runtime ready。
- pod IP / server IP placeholder 錯誤會直接影響 peer connection。
- troubleshooting 要同時看 pod、log、ZK path、connection pool。

## 4. ConfigMap / Secret externalization

選擇：per-service server properties、shared zookeeper config、lua / redis config、log4j2 config 從 image 拆出；敏感值走 Secret / env。

好處：

- image 不必因每個 config 改動重建。
- config diff 更清楚。
- secret 與 non-secret config 有邊界。

代價：

- config version 與 image version 需要一起治理。
- Secret envFrom 改了通常不會自動讓既有 process reload。
- per-service ConfigMap 多，generator 若出錯會放大成多服務 drift。

## 5. 同 image 多服務

選擇：同一個 image，透過 env 指定 jar、appid、apptype、main class。

好處：

- 建置與 registry 管理簡化。
- 多個 service 的 runtime baseline 一致。

代價：

- 一個 image change 可能影響所有角色。
- 服務身分與 config 正確性更依賴 generator。
- 面試時要強調這是 legacy runtime containerization 的 trade-off，不是所有服務都應該這樣做。

## 6. Console logging

選擇：log4j2 收斂到 Console，接 container stdout pipeline。

好處：

- 符合 Kubernetes log collection model。
- Fluent Bit / Loki / Grafana 可統一查詢。

代價：

- 需要好 label、retention、query discipline。
- 若只收 stdout 而缺 metrics / alert，不等於完整 observability。
