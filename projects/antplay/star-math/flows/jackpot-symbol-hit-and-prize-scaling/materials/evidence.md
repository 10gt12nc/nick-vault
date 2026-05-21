# Evidence - Jackpot Symbol Hit / Prize Scaling

## 0. 掃描紀錄

- 日期: 2026-05-21
- 任務: `antplay *-math jackpot-symbol-hit-and-prize-scaling Step 3`
- 掃描深度: Level 2 Flow 深掃
- Vault branch: `main`
- Source repos: 只讀

## 1. 已重讀

KB:

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`

Vault:

- `projects/antplay/README.md`
- `projects/antplay/star-math/README.md`
- `projects/antplay/star-math/step1-candidate-flows.md`
- `projects/antplay/star-math/step2-flow-comparison.md`
- `projects/antplay/star-math/contribution-claim-consolidation.md`
- `projects/antplay/star-math/flows/rtp-reel-strip-simulation-validation/flow.md`

Source:

- `/Users/nick/Git/antplay/sph-math`
- `/Users/nick/Git/antplay/sdt-math`
- `/Users/nick/Git/antplay/slc-math`
- `/Users/nick/Git/antplay/math-core`

## 2. Source Repo 最新性

依 KB 規則，本輪先嘗試 fetch remote refs；內網 remote 不可達，fetch 失敗一次後停止重試，改用本地 refs / 本地工作樹保守分析。本檔不記錄內網 URL、IP 或敏感 remote 細節。

| Repo | Branch | Local HEAD | Local upstream ref | ahead / behind | Dirty | 遠端最新性 |
| --- | --- | --- | --- | --- | ---: | --- |
| `sph-math` | `master` | `17996c5` | `origin/master` = `17996c5` | `0 / 0` | 0 | fetch 失敗，依本地 refs |
| `sdt-math` | `master` | `146e256` | `origin/master` = `146e256` | `0 / 0` | 0 | fetch 失敗，依本地 refs |
| `slc-math` | `master` | `1d8a137` | `origin/master` = `1d8a137` | `0 / 0` | 0 | fetch 失敗，依本地 refs |
| `math-core` | `master` | `7f1533b` | `origin/master` = `7f1533b` | `0 / 0` | 0 | fetch 失敗，依本地 refs |

## 3. 主要 Code Path

### `sph-math`

- `src/main/java/com/ps/math/sph/game/P21008SlotMath.java`
  - `afterCheck` 在 free game 後呼叫 `checkJackpot`。
- `src/main/java/com/ps/math/sph/game/AbstractSlotMath.java`
  - `init` 初始化 `jackpotRewardList`。
  - `checkJackpot` 掃盤面 701~704 類 symbol，任一類型達 3 顆則 hit。
  - 使用 `lineBet / maxLineBet` 計算 jackpot multiplier。
  - 使用 payload `agentId|winSymbol|currency` 呼叫 `SPHOperatorService.getJackpotBalance`。
  - 使用 `RoundingMode.DOWN` 取整，寫入 `AbstractBetResult.JackpotReward`。
- `src/main/java/com/ps/math/sph/service/SPHOperatorService.java`
  - `registerJackpotBalance(Function<String, BigDecimal>)`。
  - `getJackpotBalance(String payload)`，本地 test 未註冊 function 時回 0。
- `src/main/java/com/ps/math/sph/factory/SPHMathFactory.java`
  - `processGameSpinResult` 彙總 `jackpotRewardList` amount 並加入 `betTotalWin`。
- `src/main/java/com/ps/math/sph/constant/SlotSymbolTable.java`
  - W1~W4 對應 jackpot Grand / Major / Minor / Min。
- `src/main/java/com/ps/math/sph/game/P21008SlotMathTestNew.java`
  - 統計 Jackpot hit / hit rate / total win。

### `sdt-math`

- `src/main/java/com/ps/math/sdt/game/AbstractSlotMath.java`
  - `init` 帶入 `fixedMultiBet` 與 `getTotalBet(..., currency, fixedMultiBet)`。
  - `checkJackpot` 先找 `FW`，再用 `GameSetting.WILD_RATE` 進 jackpot。
  - `JackpotFlip.plan()` 回傳 `winSymbol` 與前端翻牌 deck。
  - `maxBet = maxLineBet * maxFixedMultiBet`。
  - `nowBet = lineBet * fixedMultiBet`。
  - `jackpotAmount = getJackpotBalance(payload) * (nowBet / maxBet)`。
- `src/main/java/com/ps/math/sdt/game/JackpotFlip.java`
  - 建立 Min / Minor / Major / Grand 的翻牌 deck。
  - 透過 `CUM_PROB` 決定中獎獎種。
- `src/main/java/com/ps/math/sdt/factory/SDTMathFactory.java`
  - `haveMinJackpot`、`haveMinorJackpot`、`haveMajorJackpot`、`haveGrandJackpot` debug helpers。
  - `haveMinJackpotInFreeSpin` 驗證 free spin 中 jackpot。
  - `convertRngDataByFixedMultiBet` 對應不同 fixedMultiBet 的 debug RNG。
  - `processGameSpinResult` 把 `jackpotRewardList` 傳入 free spin input。
- `src/main/java/com/ps/math/sdt/service/SDTOperatorService.java`
  - jackpot balance function registration / callback。
- `src/main/java/com/ps/math/sdt/constant/GameSetting.java`
  - `NAMES_INT` 對應 `Symbol.JP_4`、`JP_3`、`JP_2`、`JP_1`。
  - `WILD_RATE` 控制進 jackpot 機率。

### `slc-math`

- `src/main/java/com/ps/math/slc/game/AbstractSlotMath.java`
  - 與 `sdt-math` 類似：FW hit、`JackpotFlip`、`maxBet / nowBet` scaling、payload、reward list。
- `src/main/java/com/ps/math/slc/factory/SLCMathFactory.java`
  - 有 Min / Minor / Major / Grand debug helpers。
- `src/main/java/com/ps/math/slc/service/SLCOperatorService.java`
  - jackpot balance function registration / callback。

### `math-core`

- `src/main/java/com/ps/math/core/vo/AbstractBetResult.java`
  - `jackpotRewardList`。
  - `JackpotReward.jackpotType` / `jackpotAmount`。
- `src/main/java/com/ps/math/core/constant/Symbol.java`
  - `JP_1 = 701`、`JP_2 = 702`、`JP_3 = 703`、`JP_4 = 704`。
- `src/main/java/com/ps/math/core/SlotMath.java`
  - `getTotalBet` overloads，與 fixedMultiBet / currency contract 相關。

## 4. Commit Evidence

### `sph-math`

Nick / `10gt12nc` path-specific commits:

- `1fad502` `鳳凰轉生 JP log`
- `498c8fd` `鳳凰轉生 JP調整+觸發log、初版印分布柱狀圖`
- `5e62cbb` `鳳凰轉生 模擬機率 初版 (JP)`
- `eb86f85` `鳳凰轉生 3顆得ＪＰ（初版）`

判斷: `sph-math` jackpot path 有明確 direct evidence，可標 `真實開發過 + code-backed`。

### `sdt-math`

Nick / `10gt12nc` path-specific commits:

- `146e256` `fixedMultiBet 給前端`
- `82243c1` `JP 測試`
- `d1da7e7` `改 jp 定義`
- `aa45b21` `jackpotType、測試工具`
- `5f1df8c` `jackpotType、測試工具`
- `b8642e3` `前端 Jackpot 測試工具`
- `8272580` `jp 測試結果log`
- `005285f` `feat: jackpotType, jackpotDataArray`
- `5d87141` `fix: fixedMulit with math-core && do Jackpot`

判斷: `sdt-math` jackpot / fixedMultiBet scaling / debug helper 有明確 direct evidence，可標 `真實開發過 + code-backed`。

### `slc-math`

Nick / `10gt12nc` path-specific commits:

- `966de18` `debugBet`
- `2c09920` `微調`
- `b9304f4` `SYMBOLS`
- `a6c0e48` `SDT 改名 SLC`

判斷: `slc-math` 可作同型 code-backed 補充；本 Step 不把 SLC jackpot 深掃結果寫成強主 claim。

### `math-core`

Nick / `10gt12nc` path-specific commits:

- `2f2ed08` `fix: jackpot sdt describe`
- `5327a19` `feat: jackpot sdt`
- `792882a` `feat: jackpot JP_NU`
- `1fcbeaa` `feat: jackpot Symbol`

判斷: `math-core` 提供 shared contract / symbol evidence，可支撐 jackpot result contract 但不代表完整 jackpot platform owner。

## 5. 已確認 / 推測 / 待確認

已確認:

- SPH 以 701~704 jackpot symbols 三顆以上作 hit 判斷。
- SDT / SLC 以 FW + `WILD_RATE` + `JackpotFlip` 決定 jackpot type。
- SDT / SLC jackpot scaling 有納入 `fixedMultiBet`。
- math-core 有 `JackpotReward` result contract。
- factory 會將 `jackpotRewardList` 中的 amount 彙總進 `betTotalWin`。

推測:

- runtime 會在 production 註冊 `jackpotBalanceFn`，讓 math module 可查真正 jackpot balance。
- jackpot provider / caller 使用相同 `agentId|jackpotType|currency` payload contract。

待確認:

- game-api / runtime caller 實際在哪裡註冊 `registerJackpotBalance`。
- jackpot balance callback 失敗時，production 是告警、拒絕、還是以 0 處理。
- free game 中 jackpot reward list 是否存在漏加或重複加風險。
- settlement / front-end 是否同意 `RoundingMode.DOWN`。
- 701~704 在 provider / front-end 的 Min / Minor / Major / Grand 對應是否完全一致。

## 6. 本 Step 不做

- 不更新 `05-resume-master-zh.md` / `08-application-autobiography-zh.md`。
- 不宣稱完整 jackpot pool / wallet / settlement owner。
- 不平均掃全部 71 個 `*-math` repo。
- 不修改公司 source repo。
