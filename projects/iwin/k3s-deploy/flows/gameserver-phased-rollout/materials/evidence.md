# Evidence：gameserver-phased-rollout

## 本次掃描結論

- 掃描日期：2026-05-15
- 掃描等級：Level 2 Flow 深掃；Step 4 面試案例收斂
- 證據層級：`專案存在 / code-backed`
- Nick 貢獻：`待確認`
- 敏感資料處理：未複製 secret value、token、內網 IP、production URL；source 中若出現敏感值，本文件只描述類型，不貼內容。

## Vault / KB 重讀

已重讀：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/iwin/k3s-deploy/README.md`
- `projects/iwin/k3s-deploy/architecture-map.md`
- `projects/iwin/k3s-deploy/step1-candidate-flows.md`
- `projects/iwin/k3s-deploy/step2-flow-comparison.md`

既有文件判斷：

- `README.md`：需小調整；Step 3 已建立後，下一步建議不可停在 Step 3，已改為 Step 4。
- `architecture-map.md`：可沿用，已正確標記 dev-k3s / code-backed 邊界。
- `step1-candidate-flows.md`：需補 evidence；`external-service-bridge` 與 config bake-in 是早期 local snapshot，已由 Step 2 / Step 3 的 remote refs 修正，這輪補上舊 evidence 邊界。
- `step2-flow-comparison.md`：可沿用；它已補上 project-level Step 2、module / service 邊界與 flow ranking。
- `flow.md`：可沿用；Step 3 已補上業務問題、系統位置、Code 路徑與掃描範圍。Step 4 只補面試收斂摘要與下一步。
- `career-interview.md`：需補 Step 4；這輪補 90 秒 / 30 秒版本與防守句。
- `materials/interview.md`：需補 Step 4；這輪補 failure scenario drill、Senior / Lead 追問與回答邊界。
- `materials/claim-boundary.md`：需補 Step 4 claim gate；這輪補可用 / 禁止說法表。

## Source repo 狀態

### `/Users/nick/Git/iwin/k3s-deploy`

- 已執行 `git fetch --all --prune`。
- local branch：`main`
- local HEAD：`61cb42a8a21445f51ad7e032ade0d13de73ed7cc`
- remote HEAD：`48e1d50f017b8c67364072a0cb4614c843bfb474`
- ahead / behind：local ahead 0、behind 34。
- 本輪未 pull / merge / checkout；只讀 local 工作樹與 `origin/main` objects。
- repo 狀態有未追蹤 IDE 目錄；本輪未動公司 repo。

已讀 `origin/main` paths：

- `dev/iwin/iwin-gameserver/kustomization.yml`
- `dev/iwin/iwin-gameserver/gen.sh`
- `dev/iwin/iwin-gameserver/phase1-stateless/kustomization.yml`
- `dev/iwin/iwin-gameserver/phase2-center/kustomization.yml`
- `dev/iwin/iwin-gameserver/phase3-gate/kustomization.yml`
- `dev/iwin/iwin-gameserver/phase4-games/kustomization.yml`
- `dev/iwin/iwin-gameserver/*-cm.yml` 與主要 deployment / service 檔名清單
- path-specific `git log origin/main -- dev/iwin/iwin-gameserver`

重要 evidence：

- top kustomization 註解指出正式部署需依序 apply phase，而不是只看頂層 kustomization。
- `gen.sh` 生成多個 Deployment、per-service ConfigMap、global Secret / ConfigMap 與 phase kustomization。
- `gen.sh` 使用同一個 image，透過 `SVC_JAR`、`SVC_APPID`、`SVC_APPTYPE`、`SVC_MAIN` 決定服務身份。
- `SVC_APPID` 保留 legacy `server.properties` 的 `svrid`，避免業務資料與 ZK znode 名稱混亂。
- `server.properties` 變成 per-service ConfigMap，shared zookeeper / lua redis / log4j2 變成 shared ConfigMap，敏感連線欄位走 Secret / env。
- Deployment strategy 使用 `Recreate`，註解與 runtime code 共同支持「避免 ZK registration 雙跑」的判斷。
- gate 暴露玩家 TCP 入口；內部服務發現主要依賴 ZK znode，不是 Kubernetes Service。
- commit log 顯示 config externalization、zookeeper config 外掛、log4j2 收斂到 Console、lua / redis config 外掛等演進。

path-specific commits 已看摘要：

- `48e1d50`：log4j2 只留 Console。
- `1403bbb` / `432b657` / `dedbf3d`：zookeeper config externalization 與 mount 調整。
- `173f7ce` / `9ac7f76`：gameserver ConfigMap 補 zookeeper config。
- `54ad846`：Redis / Mongo auth 相關調整；不複製敏感值。
- `bd7c085`：移除 external-services abstraction，改直連既有外部依賴；不複製外部位址。
- `40ddff8`、`f1b6e7b`、`82f38d5`、`98a5a06`、`4266986`、`12b5342`、`8c13567`、`4e05891`：config / secret / log / properties externalization 演進。

### `/Users/nick/Git/iwin/iwin_gameserver`

- 已執行 `git fetch --all --prune`。
- local branch：`main`
- local HEAD：`30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- remote HEAD：`30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- ahead / behind：local ahead 0、behind 0。
- 工作樹乾淨；本輪只讀不動。

已讀 paths：

- `slots_env.sh`
- `server.sh`
- `slots-common/src/main/java/com/slots/common/server/BaseServer.java`
- `slots-common/src/main/java/com/slots/common/properties/ServerProperties.java`
- `slots-common/src/main/java/com/slots/common/properties/ZkProperties.java`
- `slots-common/src/main/java/com/slots/common/zookeeper/ZkService.java`
- `slots-common/src/main/java/com/slots/common/zookeeper/NetNodeConnectPool.java`
- `slots-common/src/main/java/com/slots/common/zookeeper/NetNodeBeConnectPool.java`
- `slots-game-gate/src/main/java/com/slots/game/gate/server/GateServer.java`
- `slots-game-center/src/main/java/com/slots/game/center/server/CenterServer.java`
- `slots-games/slots-game-common/src/main/java/com/slots/game/common/server/GameServer.java`
- game main class example：`Game2Main` 類型入口

重要 evidence：

- `server.sh` 透過 JVM system properties 指定 appid、apptype、confpath、logpath、jsonpath、modulespath。
- `BaseServer.initApp()` 會讀 server config、ZK config、初始化 ZK、初始化 modules。
- `BaseServer.checkServerPort()` 會查 ZK registration，若 server id 重複則拋錯。
- `BaseServer.initAfter()` 將已啟動的 NetServer / HttpServer 註冊到 ZK。
- `ZkService.regServer()` 會把 server address 寫入 ZK node。
- `ZkService.connectServer()` / watch callback 會監聽目標 server type，交給 `NetNodeConnectPool` 更新 peer connection。
- `GateServer` 會註冊玩家入口並連 center / games。
- `CenterServer` 會註冊 gate / game 相關 server，連 dbproxy / log / social；shutdown 時會等待 game 節點收斂。
- `GameServer` 會註冊 game server，連 log / center / dbproxy；shutdown 時等待玩家離場、踢玩家、停止 NetServer / GameWorld / Log / Schedule。

## 已確認

- phase rollout 是 manifests 明確設計，不是後設猜測。
- gameserver runtime 使用 ZK 做服務註冊 / 發現。
- per-service ConfigMap / shared ConfigMap / Secret externalization 是 remote 最新 refs 的重要演進。
- `Recreate` 與 runtime 重複註冊檢查、server id 語意有合理關聯。
- observability 方向是 stdout -> Fluent Bit / Loki / Grafana，但 dashboard / incident evidence 未掃。

## 合理推論

- `Recreate` 的主要價值是降低同 server id 短暫雙跑 / 雙註冊風險。
- phase gate 應該不只看 Kubernetes pod ready，也要看 runtime ZK registration 是否完成。
- rollback 需要同時管理 image tag、per-service ConfigMap、shared ConfigMap 與 Secret / env，而不是只做 Deployment rollback。

## 待確認

- Nick 是否參與任何相關 MR / commit / review / ticket。
- 實際 deploy pipeline 是否自動化 phase gate。
- 是否有真實 rollback / incident / pod log / ZK znode 查核紀錄。
- production 與 dev-k3s 的 deployment 差異。
- terminationGracePeriod / preStop 是否與 runtime graceful shutdown 完整對齊。

## Step 4 面試收斂 evidence

本輪沒有新增 source code path，使用 Step 3 已讀 evidence 做面試 case 收斂。重新確認 source repo 遠端狀態：

- `/Users/nick/Git/iwin/k3s-deploy`
  - 已執行 `git fetch --all --prune`。
  - local `main`：`61cb42a8a21445f51ad7e032ade0d13de73ed7cc`
  - `origin/main`：`48e1d50f017b8c67364072a0cb4614c843bfb474`
  - ahead / behind：0 / 34
  - 公司 repo 工作樹未 pull / merge / checkout；仍以 `origin/main` objects 作最新 evidence。
- `/Users/nick/Git/iwin/iwin_gameserver`
  - 已執行 `git fetch --all --prune`。
  - local `main` = `origin/main`：`30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
  - ahead / behind：0 / 0

Step 4 新增的是面試表達與 claim gate，不新增 production claim，不更新正式履歷。

## 未掃範圍

- 沒有連線 cluster，未跑 `kubectl`。
- 沒有讀 GitLab MR / issue / pipeline。
- 沒有讀 secret value、token、內網 IP、production URL。
- 沒有逐檔逐行掃全部 gameserver。
- 沒有逐 commit diff 追每個 config externalization 的原始原因。
