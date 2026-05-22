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

## Decision 5：Step 4 用面試 case 講 owner thinking，不升級履歷 claim

Step 4 已把這條轉成正式面試 case，但它的定位仍是 Flow Track。

面試可以講：

- `SpinNeedData` 是 rule state。
- `log_spin_bet` 是 audit source。
- 重複 command、offline player、commExt persistence、log failure、betTotal 漏累加是 owner failure window。
- coupon direct commits 可以說成 supporting evidence。

仍不更新正式履歷：

- Step 4 不是 claim gate。
- 未掃上游 `app_bi` / `payment` / `game_api` caller。
- 未確認 ticket / production incident。
- 未追完整 persistence / offline fallback。

## Decision 6：Step 5 要回答的是 claim gate，不是重寫面試稿

下一步 Step 5 應聚焦：

- coupon direct commits 能否回填 project-level claim。
- 本 flow 是否只保留 interview-only。
- 哪些說法可以寫、只能面試講、不可誇大。
- 是否需要同步 `iwin_gameserver` project-level consolidation。

## 下一步

```text
iwin iwin_gameserver bet-target-set-query Step 5
```
