# Career Interview：bet-target-set-query

狀態：Step 5 completed
證據層級：分層 claim。coupon 打碼目標入口是真實開發過 + code-backed；一般完整打碼系統 owner / BI / payment / app_bi caller 為 code-backed / interview-only
履歷結論：不新增獨立 gameserver 打碼履歷 bullet；coupon 打碼入口只作既有 coupon / gameserver supporting evidence

## 這條 flow 可以怎麼講

保守說法：

```text
我追過 iwin_gameserver 的打碼目標設定與查詢 flow。這條不是單純後台欄位，而是玩家拿到 coupon、活動獎勵或後台調整後，系統如何設定 wagering target，後續一般 spin 或第三方 provider 投注如何累加 betTotal，最後查詢剩餘打碼量並支撐提款檢查。
```

## 30 秒版本

```text
這條 flow 是打碼目標的設定、累加和查詢。入口在 HttpService 的 SET_BET_TARGET、SET_BET_TARGET_COUPON、QUERY_BET_TARGET，狀態存在 PlayerData.commExt.spinNeeds，裡面有 betTarget 和 betTotal。玩家投注時 addWithDrawSpinCount 會累加 betTotal，查詢時回 betTarget - betTotal。重點是這是提款規則，不是普通設定，所以要看重複設定、offline player、log audit 和投注扣減一致性。
```

## 90 秒版本

```text
我會把它拆成三段。第一段是 target setup：上游透過 SET_BET_TARGET 或 SET_BET_TARGET_COUPON 進 HttpService，最後呼叫 PlayerData.addWithDrawSpinNeeds，把目標存到 SpinNeedData。第二段是 bet accumulation：一般 spin 的 GameToCenterSpinResultJob 或第三方 provider transfer-in-out job，會在有效投注時呼叫 addWithDrawSpinCount 或 provider-specific addBetTotal，把 betTotal 往上累加。第三段是 query / audit：QUERY_BET_TARGET 回傳剩餘打碼量，同時每次 target 或 betTotal 變更都會送 PLAYER_BET_RECORD 到 log server，寫 log_spin_bet。這條的 owner 風險是重複 coupon 設定、玩家離線、commExt persistence 和 log 缺資料。
```

## 3 分鐘版本

```text
這條我會先把它定位成提款前 wagering rule，而不是普通查詢 API。

入口在 center 的 HttpService。SET_BET_TARGET 是一般 BI handle，SET_BET_TARGET_COUPON 是 coupon 來源，QUERY_BET_TARGET 用來查剩餘打碼量。設定時會找 PlayerData，再呼叫 addWithDrawSpinNeeds，實際狀態放在 commExt.spinNeeds 的 SpinNeedData，核心欄位是 betTarget 和 betTotal。

狀態轉移是：沒有打碼目標時，設定 target 會讓 betTarget = targetCnt * betRate，betTotal 歸零；如果原本有 target 且還沒完成，新的 target 會累加；如果原 target 已完成，就重設成新目標。玩家後續 spin 或 provider bet 成功後，GameToCenterSpinResultJob 或第三方 job 會呼叫 addWithDrawSpinCount / addBetTotal，把 betTotal 往上加。查詢時 queryPlayerBetTarget 如果 betTotal 已達 target 就回 0，否則回 target - total。

Senior 角度我會看四個點。第一是 idempotency：coupon 或後台指令如果 timeout 重送，現在看到的核心方法是累加 target，所以要確認上游有沒有 couponId / requestId 防重。第二是 availability：HttpService 直接 findPlayer，如果玩家不在線，離線行為要確認，不能假設一定成功。第三是 consistency：rule state 在 SpinNeedData，log_spin_bet 是 audit，不是 source of truth；log fail 不能直接改 rule，但會影響客服稽核。第四是 reconciliation：如果玩家投注成功但 betTotal 沒累加，會造成玩家不能提款，應該能用 game bet log / provider bet log / PLAYER_BET_RECORD 對帳。

我的 evidence 會保守講：coupon 打碼入口有 10gt12nc 的 direct commits，包含新增 SET_BET_TARGET_COUPON / COUPON reason，以及後續修正 coupon betCnt；但完整打碼系統 owner、上游 payment/app_bi contract 和 production incident，我不會誇大。
```

## STAR 版本

Situation：

```text
遊戲平台會用 coupon、活動、充值返利或後台調整建立打碼限制，玩家需要完成一定投注量後才能提款。這類規則如果被重複設定、漏扣減或 audit 缺資料，會直接造成玩家提款爭議。
```

Task：

```text
我需要把 iwin_gameserver 裡打碼目標設定、投注累加、查詢與 audit log 的 flow 讀清楚，整理成能面試說明 money rule correctness 的案例，同時保守區分 direct commit evidence 和 code-backed 分析。
```

Action：

```text
我從 HttpService 的 SET_BET_TARGET / SET_BET_TARGET_COUPON / QUERY_BET_TARGET 入口往下追，到 PlayerData.addWithDrawSpinNeeds / addWithDrawSpinCount，再到 SpinNeedData 的 betTarget / betTotal 狀態轉移與 PLAYER_BET_RECORD log projection。接著補看一般 spin 與第三方 provider 投注如何累加 betTotal，並用 git history 確認 coupon 打碼入口有 10gt12nc direct commits。
```

Result：

```text
最後我把這條整理成三層：rule state 是 SpinNeedData，audit 是 log_spin_bet，風險是重複 target、offline player、commExt persistence、log 缺失與 betTotal 漏累加。履歷上只保守使用 coupon 入口和 project-level provider 投派整合 evidence，不把整套 wagering rule engine 說成我主導。
```

## Senior / Lead 追問

### Q1：如果 coupon 設定 timeout，上游重送會怎樣？

保守回答：

```text
目前我看到 addBetTarget 在原 target 未完成時會累加 target，所以如果同一 coupon command 被重送，而上游或 gameserver 沒有 couponId / requestId 防重，就可能把 betTarget 加兩次。Owner 解法會是把 sourceType + sourceBillNo / couponId 做成 idempotency key，已處理過就回同一結果，而不是再次累加。
```

### Q2：`log_spin_bet` 可以當提款判斷來源嗎？

保守回答：

```text
不建議。rule source 是 PlayerData.commExt.spinNeeds 裡的 SpinNeedData，log_spin_bet 是 audit trail。log 可以用來查 target change / betTotal change，但線上提款判斷應該看 rule state。若 log server fail，應該補 log / replay / reconciliation，不應直接把 log 當成即時提款規則。
```

### Q3：玩家離線時能不能設定打碼？

保守回答：

```text
Step 4 目前只能說待確認。HttpService.setPlayerBetTarget / setPlayerBetTargetCoupon 看到的是 CenterWorld.findPlayer(accountId) 後直接使用 PlayerData；這代表要確認上游是不是只對在線玩家呼叫，或其他地方是否會 load cache。若沒有 offline fallback，這會是可用性和資料一致性風險。
```

### Q4：玩家投注成功，但打碼沒有扣減，怎麼排查？

保守回答：

```text
我會先查投注是否真的進入一般 spin 或 provider bet 成功路徑，再看該路徑是否呼叫 addWithDrawSpinCount / addBetTotal。接著對照 SpinNeedData 的 betTotal、PLAYER_BET_RECORD、game / provider bet log。若 wallet / bet 成功但 betTotal 沒變，就要補 reconciliation 或 replay，而不是手動改一筆查詢結果。
```

### Q5：這條和你可放履歷的 gameserver 經驗怎麼區分？

保守回答：

```text
我會區分。iwin_gameserver project-level 可放履歷的強 evidence 是第三方 provider 投派整合與 gameserver wallet / log projection 串接。bet-target-set-query 這條可作 money rule 面試案例；coupon 打碼入口有 direct commits，可當 coupon / gameserver supporting evidence，但完整打碼系統、payment/app_bi 上游 contract 和完整提款 owner 不會寫成我主導。
```

## 可講的 Senior 點

- `betTarget` 是提款限制 rule state，`betTotal` 是投注累積 state。
- `log_spin_bet` 是 audit，不是線上提款 rule source。
- coupon 類設定要有 source request id / coupon id 防重。
- `SET_BET_TARGET` 直接找 online `PlayerData`，offline 行為要確認。
- 打碼扣減要和一般 spin / third-party provider bet flow 對齊，否則玩家投注與提款限制會不一致。
- `commExt.spinNeeds` 的 persistence / flush timing 是後續若要升級 claim 時要補的 evidence，不應假設已完整落庫。

## 不能誇大的地方

不能說：

- 我主導完整提款打碼系統。
- 我設計完整 wagering / turnover rule engine。
- 我負責 app_bi / payment / game_api 全上游 contract。
- 我解過打碼錯誤 production incident。
- 我建立完整 idempotency / reconciliation 機制。

目前可以說：

- coupon 打碼目標入口有 direct commit evidence。
- 本 flow 是 code-backed money rule 正式面試素材。
- 我能說明 target setup、bet accumulation、query、audit log 與 failure window。

## Step 5 結論

這條已完成單條 flow claim gate。面試時可以拿來講 money rule correctness、wagering target 的 state transition、重複設定風險、offline player 風險、audit log 和 source of truth 的差異。

履歷上不新增獨立 gameserver 打碼 bullet。可保守使用的只有：

- coupon 打碼入口：真實開發過 + code-backed supporting evidence。
- 完整打碼 / 提款限制 flow：code-backed 面試素材。
- payment / app_bi 查詢與 BI 人工設定：只作上游 / 操作面關聯，不作 Nick direct development claim。

## 下一步

```text
iwin third_games_api gsc-transfer-bet-settle-rollback Step 5
```
