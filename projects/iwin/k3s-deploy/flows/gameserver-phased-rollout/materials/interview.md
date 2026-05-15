# Interview：gameserver-phased-rollout

## Step 4 面試案例定位

這份 Step 4 的用途是把 Step 3 的技術分析轉成可面試、可被追問、可守住 claim 邊界的回答。核心立場：

- 可以講設計分析與風險判斷。
- 可以講 code-backed evidence。
- 不講 Nick 已主導、已上 production、已改善 downtime。
- 被追問實戰時，回到「待補 MR / ticket / deploy log / incident evidence」。

## 0. 面試主線

```text
legacy gameserver 上 K3s
-> 不是只把 YAML apply 上去
-> runtime service discovery 靠 ZK registration
-> pod ready 不等於 peer ready
-> phase rollout + Recreate + config rollback discipline
-> 目前是 code-backed analysis，不是 production claim
```

## 1. Senior 追問

### Q1：為什麼不能直接 `kubectl apply -k dev/iwin/iwin-gameserver`？

因為這組服務不是純 stateless HTTP API。runtime 會透過 Zookeeper 註冊自己的 server address，也會 watch 其他 server type 建立 NetNode connection。若全部同時 apply，pod Running 不代表 center / gate / game 的 runtime dependency 都 ready。phase apply 的價值是把依賴層逐步穩住。

### Q2：為什麼用 `Recreate`，不是 RollingUpdate？

這類服務有 server id 與 ZK znode 語意。RollingUpdate 可能讓同一 server id 的新舊 pod 短暫並存，造成雙註冊或 repeated registration。code 裡也有 `checkServerReg()` 類似保護，所以 `Recreate` 是 runtime-aware 的選擇。代價是更新時可用性較弱，需要 phase / drain / graceful shutdown 補足。

### Q3：ConfigMap 外掛後 rollback 變簡單還是更複雜？

兩者都有。外掛讓 config diff 更清楚，也不必為小 config 改 image；但 rollback 從「回 image」變成「image + per-service ConfigMap + shared ConfigMap + Secret/env 都要對齊」。如果沒有 version discipline，反而會出現舊 image 配新 config 或新 image 配舊 config 的問題。

### Q4：Kubernetes Service 還重要嗎？

gate 對玩家入口與部分外部暴露仍重要，但 gameserver 內部 peer discovery 的核心 evidence 是 ZK。這代表排查時不能只看 Service / Endpoints，也要看 ZK path、server id、pod IP registration 與 app log。

### Q5：怎麼判斷 phase 可以進下一階段？

保守答案是至少要看：

- 相關 pods Running / Ready。
- app boot log 沒有 config / ZK connection error。
- 對應 service type 已註冊到 ZK。
- 下游 watch / connection pool 有更新 log。

本輪沒有真實 runbook evidence，所以不能說這些已自動化，只能說這是 owner 應該補的 gate。

### Q6：如果 phase4 games 起不來，你會怎麼排查？

我會先把排查分成 Kubernetes 層與 runtime 層。Kubernetes 層看 pod 狀態、events、mount config 是否成功、env 是否有注入；runtime 層看 boot log、`server.properties` / `zookeeper.properties` load 是否成功、ZK connect 是否成功、是否有 repeated registration。這個 case 的重點是不要只看到 pod crash 或 ready false 就停住，因為真正的 dependency 是 ZK znode 與 peer connection。

### Q7：如果 ConfigMap 改錯，要 rollback 什麼？

不能只 rollback Deployment image。這條 flow 的 config 已拆成 per-service ConfigMap、shared zookeeper / lua / log4j2 ConfigMap、Secret / env。rollback 時要確認 image tag、per-service properties、shared config 與 Secret 是否是同一組相容版本。若 Secret 經 envFrom 注入，既有 process 通常不會自動 reload，還要 restart / recreate pod。

### Q8：如果要改善可用性，是否直接改成 RollingUpdate？

不建議直接改。RollingUpdate 可能讓同 server id 的新舊 pod 短暫並存，撞到 ZK registration 或 repeated registration。若要降低不可用時間，應該先確認 runtime 是否支援 server id 分流、traffic drain、graceful shutdown、phase gate automation，再評估 blue-green 或更細的 rollout，而不是單純把 strategy 改掉。

### Q9：這和 Spring Boot readiness probe 最大差異是什麼？

Spring Boot API 的 readiness 通常可以代表服務是否能接 HTTP traffic；這裡的 ready 要再加 application-level registry。gameserver 啟動後要註冊 ZK、watch peer path、更新 NetNode connection pool，這些都不是 Kubernetes 原生 readiness 自動知道的狀態。

### Q10：這條 flow 的 observability 最小要補什麼？

最小要能查三種東西：pod / phase 狀態、app boot / config load / ZK registration log、peer connection pool 更新 log。現有 evidence 只支持 stdout -> Fluent Bit -> Loki / Grafana 的方向，還沒有 dashboard / alert / query evidence，所以不能說完整 observability 已完成。

## 2. Lead / Architect 追問

### Q1：如果要把這套 flow 做成 production-grade，你會補什麼？

我會先補 phase gate 的自動化與可觀測性：每 phase 不只看 pod ready，還要查 ZK registration、boot log、connection pool health。接著補 rollback runbook，明確定義 image / config / Secret 如何一起回退。最後才看是否能從 Recreate 演進到更細緻的 rolling / blue-green，但前提是 runtime 支援 server id 與 traffic drain。

### Q2：你怎麼避免 generator 造成 config drift？

把 generator output 視為可驗證 artifact。部署前要 diff output，檢查每個 phase kustomization 是否包含對應 deployment + ConfigMap，shared config 是否只有一份 source of truth，並且避免手改 generated YAML。若要更嚴謹，CI 可以跑 manifest validation 或 kustomize build diff。

### Q3：這個案例跟一般 Spring Boot service rollout 最大差異是什麼？

一般 Spring Boot API 多半以 readiness / liveness、Service、Deployment strategy 為主；這個 gameserver 另外有 runtime service registry 與 server id。也就是 Kubernetes desired state 和 application runtime state 不是同一件事，owner 要把兩者對齊。

### Q4：如果你是 owner，會怎麼設計 phase gate？

我會把 gate 分三層。第一層是 Kubernetes：pod scheduled、containers ready、config mount 成功。第二層是 app boot：config load、ZK connect、service address registration 成功。第三層是 dependency：需要連的 server type 已 watch 到，connection pool 有更新。只有三層都通過，才進下一 phase。

### Q5：這套設計最大的技術債是什麼？

最大的技術債是 Kubernetes desired state 和 legacy ZK registry state 分裂。部署工具看到的是 pod / deployment；runtime 看到的是 server id、znode、peer address。只要沒有把兩邊的 health / rollback / ownership 串起來，operator 就可能誤判系統已 ready。

### Q6：如果要把它寫進履歷，你會怎麼保守寫？

目前我不會直接寫正式履歷。最多在面試裡說「分析過 code-backed case」。如果補到 Nick 本人的 MR / ticket / deploy runbook，才可能寫成「參與 gameserver K3s rollout 風險分析，整理 phase deployment、ZK registration 與 config rollback 邊界」這種非常保守的版本。

## 2.1 Failure Scenario Drill

| 情境 | 首要判斷 | 可說的 owner action | 不能誇大的地方 |
| --- | --- | --- | --- |
| phase1 未 ready 就進 phase2 | center 依賴 dbproxy / log / social 可能不完整 | 停在 phase2，補查 phase1 pod + ZK registration | 不能說已有自動 gate |
| gate ready 但玩家連不上 | NodePort / gate app / ZK peer 皆可能 | 分層查 service exposure、gate boot log、center/game peer watch | 不能說已驗證 production runbook |
| game pod 重啟後 peer 找不到 | znode / POD_IP / config 可能錯 | 查 pod IP replacement、server.properties、ZK path、connection pool log | 不貼內部位址 |
| config 更新後行為不一致 | image / per-service CM / shared CM 版本可能不一致 | 比對 generated manifests 與 rollout restart 範圍 | 不能說有 config versioning system |
| RollingUpdate 造成異常 | 新舊 pod 可能同 server id 雙跑 | 回到 Recreate，等舊 znode/session 收斂 | 不能說 Recreate 保證零 downtime |
| shutdown 卡住 | 玩家 / room / center 等待 game 節點收斂 | 對齊 terminationGracePeriod、stop log 與 ZK disappearance | 未確認 K8s termination 設定 |

## 3. 可用一句話

- 「這條 flow 的關鍵是 Kubernetes rollout 必須尊重 legacy runtime 的 ZK service discovery，不然 pod ready 也可能不代表遊戲服務可用。」
- 「`Recreate` 不是保守或落後，而是針對 server id / znode 雙註冊風險做的取捨。」
- 「ConfigMap externalization 讓 config 更透明，但 rollback discipline 反而更重要。」
- 「這個 case 不是 Kubernetes 教科書題，而是 app-level service registry 和 deploy strategy 的對齊題。」
- 「我會把它定位成 code-backed analysis，不把它講成 production owner claim。」

## 4. 不能回答過頭

若被問「你是否實際上線過？」目前應回答：

> 這份整理目前是 code-backed analysis。我還需要補 Nick 本人的 MR / ticket / deploy log，才會把它說成我實際主導或上線的成果。

若被問「那這對你有什麼價值？」目前應回答：

> 價值在於我能把 deployment surface 連回 runtime code，而不是只看 YAML。從 ZK registration、server id、Recreate、config externalization 到 rollback discipline，這些都是 Lead / Platform Backend 需要能判斷的風險。
