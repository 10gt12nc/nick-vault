# runtime-rtp-darkpool-player-control Interview Notes

## 0. 狀態

Step 4 完成。本檔是正式面試素材，不是 Step 5 claim gate。

## 1. 30 秒版本

> 這條 flow 是 AntPlay game-api 的 runtime decision。每次 `/game/bet` 不只是呼叫 math layer 算結果，還會先讀 RTP cache、dark pool total bet / win、player control config 和 jackpot RTP / balance。結果出來後，game-api 會判斷是否超過 dark pool 或 player control 條件；如果不符合就 respin，通過後才落 bet record、settle、dark pool counter、player control 與 jackpot record。

## 2. 90 秒版本

> 我會把這條 flow 分成三層。第一層是 `GameFacade#bet` 做下注 orchestration，包含驗證玩家、voucher / buy free spin、扣款與建立 bet record draft。第二層是 runtime decision：`RtpCache` 提供 normal / free / activity / jackpot RTP，`DarkpoolService` 提供 agent / game / currency 的 total bet / win，`PlayerControlService` 依特定玩家設定判斷 kill / gift 是否要 respin，`JackpotService` 判斷 jackpot 結果是否超過池控。第三層是結果落地：通過後才寫 bet record、settle、更新 dark pool counter、player control record 和 jackpot record。這裡最重要的 owner risk 是 cache miss、Redis counter 單位、respin timeout，以及 side effect 失敗時不能影響主交易或留下不一致。

## 3. 3 分鐘版本

> 我會把這個案例定位成 game API runtime decision，不會把它講成完整 RTP 策略或遊戲數學。入口是 `/game/bet`，主要 orchestration 在 `GameFacade#bet`。玩家送 bet 進來後，系統先處理玩家驗證、game 驗證、voucher / buy free spin、扣款或餘額確認，然後才進入結果決策。
>
> 第一段是 RTP。Game API 會從 `RtpCache` 讀 normal、free、activity、activity free 與 jackpot RTP。這樣可以避免每局 bet 查 DB，但 cache miss、stale 與 default RTP 都是 owner risk。面試時我會主動說：default RTP 保可用性，但要有監控與 audit，否則營運設定缺漏會被掩蓋。
>
> 第二段是 result acceptance。`SlotMathFacade` 呼叫 math layer 產生結果後，game-api 會看 dark pool、player control、jackpot 三種 guardrail。Dark pool 用 Redis total bet / win 算 `maxWin`；player control 依特定玩家設定判斷 kill / gift 是否需要 respin；jackpot 則看 jackpot RTP、reward amount 和 pool balance。只要結果不符合 policy，就可能重新呼叫 math layer。
>
> 第三段是落地與 failure window。結果被接受後，才落 bet record、settle、dark pool counter、player control record、jackpot record。這裡我會講幾個風險：respin loop timeout 時錢和 bet record 的狀態、PlayerControl MQ 發送失敗是否影響主交易、Jackpot `setData` 失敗只 log 是否夠、以及 `additional_info` 被截斷後 RCA 是否足夠。
>
> 這條 case 的重點是邊界感。Game-api 負責 runtime orchestration 和 result acceptance；math module 負責產生 result contract；RTP / dark pool / player control 是營運策略和後端 runtime guardrail 的交界。我可以講這段 code-backed 的分析與維護經驗，但不會說自己主導完整 RTP 策略或遊戲數學模型。

## 4. STAR

Situation:

AntPlay slot game-api 的 `/game/bet` 同時牽涉下注、math result、RTP、dark pool、player control、jackpot 與後續 settle。若只把它講成「呼叫 math 算結果」，會漏掉真正的 runtime owner 風險；若講成「主導 RTP 策略」，又會誇大。

Task:

把這條 flow 整理成能面試講的 Senior Backend case，清楚拆出 code path、資料狀態、failure window、owner trade-off 與 claim boundary。

Action:

- 追 `GameFacade#bet`、`SlotMathFacade`、`RtpCache`、`DarkpoolService`、`PlayerControlService`、`JackpotService`。
- 拆出 RTP cache、math result、dark pool maxWin、player control respin、jackpot force respin、result acceptance、side effect 落地。
- 補 cache stale、Redis counter loss、respin timeout、MQ failure、jackpot record failure、audit payload 截斷等追問。
- 明確標出不直接改 `05 / 08`，等 Step 5 再做 claim gate。

Result:

形成一個可講的 runtime decision 面試案例：能展示我對 game API、math contract、營運策略與 money correctness 邊界的理解，也能守住不誇大成完整 RTP / math owner 的界線。

## 5. Senior 追問

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

Q: `additional_info` 截斷有什麼風險？

A: 它保護 DB 欄位長度，但可能讓排查資料不完整。比較好的做法是把 runtime decision reason code 結構化，例如 RTP source、dark pool before / after、player control config id、jackpot force respin reason，而不是只靠一段可能被截斷的 JSON。

Q: Dark pool、player control、jackpot 同時生效時，優先序怎麼說？

A: Step 4 只能依 code 現象保守說：現行有 `jackpotDpControl = !isPlayerControl && !useVoucher` 這類互斥條件，代表系統避免多套 policy 同時拉扯同一局結果。實際營運優先序要等產品 / policy 文件或 Step 5 補 evidence。

## 6. Lead / Architect 追問

Q: 你會怎麼重構這段？

A: 我不會一開始就把所有東西拆服務。先把 `GameFacade#bet` 內的 runtime decision 抽成明確 evaluator：input 是 agent、game、currency、bet amount、math result、dark pool counters、player control config、jackpot state；output 是 accept / respin / reject 與 audit snapshot。這樣保留 bet context，又能讓 policy 測試與 observability 清楚。

Q: game-api 和 math module 的責任怎麼切？

A: math module 負責依 RTP / input 產生 result contract，例如 totalWin、betTotalWin、jackpotRewardList。game-api 負責選 RTP、呼叫 normal/free/buy-free path、判斷 result 是否通過 runtime policy、落 bet record / wallet / jackpot side effects。不能把 math module 的機率模型成果說成 game-api owner。

Q: 要不要把 runtime policy 拆成獨立服務？

A: 不一定。若目前只有單一 game-api 使用，先抽 evaluator、補測試、補 audit 會比拆服務更有效。若未來多個 runtime 共用同一套 policy、營運調參頻繁、需要獨立審核與回放，再考慮獨立 policy service。不能為了架構漂亮把一局 bet 的 money context 拆散。

Q: 如果要補可靠性，你會先做哪一件？

A: 先補可觀測與可回放。每次 runtime decision 要留下 reason code 和關鍵 snapshot，包含 RTP source、dark pool total bet / win、player control config id、jackpot force respin reason。沒有這些 evidence，任何 policy 或補償改動都很難驗證。

## 7. 可用反問

- 目前 RTP 調整是即時生效、定時 reload，還是需要重啟？
- Dark pool counter 是否能從 bet record 重建？
- Player control record 是主交易資料，還是 audit side effect？
- Jackpot record / balance 失敗時，現場是重試、人工修復，還是以 bet record 為準？
- 面試官在意的是 runtime policy 設計，還是 money correctness failure window？

## 8. 下一步

```text
antplay antplay-slot-game-api runtime-rtp-darkpool-player-control Step 5
```
