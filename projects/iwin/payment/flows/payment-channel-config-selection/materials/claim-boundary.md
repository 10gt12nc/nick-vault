# claim-boundary

## Step 5 claim 判斷

本 flow 目前是：

- `專案存在 / code-backed`
- `分析素材 / learning-only`
- Nick 個人貢獻：未確認到可放履歷的直接 evidence

## 可以使用

- 面試時作為 runtime config consistency 案例。
- 說明 payment list/detail/withdrawConfig 如何消費 app_bi 同步到 Redis 的設定。
- 說明 DB / Redis / runtime filter 三層一致性的風險。
- 說明玩家層級、channel、device、merchant status、merchant account 對應的交叉條件。
- 說明 partial sync、cold-cache fallback、fail closed、config version 與 explainability 的 owner decision。

## 不可使用

- 不放入正式履歷。
- 不寫 Nick 主導 payment config selection。
- 不寫 Nick 設計設定中心。
- 不寫 Nick 解決支付列表 production incident。
- 不寫已確認完整 config version / atomic rollout。

## Evidence 判斷

已確認：

- payment runtime code-backed 主線存在。
- app_bi Redis sync code-backed 上游存在。
- path history 中有 Derek / gill / arnold 的 config / Redis 相關修改。

未確認：

- `10gt12nc` 直接修改 payment list/detail/withdrawConfig。
- `10gt12nc` 直接修改 app_bi payment config sync。
- Nick 本人 ticket / MR / production issue。

## Step 5 最終判定

不更新：

- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

原因：

- Step 5 補查 `payment` / `app_bi` 相關 path history 後，沒有找到 Nick 直接修改 payment list/detail/withdrawConfig 或 app_bi Redis sync 的 evidence。
- `10gt12nc` 在相關 path 的 `03c28e3` / `6539d7a` 是 order insert id copy 修正，已歸到 provider request / order insert consistency，不應轉嫁成本 flow 的履歷 claim。
- 本 flow 可以展示 Senior / Owner 思考，但仍屬分析素材，不是已確認個人成果。

## 下一步

```text
iwin game_api partner-deposit-withdraw-bill Step 3
```

## 履歷 claim 分層（2026-05-18 KB 對齊）

- 可放履歷：目前不單獨升級成本 flow 的真實開發成果；但不得否定 Nick 在 `payment` 的整體實際開發經驗。project-level payment contribution consolidation 已完成，payment 履歷只保守寫 provider 對接 / 維護與 order consistency。
- 可面試講：code-backed / 分析過。可用本 flow 說明 money correctness、狀態轉移、冪等、retry、補償、人工修復或 runtime config consistency。
- 不可誇大：不得把本 flow 寫成 Nick 主導完整 payment / wallet owner、設計整套金流架構、解決全部對帳或 production incident，除非後續補到本人 MR / ticket / production issue / 本人確認與重要 diff。
