# Interview：bet-target-set-query

狀態：Step 3 初版面試素材
證據層級：部分真實開發過 + code-backed；正式面試 case 待 Step 4

## Q1：這條 flow 的核心問題是什麼？

核心是提款前 wagering rule 的正確性：玩家拿到 coupon / 活動 / 後台調整後，需要投注多少才能解除限制。

## Q2：主要資料在哪裡？

在 `PlayerData.commExt.spinNeeds`，對應 `SpinNeedData`：

- `betTarget`：需要完成的總打碼目標。
- `betTotal`：已累積的打碼量。

## Q3：設定和查詢入口是什麼？

`HttpService`：

- `SET_BET_TARGET`
- `SET_BET_TARGET_COUPON`
- `QUERY_BET_TARGET`

## Q4：投注怎麼扣減打碼？

一般 spin 成功後，center 的 `GameToCenterSpinResultJob` 會用 `rq.getBetCoin()` 呼叫 `player.addWithDrawSpinCount()`。第三方 provider 投派整合也有 Antplay / GSC / PG / AP 專用累加方法。

## Q5：這條的最大風險是什麼？

最大風險不是查詢慢，而是重複設定或漏扣減：

- 重複設定會讓玩家被要求過高打碼。
- 漏扣減會讓玩家投注後仍無法提款。
- log 缺失會讓客服無法解釋 target / total 變化。

## Q6：能不能放履歷？

Step 3 不直接放。coupon 打碼目標入口有 Nick / `10gt12nc` direct commits，可作 project-level supporting evidence；完整打碼系統 owner 要等 Step 5 claim gate。
