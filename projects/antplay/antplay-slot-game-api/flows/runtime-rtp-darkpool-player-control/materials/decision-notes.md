# runtime-rtp-darkpool-player-control Decision Notes

## 1. RTP cache vs DB 查詢

現行 `RtpCache` 每 30 分鐘從 `agent_game_rtp` 載入 normal / free / activity / jackpot RTP。每局 bet 直接讀 memory cache。

好處:

- 避免高頻 bet 每局查 DB。
- runtime latency 較穩定。
- 可用 `all` fallback 與 default RTP 避免缺資料直接中斷。

代價:

- cache stale 可能導致一段時間使用舊 RTP。
- default RTP 會讓系統可用性提高，但也可能掩蓋 config 缺漏。
- Step 3 未看到 cache stale alert / reload failure monitor。

Owner 問題:

- 如果 RTP 是營運可調參數，後端要能說清楚 reload latency、fallback policy、誰負責監控缺值。

## 2. Runtime decision 放在 GameFacade

`GameFacade#bet` 同時處理 player validation、wallet pre-deduct、math result、dark pool、player control、jackpot、bet record 與 settle。

好處:

- 一局 bet 的決策集中，容易掌握當局上下文。
- respin 前後能共享 `betAmount`、`result`、`agentTotalBet`、`playerControl` 等資料。

代價:

- `GameFacade` 複雜度高。
- dark pool / jackpot / player control 的 policy 很容易互相影響。
- failure window 不容易一眼看出，尤其是已扣款但 respin loop timeout、result 產生後 side effect 落地失敗。

Owner 問題:

- 若要重構，應先抽出 runtime decision evaluator，但不能先拆到看不見 money / bet record context。

## 3. Respin loop 的風險

Respin loop 的意義是「math result 可以被 runtime policy 拒絕」。這讓後端能在池控 / 玩家控制條件下重抽結果。

風險:

- loop 過久會造成請求 timeout。
- 下注前可能已扣款，timeout 後 refund / fail state 必須清楚。
- 若 `ranges` 或 control config 缺資料，可能造成 NPE 或過度 respin。

現行 evidence:

- 有 2 分鐘 timeout。
- timeout 對 transfer wallet 有直接 refund 呼叫。
- deadlock compensation 部分在現行 code 有註解掉的呼叫，因此不能誇大成完整補償已落地。

## 4. Player control 與 jackpot / dark pool 互斥

現行 `JackpotService#jackpotDpControl` 是 `!isPlayerControl && !useVoucher`，代表 player control 或 voucher 會影響 jackpot dark pool control 是否生效。

這是 owner decision，不只是 if else：

- 避免同一局被 player control、voucher、jackpot dark pool 多套 policy 同時拉扯。
- 但也代表面試要能說清楚優先序：哪個 policy 優先，哪個 policy 只記錄不控制。

Step 3 只能確認 code 現象；實際營運規則要待確認。

## 5. Math contract 邊界

Game API 透過 `SlotMathFacade` 呼叫 math module：

- `normalBet(agentId, currency, lineBet, rtp)`
- `normalBet(..., fixedMultiBet)`
- `freeSpinBet(agentId, currency, lineBet, type)`
- `freeSpinBet(..., fixedMultiBet)`

Game API 不應把每個遊戲的 reel strip / RTP simulation / jackpot hit rule 都包成自己的成果。可以說：

- game-api 負責 runtime orchestration 與 result acceptance。
- math-core / `*-math` 負責結果產生與 game-specific contract。

這個邊界對履歷很重要，不能把兩邊混成「主導完整遊戲數學」。

## 6. Step 4 可追問題

- 如果 RTP cache miss，default RTP 是保護還是風險？
- Dark pool total bet / total win 用 Redis，Redis 遺失或延遲怎麼補？
- Respin loop 2 分鐘 timeout 後，玩家錢、bet record、jackpot record 各處於什麼狀態？
- Player control MQ 發送失敗要不要影響 bet？
- Jackpot `setData` catch error 後只 log，是否需要補償？
- `additional_info` 截斷後，排查 runtime decision 是否足夠？

## 7. 下一步

```text
antplay antplay-slot-game-api runtime-rtp-darkpool-player-control Step 4
```
