# Evidence - Buy Free / Scatter / RTP_3 Result Contract

## 0. 本次掃描紀錄

- 日期: 2026-05-21
- 掃描深度: Level 2 Flow 深掃
- Vault branch: `main`
- Source repo policy: 公司 / 來源 repo 只讀；未改 source repo。
- Remote policy: 已依 KB 嘗試 fetch remote refs；內網遠端不可達，本輪停止重試，依本地 refs / 本地工作樹保守分析。本文件不記錄內網 URL、IP 或敏感 remote 細節。

## 1. Source Repo 狀態

| Repo | Branch | Local HEAD | Local upstream ref | Working tree | 遠端最新性 |
| --- | --- | --- | --- | --- | --- |
| `spn-math` | `master` | `73f32d4` | `origin/master` = `73f32d4` | clean | fetch 失敗；依本地 refs |
| `sph-math` | `master` | `17996c5` | `origin/master` = `17996c5` | clean | fetch 失敗；依本地 refs |
| `sfm-math` | `master` | `ea458d5` | `origin/master` = `ea458d5` | clean | fetch 失敗；依本地 refs / 補充 |
| `antplay-slot-game-api` | `develop` | `079aa66` | `origin/develop` = `079aa66` | clean | fetch 失敗；依本地 refs / 上游補讀 |

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
- 已完成 flow: `fixed-multi-bet-currency-math-core-compatibility`、`rtp-reel-strip-simulation-validation`

既有文件狀態:

- Step 1 / Step 2 可沿用。
- 前兩條代表 flow 已完成 Step 5。
- 本 flow 是 Step 2 Rank 3，符合目前 KB 的下一步。

## 3. 已讀 Source Code

主樣本 `spn-math`:

- `src/main/java/com/ps/math/spn/service/SPNOperatorService.java`
- `src/main/java/com/ps/math/spn/factory/SPNMathFactory.java`
- `src/main/java/com/ps/math/spn/game/AbstractSlotMath.java`
- `src/main/java/com/ps/math/spn/game/P21008SlotMath.java`
- `src/main/java/com/ps/math/spn/constant/GameSetting.java`
- `src/main/java/com/ps/math/spn/constant/SlotReelStripTable.java`
- `src/main/java/com/ps/math/spn/config/SlotGameInitial.java`
- `src/main/java/com/ps/math/spn/bo/InputData.java`
- `src/main/java/com/ps/math/spn/bo/SlotSpinResultTemp.java`
- `src/main/java/com/ps/math/spn/bo/RoundResultTemp.java`
- `src/main/java/com/ps/math/spn/vo/SlotBetResult.java`
- `src/main/java/com/ps/math/spn/vo/RoundResult.java`
- `src/main/java/com/ps/math/spn/vo/FreeBetResult.java`
- `src/main/java/com/ps/math/spn/game/P21008SlotMathTestNew.java`

對照 `sph-math`:

- `src/main/java/com/ps/math/sph/service/SPHOperatorService.java`
- `src/main/java/com/ps/math/sph/factory/SPHMathFactory.java`
- `src/main/java/com/ps/math/sph/game/AbstractSlotMath.java`
- `src/main/java/com/ps/math/sph/game/P21008SlotMath.java`
- `src/main/java/com/ps/math/sph/constant/GameSetting.java`
- `src/main/java/com/ps/math/sph/constant/SlotReelStripTable.java`
- `src/main/java/com/ps/math/sph/game/P21008SlotMathTestNew.java`

補充 `sfm-math`:

- `SFMMathFactory`、`SFMOperatorService`、`AbstractSlotMath`、`SlotReelStripTable`、`GameSetting` 的 buy free / `RTP_3` pattern，未深入展開。

上游補讀 `antplay-slot-game-api`:

- `src/main/java/com/ps/domain/game/slot/facade/GameFacade.java`
- `src/main/java/com/ps/domain/game/slot/facade/GameFlowFacade.java`
- `src/main/java/com/ps/domain/game/slot/facade/SlotMathFacade.java`

## 4. Commit Evidence

`spn-math` Nick / `10gt12nc` relevant commits:

- `ec1d9ca` `神偷怪盜  RTP_3＃ ＆ buyFreeWinInfo HAVE_3_SCATTER_WIN`
  - touched `SlotReelStripTable`、`P21008SlotMathTestNew`、`MainTest`、`Ratio_RTP_3`、`Simulate`。
  - `buyFreeWinInfo` 補 odds 推算與 `HAVE_3_SCATTER_WIN` log。
- `d9beaa4` `神偷怪盜  RTP_3 調整中 重置lastSymbols`
  - touched `SPNMathFactory`、`AbstractSlotMath`、`SlotReelStripTable`、`Simulate`。
  - 在多個 debug / have free loop 前重置 `lastSymbols`，避免 state 污染。
- `bfeaadc` `神偷怪盜  RTP_3 調整中 01`
  - touched `SlotReelStripTable`、`SlotSymbolTable`、`P21008SlotMathTestNew`、`Ratio_RTP_3`、`Simulate`。
- `da350ca` `神偷怪盜 HAVE_3_SCATTER_WIN   59`
  - touched `GameSetting`，把 `HAVE_3_SCATTER_WIN` odds 調為 59。

補充 history:

- `74e73bf`、`6ed13fd`、`72aaa2f`、`6b05353` 等集中在 RTP_3 / RTP / FG / reel strip 調整。

## 5. 已確認事實

- `SPNOperatorService#freeSpinBet` 會呼叫 `SPNMathFactory#boughtFreeSpinBet`。
- `SPNMathFactory#boughtFreeSpinBet` 建立 `InputData`，設定 `flagRTP=RTP_3`、`expectRTP=10700`、`currency`。
- `SPNMathFactory#haveFreeWin` 也使用 `flagRTP=RTP_3`，並在 loop 前重置 `lastSymbols`。
- `AbstractSlotMath#init` 依 `flagRTP=RTP_3` 選 `config.getStripTableContainer_buyFreeGame()`。
- `P21008SlotMath` 初始化時把 buy free game strip 對到 `SlotReelStripTable.RTP_3`。
- `SlotReelStripTable.RTP_3` 有 Base game 與 Free game 輪帶。
- `AbstractSlotMath` 的 base / free game check 會使用 `lastSymbols` 保存、回寫 scatter / wild，並影響 re-spin / free game state。
- `RoundResult` 含 `freeScatterPay`、`freeSpin`、`freeTotalWin`、`freeBetResults`。
- `FreeBetResult` 含 `totalWin` 與 free round list。
- `GameSetting.FREE_SPIN_TYPE_LIST` 定義 `HAVE_3_SCATTER_WIN` 與 odds。
- `antplay-slot-game-api GameFacade` 在 `boughtFreeSpin=true` 時會呼叫 `SlotMathFacade.freeSpinBet`。
- `GameFlowFacade#afterBet` 會把 `isBoughtFreeSpin`、`boughtFreeSpinOdds`、`boughtFreeSpinType` 放進 result JSON，並調整 totalBet 語意。

## 6. 合理推論

- `RTP_3` 是 buy free runtime 的專用 routing key，不只是 simulation label。
- `HAVE_3_SCATTER_WIN` odds 需要和 simulation / math validation 對齊，否則 buy free pricing 與長期 RTP 會偏。
- `lastSymbols` reset 是 SPN 這條 flow 的重要風險收斂點，因為 scatter / wild state 會跨 spin 影響結果。
- game-api 的 totalBet / odds result JSON 是 result contract 的一部分；完整 money correctness 仍需追 wallet / bet record DB。

## 7. 未掃 / 待確認

- 未掃 game-api Controller route。
- 未掃 bet record DB schema / mapper / migration。
- 未掃 wallet mutation / settlement / rollback 全鏈路。
- 未掃前端如何顯示 `freeBetResults`。
- 未掃 darkpool 報表如何分 normal bet 與 buy free。
- 未掃正式 GDD / math spec / validation report。
- 未逐檔逐行掃全部 71 個 `*-math` repo。
- `sfm-math` 只作補充 pattern，不作本 flow 主 evidence。

## 8. Claim Boundary Evidence

可支撐:

- Nick / `10gt12nc` 在 `spn-math` 有 RTP_3、buy free odds、scatter / `lastSymbols` 相關 direct commits。
- 可說參與 / 維護 slot math module 的 buy free / scatter / RTP_3 result contract 類調整。
- 可面試講跨 game-api caller、math routing、result contract 的 code-backed flow。

不可支撐:

- 不足以說 Nick 主導完整 buy free 商業設計。
- 不足以說 Nick 主導完整 RTP 策略或遊戲數學模型。
- 不足以說 Nick 負責完整 wallet / settlement / darkpool。
- 不足以說所有 `*-math` repo 的 buy free flow 都已完整深掃。
