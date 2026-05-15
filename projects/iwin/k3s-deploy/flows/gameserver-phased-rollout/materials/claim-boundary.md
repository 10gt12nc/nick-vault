# Claim Boundary：gameserver-phased-rollout

## Evidence level

| 說法 | 目前層級 | 是否可放正式履歷 |
| --- | --- | --- |
| 分析過 iwin-gameserver K3s phase rollout 設計 | `專案存在 / code-backed` | 暫不建議，除非定位為學習 / 分析 |
| 能說明 ZK registration 對 rollout strategy 的影響 | `分析素材 / learning-only` + `code-backed` | 可在面試討論，不放成果 bullet |
| Nick 參與 gameserver K3s 遷移 | `待確認` | 不可 |
| Nick 主導 production rollout / rollback | `待確認` | 不可 |
| Nick 建立 observability pipeline | `待確認` | 不可 |
| 改善 downtime / deploy success rate | `待確認` | 不可 |

## 可說

- 「這份 flow 是 code-backed 的系統分析，涵蓋 Kustomize phase rollout、ConfigMap / Secret externalization、ZK service discovery 與 runtime shutdown。」
- 「我可以用這個案例說明 Kubernetes deployment strategy 需要尊重 application-level service registry。」
- 「如果要升級成履歷成果，需要補 Nick 本人貢獻 evidence。」

## 不可說

- 「我主導 gameserver 遷移到 K3s。」
- 「我設計並落地 production phase rollout。」
- 「我負責 iwin 整套 Kubernetes 平台。」
- 「我建立完整 SRE / observability system。」
- 「此 flow 已經在 production 證明降低 downtime。」

## 補 evidence 後的升級路徑

1. 找 Nick 本人 MR / commit / review comment。
2. 找 ticket / deploy note / incident note。
3. 找 pipeline config 或 runbook，證明 phase gate / rollback 不是紙上分析。
4. 找 production 或 staging log，證明 ZK registration / shutdown / rollback 曾被實際驗證。
5. Nick 本人確認參與範圍後，才能改寫成保守履歷 bullet。
