# Evidence

## 0. 掃描紀錄

- 日期: 2026-05-21
- 任務: `antplay *-math special-wild-feature-state-transform Step 3`
- 掃描深度: Level 2 Flow 深掃
- Vault branch: `main`
- Source repo: 只讀，未修改公司專案
- 結論: `sfm-math` 作主樣本，`slc-math` 作 feature state 對照，`math-core` 作 symbol contract 參考。

## 1. 已重讀

KB:

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/CONVENTIONS.md`

Vault:

- `projects/antplay/README.md`
- `projects/antplay/star-math/README.md`
- `projects/antplay/star-math/step1-candidate-flows.md`
- `projects/antplay/star-math/step2-flow-comparison.md`
- `projects/antplay/star-math/contribution-claim-consolidation.md`

## 2. Source Repo 最新性

依 KB 規則，本輪先嘗試 `git fetch --all --prune`。三個來源 repo fetch 皆失敗一次，判斷為內網 GitLab / remote 不可達；依規則不反覆重試，改用本地 refs / 本地工作樹保守分析。本檔不記錄內網 URL、IP 或敏感 remote 細節。

| Repo | Branch | Local HEAD | Local upstream ref | ahead / behind | Dirty | 遠端最新性 |
| --- | --- | --- | --- | --- | ---: | --- |
| `sfm-math` | `master` | `ea458d5` | `origin/master = ea458d5` | `0 / 0` | 0 | fetch 失敗，依本地 refs |
| `slc-math` | `master` | `1d8a137` | `origin/master = 1d8a137` | `0 / 0` | 0 | fetch 失敗，依本地 refs |
| `math-core` | `master` | `7f1533b` | `origin/master = 7f1533b` | `0 / 0` | 0 | fetch 失敗，依本地 refs |

## 3. 掃描範圍

`sfm-math`:

- `src/main/java/com/ps/math/sfm/game/AbstractSlotMath.java`
- `src/main/java/com/ps/math/sfm/game/P21008SlotMath.java`
- `src/main/java/com/ps/math/sfm/factory/SFMMathFactory.java`
- `src/main/java/com/ps/math/sfm/vo/ExtraData.java`
- `src/main/java/com/ps/math/sfm/vo/RoundResult.java`
- `src/main/java/com/ps/math/sfm/bo/SlotSpinResultTemp.java`
- `src/main/java/com/ps/math/sfm/constant/SlotSymbolTable.java`

`slc-math`:

- `src/main/java/com/ps/math/slc/game/clover/LuckyCloverTracker.java`
- `src/main/java/com/ps/math/slc/game/P21014SlotMath.java`
- `src/main/java/com/ps/math/slc/factory/SLCMathFactory.java`

`math-core`:

- `src/main/java/com/ps/math/core/constant/Symbol.java`

Git history:

- `git log --author='10gt12nc|Nick|nick'` on SFM special wild / result contract paths.
- `git show` on key SFM commits.
- `git log --author='10gt12nc|Nick|nick'` on SLC LuckyClover paths.
- full path-specific SLC log for comparison.

## 4. `sfm-math` code evidence

### 4.1 `AbstractSlotMath#spin`

確認單局 orchestration:

- 建立 `RoundResultTemp`。
- 依 RNG / reel strip 初始化盤面。
- 呼叫 `mainCheck` / `afterCheck`。
- 將 `RoundResultTemp.extraData` 映射到 `RoundResult.extraData`。

### 4.2 `AbstractSlotMath#initTable`

確認 Special Wild 進入點:

- 產生 `symbols`、`originalSymbols`、`allSymbolCount`。
- 建立 `List<ExtraData> extraData`。
- `EGS_FREE_GAME_1` 使用 `Symbol.W2`。
- `EGS_FREE_GAME_2` 使用 `Symbol.W3`。
- 其他狀態使用 `Symbol.W1`。
- 將 `extraData` 放入第一個 round result temp。

### 4.3 `AbstractSlotMath#changeWildState`

確認核心 transform:

- 使用 `2 - i + 3 * j` 將盤面轉成 15 格 `wildState`。
- `markSpecialWilds` 找 parent wild，標成 `specialWildType * 10 + sequence`。
- `buildWildFamilyMap` 建 parent -> child count。
- `W2` 使用 reel-based `handleSpecialWildType`。
- `W1` / `W3` 使用 random-position `handleSpecialWildType`。
- `addExtraDataForWilds` 寫 `launchIndex` / `transformIndex`。
- `revertSpecialWildsToNormal` 將 family marker 轉回 `W=50`。
- `writeBackWildState` 寫回 `symbols`。

### 4.4 `P21008SlotMath#checkFreeGame`

確認 free game type contract:

- Scatter count 達門檻時設定 free spin。
- 若 `extraData` 為空，先補一筆 `ExtraData`。
- 以 random 50 / 50 寫入 `extraData[0].gameType`，決定 `EGS_FREE_GAME_1` 或 `EGS_FREE_GAME_2`。

### 4.5 `SFMMathFactory#processGameSpinResult`

確認 factory 使用 `extraData`:

- base spin 出現 FG 時，讀 `roundResult.extraData[0].gameType`。
- 將 `gameType` 帶入 free game `InputData.gameState`。
- 每個 `RoundResult` 都保留 `extraData` 回傳。

### 4.6 VO / symbol contract

- `ExtraData`: `gameType`、`launchIndex`、`transformIndex`。
- `RoundResult`: `List<ExtraData> extraData`。
- `SlotSymbolTable`: `W=50`、`W1=51`、`W2=52`、`W3=53`，W1 / W2 / W3 是特殊 wild 類 symbol，算分前會收斂成一般 W。
- `math-core Symbol`: 同步存在 `W`、`W1`、`W2`、`W3`、`FW`、`B1`、`SPIN_PLUS_ONE`、`F1_GOLD` 等 constants。

## 5. `sfm-math` commit evidence

Path-specific Nick / `10gt12nc` evidence:

| Commit | Message | 判斷 |
| --- | --- | --- |
| `acac921` | `找不到父 wild 前端卡` | 強 evidence。修正 parent wild 找不到時的 fallback，避免前端動畫卡住 / 錯誤 mapping。 |
| `412ad07` | `特殊符號 間隔` | 強 evidence。特殊符號間隔 / strip / test / optimizer 相關調整，和 symbol feature 分布有關。 |
| `15ba947` | `初始盤 originalSymbols` | 中高 evidence。保留原始盤面，支援 transform 前後對照。 |
| `697302b` / `af41db6` | `fix: fixedMulit with math core` | 背景 evidence。證明 `sfm-math` 是 Nick direct evidence 強 module。 |
| `e4210aa` / `3b343ad` / `57ec4ef` | `購買免費局容易中` | 背景 evidence。和 free game / feature 調整相關，但不是本 flow 主 evidence。 |

`acac921` diff 重點:

- `findReelOfParent` 找不到 parent 時由預設 `0` 改成 `-1`。
- 呼叫端遇到 `-1` 直接 `continue`。
- 解讀: 不讓 missing parent 被誤當成 reel 0，避免前端 display contract 進入錯誤狀態。

## 6. `slc-math` comparison evidence

`LuckyCloverTracker`:

- 追 sticky board state。
- `F1_GOLD...F6_GOLD` 類 green symbols 會 lock。
- `FW` 轉成 gold 並帶 coin value。
- `B1` 轉成 blue，value 取 board sum。
- `SPIN_PLUS_ONE` 增加 free spin。
- `applySpin` 會略過 locked positions，並產出 board snapshot。

`P21014SlotMath`:

- `seedLuckyCloverTracker` 在 free-game trigger 建立 tracker。
- `extraCheck` 依 game state 進入 LuckyClover spin。
- `handleLuckyCloverSpin` 使用 tracker 更新盤面狀態、`flagWins` snapshot 與 free spin gain。

`SLCMathFactory`:

- selected reel set 為 LuckyClover 時建立 tracker。
- 每個 free spin 後套用 `luckyClover.applySpin(roundResult.getSymbols())`。
- final result 會帶 `flagWins`。

SLC Nick / `10gt12nc` direct commits on related paths:

- `b05e60b 初始化拿 SlotReelStripTableNew`
- `2c09920 微調`
- `b9304f4 SYMBOLS`
- `a6c0e48 SDT 改名 SLC`

判斷:

- 這些 commits 可證明 `slc-math` 是 Nick 有 direct evidence 的 module。
- 但 LuckyClover 相關 path full log 有多筆其他作者 / wip commits；目前不把 LuckyClover 寫成 Nick 主導，只作 code-backed 對照。

## 7. 已確認 / 推測 / 待確認

已確認:

- SFM Special Wild 會在 `initTable` 內依 game state 選 W1 / W2 / W3。
- Transform 會產生 family marker，最後收斂成 `W=50`。
- `extraData` 同時支援前端動畫與 free game state routing。
- `acac921` 是 parent wild 找不到導致前端卡住的 direct bugfix evidence。
- SLC LuckyClover 是另一種 stateful feature transform，可作比較。

推測:

- W1 / W2 / W3 的 exact game design 來自 GDD，code 可看出規則，但本輪未讀 GDD。
- 第一 reel exclusion / child count range 是遊戲設計規則，本輪只依 code 判斷。

待確認:

- 前端實際如何消費 `extraData.launchIndex` / `transformIndex`。
- runtime / game-api 是否保存足夠 debug replay 材料。
- GDD / QA / certification 對 W1 / W2 / W3 規則的正式描述。
- 是否有 production issue / ticket 可支撐 `acac921` 的 incident context。

## 8. 本輪未做

- 未掃 71 個 `*-math` repo。
- 未掃前端 repo。
- 未掃 game-api runtime caller。
- 未讀 GDD / certification 文件。
- 未更新 `05-resume-master-zh.md` 或 `08-application-autobiography-zh.md`。
