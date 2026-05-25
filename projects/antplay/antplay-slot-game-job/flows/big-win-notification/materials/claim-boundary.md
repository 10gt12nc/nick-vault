# Claim Boundary - big-win-notification

日期: 2026-05-25

## Claim 分層

| 層級 | 判斷 |
| --- | --- |
| 真實開發過 | 可成立。`10gt12nc` 有 `#303` direct commits 新增初版 BigWin consumer 與格式修正。 |
| code-backed | 可成立。current code path 可追到 consumer、cache、translation、producer wrapper 與 config toggle。 |
| 可面試講 | 可成立。Step 4 已整理成正式 derived notification / Kafka push message 面試 case，Step 5 已補下游與去重邊界。 |
| 可直接履歷 | 不建議單獨新增正式 bullet；可作 `antplay-slot-game-job` project-level supporting evidence。 |
| 不可誇大 | 不能說完整 push platform owner、完整 jackpot / bonus owner、guaranteed delivery 或 exactly-once notification。 |

## 可保守說

- 我參與過 AntPlay job 的中大獎通知 flow 初版。
- 這條 flow 消費 settled bet event，判斷 win / bet 倍數，組 push user message。
- 我能說明玩家名稱遮罩、遊戲翻譯、金額格式、producer failure 與重複通知邊界。
- Step 5 補查到 `BetIdPersistence` 不是通知去重；下游 `antplay-push` 也未見 `_id` dedupe，這正好可作面試中的可靠性改善點。
- 後續 current behavior 有其他同事修正，我會區分初版 direct evidence 與 current code-backed 分析。

## 不可說

- 不說主導完整 push notification platform。
- 不說完整 jackpot / bonus / reward owner。
- 不說通知有 exactly-once 或 guaranteed delivery。
- 不說下游 push user consumer 已完整確認。
- 不說 `BetIdPersistence` 已解決 big-win notification 去重。
- 不說 `fullPlayerName` 已被下游安全過濾。
- 不把 Gill / Arnold / Eliot 後續 commits 說成 Nick direct contribution。

## Step 5 補強完成

- 已整理 30 秒 / 90 秒 / 3 分鐘面試說法、STAR、failure scenarios、Senior / Lead 追問、可用反問與常見陷阱。
- 已補查 `_id` / bet id / `BetIdPersistence`：目前不能作通知去重證據。
- 已補查下游 `antplay-push`：本地 code 顯示 `push_user` JSON 會原樣送 websocket topic，未見 `_id` dedupe 或 `fullPlayerName` 過濾。

## Claim Gate 結論

- 可回填 project-level consolidation，作為 `antplay-slot-game-job` big-win notification supporting evidence。
- 不直接更新 `05 / 08`，因為它仍是單條 flow Step 5；正式履歷要吃 project-level consolidation / rolling resume package。
- 2026-05-25 後續進度: Rank 4 `settle-pool-monitor-darkpool-sync Step 4` 已完成；下一步做同 flow Step 5，不跨 project。
