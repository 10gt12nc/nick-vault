# Interview：bet-target-set-query

狀態：Step 4 completed
證據層級：部分真實開發過 + code-backed；正式履歷 claim 待 Step 5

## 一句話定位

```text
這條是提款前打碼規則的 target setup、bet accumulation、query 與 audit flow；核心不是查詢欄位，而是 money rule correctness。
```

## 30 秒講法

```text
這條 flow 是打碼目標的設定、累加和查詢。入口在 HttpService 的 SET_BET_TARGET、SET_BET_TARGET_COUPON、QUERY_BET_TARGET，狀態存在 PlayerData.commExt.spinNeeds，裡面有 betTarget 和 betTotal。玩家投注時 addWithDrawSpinCount 會累加 betTotal，查詢時回 betTarget - betTotal。重點是這是提款規則，不是普通設定，所以要看重複設定、offline player、log audit 和投注扣減一致性。
```

## 90 秒講法

```text
我會把它拆成三段。第一段是 target setup：上游透過 SET_BET_TARGET 或 SET_BET_TARGET_COUPON 進 HttpService，最後呼叫 PlayerData.addWithDrawSpinNeeds，把目標存到 SpinNeedData。第二段是 bet accumulation：一般 spin 的 GameToCenterSpinResultJob 或第三方 provider transfer-in-out job，會在有效投注時呼叫 addWithDrawSpinCount 或 provider-specific addBetTotal，把 betTotal 往上累加。第三段是 query / audit：QUERY_BET_TARGET 回傳剩餘打碼量，同時每次 target 或 betTotal 變更都會送 PLAYER_BET_RECORD 到 log server，寫 log_spin_bet。這條的 owner 風險是重複 coupon 設定、玩家離線、commExt persistence 和 log 缺資料。
```

## 3 分鐘講法

```text
這條我會先把它定位成提款前 wagering rule，而不是普通查詢 API。

入口在 center 的 HttpService。SET_BET_TARGET 是一般 BI handle，SET_BET_TARGET_COUPON 是 coupon 來源，QUERY_BET_TARGET 用來查剩餘打碼量。設定時會找 PlayerData，再呼叫 addWithDrawSpinNeeds，實際狀態放在 commExt.spinNeeds 的 SpinNeedData，核心欄位是 betTarget 和 betTotal。

狀態轉移是：沒有打碼目標時，設定 target 會讓 betTarget = targetCnt * betRate，betTotal 歸零；如果原本有 target 且還沒完成，新的 target 會累加；如果原 target 已完成，就重設成新目標。玩家後續 spin 或 provider bet 成功後，GameToCenterSpinResultJob 或第三方 job 會呼叫 addWithDrawSpinCount / addBetTotal，把 betTotal 往上加。查詢時 queryPlayerBetTarget 如果 betTotal 已達 target 就回 0，否則回 target - total。

Senior 角度我會看四個點。第一是 idempotency：coupon 或後台指令如果 timeout 重送，現在看到的核心方法是累加 target，所以要確認上游有沒有 couponId / requestId 防重。第二是 availability：HttpService 直接 findPlayer，如果玩家不在線，離線行為要確認，不能假設一定成功。第三是 consistency：rule state 在 SpinNeedData，log_spin_bet 是 audit，不是 source of truth；log fail 不能直接改 rule，但會影響客服稽核。第四是 reconciliation：如果玩家投注成功但 betTotal 沒累加，會造成玩家不能提款，應該能用 game bet log / provider bet log / PLAYER_BET_RECORD 對帳。

我的 evidence 會保守講：coupon 打碼入口有 10gt12nc 的 direct commits，包含新增 SET_BET_TARGET_COUPON / COUPON reason，以及後續修正 coupon betCnt；但完整打碼系統 owner、上游 payment/app_bi/game_api contract 和 production incident，我不會誇大。
```

## STAR

| 區塊 | 內容 |
| --- | --- |
| Situation | 平台有 coupon、活動、充值返利、後台調整等來源會建立打碼限制，錯誤會造成提款爭議。 |
| Task | 釐清 iwin_gameserver 裡 target setup、投注累加、查詢與 audit log 的完整 flow，轉成可面試說明的 money rule case。 |
| Action | 從 `HttpService` 三個 command 往下追 `PlayerData`、`SpinNeedData`、一般 spin / provider bet 累加與 `PLAYER_BET_RECORD` log projection，並查 Nick / `10gt12nc` coupon direct commits。 |
| Result | 形成正式面試案例：rule state / audit source 分離、重複設定、offline player、persistence、log failure 與 reconciliation 風險清楚，履歷 claim 保守留到 Step 5。 |

## Senior 追問與回答

### Q1：這條 flow 的 source of truth 是哪裡？

`SpinNeedData` 裡的 `betTarget` / `betTotal` 是 rule state。`log_spin_bet` 是 audit trail，用來查變更與排查，不應當成提款規則的即時判斷來源。

### Q2：為什麼重複設定是高風險？

因為 `addBetTarget()` 在原目標未完成時會累加 target。若 coupon / BI command timeout 後被上游重送，而沒有 source id 防重，玩家的 required turnover 可能被加兩次。

### Q3：如果玩家已投注但 still cannot withdraw，要怎麼查？

先確認投注是否進到成功路徑，再查該路徑是否呼叫 `addWithDrawSpinCount()` 或 provider-specific `addBetTotal*()`。接著對照 `SpinNeedData.betTotal`、`PLAYER_BET_RECORD`、一般 game bet log 或 provider bet log。若 bet 成功但 `betTotal` 未累加，方向是補 replay / reconciliation，不是手動改查詢結果。

### Q4：log server 掛了會不會影響提款？

從目前 code-backed 判斷，rule state 更新和 log push 是分開的；log fail 會影響 audit / 客服查詢，但不應該直接改變 rule state。Owner 要補的是 durable log、重送、對帳與監控。

### Q5：玩家離線時行為如何？

目前保守標待確認。`HttpService` 看到的是 `CenterWorld.findPlayer(accountId)` 後直接用 `PlayerData`，Step 4 未確認 offline fallback。這是 Step 5 要補的 evidence：上游是否只對在線玩家呼叫、或 gameserver 是否有 load cache path。

### Q6：`betTarget <= MONEY_RATIO` 歸零代表什麼？

這表示很小的 target 會被清掉，可能是避免微小打碼限制殘留。面試時可以提它是 rule normalization，但不要過度解讀成完整風控策略，因為沒有看到產品規格或 ticket。

### Q7：coupon direct evidence 可以怎麼講？

可以說 Nick / `10gt12nc` 有 coupon 打碼入口的 direct commits：新增 `SET_BET_TARGET_COUPON` / `COUPON` reason，以及把 coupon 的 `betCnt` 從 0 修成 `betTarget`。但不能說成完整打碼系統 owner。

## Lead / Architect 追問

### Q1：你會怎麼補 idempotency？

用 `sourceType + sourceBillNo/couponId + uid` 建 idempotency key。第一次處理時保存 target change 與 result；重送時回同一結果，不再呼叫累加。若現有架構不方便即時改 DB，可先在上游 coupon / BI command 層補 request id，再逐步沉到 gameserver rule state。

### Q2：你會怎麼做 reconciliation？

把三個來源串起來：game / provider bet source、`SpinNeedData` rule state、`PLAYER_BET_RECORD` audit。用 `uid + gameId/sourceType + transactionId/couponId + time bucket` 查漏累加或漏 log，並提供補 log / 補累加的人工審核工具。

### Q3：你會怎麼監控？

- `SET_BET_TARGET*` success / fail / duplicate count。
- `QUERY_BET_TARGET` target abnormal distribution。
- `PLAYER_BET_RECORD` push fail count。
- 有投注成功但 betTotal 長時間不變的玩家數。
- offline player target setup failure。

### Q4：這條要不要抽成 rule engine？

面試可以保守回答：先不急著抽。這條目前風險在 source idempotency、persistence、audit 與 reconciliation。若後續來源越來越多、倍率規則越來越複雜，再把 `SpinBetTargetConst`、target calculation、source idempotency、rule audit 抽成更明確的 rule module。

## 可反問面試官

- 你們的 bonus / wagering rule 是跟 wallet transaction 綁在一起，還是獨立 rule state？
- coupon / activity reward 有沒有 source idempotency key？
- 玩家投注成功但打碼未扣減時，你們怎麼 reconcile？
- log / report table 是 audit source 還是會被拿來做線上判斷？

## 面試邊界

可說：

- 我能把打碼 flow 拆成 target setup、bet accumulation、query、audit log。
- 我能說明 `betTarget` / `betTotal` 的 state transition。
- 我能指出重複設定、offline player、log failure、漏扣減與 reconciliation 風險。
- coupon 打碼入口有 direct commit evidence。

不可說：

- 我主導完整打碼系統。
- 我建立完整 wagering rule engine。
- 我負責 payment / app_bi / game_api 全上游 contract。
- 我解決過 production 打碼錯誤 incident。

## 下一步

```text
iwin iwin_gameserver bet-target-set-query Step 5
```
