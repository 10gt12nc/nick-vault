# big-win-notification Career / Interview

日期: 2026-05-25

## Evidence Level

- 證據層級: 真實開發過 + code-backed。
- Nick evidence: `1135490` / `c2cd523` 等 `#303` commits 由 `10gt12nc` 新增中大獎通知與小數格式修正。
- Current behavior evidence: current `BigWinConsumerService` 仍保留大量 `10gt12nc` lines，但後續 Gill / Arnold / Eliot 修改玩家遮罩、currency / translation、totalWin 判斷與 bet id collection。
- 完成狀態: Step 4 面試 case 已完成。
- 本文件是 flow-level 素材，不是 project-level final consolidation。正式履歷仍以 `contribution-claim-consolidation.md` 與 rolling resume package 為準。

## 履歷保守 Bullet

不建議單獨拉成正式主 bullet；可併入 `antplay-slot-game-job` project-level 經驗。

保守寫法:

- 參與 AntPlay slot job 的中大獎通知 flow，處理 settled bet event 判斷、玩家名稱遮罩、遊戲名稱翻譯、金額格式與 push user topic message。

更短版本:

- 參與 Kafka derived notification flow，將 settled bet event 轉成 big-win push message。

## 面試定位

這條 flow 面試時不要講成「我做完整推播平台」。比較安全的定位是:

```text
settlement event -> big-win rule -> privacy / translation / payload formatting -> push topic
```

主軸是 derived notification correctness，不是交易正確性。

## 面試 30 秒版本

我在 AntPlay job repo 有參與過中大獎通知相關 flow。它消費 `settled_bets`，判斷玩家 `totalWin` 是否達到投注額與 voucher bet 的 10 倍，然後組出 push user message，包含玩家遮罩名稱、遊戲代碼、翻譯名稱、幣別與金額。這條 case 我會保守講成 Kafka derived notification，不是下注或錢包 source of truth；面試重點會放在重複通知、producer failure、缺翻譯 fallback 和玩家隱私。

## 面試 90 秒版本

我可以講一條 AntPlay job 裡的中大獎通知 flow。這條 flow 不是下注結算主流程，而是結算事件後的衍生通知。Job consumer 會消費 `settled_bets`，把 event 反序列化成 `SettleBetsVo`，逐筆看 `BetRecord` 的 bet、voucher bet 和 total win。current code 的門檻是 `totalWin >= (bet + voucherBet) * 10`，符合條件就組出 push user message 送到 `push_user` topic。

這條我會保守說是我參與初版，後續 current behavior 有其他同事接著修。我的 direct evidence 是 `#303` 建立 big-win consumer 和金額小數格式；後續像玩家名稱遮罩、currency-based translation、用 `totalWin` 取代其他欄位，都是 current code-backed 分析素材。

面試時我會把重點放在 notification reliability，而不是交易正確性。像 Kafka event 重送可能重複通知，producer send failure 目前只 log，缺遊戲翻譯會跳過通知，payload 同時有 masked name 和 full player name 也有隱私邊界。這些是 Senior Backend 在非同步通知 flow 裡要能講清楚的取捨。

## 面試 3 分鐘版本

我有參與過 AntPlay slot job 裡的中大獎通知 flow。這條 flow 的定位是 settlement event 後的 derived notification，不是下注結算、錢包或 jackpot source of truth。上游完成結算後會送 `settled_bets` Kafka event，job consumer 端的 `BigWinConsumerService` 會讀 event 裡的 `BetRecord`，判斷玩家這次 `totalWin` 是否達到投注額加 voucher bet 的 10 倍。

如果達標，consumer 會依 `agentId` 找對應 agent，再根據幣別決定遊戲名稱翻譯語系。current code 是 CNY 走 `cn`，其他走 `en`，再用 game code 到 `TransGames` 找顯示名稱。找不到翻譯時不會硬送空名稱，而是 log error 後跳過這筆通知。這個設計避免錯誤顯示，但代價是可能漏通知，所以 owner 視角要補 alert 或 fallback，例如至少用 game code 顯示。

通知 payload 會包含 `_id`、`type=3`、`channel=agent_{agentId}_all`，以及玩家遮罩名稱、game code、翻譯名稱、prize、currency。這裡有一個隱私邊界：current code 有 `maskPlayerName`，但 payload 也帶 `fullPlayerName`。如果下游不需要完整帳號，應該移除或拆成內部 audit channel，避免把個資擴散到 notification topic。

可靠性上，這條 flow 目前比較像 best-effort notification。`KafkaProducerService` 用 async send，callback 只記成功或失敗 log，呼叫端不等待，也沒有 durable outbox。若 push 失敗，交易本身不會壞，但營運或玩家會漏看通知。若 event 重送，因為每次都產新的 UUID `_id`，也可能重複通知。比較完整的做法是用 `betRecord.id + notificationType` 當 deterministic key，讓下游或 producer-side outbox 可以去重，並對 producer failure 加 metric / alert / replay。

我會保守說，這條 flow 我有 `#303` 的 direct evidence，參與初版 consumer 和金額格式；但 current code 後續有 Gill / Arnold / Eliot 的修改，所以我不會說自己主導完整 push platform。我會把它當成 Kafka derived notification 的面試 case，講非同步通知的可靠性、隱私、翻譯 fallback 和重複通知邊界。

## STAR

| 面向 | 內容 |
| --- | --- |
| Situation | AntPlay slot 需要在玩家結算後達到中大獎門檻時，送出通知給對應代理的玩家。 |
| Task | 參與中大獎通知初版，將 settled bet event 轉成可推送的 notification message，並處理金額顯示格式。 |
| Action | 建立 / 維護 `BigWinConsumerService`，消費 `settled_bets`，依 win / bet 倍數判斷 big-win，組 `push_user` message；後續以 current code 分析玩家遮罩、翻譯、producer failure 與重送風險。 |
| Result | 形成可面試的 Kafka derived notification case，可說明 event-driven notification 不等於交易 source of truth，並能回答 reliability / privacy / fallback 邊界。 |

## Failure Scenarios

| 情境 | 面試回答重點 |
| --- | --- |
| Kafka event 重送 | current `_id` 每次 UUID，可能重複通知；應補 deterministic key 或下游去重。 |
| producer send failure | `KafkaProducerService` async callback 只 log，屬 best-effort；重要通知要補 outbox / retry / alert。 |
| 缺遊戲翻譯 | current code skip，避免錯名但會漏通知；可補 game code fallback + alert。 |
| fullPlayerName 出現在 payload | 有隱私最小化風險；public channel 應只放 masked name，full name 留內部 audit。 |
| 金額單位錯 | `totalWin / 100.0` 和 `(bet + voucherBet) * 10` 要確認單位一致；避免錯判大獎或顯示錯。 |
| cache stale | `GameDataCache` 60 秒更新，翻譯或 agent 修改可能短暫不一致；需可觀測與手動 refresh 策略。 |

## Senior 追問短答

### Q1: 這條 flow 為什麼不能當交易 source of truth？

因為它是 settled event 之後的衍生通知。真正的下注與派彩結果要回到 bet record / settlement；push message 只是展示與營運效果，不能反推交易成立。

### Q2: 你會怎麼避免重複通知？

我會用 deterministic idempotency key，例如 `betRecord.id + notificationType`，讓 producer outbox 或 downstream consumer 可以去重。UUID 適合 trace 單筆 message，但不適合作為重送去重 key。

### Q3: 如果 push topic send 失敗，要不要讓 consumer 失敗重跑？

要看通知重要性。如果只是低風險 UX 通知，可以 best-effort 但要有 metric / alert；如果業務要求必達，就不應只靠 Kafka callback log，應該落 outbox，再由 retry job 補推。

### Q4: 缺翻譯時跳過合理嗎？

這是保守顯示策略，避免錯誤名稱曝光；但 owner 要加告警，否則營運不知道漏通知。另一種做法是 game code fallback，至少通知不會消失。

### Q5: 為什麼 fullPlayerName 是風險？

因為通知 topic 可能被多個下游消費。公開展示只需要 masked name，full account 應該只在內部 audit 或權限更嚴格的通道使用。

## Lead / Architect 追問

| 追問 | 回答方向 |
| --- | --- |
| 如果只能改一件事，先改什麼？ | 先補 deterministic notification key，解決重送重複通知與 retry 安全性。 |
| 要怎麼補 observability？ | producer failure metric、translation missing metric、通知產生數 / send success / send failure dashboard。 |
| 要不要 outbox？ | 若通知是營運必達或牽涉活動，建議 outbox；若只是展示效果，可以先 metric + replay tool。 |
| 要怎麼做 privacy review？ | 清楚分 public payload 與 internal audit payload，public topic 不放 full player name。 |
| 怎麼和產品確認門檻？ | 明確定義 `bet`、`voucherBet`、`totalWin` 單位與公式，並用測試案例覆蓋。 |

## 面試收尾句

這個 case 我會定位成 Kafka derived notification。它不難在 code 上做出來，但 Senior 要能講清楚它不是交易真相，並且要處理重送、漏送、翻譯 fallback、隱私與觀測性。

## 可說

- `#303` direct commits 建立中大獎通知 consumer 與小數格式修正。
- current code 消費 `settled_bets`，輸出 `push_user` topic。
- 大獎條件是 current code 的 `(bet + voucherBet) * 10` 與 `totalWin`。
- 後續 current behavior 有其他人修改，這要如實說。
- 可以分析 notification flow 的 best-effort、idempotency、privacy、translation fallback。

## 不可誇大

- 不說完整 push platform owner。
- 不說完整 jackpot / bonus / reward owner。
- 不說已建立 exactly-once notification。
- 不說下游 push user delivery 已完整確認。
- 不把 Gill / Arnold / Eliot 後續改動說成 Nick direct contribution。

## Step 4 結論

Step 4 已完成正式面試 case。下一步 Step 5 應補 claim gate: `_id` / bet id 是否可作下游去重、`BetIdPersistence` 用途、下游 push consumer / frontend 隱私邊界，以及是否回填 project-level consolidation；仍不直接更新 `05 / 08`。
