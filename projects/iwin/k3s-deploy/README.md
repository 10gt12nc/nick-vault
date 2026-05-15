# iwin k3s-deploy

本資料夾整理 `/Users/nick/Git/iwin/k3s-deploy` 的專案知識。

`k3s-deploy` 是 iwin dev-k3s 環境的 Kubernetes / Kustomize 部署 manifests，負責把 `game-api`、`game-job`、`payment`、`third-games-api`、`app-bi`、`bi-share`、`iwin-gameserver` 與共享基礎設施組成可部署的 dev cluster。它的 Senior / Owner 價值不在「會寫 YAML」，而在 release risk、service topology、config / secret 邊界、probe / resource、phase rollout、log pipeline 與回滾判斷。

目前證據層級是 `專案存在 / code-backed`；沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認前，不寫成 Nick 真實主導的 DevOps / 平台遷移成果。

## 讀檔順序

1. [step1-candidate-flows.md](step1-candidate-flows.md)：Step 1 候選 flow 盤點。
2. [architecture-map.md](architecture-map.md)：最小專案地圖與部署拓撲。
3. [step2-flow-comparison.md](step2-flow-comparison.md)：Step 2 候選 flow 風險與價值比較。
4. 待建立：`flows/{flow-name}/flow.md`：單條 deploy flow 的主研究報告。
5. 待建立：`flows/{flow-name}/career-interview.md`：該 flow 的保守面試 / 履歷素材。
6. 待建立：`flows/{flow-name}/materials/`：證據、技術決策、詳細面試稿與 claim 邊界附錄。

## 目前狀態

| 文件 / flow | 狀態 | 說明 |
| --- | --- | --- |
| `step1-candidate-flows.md` | 已建立 | Level 1 掃描，找出 Top 5 deploy / observability 候選 flow |
| `architecture-map.md` | 已建立 | 最小拓撲，用來定位 shared / iwin namespace 與主要服務 |
| `step2-flow-comparison.md` | 已建立 | 已比較 phase rollout、服務 rollout、observability、config / storage 取捨 |
| `flows/` | 尚未建立 | Step 1 不建立 flow folder，等 Nick 選定單條 flow 後再建 |

## 專案定位

已確認：

- Kustomize manifests 分成 shared namespace 與 iwin namespace。
- shared 側包含 log stack、message admin；遠端最新 refs 顯示外部依賴已由 Kubernetes Service / Endpoints abstraction 改成直連既有外部服務，需在 Step 3 深挖時再確認本機工作樹是否更新。
- iwin 側包含 `game-api`、`game-job`、`payment`、`third-games-api`、`app-bi`、`bi-share` 與 `iwin-gameserver`。
- `iwin-gameserver` 採 phase deployment：stateless / center / gate / games，文件註明正式部署需依順序手動 apply。
- 多個 Java service 使用 Deployment、readiness / liveness probe、resource request / limit、private registry image tag 與 rollout 註解。
- observability 採 container stdout / stderr 經 Fluent Bit 收集到 Loki，再由 Grafana 查詢。
- manifests 中存在 secret 檔案；本 vault 不讀取、不複製、不摘要 secret value。

推測：

- 這個 repo 比較像 dev-k3s 遷移與環境標準化，不一定是 production deploy source of truth。
- `k3s-deploy` 可支撐 Lead / Platform Backend 面試中的 release risk 與 observability 討論，但仍需和實際 CI job、服務 repo branch、事故 / rollback 記錄交叉確認。
- phase rollout 的價值可能高於單一 service YAML，因為它牽涉服務註冊、啟動依賴與雙開風險。

待確認：

- Nick 實際參與過哪些 commits / MR / ticket。
- CI deploy job 如何把 image tag 推到 cluster。
- dev-k3s 與 production deployment 的差異。
- 是否有實際 rollback、incident、log 查詢或 troubleshooting 記錄。
- secret 管理是否只是 dev 環境權宜，或已有外部 secret manager / GitLab protected variable 流程。

## 履歷邊界

目前只能說：

- 可用來理解 / 分析 K3s rollout、Kustomize service topology、observability pipeline 與 legacy app containerization trade-off。
- 可作為 Lead / Platform Backend 面試素材的 evidence base，但需完成單條 flow Level 2 深掃。

目前不能說：

- Nick 主導 iwin K3s 遷移。
- Nick 負責完整 production Kubernetes 平台。
- Nick 建立完整 DevOps / SRE system。
- 單靠 Step 1 寫入正式履歷 / 自傳。

## 下一步建議

只推薦一件事：

```text
iwin k3s-deploy gameserver-phased-rollout Step 3
```

原因：

- Step 2 已比較 candidate flows。
- `gameserver-phased-rollout` 最能承載啟動依賴、服務註冊、config 外掛、log4j2 / lua config、Recreate / RollingUpdate 與 rollback 風險。
- Step 3 不更新正式履歷，會建立單條 flow 學習包；完成後依規則自動 commit。
