# Claim Boundary - Buy Free / Scatter / RTP_3 Result Contract

## 1. 可放履歷

只建議併入 `*-math` grouped bullet：

> 參與 AntPlay 多個 slot math module 維護與驗證，處理 RTP / reel strip、buy free / scatter、debug bet、fixedMultiBet、jackpot / symbol、currency 與 result contract 類調整。

證據層級:

- `真實開發過 + code-backed`
- `spn-math` 有 Nick / `10gt12nc` direct commits，集中在 RTP_3、buy free odds、scatter / `lastSymbols` 相關 path。
- `antplay-slot-game-api` 補讀到 runtime caller 與 afterBet result JSON path，但本 flow 不宣稱 Nick 在 game-api 直接開發此 path。

## 2. 可面試講

- Buy free 從 game-api `boughtFreeSpin` 到 math module `freeSpinBet` 的 runtime path。
- `RTP_3` 如何作為 buy free 專用 reel strip routing。
- `HAVE_3_SCATTER_WIN` odds 如何影響 buy free cost。
- `lastSymbols` 如何影響 scatter / wild state 與 free game。
- `RoundResult` / `FreeBetResult` 如何形成 result contract。

## 3. 只能說參與 / 維護 / 驗證

目前可以說：

- 參與 slot math module buy free / scatter / RTP_3 類維護。
- 參與 result contract / free game result 類調整與驗證。
- 能分析 buy free feature 從 runtime caller 到 math result 的 failure window。

目前不應說：

- 不得寫成主導完整 buy free 商業設計。
- 不得寫成主導完整 RTP 策略或遊戲數學模型。
- 不得寫成主導完整 wallet / settlement / darkpool。
- 完整負責所有 `*-math` buy free flow。
- 完整負責前端展示契約。

## 4. 待補 Evidence

若未來要把 claim 升級，需要補：

- MR / ticket / reviewer comment。
- 正式 GDD / math spec / validation report。
- game-api bet record DB schema 與 result JSON snapshot。
- 前端展示 contract。
- production issue / bug fix / monitoring evidence。

## 5. 本 flow Step 5 判定

本 flow 已完成 Step 5 單條 flow claim gate。正式履歷 master 不因單條 flow 直接新增 standalone bullet；要更新 05 / 08，仍以 `*-math` project-level contribution consolidation 或 rolling resume package 為準。

Step 5 gate 結論:

- 可放履歷：可以，但只能併入 `*-math` grouped bullet，作 buy free / scatter / RTP_3 / result contract 的強化 evidence。
- 可面試講：可以，這是正式 code-backed 面試 case，主軸是 buy free 的 pricing / routing / result contract consistency。
- 不更新 05 / 08：本輪是單條 flow Step 5，不是 project-level resume package。
- 不可誇大：不能寫成完整 buy free owner、完整 RTP / 遊戲數學 owner、完整 wallet / settlement / darkpool owner。

可說:

- 可以講 `GameFacade` beforeBet odds、`SlotMathFacade.freeSpinBet`、`RTP_3` routing、`lastSymbols` state、`GameFlowFacade#afterBet` result JSON。
- 可以講 troubleshooting path 與 failure window。
- 可以講 Nick / `10gt12nc` 在 `spn-math` 的 RTP_3、buyFreeWinInfo、`HAVE_3_SCATTER_WIN`、`lastSymbols` reset direct evidence。

不更新:

- 不更新 05 / 08。
- 不升級為完整 `*-math` final consolidation。
- 不把 game-api / wallet / darkpool 說成 Nick 主導完整開發。

後續狀態：後續 Rank 4、Rank 5、`*-math` contribution claim consolidation refresh 與 rolling resume package 都已完成；目前沒有預設下一步。
