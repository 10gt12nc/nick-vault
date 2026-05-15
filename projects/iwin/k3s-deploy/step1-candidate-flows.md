# iwin k3s-deploy Step 1：候選 Flow 盤點

更新時間：2026-05-15
掃描等級：Level 1 Flow 掃描
狀態：已建立
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 本次結論

`k3s-deploy` 是 iwin dev-k3s 的 deployment / observability / service topology repo。這個 project 的價值不是交易狀態機，而是把多個 legacy / Java / game runtime 服務放進 K3s 時，怎麼控制 release risk、啟動順序、service discovery、config / secret、log pipeline 與 rollback。

目前最值得延伸的候選 flow：

1. `gameserver-phased-rollout`：`iwin-gameserver` phase deployment，含 stateless / center / gate / games 啟動順序、服務註冊、Recreate / RollingUpdate 取捨與雙開風險。
2. `java-service-rollout-config`：`game-api`、`payment`、`third-games-api`、`game-job` 的 Deployment / Service / probe / resource / config 覆蓋與 image tag rollout。
3. `observability-pipeline`：Fluent Bit 收集 container log 到 Loki，再由 Grafana 查詢，適合作為 incident RCA 與 troubleshooting 面試案例。
4. `legacy-app-containerization`：`app-bi`、`bi-share` 從 host runtime / file log 轉向 stdout / stderr、emptyDir / hostPath 的取捨。
5. `external-service-bridge`：用 Kubernetes Service / Endpoints 把 cluster 內服務連到既有外部依賴，重點是 dev/prod 差異、blast radius 與命名抽象。

不更新履歷。沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認前，所有候選 flow 都只當 `專案存在 / code-backed` 或 `分析素材 / learning-only`。

## 自動重讀紀錄

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/CONVENTIONS.md`
- `projects/source-repo-inventory.md`

已重讀 vault：

- `projects/README.md`
- `projects/iwin/app_bi/README.md`
- `projects/iwin/game_api/README.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/04-interview-casebook.md`
- `senior-owner-playbook/12-role-target-readiness-matrix.md`
- `senior-owner-playbook/13-code-capability-map.md`
- 確認 `projects/iwin/k3s-deploy/` 原本不存在，這輪新建 README、architecture map 與 Step 1。

已重讀 code repo：

- `/Users/nick/Git/iwin/k3s-deploy`
- 目前分支：`main`
- 遠端分支：目前只看到 `origin/main`
- repo 狀態：`main` 追蹤 `origin/main`；有未追蹤 `.idea/`，本輪只讀不動
- 近期主線 log：已看到 game-api、payment、third-games-api、game-job、app-bi、bi-share、iwin-gameserver、Fluent Bit / Loki / Grafana、external-services 相關 commits。
- path-specific log：已看 `dev/iwin/iwin-gameserver`、`dev/iwin/payment`、`dev/iwin/game-api`、`dev/fluent-bit`、`dev/loki`、`dev/grafana`、`dev/external-services` 等路徑。

已看主要 code path：

- `dev/kustomization.yml`
- `dev/iwin/kustomization.yml`
- `dev/iwin/iwin-gameserver/kustomization.yml`
- `dev/iwin/iwin-gameserver/phase1-stateless/`
- `dev/iwin/iwin-gameserver/phase2-center/`
- `dev/iwin/iwin-gameserver/phase3-gate/`
- `dev/iwin/iwin-gameserver/phase4-games/`
- `dev/iwin/game-api/`
- `dev/iwin/game-job/`
- `dev/iwin/payment/`
- `dev/iwin/third-games-api/`
- `dev/iwin/app-bi/`
- `dev/iwin/bi-share/`
- `dev/fluent-bit/`
- `dev/loki/`
- `dev/grafana/`
- `dev/external-services/`

已看參考 workspace：

- `/Users/nick/Git/iwin/iwin-workspace/docs/dev-k3s環境-運維/` 的檔案清單。
- 僅用來定位存在，不把舊文直接搬進 vault。

未重讀：

- 未讀 secret value；只確認 manifests 中有 secret 引用或 secret 檔名。
- 未 checkout 其他 branch；目前未看到其他遠端分支。
- 未逐 commit diff；只看近期 log、path-specific log 與少量重要 stat。
- 未執行 `kubectl` 或連線 cluster。
- 未掃各服務 repo 的 k3s branch / Dockerfile / CI pipeline 全貌。
- 未確認 Nick 個人 MR / ticket / production issue。

## 舊文件狀態判斷

| 文件 | 狀態 | 判斷 |
| --- | --- | --- |
| `projects/iwin/k3s-deploy/README.md` | 本輪新建 | 專案入口，保守標註履歷邊界 |
| `projects/iwin/k3s-deploy/architecture-map.md` | 本輪新建 | 最小拓撲，避免 Step 1 變成散亂 YAML 清單 |
| `projects/iwin/k3s-deploy/step1-candidate-flows.md` | 本輪新建 | Level 1 candidate flow 盤點 |
| `senior-owner-playbook/04-interview-casebook.md` | 可沿用 | 已有 K3s / rollout / observability 面試主軸，可和本 project 後續 flow 對齊 |
| `senior-owner-playbook/13-code-capability-map.md` | 可沿用 | 已把 `k3s-deploy` 定位為 K3s deployment、kustomization、service rollout、phase deployment |
| workspace dev-k3s 運維文件 | 可參考 / 不搬運 | 可能包含環境細節與敏感資訊，本輪只作索引參考 |

## 掃描等級判斷

本次使用 Level 1。

原因：

- Nick 只指定 `iwin k3s-deploy`，目前 project 尚未在 vault 建立。
- Step 1 目標是找 Top 3-5 candidate flows，不是深挖單一 deploy flow。
- `k3s-deploy` 有多條 platform / rollout 候選，直接 Level 2 容易選錯主軸。
- Level 3 目前不值得，因為尚未選定 flow，也尚未確認 Nick 本人 evidence。

本次做：

- 建立 `k3s-deploy` 專案入口。
- 建立最小 architecture map。
- 找出 Top 5 deploy / observability candidate flows。
- 標清楚已確認、推測、待確認。
- 標清楚不可更新正式履歷。

本次不做：

- 不建立單條 flow folder。
- 不逐檔逐行掃完整 repo。
- 不讀 secret value。
- 不連線 cluster。
- 不更新履歷 / 自傳。
- 不把 dev-k3s manifests 包裝成 Nick 主導 production platform。

## 本次掃描範圍

已看 repo：

- `/Users/nick/Git/iwin/k3s-deploy`
- `/Users/nick/Git/iwin/iwin-workspace` 的 dev-k3s 運維文件清單
- `/Users/nick/Git/nick/nick-vault`

source repo 狀態：

- `/Users/nick/Git/iwin/k3s-deploy`
- 目前分支：`main`
- repo 有未追蹤 IDE 目錄；本輪未動。

已看分支：

- 本地：`main`
- 遠端：`origin/main`

已看 git log：

- 近期主線 log，包含 `iwin-gameserver` phase service、`game-api` replica / image tag、`third-games-api` NodePort、`bi-share` log pipeline、`app-bi` runtime storage、`payment` Java options / secret path、`game-job` deployment、Fluent Bit / Loki / Grafana、external-services。
- path-specific log：`dev/iwin/iwin-gameserver`、`dev/iwin/payment`、`dev/iwin/game-api`、`dev/fluent-bit`、`dev/loki`、`dev/grafana`、`dev/external-services`。

已看 code path：

- Kustomize root：`dev/kustomization.yml`
- iwin root：`dev/iwin/kustomization.yml`
- gameserver phase：`dev/iwin/iwin-gameserver/**`
- Java services：`dev/iwin/game-api/**`、`dev/iwin/payment/**`、`dev/iwin/third-games-api/**`、`dev/iwin/game-job/**`
- legacy / BI services：`dev/iwin/app-bi/**`、`dev/iwin/bi-share/**`
- observability：`dev/fluent-bit/**`、`dev/loki/**`、`dev/grafana/**`
- external dependencies abstraction：`dev/external-services/**`

未掃範圍：

- 各服務 repo 的 Dockerfile、CI pipeline 與 k3s branch。
- 真實 cluster 狀態、kubectl events、rollout history。
- GitLab MR / issue / pipeline history。
- Secret value、credential、內網位址與 production URL。
- 是否有事故、rollback、hotfix 或 production support evidence。

## 專案定位

`k3s-deploy` 是 deploy / platform support repo，不是業務 API repo。它把多個服務用 Kustomize 組成 dev-k3s 環境：

- shared：log stack、message support、外部依賴 service abstraction。
- iwin app：玩家 API、job、payment、third-party API、BI / admin 類服務。
- gameserver：分 phase 的 game runtime deployment。

這類 repo 最適合用來整理：

- release risk management。
- service startup dependency。
- config / secret / image tag 的邊界。
- readiness / liveness / resource 設定。
- legacy app containerization。
- log pipeline 與 troubleshooting。

不適合直接整理成：

- money correctness 主 flow。
- transaction consistency 主案例。
- Nick production owner 履歷 claim。

## 核心入口

### Kustomize 入口

已確認：

- `dev/kustomization.yml` 管 shared resources。
- `dev/iwin/kustomization.yml` 管 iwin application resources。
- `dev/iwin/iwin-gameserver/kustomization.yml` 註明正式部署需要依 phase 順序手動 apply。

推測：

- root kustomization 可能用於預覽或整批 apply，但 gameserver 需要依賴順序，所以不能無腦套全部。

待確認：

- 實際 CI / operator 使用哪個 apply path。
- 是否有 dry-run / diff / rollback 流程。

### Service / Deployment 入口

已確認：

- 多數 API / app 服務用 Deployment + Service。
- 對外測試入口多用 NodePort；cluster 內服務互通多用 ClusterIP。
- 多個 service 設定 readiness / liveness probe、resource requests / limits、nodeSelector。
- Java service 透過 image tag 與 rollout 註解表達 deploy 流程。

待確認：

- 真實 deploy command。
- image tag 與 Git commit / pipeline 的追溯關係。
- 是否有自動 rollback。

### Observability 入口

已確認：

- Fluent Bit DaemonSet tail container logs。
- filter 保留 iwin 相關 namespace，並過濾 health check 類 log。
- Loki 作為 log storage。
- Grafana 透過 datasource 查詢。

待確認：

- retention、容量規劃、告警規則、dashboard / query 標準。
- 是否有實際 incident RCA 使用紀錄。

## Top 5 Candidate Flows

### 1. `gameserver-phased-rollout`

中文名稱：iwin-gameserver 分階段部署與服務註冊

為什麼重要：

- gameserver 拆成 stateless、center、gate、games 四段。
- kustomization 註明正式部署必須依 phase 順序 apply。
- gate / game 類服務使用 pod IP、service registration、Recreate 策略等線索，牽涉雙開與啟動依賴風險。

production 風險：

- 順序錯誤可能導致註冊依賴未 ready。
- 雙開可能造成 ephemeral registration、玩家入口或房間服務狀態不一致。
- image / config 不一致可能造成部分 game server 行為不同。

已確認 evidence：

- `dev/iwin/iwin-gameserver/kustomization.yml` 有 phase deployment 註解。
- `phase1-stateless`、`phase2-center`、`phase3-gate`、`phase4-games` 目錄存在。
- phase3 gate service 對外，部分 center / social service 改為 cluster 內 service。
- 近期 commit 有新增 center / social ClusterIP service、移除 PVC、configs 改 bake 進 image、POD_IP / SERVER_IP 相關修正。

推測：

- 這條 flow 最能轉成 Lead / Architect 面試案例，因為它不是單一 YAML，而是啟動依賴和 rollout risk。

待確認：

- `iwin_gameserver` repo 中 entrypoint 如何替換 config。
- ZK 註冊與服務發現實作。
- 真實 rollout / rollback 操作紀錄。
- Nick 是否參與 phase 設計或 debug。

### 2. `java-service-rollout-config`

中文名稱：Java service rollout、config override 與 probe 設計

為什麼重要：

- `game-api`、`payment`、`third-games-api`、`game-job` 都是後端服務部署入口。
- 可分析 image tag、readiness / liveness、resource、config / secret、NodePort / ClusterIP 的 owner decision。
- 對 Senior Backend 面試有用，因為 deployment 設計會影響 API availability 與故障排查。

production 風險：

- readiness 太早會導致還沒初始化完就收流量。
- liveness 太 aggressive 會造成啟動中被重啟。
- config / secret mount 錯誤會讓服務啟動失敗或連錯依賴。
- 浮動 image tag 與固定 tag 混用會影響可追溯性。

已確認 evidence：

- `dev/iwin/game-api/deployment.yml`
- `dev/iwin/payment/deployment.yml`
- `dev/iwin/third-games-api/deployment.yml`
- `dev/iwin/game-job/deployment.yml`
- 近期 commit 有 `game-api` replica 調整、`payment` Java options / config path 修正、`third-games-api` deployment 新增、`game-job` manifests 新增。

推測：

- 這條適合 Step 2 比較，但單條 flow 深挖時可能不如 gameserver phase 有獨特性。

待確認：

- 各 service repo 的 Dockerfile / config loading。
- CI job 是否 `kubectl set image`，如何追 tag。
- dev-k3s 與 production 的 replica / LB 差異。

### 3. `observability-pipeline`

中文名稱：Fluent Bit / Loki / Grafana 觀測性 pipeline

為什麼重要：

- log pipeline 是 incident RCA 的入口。
- manifests 已包含 Fluent Bit DaemonSet、Loki Deployment / Service、Grafana Deployment / datasource。
- 可轉成「如何讓 deploy 後能查問題」的面試案例。

production 風險：

- label 不足會導致查不到某個 pod / container / namespace。
- 過濾規則太寬或太窄都會影響 RCA。
- Loki storage / retention / resource 不足會造成 log 丟失或查詢慢。
- legacy app 若仍寫 file log，可能不完整進 log pipeline。

已確認 evidence：

- `dev/fluent-bit/configmap.yml`
- `dev/fluent-bit/daemonset.yml`
- `dev/loki/deployment.yml`
- `dev/grafana/deployment.yml`
- 近期 commit 有加入 Fluent Bit / Grafana / Loki，以及 bi-share / app-bi log pipeline 調整。

推測：

- 這條可與 `senior-owner-playbook/04-interview-casebook.md` 的 K3s / rollout / observability 案例對齊。

待確認：

- Grafana dashboard / query。
- Loki retention 與容量規劃。
- 是否有告警、on-call、incident 使用紀錄。

### 4. `legacy-app-containerization`

中文名稱：legacy BI / admin app 容器化與 runtime storage 取捨

為什麼重要：

- `app-bi`、`bi-share` 代表 legacy PHP / BI 類服務從 host path / runtime cache / file log 遷移到 container 的取捨。
- 這類遷移常見風險不是 code，而是 stateful file、log、cache、權限與 node affinity。

production 風險：

- hostPath 會綁 node，影響 reschedule。
- emptyDir 重啟會清空，必須確認只放 cache / temp。
- file log 若沒有進 stdout / stderr，會影響 centralized logging。
- RollingUpdate / Recreate 選錯可能造成檔案鎖、重複寫入或短暫不可用。

已確認 evidence：

- `dev/iwin/app-bi/deployment.yml`
- `dev/iwin/bi-share/deployment.yml`
- 近期 commit 有 app-bi 拆 hostPath runtime、bi-share log 轉 stderr / JSON 到 Loki 的線索。

推測：

- 這條可補 legacy app migration 的技術硬底子，但履歷價值要看 Nick 是否真的參與。

待確認：

- PHP / Laravel app 的 log channel 與 runtime path 實作。
- 哪些檔案必須持久化，哪些只是 cache。
- 實際切換時是否遇到權限 / lock / log parsing 問題。

### 5. `external-service-bridge`

中文名稱：cluster 內服務透過 Service / Endpoints 連既有外部依賴

為什麼重要：

- dev-k3s 不一定把 DB / Redis / ZK / Mongo 全部搬進 cluster。
- 透過 Kubernetes Service / Endpoints 給 pod 穩定 DNS 名稱，可降低 app config 對實體 host 的耦合。
- 這是 migration 中常見的「先抽象依賴，再逐步替換實體位置」策略。

production 風險：

- Endpoints 指向既有外部依賴，cluster DNS 正常不代表外部服務健康。
- network / firewall / DNS 問題會被包裝成 app 啟動或 timeout 問題。
- dev/prod 差異太大時，不能把 dev-k3s 行為直接當 production evidence。

已確認 evidence：

- `dev/external-services/kustomization.yml`
- `dev/external-services/mysql.yml`
- `dev/external-services/mongodb.yml`
- `dev/external-services/redis-1.yml`
- `dev/external-services/redis-2.yml`
- `dev/external-services/zookeeper.yml`

推測：

- 這條適合放在 Step 2 比較或作為其他 flow 的 dependency section，不一定值得獨立深挖成主 flow。

待確認：

- 各 app config 是否已全面使用 service DNS。
- 外部依賴健康檢查與故障告警。
- 服務遷移到 cluster 內時是否需要改 app config。

## 建議第一條深挖 flow

建議先深挖：

```text
gameserver-phased-rollout
```

理由：

- 它最不像單純 YAML 清單，牽涉啟動順序、服務註冊、雙開風險、pod IP、service exposure 與 rollback。
- 比單一 Java service rollout 更有 System Owner / Lead 面試價值。
- 比 observability pipeline 更貼近 iwin 遊戲 runtime 的服務拓撲。
- 但在正式深挖前，Step 2 仍值得先比較候選 flow，避免過早鎖定。

## 下一步要讀的 code path

若 Nick 下 `iwin k3s-deploy Step 2`：

- `dev/iwin/iwin-gameserver/**`
- `dev/iwin/game-api/**`
- `dev/iwin/payment/**`
- `dev/fluent-bit/**`
- `dev/loki/**`
- `dev/grafana/**`
- `dev/iwin/app-bi/**`
- `dev/iwin/bi-share/**`
- 相關 commit stat / path-specific log

若 Nick 之後選 `gameserver-phased-rollout` 做 Step 3：

- `/Users/nick/Git/iwin/k3s-deploy/dev/iwin/iwin-gameserver/**`
- `/Users/nick/Git/iwin/iwin_gameserver` 的 Dockerfile / entrypoint / config loading / ZK registration 相關路徑
- path-specific history 與重要 diff：phase deploy、POD_IP / SERVER_IP、PVC removal、ClusterIP service commits

## 履歷 / 面試邊界

可作為面試素材：

- `專案存在 / code-backed`：能說明 K3s manifests 中如何處理 service rollout、probe、resource、log pipeline、phase deployment。
- `分析素材 / learning-only`：能整理 owner decision，例如 Recreate vs RollingUpdate、config bake into image vs mount、hostPath vs emptyDir、NodePort vs ClusterIP。

不可直接進正式履歷：

- Nick 主導 K3s migration。
- Nick 建立 observability platform。
- Nick 完成 production Kubernetes rollout。
- Nick 保障全站 zero downtime。

待補 evidence：

- Nick 本人 MR / commit / ticket。
- 實際 deploy / rollback / incident 紀錄。
- CI pipeline 與 cluster apply flow。
- 服務 repo 的 k3s branch、Dockerfile、entrypoint 與 config loading。

## 下一步建議

只推薦一件事：

```text
iwin k3s-deploy Step 2
```

原因：

- Step 1 已先定位 Top 5 deploy / observability candidate flows。
- 這個 repo 的履歷價值取決於能不能選出最有 owner decision 的單條 flow，不能平均整理所有 YAML。
- Step 2 不更新正式履歷，可能建立 `step2-flow-comparison.md`，不需要 push；完成後依規則自動 commit。
