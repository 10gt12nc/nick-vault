# Decision Notes：bet-target-set-query

## Decision 1：把它當 money rule，不當普通設定

`SET_BET_TARGET` 看起來像後台設定，但實際影響提款資格。

Owner 角度要追：

- target 來源。
- target 是否會重複累加。
- betTotal 是否可靠累加。
- query / withdraw check 是否和 runtime state 一致。

## Decision 2：`SpinNeedData` 是 rule state，`log_spin_bet` 是 audit

`SpinNeedData.betTarget` / `betTotal` 決定玩家還差多少打碼。

`PLAYER_BET_RECORD` / `log_spin_bet` 用來稽核，不應被當成即時 rule source。

## Decision 3：coupon command 有 direct evidence，但不擴張成完整 owner

Nick / `10gt12nc` 明確新增 / 修正 `SET_BET_TARGET_COUPON` 與 `COUPON` reason。

這能支撐 coupon 打碼目標入口，但還不能支撐完整提款打碼系統 owner，因為：

- 一般 `SET_BET_TARGET` 與核心 `SpinNeedData` 不是本輪直接開發 evidence。
- 上游 app_bi / payment / game_api contract 未全掃。
- persistence / offline 行為未完整確認。

## Decision 4：Step 3 不更新履歷

Step 3 只建立 flow understanding。是否能回填履歷要到 Step 5 claim gate，再和 project-level consolidation 對齊。

## 下一步

```text
iwin iwin_gameserver bet-target-set-query Step 4
```
