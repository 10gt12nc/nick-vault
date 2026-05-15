# iwin k3s-deploy Step 2：Flow 技術點與風險比較

更新時間：2026-05-15
掃描等級：Step 2 比較掃描；介於 Level 1 與 Level 2 之間
狀態：已建立
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 本次結論

建議第一條進 Step 3 的 flow：

```text
gameserver-phased-rollout
```

理由：

- 它最不像單純 YAML 管理，真正碰到 service startup dependency、ZK registration、pod IP、ConfigMap / Secret 外掛、log4j2 / lua config、phase apply 順序與 rollback risk。
- 遠端最新 refs 顯示 `iwin-gameserver` 從「configs bake into image」一路演進到多個 per-service ConfigMap / Secret / shared config，這比單一 service rollout 更有 owner decision 價值。
- `java-service-rollout-config` 與 `observability-pipeline` 也值得做，但它們比較適合作為 Step 3 的 supporting section 或下一條 flow；第一條先做 gameserver，會更有 iwin runtime 特色。
- `external-service-bridge` 在遠端最新 refs 已被「移除 Service / Endpoints abstraction，改直連既有外部依賴」取代，因此不建議作為主 flow，只保留為 migration trade-off 比較素材。

不更新正式履歷 / 自傳。這輪仍沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認。

## 自動重讀紀錄

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/CONVENTIONS.md`
- `projects/source-repo-inventory.md`

已重讀 vault：

- `projects/iwin/k3s-deploy/README.md`
- `projects/iwin/k3s-deploy/architecture-map.md`
- `projects/iwin/k3s-deploy/step1-candidate-flows.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/04-interview-casebook.md`
- `senior-owner-playbook/13-code-capability-map.md`

已重讀 source repo：

- `/Users/nick/Git/iwin/k3s-deploy`
- 已執行 `git fetch --all --prune` 更新 remote refs。
- local HEAD：`61cb42a8a21445f51ad7e032ade0d13de73ed7cc`
- remote HEAD：`48e1d50f017b8c67364072a0cb4614c843bfb474`
- ahead / behind：local ahead 0、behind 34。
- 公司 repo 工作樹未 pull / merge / checkout；本輪只讀 local 工作樹與 `origin/main` objects。
- repo 狀態：本機 `main` 落後 `origin/main`，且有未追蹤 IDE 目錄；本輪只讀不動。

已看 remote 差異：

- `HEAD..origin/main` 共有 34 個新 commits。
- 主要變動集中在 `iwin-gameserver` ConfigMap / Secret 外掛、log4j2 / lua config、server properties、payment / game-api / game-job / third-games-api ConfigMap / Secret、app-bi / bi-share config 拆分，以及 external-services abstraction 移除。
- 已看 `origin/main` 的 `iwin-gameserver` kustomization、phase kustomization、payment / game-api deployment、external-services README。

未重讀 / 未做：

- 未更新公司 repo 工作樹，因此本機檔案仍是舊版；Step 2 的「最新 refs」判斷來自 `origin/main` objects。
- 未讀 secret value，也不把 secret value、內網位址、registry URL、production URL 寫入 vault。
- 未掃各服務 repo 的 Dockerfile / entrypoint / CI pipeline 全貌。
- 未連線 cluster、未查 rollout history、未查 GitLab MR / issue / pipeline。
- 未確認 Nick 個人 MR / ticket / production issue。

## 既有文件狀態判斷

| 文件 | 狀態 | 判斷 |
| --- | --- | --- |
| `README.md` | 已同步 | 已把 Step 2 狀態改為已建立，並修正外部依賴定位 |
| `architecture-map.md` | 已同步 | Step 1 的 Service / Endpoints abstraction 在遠端最新 refs 已過時，已改成外部依賴存取的中性描述 |
| `step1-candidate-flows.md` | 需補 evidence / 可沿用 | candidate flow 大方向可用，但 remote refs 新增 34 commits，`external-service-bridge` 排序需下降；不直接改 Step 1，避免改寫歷史盤點 |
| `step2-flow-comparison.md` | 本輪新建 | 作為 Step 3 前的 project-level 比較 |

## Module / Service 邊界

| 區域 | 角色 | Step 2 判斷 |
| --- | --- | --- |
| `dev/iwin/iwin-gameserver` | game runtime 多服務部署 | 最值得進 Step 3；phase order 與 config 外掛是主要 owner decision |
| `dev/iwin/game-api` | 玩家 / partner API 部署 | 有 rollout、replica、ConfigMap / Secret、probe 價值，但需回服務 repo 才能看完整 config loading |
| `dev/iwin/payment` | payment service 部署 | 與 money service 有關，但此 repo 只看到 deploy surface；不能替代 payment business flow |
| `dev/iwin/game-job` | job service 部署 | 有 config / secret / log pipeline 價值，但 production flow 還是要回 `game_job` repo |
| `dev/iwin/third-games-api` | third-party API 部署 | 有 service topology 與 secret boundary，但不是本輪最強 |
| `dev/iwin/app-bi` / `dev/iwin/bi-share` | legacy admin / BI containerization | 適合作為 storage / log / env migration 案例，不適合包裝成核心後端成果 |
| `dev/fluent-bit` / `dev/loki` / `dev/grafana` | observability stack | 面試價值高，但需補 dashboard / incident evidence 才能變強 |
| `dev/external-services` | 外部依賴存取 | 遠端最新 refs 已從 K8s abstraction 改成直連既有外部依賴；適合作為 trade-off，不建議獨立 Step 3 |

## Candidate Flow 比較總表

| 排名 | Flow | Senior / Owner 價值 | Evidence 強度 | 履歷風險 | Step 2 判斷 |
| --- | --- | --- | --- | --- | --- |
| 1 | `gameserver-phased-rollout` | 很高 | 高 | 中 | 建議第一條 Step 3 |
| 2 | `java-service-rollout-config` | 高 | 高 | 中高 | 值得做，但容易變成多服務 YAML 平均整理；先當第二順位 |
| 3 | `observability-pipeline` | 高 | 中高 | 中 | 面試好用，但需 incident / dashboard evidence 才夠強 |
| 4 | `legacy-app-containerization` | 中高 | 中高 | 中高 | 適合作為 supporting case，不建議第一條 |
| 5 | `external-service-bridge` | 中 | 中 | 高 | 遠端最新 refs 已改設計；不建議作為主 flow |

## 維度比較

| Flow | service dependency | rollout / rollback | config / secret boundary | observability | stateful / storage | interview value | resume value |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `gameserver-phased-rollout` | 很高 | 很高 | 很高 | 中高 | 中 | 很高 | 中，需 Nick evidence |
| `java-service-rollout-config` | 中 | 高 | 高 | 中高 | 低中 | 高 | 中，需避免誇大 |
| `observability-pipeline` | 中 | 中 | 低中 | 很高 | 中 | 高 | 中，需 incident evidence |
| `legacy-app-containerization` | 中 | 中高 | 中高 | 中高 | 高 | 中高 | 低中，需 Nick evidence |
| `external-service-bridge` | 中高 | 低 | 中 | 低 | 低 | 中 | 低 |

## 1. `gameserver-phased-rollout`

中文名稱：iwin-gameserver 分階段部署與服務註冊

### 為什麼排第一

這條 flow 同時有三層價值：

1. deployment order：phase1 stateless、phase2 center、phase3 gate、phase4 games。
2. runtime dependency：ZK registration、pod IP、service discovery、center / gate / game 之間的啟動依賴。
3. config evolution：remote refs 顯示 server properties、lua redis、log4j2、zookeeper config 從 image / local file 逐步外掛到 ConfigMap / Secret。

這些都不是「會寫 Deployment」就能回答的問題，而是需要 owner 判斷：什麼可以 rolling、什麼要 Recreate、什麼 config 可以熱更新、什麼 secret 改了要 rollout restart、哪個 phase 失敗時 rollback 到哪裡。

### 已確認 evidence

- `origin/main:dev/iwin/iwin-gameserver/kustomization.yml` 明確註記正式部署需依 phase 順序手動 apply。
- `origin/main:dev/iwin/iwin-gameserver/phase1-stateless/kustomization.yml` 已包含 shared env、zookeeper config、lua redis config、log4j2 config、per-service config、Secret 與 service manifest。
- `origin/main:dev/iwin/iwin-gameserver/phase4-games/kustomization.yml` 顯示多個 game service 都有對應 `*-cm.yml` 與 deployment。
- `HEAD..origin/main` commit log 顯示：properties 外掛、敏感連線欄位 secret 化、log4j2 收編、lua redis ConfigMap、zookeeper config 外掛、log4j2 只留 Console 等演進。

### 風險與 owner decision

- phase 順序錯誤：center / gate / game 可能在依賴未 ready 時啟動。
- 雙開風險：部分服務若以 ZK ephemeral registration 或 singleton 語意運作，RollingUpdate 可能造成短暫雙註冊。
- config drift：per-service ConfigMap 多，若生成腳本或共用 config 沒收斂，某些 game service 可能跟其他服務不一致。
- secret reload：Secret 透過 env 或 volume 時，改 secret 不一定自動讓 app 重載，可能需要 rollout restart。
- rollback：若 config 與 image 分離，rollback 必須同時知道 image、ConfigMap、Secret、phase apply 順序。

### 待確認

- `/Users/nick/Git/iwin/iwin_gameserver` 的 entrypoint / config loading / ZK registration 實作。
- `gen.sh` 如何生成 per-service ConfigMap / deployment。
- 實際 deploy command、rollback procedure、是否有 kubectl rollout history。
- Nick 是否參與 phase 設計、config externalization 或 debug。

### Step 3 價值

最高。建議下一步直接做這條。

## 2. `java-service-rollout-config`

中文名稱：Java service rollout、config override 與 probe 設計

### 為什麼排第二

遠端 refs 顯示 Java services 從單純 Deployment / Secret mount，往 ConfigMap + Secret + env placeholder 的方向演進。這很適合整理：

- external config priority。
- image tag / rollout trigger。
- readiness / liveness initial delay。
- ConfigMap / Secret 變更後如何重載。
- replica / service topology 的取捨。

### 已確認 evidence

- `origin/main:dev/iwin/game-api/deployment.yml` 顯示 replicas、single Service LB、ConfigMap 掛 `/app/config`、Secret envFrom、log4j2 external config。
- `origin/main:dev/iwin/payment/deployment.yml` 顯示 ConfigMap 提供 Spring Boot external config，Secret 走 envFrom，probe initial delay 較長。
- `HEAD..origin/main` commit log 顯示 game-api / game-job / third-games-api 新增 ConfigMap、Secret 分離，payment config map / secret 調整。

### 風險與 owner decision

- ConfigMap 改了但 pod 不重啟，app 可能仍吃舊設定。
- Secret 經 envFrom 注入，secret rotate 後通常需要 rollout restart。
- `imagePullPolicy: Always` 搭配浮動 tag 對 dev 快，但正式環境需要更強 provenance。
- probe 若只做 TCP，無法驗證 DB / Redis / downstream 是否可用；但 probe 太深又可能引發 cascading restart。
- `payment` 是 money service，但此 repo 只確認 deployment，不可當作 payment business correctness evidence。

### 待確認

- 各服務 repo Dockerfile / entrypoint / Spring config loading。
- CI pipeline 如何 set image tag。
- dev / prod replica、LB、config source 差異。
- 是否有 rollout 失敗或 config 錯誤 incident。

### Step 3 價值

高，但容易平均整理多個 service。若要做，應縮成單一子題，例如 `game-api-rollout-config` 或 `payment-deploy-config-boundary`。

## 3. `observability-pipeline`

中文名稱：Fluent Bit / Loki / Grafana 觀測性 pipeline

### 為什麼排第三

這條很適合 Lead / Platform 面試，因為它回答「部署後怎麼查問題」。但 Step 2 看到的 evidence 主要是 manifests，尚未看到 dashboard、query、incident 或 alert，因此目前排在 gameserver 與 Java rollout 後面。

### 已確認 evidence

- local Step 1 已確認 `dev/fluent-bit/`、`dev/loki/`、`dev/grafana/` 存在。
- manifests 顯示 container stdout / stderr -> Fluent Bit -> Loki -> Grafana 的路徑。
- `HEAD..origin/main` 有 gameserver log4j2 只留 Console 的 commit，代表 log pipeline 與 app logging style 有連動。

### 風險與 owner decision

- app log 未統一走 stdout / stderr，centralized logging 會破洞。
- label 設計不足會讓 incident 查詢只能 grep 大海。
- health check log 過濾、namespace filter、Loki retention 都會影響 RCA。
- 只有 log pipeline 不等於 observability；還缺 metrics、alerts、dashboard、runbook。

### 待確認

- Grafana datasource / dashboard / query。
- Loki retention / storage / resource。
- 是否有 incident RCA 使用紀錄。
- 各服務 log format 是否一致。

### Step 3 價值

高，但建議等 gameserver 或 Java rollout 做完後，再作為第二條或補充 section。

## 4. `legacy-app-containerization`

中文名稱：legacy BI / admin app 容器化與 runtime storage 取捨

### 為什麼排第四

`app-bi`、`bi-share` 牽涉 PHP / Laravel / BI 類服務的 `.env`、file log、runtime cache、hostPath / emptyDir、stdout / stderr。這是常見 legacy containerization 題，但它不是 Nick 的核心 Senior Java Backend 主戰場，履歷要非常保守。

### 已確認 evidence

- `HEAD..origin/main` commit log 顯示 app-bi / bi-share `.env` 拆 ConfigMap + Secret。
- Step 1 已確認 app-bi runtime storage、bi-share log pipeline 相關 manifests。

### 風險與 owner decision

- `.env` 合併方式不清楚會造成 config precedence 混亂。
- hostPath 綁 node，reschedule 風險高。
- emptyDir 適合 cache / temp，不適合 source of truth。
- file log 是否進集中式 log pipeline，會影響 troubleshootability。

### 待確認

- app-bi / bi-share Dockerfile 與 entrypoint。
- runtime 目錄哪些是 cache，哪些是業務輸出。
- 是否曾因權限、檔案鎖、log 格式發生問題。

### Step 3 價值

中高，但不建議第一條。可以作為 deployment migration supporting case。

## 5. `external-service-bridge`

中文名稱：外部依賴存取抽象與簡化

### 為什麼降級

Step 1 時它看起來像「用 Kubernetes Service / Endpoints 抽象既有外部依賴」。但 fetch 後的 remote refs 顯示這個設計已被移除，最新方向是 pod 直接連既有外部依賴，理由是 dev-k3s 環境較單純、debug 更直接、manifest 維護面更小。

所以這條不適合再作主 flow。它更適合放進 Step 3 的 trade-off：

- abstraction vs debug simplicity。
- dev-only shortcut vs prod topology。
- Kubernetes resource 少一層 vs app config 對外部位置耦合增加。

### 已確認 evidence

- `origin/main:dev/external-services/README.md` 記錄設計已從 K8s Service / Endpoints abstraction 改成直連既有外部依賴。
- `HEAD..origin/main` diff stat 顯示 external-services 舊 Service / Endpoints manifests 被刪除，新增 README 與 setup script。

### 風險與 owner decision

- dev debug 變簡單，但 app config 更直接依賴外部位置。
- dev / prod 差異擴大，不可把 dev 行為當 production evidence。
- 少了 Kubernetes service abstraction，也少了一層可替換點。

### 待確認

- 這個改動是否只是短期 dev decision。
- 正式環境是否使用不同 service discovery / DNS。
- 變更時是否同步更新所有服務 config。

### Step 3 價值

低。不建議獨立成 flow。

## Step 3 建議範圍

建議建立單條 flow：

```text
projects/iwin/k3s-deploy/flows/gameserver-phased-rollout/
```

預期產出：

- `flow.md`：先用白話講清楚 gameserver phase deployment 在做什麼，再進啟動依賴、config boundary、rollback、observability。
- `career-interview.md`：保守面試素材，不能寫成 Nick 主導平台遷移。
- `materials/evidence.md`：記錄 local / remote HEAD、已看 code path、未看 cluster / service repo / MR。
- `materials/decision-notes.md`：整理 Recreate vs RollingUpdate、ConfigMap vs image bake、Secret reload、phase apply、direct dependency vs service abstraction。
- `materials/interview.md`：3 分鐘講法與 Lead / Architect 追問。
- `materials/claim-boundary.md`：明確列出不可誇大的履歷邊界。

Step 3 需要讀：

- `/Users/nick/Git/iwin/k3s-deploy` 的 `origin/main:dev/iwin/iwin-gameserver/**`
- `/Users/nick/Git/iwin/iwin_gameserver` 的 Dockerfile / entrypoint / config loading / ZK registration 相關路徑
- `k3s-deploy` path-specific history：phase apply、POD_IP / SERVER_IP、ConfigMap / Secret externalization、log4j2、lua redis、zookeeper config
- 若可行，再補 iwin-workspace 的 dev-k3s gameserver 運維文件，但只作參考，不搬敏感環境資訊

## 履歷 / 面試邊界

目前可說：

- `專案存在 / code-backed`：iwin dev-k3s manifests 具備 phase rollout、service topology、ConfigMap / Secret、log pipeline 等 platform evidence。
- `分析素材 / learning-only`：可用來練習 Lead / Platform Backend 對 release risk、config boundary、rollback、observability 的回答。

目前不可說：

- Nick 主導 iwin K3s migration。
- Nick 建立 production Kubernetes platform。
- Nick 負責 gameserver release owner。
- Nick 建立完整 observability / SRE 流程。
- Nick 改善 downtime、MTTR 或錯誤率。

需要補的 evidence：

- Nick 本人 MR / commit / ticket / production issue / 本人確認。
- 實際 deploy / rollback / incident / log query 紀錄。
- CI pipeline 與 cluster apply flow。
- `iwin_gameserver` service registration / config loading source code。

## 自動維護判斷

本輪已更新：

- `README.md`：Step 2 狀態與下一步建議。
- `architecture-map.md`：修正 external dependency abstraction 的最新定位。
- `step2-flow-comparison.md`：新增 Step 2 比較文件。

本輪不更新：

- `senior-owner-playbook/05-resume-master-zh.md`：evidence 不足，且 Nick 未要求正式履歷。
- `senior-owner-playbook/08-application-autobiography-zh.md`：同上。
- 共用 KB：本輪是 project-level 比較，沒有新增全 vault 規則。

## 下一步建議

只推薦一件事：

```text
iwin k3s-deploy gameserver-phased-rollout Step 3
```

原因：

- Step 2 已比較完候選 flow。
- `gameserver-phased-rollout` 最有 release risk、service dependency、config boundary 與 owner decision 價值。
- 下一步會建立單條 flow 學習包；不更新正式履歷，需要 commit，不需要 push，除非 Nick 明確要求。
