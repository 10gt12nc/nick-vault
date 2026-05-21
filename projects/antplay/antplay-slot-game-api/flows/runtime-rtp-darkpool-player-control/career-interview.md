# runtime-rtp-darkpool-player-control Career / Interview

## 0. 狀態

| 項目 | 內容 |
| --- | --- |
| Flow | `runtime-rtp-darkpool-player-control` |
| 狀態 | Step 3 完成，Step 4 / Step 5 待做 |
| 證據層級 | 真實開發過 + code-backed；本 flow 尚未 claim gate |
| 履歷更新 | 本輪不直接更新 `05 / 08` |

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
- Step 3 已看到 Nick / `10gt12nc` 在 `GameFacade` dark pool / respin loop、player control 相關修正、deadlock / bet runtime 附近有 direct commits；但這條 flow 的正式履歷 claim 要等 Step 5。

## 3. 不可誇大

- 不說主導完整 RTP 策略。
- 不說主導完整遊戲數學或機率模型。
- 不說完整 dark pool / player control policy owner。
- 不說完整 jackpot 系統 owner。
- 不說完整風控平台 owner。
- 不說量化改善或事故修復，除非 Step 5 補到 production issue / metric。

## 4. 履歷草稿

Step 3 只能先放 project-level 回填草稿，不直接改 `05 / 08`：

> 參與 AntPlay slot game API runtime 維護，理解並整理 RTP cache、dark pool、player control、jackpot 與 math result contract 在 `/game/bet` 主流程中的一致性、failure window 與 owner boundary。

若 Step 5 補到更強 direct evidence，可再提升成：

> 參與 AntPlay slot game API runtime decision flow 維護，處理 RTP / dark pool / player control / jackpot 與 math result contract 的整合邊界，協助降低遊戲結果、池控與下注記錄間的不一致風險。

第二句目前仍需 Step 5 claim gate。

## 5. 面試講法草稿

30 秒:

> 我在 AntPlay game-api 這條 flow 主要看的是 `/game/bet` 的 runtime decision。下注不是直接呼叫 math 算完就結束，而是先讀 RTP cache、dark pool total bet / win、player control config 和 jackpot RTP，再決定這局結果能不能接受。如果超過 dark pool 或 player control 條件，就會 respin，通過後才落 bet record、settle 和相關 audit。

90 秒:

> 這條 flow 我會分三層講。第一層是 game-api orchestration，`GameFacade#bet` 負責驗證玩家、扣款、建立 bet record draft、呼叫 math。第二層是 runtime decision，RTP cache 決定 normal / free / activity / jackpot RTP，dark pool 用 Redis 累積投注與派彩算 maxWin，player control 針對特定玩家判斷 kill / gift 是否要 respin。第三層是落地，結果通過後才寫 bet record、更新 dark pool counters、player control record 和 jackpot record。這裡的 owner risk 是：cache staleness、Redis counter 單位、respin timeout、side effect 落地失敗，都要明確區分哪些是主交易、哪些是 audit 或營運控制。

## 6. Step 4 要補

- 30 秒 / 90 秒 / 3 分鐘正式版。
- STAR 案例。
- Senior 追問：RTP cache stale、dark pool Redis loss、respin loop timeout、player control MQ failure。
- Lead / Architect 追問：runtime policy 是否應拆服務、math contract 如何版本化、reconciliation 如何補。

## 7. 下一步

```text
antplay antplay-slot-game-api runtime-rtp-darkpool-player-control Step 4
```
