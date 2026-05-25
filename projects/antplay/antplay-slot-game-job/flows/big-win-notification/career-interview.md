# big-win-notification Career / Interview

日期: 2026-05-25

## Evidence Level

- 證據層級: 真實開發過 + code-backed。
- Nick evidence: `1135490` / `c2cd523` 等 `#303` commits 由 `10gt12nc` 新增中大獎通知與小數格式修正。
- Current behavior evidence: current `BigWinConsumerService` 仍保留大量 `10gt12nc` lines，但後續 Gill / Arnold / Eliot 修改玩家遮罩、currency / translation、totalWin 判斷與 bet id collection。
- 完成狀態: Step 3 flow learning package 已完成。
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

## 30 秒說法草稿

我在 AntPlay job repo 有參與過中大獎通知相關 flow。它消費 `settled_bets`，判斷玩家 `totalWin` 是否達到投注額與 voucher bet 的 10 倍，然後組出 push user message，包含玩家遮罩名稱、遊戲代碼、翻譯名稱、幣別與金額。這條 case 我會保守講成 Kafka derived notification，不是下注或錢包 source of truth；面試重點會放在重複通知、producer failure、缺翻譯 fallback 和玩家隱私。

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

## Step 3 結論

Step 3 已完成 code-backed flow learning package。下一步 Step 4 要把這條整理成正式面試 case，尤其要練「我參與初版 / current code 有後續修正 / 我能用 owner 視角分析通知可靠性」這種保守而有分量的說法。
