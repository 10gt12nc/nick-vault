# Claim Boundary - big-win-notification

日期: 2026-05-25

## Claim 分層

| 層級 | 判斷 |
| --- | --- |
| 真實開發過 | 可成立。`10gt12nc` 有 `#303` direct commits 新增初版 BigWin consumer 與格式修正。 |
| code-backed | 可成立。current code path 可追到 consumer、cache、translation、producer wrapper 與 config toggle。 |
| 可面試講 | 可成立。Step 4 已整理成正式 derived notification / Kafka push message 面試 case。 |
| 可直接履歷 | 不建議單獨新增正式 bullet；可作 `antplay-slot-game-job` project-level supporting evidence。 |
| 不可誇大 | 不能說完整 push platform owner、完整 jackpot / bonus owner、guaranteed delivery 或 exactly-once notification。 |

## 可保守說

- 我參與過 AntPlay job 的中大獎通知 flow 初版。
- 這條 flow 消費 settled bet event，判斷 win / bet 倍數，組 push user message。
- 我能說明玩家名稱遮罩、遊戲翻譯、金額格式、producer failure 與重複通知邊界。
- 後續 current behavior 有其他同事修正，我會區分初版 direct evidence 與 current code-backed 分析。

## 不可說

- 不說主導完整 push notification platform。
- 不說完整 jackpot / bonus / reward owner。
- 不說通知有 exactly-once 或 guaranteed delivery。
- 不說下游 push user consumer 已完整確認。
- 不把 Gill / Arnold / Eliot 後續 commits 說成 Nick direct contribution。

## Step 4 補強完成

- 已整理 30 秒 / 90 秒 / 3 分鐘面試說法、STAR、failure scenarios、Senior / Lead 追問、可用反問與常見陷阱。

## 待 Step 5 補強

- 補查 `_id` / bet id 是否可作下游去重。
- 補查 `BetIdPersistence#collectBetIdsByAgent` 用途。
- 補查下游 push consumer / frontend 隱私邊界。
- 判斷是否回填 project-level consolidation；目前仍只作 flow-level 面試素材。
