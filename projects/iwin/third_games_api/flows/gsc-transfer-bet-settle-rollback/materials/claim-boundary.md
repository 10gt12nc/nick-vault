# Claim Boundary：GSC transfer bet / settle / rollback

## 證據分級

| Claim | 分級 | 是否可進正式履歷 | 備註 |
| --- | --- | --- | --- |
| `third_games_api` 有 GSC `/api/seamless/transfer` callback | `專案存在 / code-backed` | 否，除非改成專案描述 | 已由 code 確認 |
| transfer flow 會呼叫 gameserver `PGTRANSFERINOUT` | `專案存在 / code-backed` | 否，除非是技術理解背景 | 已由 code 確認 |
| gameserver 負責 wallet mutation | `專案存在 / code-backed` | 否，除非是面試分析 | 已追到 job 與 wallet method 呼叫 |
| Mongo `third_log_gsc` / `third_transaction_gsc` 可作 audit evidence | `專案存在 / code-backed` | 否，除非是面試分析 | 不等於唯一帳本 |
| `ROLLBACK` 不呼叫 gameserver mutation | `專案存在 / code-backed` | 否 | 可作風險分析，不可說已修 |
| Nick 主導 GSC provider 串接 | `待確認` | 否 | 沒有本人 MR / ticket / commit / production issue |
| Nick 設計 idempotency / rollback 補償 | `待確認` | 否 | 沒有 evidence |
| 系統已完整 exactly-once | `待確認` | 否 | 目前不能這樣說 |

## 現在可以說

- 我整理 / 分析過 GSC seamless wallet transfer flow。
- 我能說明 `third_games_api` adapter、Redis routing、gameserver wallet mutation、Mongo audit 的責任切分。
- 我能指出 provider retry、Mongo after wallet、rollback semantics、transactions 多筆處理的風險。
- 我能把這條 flow 轉成 Senior Backend 面試中的 transaction consistency / idempotency case。

## 現在不可以說

- 我主導 GSC 串接。
- 我修過 GSC rollback。
- 我設計過 gameserver wallet 冪等。
- 我讓第三方遊戲交易達成 exactly-once。
- 我負責 production reconciliation。
- 我改善了交易成功率或降低了金流事故。

## 若 Nick 後續補 evidence，才可升級的說法

需要 evidence：

- 本人 commit / MR / ticket。
- production issue 或 incident record。
- 設計文件或 code review。
- 本人明確確認的參與範圍。

可升級方向：

- 如果確認 Nick 只參與分析 / bug fix：可以寫成「協助排查第三方遊戲 callback 的交易一致性問題」。
- 如果確認 Nick 實作冪等或補償：可以寫成「補強 provider retry 下的 idempotency 與 audit repair」。
- 如果確認 Nick 是 owner：才可寫成「負責 GSC seamless wallet transfer flow 的交易正確性與對帳策略」。

目前不要升級。
