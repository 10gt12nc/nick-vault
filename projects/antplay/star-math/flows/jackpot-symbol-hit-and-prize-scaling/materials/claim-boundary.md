# Claim Boundary - Jackpot Symbol Hit / Prize Scaling

## Evidence Level

| 範圍 | 判斷 |
| --- | --- |
| `sph-math` jackpot symbol hit / JP log / simulation | `真實開發過 + code-backed` |
| `sdt-math` jackpot / fixedMultiBet / debug helper | `真實開發過 + code-backed` |
| `slc-math` 同型 jackpot scaling pattern | `專案存在 / code-backed`，本 Step 只作補充 |
| `math-core` jackpot symbol / reward contract | `真實開發過 + code-backed` / shared contract evidence |
| game-api SDT runtime caller / jackpot service | `專案存在 / code-backed`；可作面試 context，不升級 Nick 主 claim |
| SPH / SLC runtime callback registration | 待確認 |
| wallet / settlement / jackpot pool | 未完整深掃，不可 claim |

## 可放履歷

不建議單獨新增一條 jackpot 履歷 bullet；併入 `*-math` grouped bullet 即可：

> 參與 AntPlay 多個 slot math module 維護與驗證，處理 RTP / reel strip、debug bet、fixedMultiBet、buy free / purchasable free spin、jackpot / symbol、currency 與模擬驗證調整。

## 可面試講

- Jackpot symbol / wild trigger flow。
- `agentId|jackpotType|currency` balance payload。
- max bet / current bet scaling。
- `fixedMultiBet` 對 jackpot amount 的影響。
- `jackpotRewardList` / `betTotalWin` result contract consistency。
- runtime 從 `jackpotRewardList` 累加 amount、force respin、扣池與寫 record 的 code-backed context。
- debug helper 如何強制各 jackpot type。

## 不可誇大

- 不寫主導完整 jackpot platform。
- 不寫 jackpot pool owner。
- 不寫 wallet / settlement / provider integration owner。
- 不寫設計完整 jackpot odds / RTP strategy。
- 不寫完整深掃所有 `*-math` jackpot modules。
- 不寫 SDT runtime caller 是 Nick 本人直接開發，除非後續補到 path-specific commit / 本人確認。

## 後續回填條件

Step 4 已補到 SDT runtime caller / jackpot service code-backed context。Step 5 可回填：

- 是否可把「jackpot result contract consistency」升級成更強面試 case。
- 是否需要更新 project-level contribution consolidation。
- 是否仍只留在 grouped bullet，或可在 05 履歷母稿中補成子案例。
