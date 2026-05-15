# Interview：gameserver-phased-rollout

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

## 2. Lead / Architect 追問

### Q1：如果要把這套 flow 做成 production-grade，你會補什麼？

我會先補 phase gate 的自動化與可觀測性：每 phase 不只看 pod ready，還要查 ZK registration、boot log、connection pool health。接著補 rollback runbook，明確定義 image / config / Secret 如何一起回退。最後才看是否能從 Recreate 演進到更細緻的 rolling / blue-green，但前提是 runtime 支援 server id 與 traffic drain。

### Q2：你怎麼避免 generator 造成 config drift？

把 generator output 視為可驗證 artifact。部署前要 diff output，檢查每個 phase kustomization 是否包含對應 deployment + ConfigMap，shared config 是否只有一份 source of truth，並且避免手改 generated YAML。若要更嚴謹，CI 可以跑 manifest validation 或 kustomize build diff。

### Q3：這個案例跟一般 Spring Boot service rollout 最大差異是什麼？

一般 Spring Boot API 多半以 readiness / liveness、Service、Deployment strategy 為主；這個 gameserver 另外有 runtime service registry 與 server id。也就是 Kubernetes desired state 和 application runtime state 不是同一件事，owner 要把兩者對齊。

## 3. 可用一句話

- 「這條 flow 的關鍵是 Kubernetes rollout 必須尊重 legacy runtime 的 ZK service discovery，不然 pod ready 也可能不代表遊戲服務可用。」
- 「`Recreate` 不是保守或落後，而是針對 server id / znode 雙註冊風險做的取捨。」
- 「ConfigMap externalization 讓 config 更透明，但 rollback discipline 反而更重要。」

## 4. 不能回答過頭

若被問「你是否實際上線過？」目前應回答：

> 這份整理目前是 code-backed analysis。我還需要補 Nick 本人的 MR / ticket / deploy log，才會把它說成我實際主導或上線的成果。
