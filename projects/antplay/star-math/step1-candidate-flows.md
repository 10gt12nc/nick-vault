# *-math Step 1 Candidate Flows

日期: 2026-05-20

## 0. 閱讀定位

- Project: `antplay *-math`
- Vault path: `projects/antplay/star-math`
- Source path: `/Users/nick/Git/antplay/*-math`
- Step: Step 1 candidate flows
- 掃描深度: Level 1 Flow 掃描
- 證據層級: `真實開發過 + code-backed` / `專案存在 / code-backed` 混合
- 本輪目標: 從 71 個 slot math repo 中挑出值得進 Step 2 比較的代表 flow，不平均整理所有 repo / class。

本 Step 1 不是單條 flow 深掃，也不是 final consolidation。`contribution-claim-consolidation.md` 已先完成 Career Track 的 grouped claim；本檔回到 Flow Track，負責找代表 flow。

## 1. 已重讀

KB:

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`

Vault:

- `projects/antplay/README.md`
- `projects/antplay/math-core/README.md`
- `projects/antplay/star-math/README.md`
- `projects/antplay/star-math/contribution-claim-consolidation.md`

Source:

- `/Users/nick/Git/antplay/*-math`
- 強 evidence module: `sph-math`、`spn-math`、`sfm-math`、`sdt-math`、`slc-math`、`setl-math`

## 2. Source Repo 最新性

本輪依 KB 規則先嘗試更新 remote refs；部分 `*-math` repo 可 fetch，部分 repo remote 目前不可達。依 Nick 最新指示，remote 不可達時不反覆重試，改用本地 refs / 本地工作樹保守判斷。

整體狀態:

- 掃到 71 個 `*-math` repo。
- 已嘗試 fetch；可達 repo 以更新後 refs 判斷。
- 不可達 repo 標示為「fetch 失敗 / 未確認最新遠端 / 依本地 refs 判斷」。
- 本檔不記錄內網 URL、IP 或敏感 remote 細節。
- 強 evidence repo 的本地 `HEAD` 與本地 `origin/master` refs 目前一致。
- `setl-math` 本地工作樹有未提交修改；本輪只讀，未採用未提交內容作履歷 claim。
- `szs-math` 本地工作樹有未提交修改；本輪只作低優先級參考。

強 evidence repo 本地狀態:

| Repo | Branch | Local HEAD | Local upstream ref | ahead / behind | Dirty | Nick commits | 遠端最新性 |
| --- | --- | --- | --- | --- | ---: | ---: | --- |
| `sph-math` | `master` | `17996c5` | `origin/master` = `17996c5` | `0 / 0` | 0 | 125 | fetch 失敗，依本地 refs |
| `spn-math` | `master` | `73f32d4` | `origin/master` = `73f32d4` | `0 / 0` | 0 | 116 | fetch 失敗，依本地 refs |
| `sfm-math` | `master` | `ea458d5` | `origin/master` = `ea458d5` | `0 / 0` | 0 | 80 | fetch 失敗，依本地 refs |
| `setl-math` | `master` | `fe25c72` | `origin/master` = `fe25c72` | `0 / 0` | 2 | 69 | fetch 失敗，依本地 refs |
| `sdt-math` | `master` | `146e256` | `origin/master` = `146e256` | `0 / 0` | 0 | 68 | fetch 失敗，依本地 refs |
| `slc-math` | `master` | `1d8a137` | `origin/master` = `1d8a137` | `0 / 0` | 0 | 66 | fetch 失敗，依本地 refs |

## 3. Module Map

`*-math` repo 大多是單一 slot game math module，結構高度相似:

```text
{game}-math/
  pom.xml
  src/main/java/com/ps/math/{game}/
    factory/{GAME}MathFactory.java
    service/{GAME}OperatorService.java
    game/AbstractSlotMath.java
    game/PxxxxxSlotMath.java
    game/PxxxxxSlotMathTest*.java
    game/reelStripOptimizer/*.java
    constant/SlotReelStripTable*.java
    constant/SlotSymbolTable.java
    config/SlotMathConfig.java
    vo/SlotBetResult.java / RoundResult.java / GameRngVO.java
    bo/SlotSpinResultTemp.java / RoundResultTemp.java
```

用後端分層翻譯:

| 層 | 在 math repo 的對應 | 作用 |
| --- | --- | --- |
| Contract / API | `math-core` 的 `SlotMath`、各 module `OperatorService` | 被 game-api / runtime 呼叫的 math 入口 |
| Orchestration | `{GAME}MathFactory` | 組 `InputData`、指定 RTP、debugBet、buy free、fixedMultiBet、custom RNG |
| Domain Core | `AbstractSlotMath#spin` | RNG、輪帶取符號、判線、free game、jackpot、feature、組回傳 |
| Config | `SlotMathConfig`、`SlotReelStripTable`、`SlotSymbolTable` | RTP / reel strip / symbol / payline / game state |
| Simulation / Validation | `reelStripOptimizer/*`、`PxxxxxSlotMathTest*` | 模擬 RTP、free game trigger、buy free / feature、debug 指定盤面 |
| Runtime Result | `SlotBetResult`、`RoundResult`、`ExtraData` | 回傳給遊戲 runtime / 前端 / log 的下注結果 |

## 4. Strong Evidence Modules

| Repo | Nick commits | 本輪看到的主要技術線 | Step 1 判斷 |
| --- | ---: | --- | --- |
| `sph-math` | 125 | Phoenix Reborn、JP / trigger log、RTP / reel strip / simulation / histogram | 最值得做 jackpot + RTP simulation 類代表 flow |
| `spn-math` | 116 | RTP_3、buy free、scatter win、lastSymbols、reel strip optimizer | 最值得做 buy free / scatter / RTP_3 類代表 flow |
| `sfm-math` | 80 | fixedMultiBet with math-core、special wild family、operator service、optimizer | 適合做 math-core contract + special feature compatibility |
| `setl-math` | 69 | VND currency、reel strip 調整、log / test | 有 dirty worktree，先不列第一順位 |
| `sdt-math` | 68 | fixedMultiBet、debugBet VO、currency、JP test、jackpot scaling | 適合做 fixedMultiBet / currency / jackpot flow |
| `slc-math` | 66 | debugBet、SlotReelStripTableNew、clover feature、fixedMultiBet / jackpot 類共通邏輯 | 適合與 `sdt` 比較做 Step 2 |

局部 evidence repo 仍可作補充，但不建議平均深挖:

```text
shf-math, salc-math, scir-math, scs-math, sdbs-math, sge-math,
sjcs-math, sml-math, snd-math, sqft-math, sam-math, scp-math,
scy-math, sjj-math, spt-math, stp-math, swb-math 等
```

未掃到 Nick direct commits 的 repo 不放 Nick 開發 claim，只可作 comparison / learning-only。

## 5. Candidate Flows

### 1. `rtp-reel-strip-simulation-validation`

中文名稱: RTP / 輪帶模擬與驗證 flow

代表 repo:

- `sph-math`
- `spn-math`
- `sfm-math`

已看到的 code path:

- `game/reelStripOptimizer/Simulate.java`
- `game/reelStripOptimizer/GenerateByRatio.java`
- `game/reelStripOptimizer/ProcessGameSpin.java`
- `constant/SlotReelStripTable.java`
- `game/AbstractSlotMath.java`

為什麼值得做:

- 這是 `*-math` 最有差異化的 Senior 面試題材，不是 CRUD。
- 可以講清楚 RTP target、reel strip、free game trigger、buy free、simulation rounds 與 validation 的關係。
- Nick 在 `sph` / `spn` 有大量 direct commits，且 commit message 明確出現 RTP、JP、scatter、模擬機率、分布柱狀圖。

風險 / 待確認:

- 本輪未驗證實際 GDD / certification 文件。
- 不可說 Nick 設計完整 RTP 策略或數學模型。
- 強 evidence repo fetch 失敗，只能依本地 refs 判斷。

履歷價值:

- 高。可支撐「參與 slot math module RTP / reel strip / simulation validation 維護」。

### 2. `buy-free-scatter-rtp3-result-contract`

中文名稱: Buy Free / Scatter / RTP_3 結果契約 flow

代表 repo:

- `spn-math`
- `sph-math`
- `sfm-math`

已看到的 code path:

- `{GAME}MathFactory#boughtFreeSpinBet`
- `{GAME}MathFactory#haveFreeWin`
- `AbstractSlotMath#init`
- `AbstractSlotMath#checkFreeGame`
- `SlotReelStripTable.RTP_3`
- `RoundResult` / `FreeBetResult`

為什麼值得做:

- 這條能把遊戲功能、RTP 設定、前端 result contract、debug / test 串起來。
- `spn-math` commit 明確有 `RTP_3`、`buyFreeWinInfo`、`HAVE_3_SCATTER_WIN`。
- 可用來面試講「feature 不是只改一張表，還會影響 result contract、free spin flow、simulation」。

風險 / 待確認:

- 尚未掃 runtime game-api 如何呼叫 buy free math。
- 尚未對齊前端顯示契約。

履歷價值:

- 中高。適合放面試 case；正式履歷只放在 grouped `*-math` 句子中。

### 3. `fixed-multi-bet-currency-math-core-compatibility`

中文名稱: fixedMultiBet / 幣別 / math-core 相容 flow

代表 repo:

- `sdt-math`
- `slc-math`
- `sfm-math`
- `math-core`

已看到的 code path:

- `AbstractSlotMath#getTotalBet`
- `InputData.fixedMultiBet`
- `{GAME}MathFactory#normalBet / debugBet / boughtFreeSpinBet`
- `{GAME}OperatorService#updateCurrencyMultiplier`
- `SlotBetResult.totalBet / betDenomination`

為什麼值得做:

- 這條最像 backend contract / money correctness 題材：下注倍數、幣別倍率、總下注、jackpot scaling 與前端回傳要一致。
- `sdt-math` 有 `fixedMultiBet 給前端`、`debugBet VO 加 currency`、`debugBet VO 加 fixedMultiBet`。
- `sfm-math` 有 `fixedMulit with math core` 系列 commit。

風險 / 待確認:

- 需要下一步掃 `math-core` contract 與 runtime caller，才能說清楚跨 module 影響。
- `setl-math` 有 dirty worktree，本輪不採用未提交內容。

履歷價值:

- 高。比純 RTP 更容易連到 backend 面試的 contract / compatibility / money correctness。

### 4. `jackpot-symbol-hit-and-prize-scaling`

中文名稱: Jackpot symbol hit / prize scaling flow

代表 repo:

- `sph-math`
- `sdt-math`
- `slc-math`

已看到的 code path:

- `AbstractSlotMath#checkJackpot`
- `OperatorService#getJackpotBalance`
- `JackpotFlip`
- `SlotBetResult.jackpotRewardList`
- `RoundResultTemp.*Jackpot*Hit`

為什麼值得做:

- 可講 jackpot hit 判斷、payload、balance callback、maxBet / nowBet scaling、round result / reward list。
- `sph-math` commit 有 JP 調整、JP log、模擬 JP 機率。
- `sdt-math` / `slc-math` 有 jackpot scaling 與 fixedMultiBet 關聯。

風險 / 待確認:

- 未掃 jackpot provider / runtime balance callback 的來源。
- 不能說 Nick 設計 jackpot 商業規則或完整 jackpot system。

履歷價值:

- 中高。適合當 money-like correctness / prize calculation 面試題。

### 5. `special-wild-feature-state-transform`

中文名稱: Special Wild / symbol state transform flow

代表 repo:

- `sfm-math`
- `slc-math`

已看到的 code path:

- `sfm AbstractSlotMath#changeWildState`
- `sfm AbstractSlotMath#handleSpecialWildType`
- `sfm AbstractSlotMath#addExtraDataForWilds`
- `slc game/clover/LuckyCloverTracker.java`

為什麼值得做:

- 可展示特殊 feature 的 state transform：盤面 symbols、wild family、extraData、前端展示與 win calculation 的互相影響。
- `sfm-math` 是強 evidence module，且 `AbstractSlotMath` 內 feature state 很厚。

風險 / 待確認:

- Step 1 只看到局部 code path，還沒追完整 feature trigger / frontend contract。
- 這條偏 game feature，不如 fixedMultiBet / RTP 那麼通用。

履歷價值:

- 中。適合作為補充面試案例，不建議第一條。

## 6. Step 1 Ranking

| Rank | Candidate Flow | 技術價值 | Nick evidence | 面試價值 | 履歷支撐 | 建議 |
| ---: | --- | --- | --- | --- | --- | --- |
| 1 | `fixed-multi-bet-currency-math-core-compatibility` | 高 | 高 | 高 | 高 | Step 2 優先比較 |
| 2 | `rtp-reel-strip-simulation-validation` | 高 | 高 | 高 | 高 | Step 2 必比 |
| 3 | `buy-free-scatter-rtp3-result-contract` | 中高 | 高 | 高 | 中高 | Step 2 必比 |
| 4 | `jackpot-symbol-hit-and-prize-scaling` | 高 | 中高 | 中高 | 中高 | Step 2 比較 |
| 5 | `special-wild-feature-state-transform` | 中 | 中高 | 中 | 中 | 後續補充 |

目前最值得 Step 2 比較的是 `fixedMultiBet / currency / math-core compatibility` 與 `RTP / reel strip / simulation validation`。前者更貼 Senior Backend / money correctness；後者更有 slot math 差異化。

2026-05-21 更新：`fixed-multi-bet-currency-math-core-compatibility` 已完成 Step 5；`rtp-reel-strip-simulation-validation` 已完成 Step 5；`buy-free-scatter-rtp3-result-contract` 已完成 Step 5；`jackpot-symbol-hit-and-prize-scaling` 已完成 Step 4。下一步做 Rank 4 `jackpot-symbol-hit-and-prize-scaling Step 5`。

## 7. Claim Boundary

可保守說:

- Nick / `10gt12nc` 在多個 `*-math` repo 有 direct commits，參與 slot math module 維護與驗證。
- 可說參與 RTP / reel strip、debug bet、fixedMultiBet、buy free / purchasable free spin、jackpot / symbol、currency 與 simulation validation 類調整。
- 可面試講 code-backed 的 math module contract、result contract、simulation / validation 與跨 module compatibility。

不可誇大:

- 不說主導全部 `*-math`。
- 不說設計完整遊戲數學模型、RTP 策略或派彩機率。
- 不說完整 simulator / certification owner。
- 不說所有 71 個 repo 都是 Nick 真實開發過。
- 不說已驗證最新遠端；fetch 失敗的 repo 只能依本地 refs。

## 8. 未掃 / 待確認

- 未掃 game-api / slot runtime 如何呼叫 math module。
- 未掃前端 result 顯示契約。
- 未掃 GDD / certification / QA validation 文件。
- 未逐檔逐行掃 71 個 repo。
- 未追每個重要 commit diff；Step 2 後選定單條 flow 再做 Level 2。
- 內網 remote 不可達的 repo 未確認最新遠端。

## 9. Step 1 結論

`*-math` 值得繼續做 Flow Track。2026-05-20 已完成 Step 2，第一條代表 flow `fixed-multi-bet-currency-math-core-compatibility` 已完成 Step 5，第二條代表 flow `rtp-reel-strip-simulation-validation` 已完成 Step 5，第三條代表 flow `buy-free-scatter-rtp3-result-contract` 已完成 Step 5，第四條代表 flow `jackpot-symbol-hit-and-prize-scaling` 已完成 Step 4。

建議下一步:

```text
antplay *-math jackpot-symbol-hit-and-prize-scaling Step 5
```
