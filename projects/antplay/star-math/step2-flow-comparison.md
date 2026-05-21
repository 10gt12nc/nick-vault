# *-math Step 2 Flow Comparison

日期: 2026-05-20

## 0. 閱讀定位

- Project: `antplay *-math`
- Vault path: `projects/antplay/star-math`
- Source path: `/Users/nick/Git/antplay/*-math`
- Step: Step 2 flow ranking / candidate comparison
- 掃描深度: Level 1.5 / Step 2 比較掃描
- 證據層級: `真實開發過 + code-backed` / `專案存在 / code-backed` 混合
- 本 Step 2 目標: 比較 Step 1 找出的候選 flow，決定第一條進 Step 3 的代表 flow。

本文件不建立單條 flow folder，也不宣稱逐檔逐行。Step 3 才會對選定 flow 做 Level 2 深掃。

## 1. 本次結論

第一條建議進 Step 3:

```text
fixed-multi-bet-currency-math-core-compatibility
```

原因:

- 它最貼近 Senior Backend / Platform Backend 面試語言：contract compatibility、money-like correctness、result contract、cross-module change management。
- 證據同時跨 `math-core`、`sdt-math`、`slc-math`、`sfm-math`，不是單一遊戲局部調參。
- Nick / `10gt12nc` 在 `math-core` 有 `fixedMultiBet`、`debugBet VO 加 currency`、`debugBet VO 加 fixedMultiBet` 直接 commits；在 `sdt-math` 有 `fixedMultiBet 給前端`、debugBet / currency / jackpot 相關 commits；在 `sfm-math` 有 `fixedMulit with math core` 系列 commits。
- 這條可把「slot math」翻成後端可聽懂的主題：下注倍數、幣別倍率、總投注、jackpot scaling、前端回傳欄位、module contract 演進與相容風險。

第二順位:

```text
rtp-reel-strip-simulation-validation
```

它的差異化最高，可以講 RTP / reel strip / simulation / validation，但更偏 math tuning / validation。若目標是遊戲數學深度面試，它很強；若目標是 Senior Java Backend，第一條 `fixedMultiBet / currency / contract` 更容易連到 backend correctness。

## 2. 已重讀

KB:

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`

Vault:

- `projects/antplay/README.md`
- `projects/antplay/star-math/README.md`
- `projects/antplay/star-math/contribution-claim-consolidation.md`
- `projects/antplay/star-math/step1-candidate-flows.md`
- `projects/antplay/math-core/README.md`

Source:

- `/Users/nick/Git/antplay/math-core`
- `/Users/nick/Git/antplay/sph-math`
- `/Users/nick/Git/antplay/spn-math`
- `/Users/nick/Git/antplay/sfm-math`
- `/Users/nick/Git/antplay/sdt-math`
- `/Users/nick/Git/antplay/slc-math`
- `/Users/nick/Git/antplay/setl-math`

## 3. Source Repo 最新性

依 Nick 指示，內網 GitLab / remote 不可達時不反覆重試。本輪沿用 Step 1 的 local refs 判斷，並補讀 `math-core` 本地狀態。

| Repo | Branch | Local HEAD | Local upstream ref | ahead / behind | Dirty | 遠端最新性 |
| --- | --- | --- | --- | --- | ---: | --- |
| `math-core` | `master` | `7f1533b` | `origin/master` = `7f1533b` | 未重跑 fetch；本地 refs 判斷 | 0 | 依本地 refs |
| `sph-math` | `master` | `17996c5` | `origin/master` = `17996c5` | `0 / 0` | 0 | fetch 失敗，依本地 refs |
| `spn-math` | `master` | `73f32d4` | `origin/master` = `73f32d4` | `0 / 0` | 0 | fetch 失敗，依本地 refs |
| `sfm-math` | `master` | `ea458d5` | `origin/master` = `ea458d5` | `0 / 0` | 0 | fetch 失敗，依本地 refs |
| `sdt-math` | `master` | `146e256` | `origin/master` = `146e256` | `0 / 0` | 0 | fetch 失敗，依本地 refs |
| `slc-math` | `master` | `1d8a137` | `origin/master` = `1d8a137` | `0 / 0` | 0 | fetch 失敗，依本地 refs |
| `setl-math` | `master` | `fe25c72` | `origin/master` = `fe25c72` | `0 / 0` | 2 | fetch 失敗，依本地 refs；dirty，不採未提交內容 |

本檔不記錄內網 URL、IP 或敏感 remote 細節。

## 4. 評分維度

| 維度 | 說明 |
| --- | --- |
| Senior Backend 價值 | 是否能談 contract、state / money correctness、相容性、failure window、測試與 release risk |
| Slot math 差異化 | 是否能展示 RTP / reel strip / feature / simulation 的 domain depth |
| Nick evidence | Nick / `10gt12nc` direct commits 是否集中且可追 path |
| 跨 module 價值 | 是否能串 `math-core` 與多個 game module，而不是單 repo 局部調整 |
| Step 3 成本 | 進 Step 3 時是否能用合理範圍追完，不需要一次掃 71 repo |
| 履歷 / 面試轉換 | 是否能轉成保守但有辨識度的 3 分鐘 case |

## 5. Ranking 總表

| Rank | Flow | 建議 | Senior Backend | Slot math 差異化 | Nick evidence | 跨 module | Step 3 成本 | 理由 |
| ---: | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | `fixed-multi-bet-currency-math-core-compatibility` | 先做 Step 3 | 高 | 中高 | 高 | 高 | 中 | 最能把 slot math 翻成 backend contract / money-like correctness；有 `math-core` + `sdt` + `sfm` direct evidence |
| 2 | `rtp-reel-strip-simulation-validation` | 第二條 | 中 | 很高 | 高 | 中 | 中高 | 差異化最高；適合講 RTP / reel strip / simulation，但要避免講成完整 RTP 策略 owner |
| 3 | `buy-free-scatter-rtp3-result-contract` | 第三條 | 中高 | 高 | 高 | 中 | 中 | `spn` / `sph` evidence 強，可講 buy free / scatter / result contract；需補 runtime caller |
| 4 | `jackpot-symbol-hit-and-prize-scaling` | 後續 | 高 | 中高 | 中高 | 中 | 中高 | money-like prize scaling 很有價值，但 jackpot balance callback / runtime 來源未定位 |
| 5 | `special-wild-feature-state-transform` | 補充 | 中 | 中高 | 中高 | 低中 | 高 | domain feature 很厚，但偏單 game feature，第一批不優先 |

## 6. Candidate Flow 比較

### 1. `fixed-multi-bet-currency-math-core-compatibility`

中文名稱: fixedMultiBet / 幣別 / math-core 相容 flow

建議: 第一條進 Step 3

代表 repo:

- `math-core`
- `sdt-math`
- `sfm-math`
- `slc-math`
- `setl-math` 作補充，不採 dirty worktree 未提交內容

已確認 code / history:

- `math-core`:
  - `SlotMath#getTotalBet(Long, int, String, int)` 已有 `currency` + `fixedMultiBet` overload。
  - Nick commits: `fixedMultiBet`、`debugBet VO 加 currency`、`debugBet VO 加 fixedMultiBet`、`fix debugBet VO`。
- `sdt-math`:
  - `AbstractSlotMath#getTotalBet(..., currency, fixedMultiBet)` 使用 currency multiplier 與 fixedMultiBet 計算 total bet。
  - `SDTMathFactory` 多個 debug / normal / bought free methods 接 `currency` 與 `fixedMultiBet`。
  - `146e256 fixedMultiBet 給前端` 影響 `SDTMathFactory`、`P21014SlotMathTestNew`、`SlotBetResult`。
  - `dcda532` / `2d89d9f` 相關 debugBet VO currency / fixedMultiBet。
- `sfm-math`:
  - `697302b`、`82b8cbc`、`af41db6` 是 `fixedMulit with math core` 系列 commits。
  - 涉及 `AbstractSlotMath`、`SFMOperatorService`、test / optimizer。
- `slc-math`:
  - `getTotalBet(..., currency, fixedMultiBet)` 與 jackpot scaling path 類似 `sdt`。

Senior / Owner 價值:

- Contract evolution: `math-core` default method 新增後，各 game module 是否一致 override。
- Money-like correctness: `lineBet * minBet * currencyMultiplier * fixedMultiBet` 是否在 total bet、win amount、jackpot scaling、front-end result 中一致。
- Backward compatibility: 舊 module 沒 override 時 default method 可能忽略 currency / fixedMultiBet。
- Observability / debug: debugBet VO 加欄位後，測試與前端 debug result 要對齊。
- Release risk: core contract 改動會影響多個 `*-math` module，不是單 repo 局部改。

Step 3 必須補:

- `math-core` 的 `DebugBetVO`、`AbstractBetResult` 欄位演進。
- `sdt-math` / `sfm-math` / `slc-math` 的 path-specific diff。
- runtime caller 是否真的傳 `currency` / `fixedMultiBet`。
- 哪些 module 未 override new overload，是否有相容風險。
- dirty `setl-math` 只標未確認，不採未提交內容。

履歷 / 面試邊界:

- 可面試講「參與 slot math core 與 game module contract 相容調整，處理 fixedMultiBet、currency、debug result 與 total bet consistency」。
- 不說設計完整錢包、完整 RTP 策略或完整 math framework owner。

### 2. `rtp-reel-strip-simulation-validation`

中文名稱: RTP / 輪帶模擬與驗證 flow

建議: 第二條候選

代表 repo:

- `sph-math`
- `spn-math`
- `sfm-math`

已確認 code / history:

- `sph-math`:
  - `498c8fd 鳳凰轉生 JP調整+觸發log、初版印分布柱狀圖` 涉及 `SlotReelStripTable`、`AbstractSlotMath`、`P21008SlotMathTestNew`、`RTPConstants`、`Simulate`、`SparklineUtil`。
  - `5e62cbb 鳳凰轉生 模擬機率 初版 (JP)` 涉及 `RoundResultTemp`、`AbstractSlotMath`、`Simulate`、`RoundResult`。
  - `1fad502 鳳凰轉生 JP log` 擴充 simulation / log。
- `spn-math`:
  - `ec1d9ca RTP_3 / buyFreeWinInfo / HAVE_3_SCATTER_WIN` 涉及 `SlotReelStripTable`、`Ratio_RTP_3`、`Simulate`。
  - `d9beaa4 RTP_3 調整中 重置lastSymbols` 涉及 `AbstractSlotMath`、factory、simulation。
- 共通:
  - `reelStripOptimizer/Simulate.java` 會把 candidate reel strip 放進 `RTP_REEL_STRIP_OPTIMIZER`，再用 `spin` 模擬 Base RTP、free trigger、free RTP。

Senior / Owner 價值:

- 可以講 validation loop: ratio -> candidate reel strip -> simulate -> compare target / bounds。
- 可以講 why simulation 不是 production transaction，但會影響 production payout correctness。
- 可以講 evidence boundary: 只能說參與維護 / 驗證，不說設計完整 RTP 策略。

Step 3 成本:

- 中高。需要讀 `RTPConstants`、`Simulate`、`ProcessGameSpin`、`GenerateByRatio` 與至少一個 game module 的 `AbstractSlotMath`。
- 不需要一次掃 71 repo；建議先以 `sph-math` 為主，`spn-math` 作比較。

履歷 / 面試邊界:

- 可面試講「參與 RTP / reel strip / simulation validation 類調整」。
- 不說「我設計 RTP」或「我負責 certification」。

### 3. `buy-free-scatter-rtp3-result-contract`

中文名稱: Buy Free / Scatter / RTP_3 結果契約 flow

建議: 第三條候選

代表 repo:

- `spn-math`
- `sph-math`
- `sfm-math`

已確認 code / history:

- `spn-math`:
  - `AbstractSlotMath#init` 依 `RtpType.RTP_3` 選 buy free reel strip。
  - `AbstractSlotMath` 有 lastSymbols / scatter 固定與 re-spin 類邏輯。
  - `ec1d9ca`、`d9beaa4`、`bfeaadc` 都集中在 RTP_3 / scatter / buyFreeWinInfo。
- `sph-math`:
  - `SPHMathFactory#boughtFreeSpinBet`、`haveFreeWin`、`processGameSpinResult` 會組 buy free / free game result。

Senior / Owner 價值:

- Result contract: buy free 不是只改 RTP_3 輪帶，還會影響 `RoundResult`、`FreeBetResult`、front-end display。
- State transition: base game -> free game / buy free path。
- Testability: debug / custom RNG / simulate 如何讓 feature 可驗證。

Step 3 成本:

- 中。需要補 runtime caller / front-end contract 才能講完整 result。

履歷 / 面試邊界:

- 可作面試案例，不建議單獨放履歷；併入 grouped `*-math` bullet。

### 4. `jackpot-symbol-hit-and-prize-scaling`

中文名稱: Jackpot symbol hit / prize scaling flow

建議: 後續候選

代表 repo:

- `sph-math`
- `sdt-math`
- `slc-math`

已確認 code / history:

- `sph-math#checkJackpot` 有 jackpot hit、payload、balance function、jackpot reward list。
- `sdt-math#checkJackpot` 有 maxBet / fixedMultiBet / nowBet scaling 與 jackpot balance。
- `sph-math` Nick commits 明確有 JP 調整、JP log、JP simulation。
- `math-core` commits 有 jackpot sdt describe / jackpot JP_NU。

Senior / Owner 價值:

- Prize correctness / scaling 很接近 money correctness。
- 可以講 max bet / current bet / jackpot balance / reward list 的 contract。

Step 3 成本:

- 中高。需要定位 jackpot balance provider / runtime callback，否則只能講 math side。

履歷 / 面試邊界:

- 可面試講 math side prize scaling；不說完整 jackpot system owner。

### 5. `special-wild-feature-state-transform`

中文名稱: Special Wild / symbol state transform flow

建議: 後續補充

代表 repo:

- `sfm-math`
- `slc-math`

已確認 code / history:

- `sfm-math#AbstractSlotMath` 有 `changeWildState`、`handleSpecialWildType`、`addExtraDataForWilds`、wild family / extraData 類邏輯。
- `slc-math` 有 `LuckyCloverTracker` 與 clover feature。

Senior / Owner 價值:

- 適合講 game feature state transform、盤面狀態、extraData / front-end contract。
- 但比 fixedMultiBet / RTP / jackpot 更偏單遊戲 domain。

Step 3 成本:

- 高。`sfm AbstractSlotMath` 很厚，需要逐段拆 feature trigger、symbols transform、result contract。

履歷 / 面試邊界:

- 補充案例即可，不列第一批。

## 7. 第一條 Step 3 建議範圍

此 Step 3 建議已於 2026-05-20 完成，原始起手式為:

```text
antplay *-math fixed-multi-bet-currency-math-core-compatibility Step 3
```

建議 Step 3 寫入:

```text
projects/antplay/star-math/flows/fixed-multi-bet-currency-math-core-compatibility/
  flow.md
  career-interview.md
  materials/
    evidence.md
    decision-notes.md
    interview.md
    claim-boundary.md
```

Step 3 需掃:

- `math-core/src/main/java/com/ps/math/core/SlotMath.java`
- `math-core/src/main/java/com/ps/math/core/vo/DebugBetVO.java`
- `math-core/src/main/java/com/ps/math/core/vo/AbstractBetResult.java`
- `sdt-math/src/main/java/com/ps/math/sdt/game/AbstractSlotMath.java`
- `sdt-math/src/main/java/com/ps/math/sdt/factory/SDTMathFactory.java`
- `sdt-math/src/main/java/com/ps/math/sdt/vo/SlotBetResult.java`
- `sfm-math/src/main/java/com/ps/math/sfm/game/AbstractSlotMath.java`
- `sfm-math/src/main/java/com/ps/math/sfm/service/SFMOperatorService.java`
- `slc-math/src/main/java/com/ps/math/slc/game/AbstractSlotMath.java`
- path-specific commits: `math-core` fixedMultiBet / DebugBetVO commits、`sdt-math 146e256`、`sfm-math 697302b / 82b8cbc / af41db6`

Step 3 不應做:

- 不平均掃 71 個 repo。
- 不把 `setl-math` dirty worktree 內容當成 evidence。
- 不直接更新 05 / 08。
- 不宣稱已確認最新 remote。

## 8. Step 2 結論

`*-math` 第一條代表 flow 選:

```text
fixed-multi-bet-currency-math-core-compatibility
```

下一步:

```text
antplay *-math special-wild-feature-state-transform Step 5
```

2026-05-20 更新：`fixed-multi-bet-currency-math-core-compatibility` 已完成 Step 5，材料位於 `flows/fixed-multi-bet-currency-math-core-compatibility/`。Step 2 排序仍保留作候選依據；下一條做 Rank 2 `rtp-reel-strip-simulation-validation`。

2026-05-20 更新：`rtp-reel-strip-simulation-validation` 已完成 Step 5，材料位於 `flows/rtp-reel-strip-simulation-validation/`。本 flow 只強化 `*-math` grouped bullet，不直接更新 05 / 08；後續已回 Step 2 ranking 做 Rank 3。

2026-05-21 更新：`buy-free-scatter-rtp3-result-contract` 已完成 Step 5，材料位於 `flows/buy-free-scatter-rtp3-result-contract/`。本 flow 補上 `spn-math` RTP_3 / buy free / scatter direct evidence 與 game-api runtime caller，已轉成 pricing / routing / result contract consistency 面試 case，並完成單條 flow claim gate；下一步回 Step 2 ranking 做 Rank 4 `jackpot-symbol-hit-and-prize-scaling`。

2026-05-21 更新：`jackpot-symbol-hit-and-prize-scaling` 已完成 Step 3，材料位於 `flows/jackpot-symbol-hit-and-prize-scaling/`。本 flow 補上 `sph-math` 三顆 JP symbol hit、`sdt-math` / `slc-math` wild + JackpotFlip + fixedMultiBet scaling、`math-core` JackpotReward contract；當時只作 Step 3 code-backed 初版，不直接更新 05 / 08。後續已於同日完成 Step 4。

2026-05-21 更新：`jackpot-symbol-hit-and-prize-scaling` 已完成 Step 4，補讀 `antplay-slot-game-api` 的 SDT callback registrar、`JackpotService` result amount / force respin / balance reduce / jackpot record path，已轉成 jackpot result contract consistency 面試 case；下一步做 Step 5 claim gate。

2026-05-21 更新：`jackpot-symbol-hit-and-prize-scaling` 已完成 Step 5，單條 flow claim gate 結論是只強化 `*-math` grouped bullet，不單獨新增 jackpot owner 履歷 claim，不直接更新 05 / 08。後續已回 Rank 5 做 `special-wild-feature-state-transform Step 3`。

2026-05-21 更新：`special-wild-feature-state-transform` 已完成 Step 4，材料位於 `flows/special-wild-feature-state-transform/`。本 flow 以 `sfm-math` Special Wild parent / child transform、`extraData` result contract、`acac921 找不到父 wild 前端卡` direct evidence 為主，`slc-math` LuckyClover 只作 code-backed 對照；已加入 `04-interview-casebook.md`，不直接更新 05 / 08。下一步做 Step 5。
