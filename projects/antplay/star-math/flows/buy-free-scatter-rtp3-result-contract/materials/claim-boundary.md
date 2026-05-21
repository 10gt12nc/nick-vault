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

- 主導完整 buy free 商業設計。
- 主導完整 RTP 策略或遊戲數學模型。
- 主導完整 wallet / settlement / darkpool。
- 完整負責所有 `*-math` buy free flow。
- 完整負責前端展示契約。

## 4. 待補 Evidence

若未來要把 claim 升級，需要補：

- MR / ticket / reviewer comment。
- 正式 GDD / math spec / validation report。
- game-api bet record DB schema 與 result JSON snapshot。
- 前端展示 contract。
- production issue / bug fix / monitoring evidence。

## 5. Step 3 判定

本 flow 已完成 Step 3，可進 Step 4 轉正式面試 case。正式履歷 master 05 / 08 本輪不直接更新；本 flow 只作 `*-math` grouped claim 的新增強化 evidence。

下一步:

```text
antplay *-math buy-free-scatter-rtp3-result-contract Step 4
```
