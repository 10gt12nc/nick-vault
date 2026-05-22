# Career Interview：bet-target-set-query

狀態：Step 3 completed
證據層級：部分真實開發過 + code-backed；coupon 打碼目標入口有 Nick / `10gt12nc` direct commits，一般完整打碼系統 owner 待 Step 5 判斷
履歷結論：本 Step 不更新正式履歷 / 自傳；先作 money rule 面試素材

## 這條 flow 可以怎麼講

保守說法：

```text
我追過 iwin_gameserver 的打碼目標設定與查詢 flow。它不是單純後台欄位，而是玩家拿到 coupon / 活動 / 後台調整後，系統怎麼設定 wagering target，後續 spin 或 provider 投注怎麼累加 betTotal，最後查詢剩餘打碼量並支撐提款檢查。
```

## 30 秒版本

```text
這條 flow 是打碼目標的設定、累加和查詢。入口在 HttpService 的 SET_BET_TARGET、SET_BET_TARGET_COUPON、QUERY_BET_TARGET，狀態存在 PlayerData.commExt.spinNeeds，裡面有 betTarget 和 betTotal。玩家投注時 addWithDrawSpinCount 會累加 betTotal，查詢時回 betTarget - betTotal。重點是這是提款規則，不是普通設定，所以要看重複設定、offline player、log audit 和投注扣減一致性。
```

## 90 秒版本

```text
我會把它拆成三段。第一段是 target setup：上游透過 SET_BET_TARGET 或 SET_BET_TARGET_COUPON 進 HttpService，最後呼叫 PlayerData.addWithDrawSpinNeeds，把目標存到 SpinNeedData。第二段是 bet accumulation：一般 spin 的 GameToCenterSpinResultJob 或第三方 provider transfer-in-out job，會在有效投注時呼叫 addWithDrawSpinCount，把 betTotal 往上累加。第三段是 query / audit：QUERY_BET_TARGET 回傳剩餘打碼量，同時每次 target 或 betTotal 變更都會送 PLAYER_BET_RECORD 到 log server，寫 log_spin_bet。這條的 owner 風險是重複 coupon 設定、玩家離線、commExt persistence 和 log 缺資料。
```

## 可講的 Senior 點

- `betTarget` 是提款規則狀態，`betTotal` 是投注累積狀態。
- `log_spin_bet` 是 audit，不是 rule source。
- coupon 類設定要有 source request id / coupon id 防重。
- `SET_BET_TARGET` 直接找 online `PlayerData`，offline 行為要確認。
- 打碼扣減要和一般 spin / third-party provider bet flow 對齊，否則玩家投注與提款限制會不一致。

## 不能誇大的地方

不能說：

- 我主導完整提款打碼系統。
- 我設計完整 wagering / turnover rule engine。
- 我負責 app_bi / payment / game_api 全上游 contract。
- 我解過打碼錯誤 production incident。

目前可以說：

- coupon 打碼目標入口有 direct commit evidence。
- 本 flow 是 code-backed money rule 分析素材。

## Step 3 後下一步

```text
iwin iwin_gameserver bet-target-set-query Step 4
```
