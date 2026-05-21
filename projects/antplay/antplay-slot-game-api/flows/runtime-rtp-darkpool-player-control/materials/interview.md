# runtime-rtp-darkpool-player-control Interview Notes

## 1. 30 秒草稿

> 這條 flow 是 AntPlay game-api 的 runtime decision。每次 `/game/bet` 不只是呼叫 math layer 算結果，還會先讀 RTP cache、dark pool total bet / win、player control config 和 jackpot RTP / balance。結果出來後，game-api 會判斷是否超過 dark pool 或 player control 條件；如果不符合就 respin，通過後才落 bet record、settle、dark pool counter、player control 與 jackpot record。

## 2. 90 秒草稿

> 我會把這條 flow 分成三層。第一層是 `GameFacade#bet` 做下注 orchestration，包含驗證玩家、voucher / buy free spin、扣款與建立 bet record draft。第二層是 runtime decision：`RtpCache` 提供 normal / free / activity / jackpot RTP，`DarkpoolService` 提供 agent / game / currency 的 total bet / win，`PlayerControlService` 依特定玩家設定判斷 kill / gift 是否要 respin，`JackpotService` 判斷 jackpot 結果是否超過池控。第三層是結果落地：通過後才寫 bet record、settle、更新 dark pool counter、player control record 和 jackpot record。這裡最重要的 owner risk 是 cache miss、Redis counter 單位、respin timeout，以及 side effect 失敗時不能影響主交易或留下不一致。

## 3. 3 分鐘草稿待 Step 4 補

Step 3 先不完整展開，Step 4 再整理正式 3 分鐘版本。

## 4. Senior 追問草稿

Q: RTP cache miss 怎麼辦？

A: 現行 `RtpCache` 有 default RTP fallback，這保住可用性，但也是風險。Senior 角度不能只說「有 default 就好」，要追 cache reload 失敗是否有 alert、default 是否符合營運規則、fallback 有沒有 audit。

Q: Dark pool 用 Redis total bet / win，Redis 資料錯了怎麼辦？

A: Step 3 只看到 Redis counter 讀寫，沒有看到完整 reconciliation。面試要保守說：這是待補 owner issue，理想上要能從 bet record 或日統計重建 total bet / win，並有監控檢查 Redis 與 source of truth 差異。

Q: Respin loop timeout 時錢怎麼辦？

A: 現行有 2 分鐘 timeout，transfer wallet branch 看到 refund 呼叫；但 deadlock compensation 另一些呼叫在 current develop 被註解。因此可以講 failure window 與設計方向，不能說完整補償已 production 落地。

Q: Player control MQ 發送失敗要 rollback bet 嗎？

A: 現行 producer catch 後回 false，主流程看起來不因 MQ failure rollback。這是保主交易可用性的取捨，但 audit / control record 可能缺失，應該補 retry、DLQ 或落庫補償。

Q: Jackpot `setData` 失敗怎麼辦？

A: Step 3 看到 `setData` catch error 後 log，不中斷 bet。這代表 jackpot side effect 可能和主 bet response 分離，需要後續補 reconciliation / job evidence，不能先宣稱完整一致性。

## 5. Lead / Architect 追問草稿

Q: 你會怎麼重構這段？

A: 我不會一開始就把所有東西拆服務。先把 `GameFacade#bet` 內的 runtime decision 抽成明確 evaluator：input 是 agent、game、currency、bet amount、math result、dark pool counters、player control config、jackpot state；output 是 accept / respin / reject 與 audit snapshot。這樣保留 bet context，又能讓 policy 測試與 observability 清楚。

Q: game-api 和 math module 的責任怎麼切？

A: math module 負責依 RTP / input 產生 result contract，例如 totalWin、betTotalWin、jackpotRewardList。game-api 負責選 RTP、呼叫 normal/free/buy-free path、判斷 result 是否通過 runtime policy、落 bet record / wallet / jackpot side effects。不能把 math module 的機率模型成果說成 game-api owner。

## 6. 下一步

```text
antplay antplay-slot-game-api runtime-rtp-darkpool-player-control Step 4
```
