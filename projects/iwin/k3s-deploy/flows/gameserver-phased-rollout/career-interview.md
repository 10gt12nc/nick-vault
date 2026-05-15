# gameserver-phased-rollout Career / Interview

## 定位

- Flow：`gameserver-phased-rollout`
- 證據層級：`專案存在 / code-backed`
- Nick 貢獻：`待確認`
- 用途：面試素材、系統設計討論、平台 / Lead Backend 能力展示的準備稿
- 不用途：正式履歷最終 bullet、主導成果聲明、production SRE claim

## 3 分鐘保守講法

我看過一個 gameserver 從傳統多服務 runtime 搬到 K3s 的部署設計。它不是單一 API，而是同一個 image 跑出 dbproxy、log、center、gate 和多個 game service，每個服務靠 appid、apptype、main class 和獨立 properties 決定身份。

這個案例最關鍵的是啟動順序和 service discovery。runtime 不是只靠 Kubernetes Service，而是透過 Zookeeper 註冊自己的 server address，也 watch 其他服務的 path 來建立連線。所以 deployment 不能只看 pod ready，還要看 runtime 是否完成 ZK registration。部署上把 flow 拆成 stateless、center、gate、games 四個 phase，避免 center / gate / game 在依賴未 ready 時亂序啟動。

另一個重點是 rollout strategy。這類服務有 server id 與 znode registration 的語意，RollingUpdate 可能造成同一 server id 短暫雙跑，所以 manifests 採用 Recreate。這犧牲一些連續可用性，但降低雙註冊與狀態混亂風險。真正的 owner decision 不是背 Kubernetes 教科書，而是要把 legacy runtime 的服務發現、config 外掛、graceful shutdown 和 log pipeline 一起看。

## 可用面試 bullet

證據層級：`專案存在 / code-backed`；Nick 貢獻 `待確認`。

- 分析過 legacy gameserver multi-service runtime 在 K3s 上的 phase rollout 設計，重點包含啟動依賴、ZK service registration、pod IP registration 與 rollout strategy。
- 能說明為何這類服務不適合直接套用一般 `RollingUpdate`，而要評估 server id、ZK znode、雙開風險與 phase gate。
- 能拆解 ConfigMap / Secret externalization 對 rollback 的影響：rollback 不只回 image，也要對齊 per-service properties、shared zookeeper config 與 env secret。
- 能從 runtime code 連回 deployment decision：`BaseServer` boot、`ServerProperties` / `ZkProperties` load、`ZkService` register / watch、`GameServer.stop()` graceful shutdown。

## 不建議放正式履歷的寫法

目前不要寫：

- 「主導 iwin gameserver K3s 遷移」
- 「設計 production Kubernetes rollout / rollback 流程」
- 「建立完整 SRE observability 平台」
- 「改善部署成功率 / downtime X%」

原因：本輪只確認 repo code 與 manifests，沒有 Nick 本人 MR / ticket / production incident / metric。

## 面試回答邊界

可以說：

- 「我用這個 case 分析過 legacy gameserver 容器化時，Kubernetes rollout 與 runtime service discovery 之間的衝突。」
- 「從 code 看，它使用 Zookeeper registration / watch 來建立服務間連線，因此 phase order 和 Recreate strategy 有合理背景。」
- 「如果要把它寫成履歷成果，我會先補 MR / ticket / deploy runbook 或事故處理 evidence。」

不要說：

- 「這是我上線過的 production 流程。」
- 「我全權負責 gameserver platform。」
- 「這套 rollback 已經被 production 驗證。」

## 待補後才可能升級的 claim

| 想升級的說法 | 需要 evidence |
| --- | --- |
| 參與 gameserver K3s 遷移 | Nick 相關 MR / commit / ticket / review comment |
| 處理 rollout / rollback 問題 | deploy failure、incident、rollback log、postmortem、Nick 本人確認 |
| 設計 phase rollout | 設計文件、MR discussion、commit author / reviewer、pipeline 變更紀錄 |
| 改善 observability | dashboard / query / alert / RCA 使用紀錄 |
