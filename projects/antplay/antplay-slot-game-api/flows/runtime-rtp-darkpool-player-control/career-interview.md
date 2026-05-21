# runtime-rtp-darkpool-player-control Career / Interview

## 0. 狀態

| 項目 | 內容 |
| --- | --- |
| Flow | `runtime-rtp-darkpool-player-control` |
| 狀態 | Step 5 完成 |
| 證據層級 | 真實開發過 + code-backed；可回填 project-level consolidation |
| 履歷更新 | 本輪不直接更新 `05 / 08`，由下一輪 project-level consolidation 統一判斷 |

## 1. 面試定位

這條 flow 適合當成「遊戲 API runtime decision」案例，而不是單純下注結算案例。前面 `slot-bet-settle-rollback` 已經能講 money correctness；這條則補上「結果產生前後，後端如何把 RTP、dark pool、player control、jackpot 與 math result 串在一起」。

一句話:

> 每局 slot bet 不是直接吃 math result；game-api 會先根據 agent / game / activity / jackpot RTP、dark pool 累積值與 player control 設定，決定是否接受本局結果或 respin，最後才落 bet record、wallet settle、player control 與 jackpot 記錄。

## 2. 可說

- `GameFacade#bet` 是 runtime orchestration 中心，會先驗證玩家、game、voucher / buy free spin，再進入 RTP / dark pool / player control 決策。
- `RtpCache` 從 `agent_game_rtp` 載入 normal / free / activity / activity free / jackpot RTP，bet 時讀 cache。
- `SlotMathFacade` 是 game-api 對 math layer 的 contract；game-api 只呼叫 `normalBet` / `freeSpinBet`，不在這裡實作每個遊戲的數學模型。
- Dark pool 用 Redis total bet / total win 與本局 win / bet 算 `maxWin`，結果超出時 respin。
- Jackpot 有自己的 RTP、balance 與 force respin 判斷。
- Player control 會讀特定 agent / currency / account 的控制設定，依 kill / gift 類型判斷是否 respin。
- Step 5 已確認 Nick / `10gt12nc` 在 `GameFacade` respin loop、dark pool `maxWin`、timeout refund branch、deadlock / afterBet failure window 與 player-control setData 呼叫周邊有 direct commits；可作 project-level runtime decision claim evidence。

## 3. 不可誇大

- 不說主導完整 RTP 策略。
- 不說主導完整遊戲數學或機率模型。
- 不說完整 dark pool / player control policy owner。
- 不說完整 jackpot 系統 owner。
- 不說完整風控平台 owner。
- 不說量化改善或事故修復，除非 Step 5 補到 production issue / metric。

## 4. 履歷草稿

Step 5 後可回填 project-level consolidation，不直接改 `05 / 08`：

> 參與 AntPlay slot game API runtime 維護，理解並整理 RTP cache、dark pool、player control、jackpot 與 math result contract 在 `/game/bet` 主流程中的一致性、failure window 與 owner boundary。

若 Step 5 補到更強 direct evidence，可再提升成：

> 參與 AntPlay slot game API runtime decision flow 維護，處理 RTP / dark pool / player control / jackpot 與 math result contract 的整合邊界，協助降低遊戲結果、池控與下注記錄間的不一致風險。

第二句可作 project-level consolidation 候選句，但正式投遞版仍要等 `antplay-slot-game-api contribution claim consolidation` refresh 後統一進 `05 / 08`。

## 5. 面試講法

### 30 秒

> 我在 AntPlay game-api 這條 flow 主要看的是 `/game/bet` 的 runtime decision。下注不是直接呼叫 math 算完就結束，而是先讀 RTP cache、dark pool total bet / win、player control config 和 jackpot RTP，再決定這局結果能不能接受。如果超過 dark pool 或 player control 條件，就會 respin，通過後才落 bet record、settle 和相關 audit。

### 90 秒

> 這條 flow 我會分三層講。第一層是 game-api orchestration，`GameFacade#bet` 負責驗證玩家、扣款、建立 bet record draft、呼叫 math。第二層是 runtime decision，RTP cache 決定 normal / free / activity / jackpot RTP，dark pool 用 Redis 累積投注與派彩算 maxWin，player control 針對特定玩家判斷 kill / gift 是否要 respin。第三層是落地，結果通過後才寫 bet record、更新 dark pool counters、player control record 和 jackpot record。這裡的 owner risk 是：cache staleness、Redis counter 單位、respin timeout、side effect 落地失敗，都要明確區分哪些是主交易、哪些是 audit 或營運控制。

### 3 分鐘

> 我會把這個案例定位成 game API 的 runtime decision，而不是把它講成我主導 RTP 或遊戲數學。入口是 `/game/bet`，主 orchestration 在 `GameFacade#bet`。玩家送 bet 進來後，系統先驗證 session、game、voucher / buy free spin、玩家封鎖與錢包，然後才進入結果決策。
>
> 第一個重點是 RTP source。Game API 會從 `RtpCache` 讀 normal、free、activity、activity free 與 jackpot RTP。這裡用 cache 是為了避免每局 bet 查 DB，但 owner 風險是 cache stale、miss 後 default RTP 是否合理，以及 reload failure 有沒有監控。
>
> 第二個重點是 dark pool 和 player control。Math layer 產生結果後，game-api 不一定立刻接受，而是用 Redis 裡的 total bet / total win 算 `maxWin`，再看這局 win 是否超過池控；player control 也會針對特定玩家的 kill / gift 設定判斷是否要求 respin。Jackpot 也有自己的 RTP、balance 與 force respin 判斷。
>
> 第三個重點是 side effect 邊界。只有結果通過 runtime decision 後，才落 bet record、settle、更新 dark pool counters、player control record 與 jackpot record。這裡我會主動講風險：respin loop timeout 時玩家錢與 bet record 怎麼處理、PlayerControl MQ 發送失敗是否該 rollback、Jackpot `setData` 失敗只 log 是否需要補償、以及 `additional_info` 截斷後排查是否足夠。
>
> 所以這條 flow 的面試價值在於：我能把 game-api、math module、營運策略與 money correctness 的邊界拆清楚。game-api 負責 runtime orchestration 與 result acceptance；math-core / game math module 負責產生 result contract；RTP / dark pool / player control 是營運策略與後端 runtime guardrail 的交界，不能把它誇大成完整遊戲數學 owner。

## 6. STAR 案例

Situation:

AntPlay slot game-api 的 `/game/bet` 不只是下注扣款與回傳開獎結果，還包含 RTP、dark pool、player control、jackpot 與 math result 的 runtime decision。這一段若邊界不清，面試時很容易把 game-api、math module、營運策略與 money correctness 混在一起。

Task:

把這條 flow 拆成可面試的 Senior Backend case：說清楚 code path、資料來源、failure window、owner decision，以及哪些可以講、哪些不能誇大。

Action:

- 從 `GameFacade#bet` 追到 `RtpCache`、`DarkpoolService`、`PlayerControlService`、`JackpotService` 與 `SlotMathFacade`。
- 把 runtime decision 拆成 RTP selection、math result generation、dark pool maxWin、player control respin、jackpot force respin、result acceptance、side effect 落地。
- 補出 cache stale、Redis counter loss、respin timeout、MQ failure、jackpot record failure、`additional_info` 截斷等追問。
- 將履歷邊界標成保守草稿，等 Step 5 再做正式 claim gate。

Result:

這條 flow 目前可作正式面試素材，也可回填 project-level runtime decision claim。它支撐 Nick 講 game API runtime decision、math contract boundary、failure window 與 owner trade-off；但仍不直接寫入 `05 / 08`，也不宣稱完整 RTP 策略或遊戲數學 owner。

## 7. Senior 追問

Q: RTP cache miss 時用 default RTP，是保護還是風險？

A: 兩者都是。default 可以避免 bet 直接中斷，但它也可能掩蓋 config 缺漏。Senior 角度要補三件事：第一，cache reload failure 要有 alert；第二，default RTP 是否符合營運規則要能被審核；第三，fallback 發生時要留下 audit，否則事後很難解釋某段時間為什麼使用 default。

Q: Dark pool 用 Redis total bet / win，Redis 遺失怎麼辦？

A: Step 4 只能保守講設計風險。理想上 Redis counter 不應是唯一 source of truth，應該能從 bet record / daily summary 重建 total bet / win，並有 repair job 或人工修復流程。若只有 Redis，重啟、過期、誤刪都可能讓 `maxWin` 判斷失真。

Q: Respin loop timeout 時最怕什麼？

A: 最怕下注前錢已扣，但結果沒有正常落地。現行有 2 分鐘 timeout，transfer wallet branch 看到 refund 呼叫，但 deadlock compensation 部分有呼叫被註解，所以面試只能講 failure window 與補強方向：timeout 要明確標記 bet 狀態、refund 要 idempotent、要能從 betId / requestId 查到最後卡在哪一步。

Q: Player control MQ 發送失敗要不要 rollback bet？

A: 不一定。若 player control record 是 audit / async side effect，主 bet 不 rollback 可以保 availability；但要補 retry、DLQ、lag alert 或 outbox，避免控制紀錄遺失後無法排查。不能把 MQ 發送成功當成主交易正確性的必要條件。

Q: Jackpot `setData` 失敗只 log，會不會有問題？

A: 會有風險，因為 jackpot result 已經回給玩家，但 jackpot record / balance side effect 可能沒落地。這種情況要看 jackpot 是否被視為主交易的一部分。若是，就不能只 log；至少要有 repair table、retry job 或 reconciliation。

## 8. Lead / Architect 追問

Q: 你會怎麼重構 `GameFacade#bet`？

A: 我會先抽 runtime decision evaluator，而不是直接拆很多微服務。Evaluator 的 input 包含 agent、game、currency、bet amount、math result、RTP、dark pool counters、player control config、jackpot state；output 是 accept / respin / reject、reason code 與 audit snapshot。這樣可以把 policy 測試、觀測與風險判斷抽出來，同時保留 bet / wallet context。

Q: game-api 和 math module 的責任怎麼切？

A: Math module 負責在給定 RTP / input 下產生 result contract，例如 totalWin、betTotalWin、jackpotRewardList。Game-api 負責選 RTP、決定是否接受 result、處理 bet record / wallet / jackpot / player control side effects。履歷和面試不能把 math module 的機率模型誇大成 game-api 的成果。

Q: 如果要補可靠性，你會先補哪裡？

A: 我會先補 observability 和 replayability。具體是把每次 runtime decision 的 reason code、RTP source、dark pool before/after、player control config id、jackpot force respin reason 寫進可查 audit；再補 side effect failure 的 retry / repair。沒有可查 evidence 前，直接改 policy 反而容易更難排查。

Q: RTP / dark pool / player control policy 要不要拆到獨立服務？

A: 要看流量、組織邊界與變更頻率。若只是單 repo 內部策略，先抽 evaluator 和測試就夠；若營運頻繁調參、跨多個 game-api 使用、需要獨立審核與回放，才適合變成 policy service。不能為了漂亮架構先把一局 bet 的 money context 拆散。

## 9. 可說 / 不可說

可說:

- 可把它講成 game API runtime decision case。
- 可講 RTP cache、dark pool、player control、jackpot、math result contract 的邊界。
- 可講 failure window 與 owner trade-off。
- 可說 Nick / `10gt12nc` 有 GameFacade runtime decision 周邊 direct commits，尤其是 respin loop、timeout refund branch、dark pool `maxWin`、deadlock / afterBet failure window 與 player-control setData 呼叫周邊。

不可說:

- 不說主導完整 RTP 策略。
- 不說主導完整遊戲數學模型。
- 不說完整 dark pool / player control / jackpot platform owner。
- 不說完整風控 owner。
- 不說已完成完整 reconciliation 或補償。

## 10. 下一步

Step 5 已完成。下一步回到 project-level contribution claim consolidation，將本批代表 flows 全部 Step 5 的 evidence 統一收斂，避免單條 flow 直接改履歷造成誇大。

```text
antplay antplay-slot-game-api contribution claim consolidation
```
