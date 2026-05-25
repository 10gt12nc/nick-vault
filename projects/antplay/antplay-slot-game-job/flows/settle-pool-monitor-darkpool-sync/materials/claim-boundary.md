# Claim Boundary - settle-pool-monitor-darkpool-sync

日期: 2026-05-25

## Claim 分層

| 層級 | 判斷 |
| --- | --- |
| 真實開發過 | 不成立。本輪未找到 Nick / `10gt12nc` direct path-specific commit。 |
| code-backed | 成立。current code path 可追到 consumer、grouping、handler、DB repository、reset sync、alert。 |
| 可面試講 | 可成立，但要標成 analysis-first。適合講 event projection / Redis DB consistency / reset sync failure window。 |
| 可直接履歷 | 不建議。只能作 `antplay-slot-game-job` project-level supporting study evidence。 |
| 不可誇大 | 不能說主導 settle pool / dark pool / risk / jackpot / player control platform。 |

## 可保守說

- 我分析過 AntPlay job 的 settle pool / dark pool monitor flow。
- 這條 flow 消費 settled bet event，分 normal / free / jackpot / activity / player control，寫入 `settled_pool`。
- 我能說明 Kafka replay、DB increment、Redis reset sync、backup / truncate / insert、alert 與單位邊界。
- 這是 code-backed analysis，不是我的 direct implementation。

## 不可說

- 不說我開發此 flow。
- 不說主導完整即時風控。
- 不說主導完整 dark pool / settle pool / jackpot / player control。
- 不說已確認 production reconciliation / DLT / restore runbook。
- 不把 Arnold / Eliot commits 說成 Nick direct contribution。
- 不直接更新 `05 / 08`。

## Step 3 結論

- 可作 Step 4 面試素材。
- 暫不回填正式履歷。
- 下一步應整理正式面試 case，並持續強調 analysis-first。
