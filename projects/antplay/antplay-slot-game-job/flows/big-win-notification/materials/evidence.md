# Evidence - big-win-notification

日期: 2026-05-25

## 1. Source Repo 狀態

| 項目 | 狀態 |
| --- | --- |
| source path | `/Users/nick/Git/antplay/antplay-slot-game-job` |
| local branch | `master` |
| local HEAD | `d847357` |
| local `origin/master` | `d847357` |
| ahead / behind | `0 / 0` |
| source working tree | clean |
| remote refs | 先前同 repo fetch 已因內網不可達失敗；依 KB 不反覆重試 |
| 最新性判斷 | 未確認最新遠端；本 Step 依本地 refs / 本地 working tree 保守分析 |

注意: source remote 是內網環境，vault 不記錄內網 URL / IP / sensitive remote detail。`application.yml` 只作 toggle / topic key evidence，不記錄環境敏感值。

## 2. 本輪掃描範圍

Vault:

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `projects/antplay/antplay-slot-game-job/README.md`
- `projects/antplay/antplay-slot-game-job/step1-candidate-flows.md`
- `projects/antplay/antplay-slot-game-job/step2-flow-comparison.md`
- `projects/antplay/antplay-slot-game-job/contribution-claim-consolidation.md`

Source code:

- `BigWinConsumerService`
- `KafkaProducerService`
- `GameDataCache`
- `TransGames`
- `TransGameRepository`
- `AgentsRepository`
- `SettleBetsVo`
- `BetRecord`
- `application.yml` toggle / topic key only

Git history:

- path-specific log for `BigWinConsumerService` / producer / cache / config。
- selected important commits for `#303` initial feature、format fix、player mask、currency / translation current behavior、bet id collection context。
- current blame summary for `BigWinConsumerService` and `KafkaProducerService`。

Related repo check:

- `/Users/nick/Git/antplay/antplay-slot-game-api` status: local `develop` is far behind `origin/develop` in local refs; no fetch performed in this Step because company repos are read-only and previous internal fetch failures exist. Search did not produce reliable `settled_bets` producer evidence; upstream producer remains待確認，不作本 Step 主證據。

未掃 / 待確認:

- 上游 `settled_bets` producer 最新 contract。
- 下游 `push_user` topic consumer / frontend rendering。
- `BetIdPersistence#collectBetIdsByAgent` 下游用途。
- Kafka listener ack / error handler runtime behavior。
- production activity / notification metrics、DLQ、alert、補推工具。

## 3. Code Evidence

| Area | File | Evidence |
| --- | --- | --- |
| Kafka consumer | `BigWinConsumerService` | `@KafkaListener(topics = {"settled_bets"}, groupId = "big_win", concurrency = ...)` |
| Feature toggle | `BigWinConsumerService` / `application.yml` | `spring.kafka.consumer.task.big-win` 控制啟用；current config 為 true |
| Big-win rule | `BigWinConsumerService` | `totalWin >= (bet + voucherBet) * 10` |
| Translation | `GameDataCache` / `TransGames` | cache all trans games；current code依 currency 選 `cn` / `en` |
| Missing translation | `BigWinConsumerService` | 找不到 `enName` 時 log error and continue |
| Privacy | `BigWinConsumerService#maskPlayerName` | 3字以下 `***`，4字首字 + `***`，5字以上前2 + `***` + 後2 |
| Payload | `BigWinConsumerService` | `_id`、`type=3`、`channel=agent_{agentId}_all`、`data` |
| Producer | `KafkaProducerService#sendMessage` | `KafkaTemplate#send` async future，success/failure callback log |
| Follow-up id collection | `BetIdPersistence#collectBetIdsByAgent` | BigWin main try/catch 後呼叫；下游未深掃 |

## 4. Commit Evidence

| Commit | Author | Date | Evidence |
| --- | --- | --- | --- |
| `1135490` | `10gt12nc` | 2025-02-21 | 新增 `BigWinConsumerService`、game / message cache、topic config，初版中大獎通知 |
| `c2cd523` | `10gt12nc` | 2025-02-25 | prize 改成 `String.format("%.2f", betTotalWin / 100.0)`，調整 log 文案 |
| `9adfb88` | Gill | 2025-09-04 | 後續 push message 增強，包含玩家名稱遮罩與 logging |
| `c20476c` | Gill | 2025-09-15 | push user message 加 currency，message template 移出 current payload |
| `51bf510` | Gill | 2025-09-15 | 環境檢查相關 current context |
| `94e0c9d` | Arnold | 2025-10-07 | 開啟 big-win current config context |
| `9f0f028` | Arnold | 2025-10-28 | 中大獎通知增加 `enName` |
| `510f0c3` / `cb1ba65` | Eliot | 2025-12-29 | id persistence / collect ids context |
| `90cecee` | Arnold | 2026-01-12 | 新增 `fullPlayerName` current context |
| `f3db35d` | Arnold | 2026-01-28 | 修正使用 detail issue，改以 bet / voucherBet / win 欄位與 by currency 送遊戲名稱 |
| `1215cd1` | Arnold | 2026-01-28 | 修正中大獎 cn 翻譯問題，改用 `totalWin` |

## 5. Blame Summary

| File | Blame 摘要 | Claim 判斷 |
| --- | --- | --- |
| `BigWinConsumerService.java` | `10gt12nc` 約 108 lines，Arnold 約 49，Gill 約 44，Eliot 約 13 | Nick direct evidence 成立；current behavior 需標示多人後續修改 |
| `KafkaProducerService.java` | `10gt12nc` 約 78 lines，Derek / Eliot / Arnold 少量 | producer wrapper 有 Nick direct evidence，但完整 Kafka platform claim 不成立 |

## 6. 已確認

- 這條 flow 是 Kafka `settled_bets` event -> big-win rule -> push user topic。
- `#303` 有 `10gt12nc` direct commits，建立初版 BigWin consumer 與格式修正。
- current code 用 `bet + voucherBet` 與 `totalWin` 判斷 10 倍門檻。
- current code 用 currency 決定 game translation language。
- current code 有玩家遮罩，但 payload 同時包含 `fullPlayerName`。
- producer send 是 async future callback log，呼叫端不等待。

## 7. 合理推論

- 這條 flow 屬於 derived notification / UX flow，不是 wallet、bet settlement 或 reward source of truth。
- 若 Kafka event 重送，current repo evidence 下可能重複推通知，因 `_id` 每次 UUID，不是 deterministic idempotency key。
- 缺翻譯直接跳過會降低錯誤顯示，但可能漏通知。
- Producer failure 只 log，偏 best-effort notification。

## 8. 待確認

- 下游 `push_user` consumer 是否有 retry / DLQ / idempotency。
- `_id` 是否在下游作去重。
- `fullPlayerName` 是否必要，是否有 privacy policy / frontend masking guarantee。
- `BetIdPersistence#collectBetIdsByAgent` 是否支援補償、去重或其他查詢。
- 上游 `settled_bets` producer 最新欄位 contract。
- 產品規格是否明確定義 `(bet + voucherBet) * 10`。
