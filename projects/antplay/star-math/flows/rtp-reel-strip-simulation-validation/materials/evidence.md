# Evidence - RTP / 輪帶模擬與驗證

## 0. 本次掃描紀錄

- 日期: 2026-05-20
- 掃描深度: Level 2 Flow 深掃
- Vault branch: `main`
- Source repo policy: 公司 / 來源 repo 只讀；未改 source repo。
- Remote policy: 依 Nick 指示，內網 GitLab / remote 不可達時不反覆重試；本輪沿用 Step 1 / Step 2 的 fetch 失敗紀錄，改用本地 refs / 本地工作樹保守分析。

## 1. Source Repo 狀態

| Repo | Branch | Local HEAD | Local upstream ref | Working tree | 遠端最新性 |
| --- | --- | --- | --- | --- | --- |
| `sph-math` | `master` | `17996c5` | `origin/master` = `17996c5` | clean | 本輪未重試 fetch；依本地 refs |
| `spn-math` | `master` | `73f32d4` | `origin/master` = `73f32d4` | clean | 本輪未重試 fetch；依本地 refs |
| `sfm-math` | `master` | `ea458d5` | `origin/master` = `ea458d5` | clean | 本輪未重試 fetch；只作補充 |

本文件不記錄內網 URL、IP 或敏感 remote 細節。

## 2. 已讀 KB / Vault

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/CONVENTIONS.md`
- `projects/antplay/README.md`
- `projects/antplay/star-math/README.md`
- `projects/antplay/star-math/step1-candidate-flows.md`
- `projects/antplay/star-math/step2-flow-comparison.md`
- `projects/antplay/star-math/contribution-claim-consolidation.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/06-todo.md`

既有文件狀態:

- Step 1 / Step 2 可沿用。
- `contribution-claim-consolidation.md` 可沿用，但它是 Career Track rolling grouped claim，不取代本 flow Step 3。
- `fixed-multi-bet-currency-math-core-compatibility` 已完成 Step 5，本 flow 是 Step 2 Rank 2 的下一條。

## 3. 已讀 Source Code

主樣本 `sph-math`:

- `src/main/java/com/ps/math/sph/game/reelStripOptimizer/MainTest.java`
- `src/main/java/com/ps/math/sph/game/reelStripOptimizer/Simulate.java`
- `src/main/java/com/ps/math/sph/game/reelStripOptimizer/RTPConstants.java`
- `src/main/java/com/ps/math/sph/game/reelStripOptimizer/GenerateByRatio.java`
- `src/main/java/com/ps/math/sph/game/reelStripOptimizer/ProcessGameSpin.java`
- `src/main/java/com/ps/math/sph/game/reelStripOptimizer/Ratio_RTP_1.java`
- `src/main/java/com/ps/math/sph/game/reelStripOptimizer/Ratio_RTP_2.java`
- `src/main/java/com/ps/math/sph/game/reelStripOptimizer/Ratio_RTP_3.java`
- `src/main/java/com/ps/math/sph/constant/SlotReelStripTable.java`
- `src/main/java/com/ps/math/sph/game/AbstractSlotMath.java`

對照 `spn-math`:

- `src/main/java/com/ps/math/spn/game/reelStripOptimizer/MainTest.java`
- `src/main/java/com/ps/math/spn/game/reelStripOptimizer/Simulate.java`
- `src/main/java/com/ps/math/spn/game/reelStripOptimizer/RTPConstants.java`
- `src/main/java/com/ps/math/spn/game/reelStripOptimizer/Ratio_RTP_3.java`
- `src/main/java/com/ps/math/spn/game/AbstractSlotMath.java`
- `src/main/java/com/ps/math/spn/factory/SPNMathFactory.java`

## 4. Commit Evidence

`sph-math` Nick / `10gt12nc` relevant commits:

- `498c8fd` `鳳凰轉生 JP調整+觸發log、初版印分布柱狀圖`
  - touched `SlotReelStripTable`、`SPHMathFactory`、`AbstractSlotMath`、`P21008SlotMathTestNew`、`RTPConstants`、`Simulate`、`SparklineUtil`。
- `5e62cbb` `鳳凰轉生 模擬機率 初版 (JP)`
  - touched `RoundResultTemp`、`SlotReelStripTable`、`SPHMathFactory`、`AbstractSlotMath`、`P21008SlotMathTestNew`、`Ratio_RTP_1`、`Simulate`、`RoundResult`。
- `1fad502` `鳳凰轉生 JP log`
  - touched `SlotReelStripTable`、`P21008SlotMathTestNew`、`MainTest`、`RTPConstants`、`Simulate`。

`spn-math` Nick / `10gt12nc` relevant commits:

- `ec1d9ca` `神偷怪盜  RTP_3＃ ＆ buyFreeWinInfo HAVE_3_SCATTER_WIN`
  - touched `SlotReelStripTable`、`P21008SlotMathTestNew`、`MainTest`、`Ratio_RTP_3`、`Simulate`。
- `d9beaa4` `神偷怪盜  RTP_3 調整中 重置lastSymbols`
  - touched `SlotReelStripTable`、`SPNMathFactory`、`AbstractSlotMath`、`P21008SlotMathTestNew`、`Simulate`。
- `bfeaadc` `神偷怪盜  RTP_3 調整中 01`
  - touched `SlotReelStripTable`、`SlotSymbolTable`、`P21008SlotMathTestNew`、`Ratio_RTP_3`、`Simulate`。

## 5. 已確認事實

- `MainTest` 設定 `reelLength`、`rounds`、`attempts`、`lineBet` 與 expect RTP，並呼叫 `RTP_1_Run` / `RTP_2_Run` / `RTP_3_Run`。
- `RTPConstants` 定義 Base RTP、Base FG trigger、Free RTP、Jackpot hit rate 與誤差上下界。
- `GenerateByRatio` 依符號比例產生 5 條輪帶，並對 scatter / special symbols 做最小間距控制。
- `Simulate` 會把 candidate reels 放到 `SlotReelStripTable.RTP_REEL_STRIP_OPTIMIZER`。
- `AbstractSlotMath#init` 會依 `flagRTP` 選 `RTP_1`、`RTP_2`、`RTP_3` 或 `RTP_REEL_STRIP_OPTIMIZER` 對應的 strip table。
- `simulateBaseRTP` 統計一般局 total bet / total win / Base RTP。
- `simulateBaseFGTrigger` 統計免費局觸發率。
- `simulateFree` 統計免費局總贏分與 Free RTP。
- `sph-math` 額外統計 Jackpot 701 / 702 / 703 / 704 hit rate，並和 target bound 比較。
- `spn-math` 的 `AbstractSlotMath` 與 `SPNMathFactory` 有 `lastSymbols` state，相關 commit message 明確提到重置。

## 6. 合理推論

- 這條 validation flow 是上線前調整輪帶 / RTP 的開發期工具，不是 production runtime API。
- 其 Senior value 在於保證 simulation path 和 runtime `spin` path 一致，而不是只用公式估算。
- 若正式用於 release gate，最好要補 seed、run config、simulation output snapshot 與 GDD target 連結；本輪未看到正式 certification 文件。

## 7. 未掃 / 待確認

- 未掃正式 GDD / math spec / certification 文件。
- 未掃 game-api / slot runtime 如何載入 math module 與使用 `SlotBetResult`。
- 未掃前端 result 顯示 contract。
- 未逐檔逐行掃 71 個 `*-math` repo。
- 未做 Level 3 逐 commit diff 全考古；本輪只看代表 commits 的 stat 與主 code path。
- `sfm-math` 本輪只確認 branch / HEAD，未深挖 RTP simulation path。

## 8. Claim Boundary Evidence

可支撐:

- Nick / `10gt12nc` 在 `sph-math`、`spn-math` 有 RTP / reel strip / simulation / JP / scatter / buy free 相關 direct commits。
- 可以保守說參與 slot math module 的 RTP / reel strip / simulation validation 維護。

不可支撐:

- 不足以說 Nick 主導完整 RTP 策略。
- 不足以說 Nick 是完整 game math model owner。
- 不足以說 Nick 負責 certification / full simulator platform。
