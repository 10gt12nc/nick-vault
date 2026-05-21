# Jackpot Symbol Hit / Prize Scaling Flow

## 0. 閱讀定位

- Domain / Project: `antplay *-math`
- Flow slug: `jackpot-symbol-hit-and-prize-scaling`
- 完成狀態: Step 5 / 單條 flow claim gate 已完成
- 掃描深度: Level 2 Flow 深掃
- 證據層級: `真實開發過 + code-backed` / `專案存在 / code-backed`
- 主要 source repo: `/Users/nick/Git/antplay/sph-math`
- 對照 source repo: `/Users/nick/Git/antplay/sdt-math`
- 補充 source repo: `/Users/nick/Git/antplay/slc-math`、`/Users/nick/Git/antplay/math-core`、`/Users/nick/Git/antplay/antplay-slot-game-api`
- 本 flow 類型: slot math module 內的 jackpot hit 判斷、獎池金額縮放與 result contract，不是完整 jackpot pool / wallet / settlement 系統。

這條 flow 的價值在於：Jackpot 在玩家體感上像「大獎」，在工程風險上像 money correctness。即使 math module 不負責真正扣款、派彩入帳或 jackpot pool 累積，它仍負責把盤面 / 觸發條件、獎池 balance、下注倍率與回傳結果組成一致的 prize contract。

## 1. 白話導讀

Slot math module 每次 spin 會先產生盤面、判一般線獎、判 free game，再依遊戲特色判 Jackpot。這條 flow 主要有兩種型態：

1. `sph-math`：盤面上出現至少三個指定 JP symbol，例如 701 / 702 / 703 / 704，就視為對應獎種 hit。
2. `sdt-math` / `slc-math`：盤面先出現 wild / FW，再用 `WILD_RATE` 機率進 jackpot，接著用 `JackpotFlip` 決定 Min / Minor / Major / Grand 與前端翻牌表演 deck。

hit 之後，math module 不是自己算 jackpot pool，而是用 `agentId|jackpotType|currency` 組 payload，透過 `OperatorService#getJackpotBalance` 拿獎池 balance。最後依目前下注與最大下注的比例做縮放：

```text
jackpotAmount = jackpotBalance * (nowBet / maxBet)
```

`sph-math` 的 `nowBet` 主要看 `lineBet`；`sdt-math` / `slc-math` 則把 `lineBet * fixedMultiBet` 視為目前下注，最大下注也要乘上最大 `fixedMultiBet`。算出來的金額會無條件捨去小數，放入 `jackpotRewardList`，再由 factory 彙總到 `betTotalWin` 與回傳結果。

## 2. 初中階 Code 分層對照

```text
Route / API:
antplay-slot-game-api/src/main/java/com/ps/domain/game/register/SdtJackpotCallbackRegistrar.java
antplay-slot-game-api/src/main/java/com/ps/domain/game/slot/facade/GameFacade.java

Contract / Core:
math-core/src/main/java/com/ps/math/core/vo/AbstractBetResult.java
math-core/src/main/java/com/ps/math/core/constant/Symbol.java

Operator Service:
sph-math/src/main/java/com/ps/math/sph/service/SPHOperatorService.java
sdt-math/src/main/java/com/ps/math/sdt/service/SDTOperatorService.java
slc-math/src/main/java/com/ps/math/slc/service/SLCOperatorService.java

Factory / Result Assembly:
sph-math/src/main/java/com/ps/math/sph/factory/SPHMathFactory.java
sdt-math/src/main/java/com/ps/math/sdt/factory/SDTMathFactory.java
slc-math/src/main/java/com/ps/math/slc/factory/SLCMathFactory.java

Domain Core:
sph-math/src/main/java/com/ps/math/sph/game/AbstractSlotMath.java
sph-math/src/main/java/com/ps/math/sph/game/P21008SlotMath.java
sdt-math/src/main/java/com/ps/math/sdt/game/AbstractSlotMath.java
sdt-math/src/main/java/com/ps/math/sdt/game/JackpotFlip.java
slc-math/src/main/java/com/ps/math/slc/game/AbstractSlotMath.java

Config / Symbol:
sph-math/src/main/java/com/ps/math/sph/constant/SlotSymbolTable.java
sdt-math/src/main/java/com/ps/math/sdt/constant/GameSetting.java
slc-math/src/main/java/com/ps/math/slc/constant/GameSetting.java

Debug / Simulation:
sph-math/src/main/java/com/ps/math/sph/game/P21008SlotMathTestNew.java
sdt-math/src/main/java/com/ps/math/sdt/game/P21014SlotMathTestNew.java
sdt-math/src/main/java/com/ps/math/sdt/factory/SDTMathFactory.java
```

DB / Redis / MQ 不在本 flow 範圍內；本 Step 未看到 math module 直接操作 DB / Redis / MQ。

Runtime 補讀後確認 `antplay-slot-game-api` 會處理 jackpot balance callback、dark-pool / force respin、jackpot record 與 balance reduce；但這屬於對接與結果處理 evidence，不代表 Nick 可 claim 完整 jackpot 平台 owner。

## 3. 最小架構圖

```mermaid
flowchart TD
  A["spin input: agentId / currency / lineBet / fixedMultiBet"] --> B["AbstractSlotMath#spin"]
  B --> C["盤面 / normal win / free game"]
  C --> D{"Jackpot enabled"}
  D -- "否" --> Z["result without jackpot reward"]
  D -- "是" --> E{"Hit 判斷"}
  E --> F["SPH: count JP701~704 symbols >= 3"]
  E --> G["SDT/SLC: FW hit + WILD_RATE + JackpotFlip"]
  F --> H["winSymbol: 701~704"]
  G --> H
  H --> I["payload: agentId|jackpotType|currency"]
  I --> J["OperatorService#getJackpotBalance"]
  J --> K["jackpotMultiplier = nowBet / maxBet"]
  K --> L["jackpotAmount = balance * multiplier"]
  L --> M["round down"]
  M --> N["jackpotRewardList"]
  N --> O["factory aggregates betTotalWin / result contract"]
```

## 4. 正常流程圖

```mermaid
sequenceDiagram
  participant Caller as Runtime / Debug caller
  participant Factory as MathFactory
  participant Math as AbstractSlotMath
  participant JP as checkJackpot
  participant Op as OperatorService
  participant Result as SlotBetResult

  Caller->>Factory: normalBet / debugBet with agentId, currency, lineBet, fixedMultiBet
  Factory->>Math: spin(agentId, InputData)
  Math->>Math: genRng / checkLine / checkFreeGame
  Math->>JP: afterCheck -> checkJackpot
  JP->>JP: 判斷 hit / winSymbol
  JP->>Op: getJackpotBalance("agentId|jackpotType|currency")
  Op-->>JP: jackpotBalance
  JP->>JP: jackpotAmount = balance * nowBet / maxBet
  JP->>Result: append JackpotReward
  Math-->>Factory: SlotBetResult with jackpotRewardList
  Factory->>Factory: sum jackpotAmount into betTotalWin
  Factory-->>Caller: result contract
```

## 5. 正常流程逐步說明

1. runtime 或 debug tool 呼叫 `MathFactory`，帶入 `agentId`、`currency`、`lineBet`，`sdt` / `slc` 額外帶 `fixedMultiBet`。
2. factory 建 `InputData`，初始化 `jackpotRewardList`，再呼叫 `math.spin(agentId, inputData)`。
3. `AbstractSlotMath#init` 將下注、幣別、RTP flag、game state、jackpot list 與 total bet 放進 `SlotSpinResultTemp`。
4. game-specific `P21008SlotMath#afterCheck` 或 `P21014SlotMath#afterCheck` 在 free game 判斷後，如果 `GameFeature.jackpotGame == 1` 就呼叫 `checkJackpot`。
5. `sph-math` 盤面逐格掃 symbol，計算 701 / 702 / 703 / 704 對應 symbol 數量，任一類型達 3 顆就命中。
6. `sdt-math` / `slc-math` 先看盤面是否有 `FW`，再用 `WILD_RATE` 決定是否進 jackpot；若進入，`JackpotFlip.plan()` 產生中獎獎種與前端翻牌 deck。
7. hit 後組出 payload：`agentId|winSymbol|currency`，交給對應 `OperatorService#getJackpotBalance`。
8. `OperatorService` 透過已註冊的 `jackpotBalanceFn` 取 balance；若本地 test 未註冊，回傳 0。
9. `sph-math` 用 `lineBet / maxLineBet` 當 jackpot multiplier；`sdt-math` / `slc-math` 用 `(lineBet * fixedMultiBet) / (maxLineBet * maxFixedMultiBet)`。
10. jackpot 金額以 `RoundingMode.DOWN` 取整數後放入 `AbstractBetResult.JackpotReward`。
11. factory 從 `spinResult.getJackpotRewardList()` 彙總 jackpot amount，加入 `betTotalWin`，並把 `jackpotRewardList` 放回 `SlotBetResult`。

## 6. 業務問題

這條 flow 解決的是「Jackpot 命中後，玩家拿到的獎池金額是否和下注倍率、幣別與獎種一致」。

它的風險不只在有沒有中獎，而是在這些地方：

- hit 條件是否對應該遊戲規格。
- jackpot type 是否和前端 / jackpot pool 使用同一組 701~704 語意。
- 取得的 balance 是否是正確 agent、獎種、幣別。
- `nowBet / maxBet` 是否跟該遊戲下注模型一致。
- 小數取整規則是否和 settlement / 前端顯示一致。
- `jackpotRewardList` 是否跟 `betTotalWin` 一起回傳，避免前端有獎池列表但總贏分漏加。

## 7. 系統位置

- 產品: AntPlay slot game math module
- Project: `*-math` grouped project
- 主樣本 module: `sph-math`
- 對照 module: `sdt-math`
- 補充 module: `slc-math`
- shared contract: `math-core` 的 `AbstractBetResult.JackpotReward` 與 `Symbol.JP_1~JP_4`
- 上游: game-api / runtime caller、jackpot balance provider，本 Step 已補讀 `antplay-slot-game-api` SDT callback registration 與 jackpot service
- 下游: front-end result display、bet record / settlement，本 Step 只補到 jackpot record / balance reduce path，未深掃完整 wallet / settlement

## 8. 資料狀態與 State Transition

| 狀態 | 來源 | 說明 |
| --- | --- | --- |
| `gameState` / `flagRTP` | `InputData` | 決定使用哪組輪帶 / RTP |
| symbols | `RoundResultTemp.symbols` | jackpot hit 的直接依據 |
| `winSymbol` | `checkJackpot` / `JackpotFlip` | 701~704 對應 jackpot type |
| payload | `agentId|winSymbol|currency` | 查 jackpot balance 的 contract |
| `jackpotBalance` | registered function | math module 外部提供，未註冊時本地 test 回 0 |
| `fixedMultiBet` | `InputData` | SDT / SLC jackpot scaling 的必要因素 |
| `jackpotRewardList` | `SlotSpinResultTemp` / `AbstractBetResult` | 回傳給 caller 的 jackpot reward contract |
| `betTotalWin` | factory aggregate | 一般 win / free win / jackpot amount 的合計 |

狀態轉移可以簡化成：

```text
spin input -> symbols -> hit decision -> jackpot type -> balance payload -> scaled jackpot amount -> reward list -> result total win
```

## 9. Transaction Boundary / Consistency

本 flow 在 math module 內沒有 DB transaction；它的 consistency 是 contract consistency：

- symbol table / jackpot type / `math-core` symbol 常數要一致。
- `OperatorService#getJackpotBalance` payload 格式要和外部 provider 一致。
- `jackpotRewardList` 裡的 `jackpotType` / `jackpotAmount` 要和 `RoundResult` 的 jackpot display 欄位一致。
- `betTotalWin` 要包含 jackpot amount，否則前端看到 reward list，但總贏分不含 jackpot。
- `fixedMultiBet` 必須同時影響 total bet、game state / reel strip、jackpot scaling 與 debug tool。

本 Step 已確認 `sdt-math` / `slc-math` 在 jackpot scaling 使用 `lineBet * fixedMultiBet`，並以 `maxLineBet * maxFixedMultiBet` 作最大下注基準；這和先前 `fixed-multi-bet-currency-math-core-compatibility` flow 可互相補強。

Step 4 補讀 `antplay-slot-game-api` 後，確認 runtime side 會：

- 用 `SdtJackpotCallbackRegistrar` 透過反射呼叫 `SDTOperatorService.registerJackpotBalance`。
- 將 payload 拆成 `agentId`、`jackpotType`、`currency`，再查 `JackpotService#getBalance(..., "sdt", ..., "cent")`。
- 在 `GameFacade` 取得 math result 後，用 `JackpotService#getResultJackpotAmountCentUnit` 累加 `jackpotRewardList`。
- 若啟用 dark-pool / jackpot control，會用 `isForceRespin` 比對 jackpot amount、dark-pool maxWin 與各 jackpot type balance。
- bet 完成後由 `jackpotSchemaProcess.setData` 將 reward list 分組、扣減 balance、寫入 jackpot record。

所以這條 flow 的 runtime consistency 不只是 math result 正確，還包含「callback 查池 -> math result -> force respin -> 扣池 / record」這段 contract 是否同一套 type / unit / currency。

## 10. Idempotency / Retry / Reconciliation

這條 flow 不處理 production retry；下注重送、入帳補償與 jackpot pool 對帳應在 runtime / wallet / settlement 層處理。

math side 能做到的是：

- 同一組 debug RNG / input 下，盤面與 hit 結果應可重現。
- Jackpot balance unavailable 時，不能默默產生錯誤大獎；本地 test path 回 0，production callback 失敗會回 null 再轉 0。
- `jackpotRewardList` 可作 result-level reconciliation，讓 caller 對照 `betTotalWin` 是否已包含 jackpot。
- debug helper 可強制 Min / Minor / Major / Grand，用來驗證 result contract，而不是只靠隨機命中。

## 11. Failure Window

| Failure | 影響 | Owner 觀點 |
| --- | --- | --- |
| jackpot symbol mapping 錯 | 命中錯獎種或前端顯示錯 | 701~704 要跨 math-core、module、provider 對齊 |
| hit 條件和 GDD 不一致 | hit rate / 體感錯 | SPH 的三顆 symbol 與 SDT/SLC 的 FW + 機率不能混成同一套規格 |
| balance callback 未註冊或失敗 | jackpot amount 變 0 | runtime 要有告警與 fallback 決策，不能只靠 math log |
| payload 格式錯 | 查錯 agent / 獎種 / 幣別 | payload contract 應由 provider / caller 做 integration test |
| maxBet / nowBet 算錯 | 下注倍率越高越容易錯付 | fixedMultiBet / lineBet / minBet 的語意要和 total bet 一致 |
| 小數處理不一致 | 前端 / settle / math 金額差一點 | `RoundingMode.DOWN` 要和 settlement 契約一致 |
| reward list 和 total win 不一致 | 前端有 jackpot，但總贏分漏加或重複加 | factory aggregate 必須明確測試 |
| free game jackpot list 傳遞錯 | BG / FG jackpot reward 漏接 | free spin input 應傳同一份 jackpot list 或明確合併 |
| callback 只註冊 SDT | 其他 game module 有 jackpot 但查不到 balance | Step 4 只確認 SDT registrar；SPH / SLC runtime registration 待確認 |
| game-api 遠端 fetch 失敗 | source 最新性仍受內網不可達限制 | 本 Step 確認本地 worktree clean、local HEAD 與 local origin ref 0/0；仍不宣稱已確認最新遠端 |
| force respin 例外被吞掉 | jackpot 超池仍可能放行 | `isForceRespin` catch 後回 false，是 owner 需要追問的風險 |

## 12. Senior / Owner Decision

可以拿這條 flow 講的 owner decision：

- Jackpot balance 的 source of truth 應由哪一層提供：math 只算命中與縮放，pool balance / 入帳不是 math owner。
- scaling 應以 line bet、total bet 還是 fixedMultiBet 後的 nowBet 為基準：不同遊戲不能硬套。
- 何時把 unavailable balance 視為 0，何時應 fail fast：本地 test 可以 0，production 需要觀測與告警。
- result contract 應該把 jackpot reward list 和 total win 都帶出，讓上游能核對。
- debug helper 應能強制各獎種，否則 jackpot case 很難靠隨機重現。

## 13. 面試 / 履歷邊界摘要

可面試講：

- 參與 slot math module 的 jackpot hit / prize scaling / result contract 維護。
- 能說清楚 jackpot symbol / FW trigger、jackpot type、balance callback、下注比例縮放、取整與 `jackpotRewardList` 如何串成完整 result。
- 能把 slot math 的 jackpot 問題翻成 backend 熟悉的 money correctness、contract consistency、observability 與 failure window。

履歷判斷：本 flow 可保守併入 `*-math` grouped bullet，作為 jackpot / symbol / fixedMultiBet result contract evidence：

> 參與 AntPlay 多個 slot math module 維護與驗證，處理 RTP / reel strip、debug bet、fixedMultiBet、buy free / purchasable free spin、jackpot / symbol、currency 與模擬驗證調整。

不可寫：

- 主導完整 jackpot platform / jackpot pool。
- 負責錢包入帳、settlement、provider pool 對帳。
- 保證所有 `*-math` jackpot module 都完整深掃。
- 設計完整遊戲數學模型或 jackpot 機率策略。

## 14. Step 3 結論

本 Step 3 已建立 `jackpot-symbol-hit-and-prize-scaling` 的 code-backed 初版 flow：

- `sph-math` 提供三顆 JP symbol hit 與 jackpot reward list 的主樣本。
- `sdt-math` 提供 `fixedMultiBet` jackpot scaling、`JackpotFlip`、debug helper 與 result contract 的主對照。
- `slc-math` 顯示同型 jackpot scaling pattern，可作補充 evidence。
- `math-core` 提供 shared `JackpotReward` contract 與 jackpot symbol 常數。

Step 3 當時留下的 Step 4 待補點，已在本輪補讀 `antplay-slot-game-api` 後收斂如下：

- runtime caller 是否註冊 jackpot balance function：已確認 SDT 由 `SdtJackpotCallbackRegistrar` 註冊 callback，SPH / SLC runtime registration 尚待確認。
- free game / BG jackpot reward list 是否有漏加或重複加風險：已確認 SDT / SLC math side 有 reward list 傳遞，SPH free-game 傳遞仍建議 Step 5 標成待回填。
- debug helper 是否足以覆蓋 Min / Minor / Major / Grand：可作面試追問，不作完整 test coverage claim。
- `jackpotRewardList` 與 `betTotalWin` 的 contract 是否能被面試講成完整 case：已可講，但邊界是 slot math result contract，不是完整 jackpot pool owner。

## 15. Step 4 面試 case 結論

這條 flow 已可轉成正式面試 case，主題是：

> Slot math jackpot result contract consistency：從 symbol / wild hit，到 balance callback、fixedMultiBet prize scaling、force respin、reward list、bet total win 與 jackpot record 的一致性。

3 分鐘講法：

我會把這條 jackpot flow 拆成兩層：math side 和 runtime side。Math side 負責命中判斷與金額縮放，例如 SPH 是盤面三顆 701~704 類 symbol 命中，SDT / SLC 是 FW 觸發後用 JackpotFlip 決定 Min / Minor / Major / Grand。金額不是 math 自己產生，而是用 `agentId|jackpotType|currency` 查 runtime 提供的 balance，再依 `nowBet / maxBet` 縮放，SDT / SLC 特別要把 `fixedMultiBet` 納入。

Runtime side 則要接住這個 contract。這次補讀到 game-api 的 SDT registrar 會把 callback 註冊進 math service，`JackpotService` 會從 result 的 `jackpotRewardList` 累加 jackpot amount，並在 dark-pool 控制下檢查 force respin，最後用 reward list 分組扣減 jackpot balance 與寫入 jackpot record。

所以我面試會強調：這不是單純「中獎給錢」，而是 high-risk result contract。要對齊 jackpot type、currency、單位、fixedMultiBet scaling、rounding、reward list / total win，以及 provider balance source。我的邊界也會講清楚：我可以講 math module jackpot / symbol / scaling / result contract 維護與驗證，不會說自己主導完整 jackpot pool、wallet 或 settlement。

Lead / Architect 追問可講：

- 如果 `registerJackpotBalance` 沒註冊，production 是回 0、fail fast 還是阻擋下注？本地 test 回 0，但 production 應有告警與政策。
- 若 `jackpotRewardList` 有金額但 `betTotalWin` 漏加，前端與 settle 會 drift；反過來重複加也會造成超付。
- `fixedMultiBet` 必須同時影響 total bet、reel strip、jackpot scaling 與 debug RNG，否則高倍數下注會錯付。
- Force respin 例外如果被吞掉並回 false，可能讓超池結果通過；這是 reliability / observability 風險。
- SDT registrar 已看到，SPH / SLC runtime registration 尚未確認，不能把所有 jackpot game 都說成已完整驗證。

履歷判斷：

- 本 Step 4 只強化面試 case 與 `*-math` grouped evidence。
- 不更新 `05` / `08`。
- 不單獨新增「jackpot 平台」履歷 bullet。

Step 4 當時留下的 Step 5 待辦，是做單條 flow claim gate，確認這條能不能、以及只能怎麼回填到 `*-math` project-level claim；本輪已在下節收斂。

## 16. Step 5 Claim Gate

單條 flow 結論：

- 可放履歷：可以作為 `*-math` grouped bullet 的強化 evidence，支撐「jackpot / symbol、fixedMultiBet、result contract、模擬驗證」這一組能力。
- 不建議單獨放履歷：不新增「Jackpot 平台 / 獎池系統」獨立 bullet，因為 wallet / settlement / jackpot pool / provider pool 對帳不是本 flow 的 direct owner evidence。
- 可面試講：可以講成 slot math jackpot result contract consistency，重點是 hit 判斷、balance callback、下注比例縮放、rounding、reward list、total win、force respin 與 jackpot record 的一致性。
- 不能誇大：不能說主導完整 jackpot platform、完整 RTP / odds 策略、全部 `*-math` jackpot module、完整 wallet / settlement / provider pool owner。

Evidence 分層：

| 範圍 | Step 5 判斷 |
| --- | --- |
| `sph-math` JP symbol hit / JP log / simulation | `真實開發過 + code-backed`，可作本 flow 主 direct evidence |
| `sdt-math` jackpot / fixedMultiBet scaling / debug helper | `真實開發過 + code-backed`，可作 fixedMultiBet jackpot scaling 主 direct evidence |
| `slc-math` 同型 jackpot scaling | `專案存在 / code-backed`，只作對照與補充，不升級主 claim |
| `math-core` JackpotReward / Symbol contract | `真實開發過 + code-backed`，可支撐 shared result contract |
| `antplay-slot-game-api` SDT callback / JackpotService / GameFacade | `專案存在 / code-backed`；本輪 path-specific log 只能支撐 runtime context，不把 jackpot registrar / service 寫成 Nick 直接開發 |
| SPH / SLC runtime callback registration | 待確認 |
| wallet / settlement / jackpot pool / provider reconciliation | 未深掃，不 claim |

回填到 `*-math` project-level consolidation 的方式：

> 參與 AntPlay 多個 slot math module 維護與驗證，處理 RTP / reel strip、debug bet、fixedMultiBet、buy free / purchasable free spin、jackpot / symbol、currency 與模擬驗證調整。

這句維持不變。Step 5 只讓「jackpot / symbol」這段更有 code-backed 面試材料，不把履歷寫得更滿。

05 / 08 判斷：

- 不直接更新 `05-resume-master-zh.md` / `08-application-autobiography-zh.md`。
- 若之後要更新 05 / 08，應由 `*-math` project-level consolidation 或 rolling resume package 統一吃這條 Step 5 結論。

本 flow 收斂狀態：

- `fixed-multi-bet-currency-math-core-compatibility`、`rtp-reel-strip-simulation-validation`、`buy-free-scatter-rtp3-result-contract`、`jackpot-symbol-hit-and-prize-scaling` 四條代表 flow 已完成 Step 5。
- 後續已回 Step 2 ranking 完成 Rank 5 `special-wild-feature-state-transform Step 4`；若繼續 `*-math`，下一步做該 flow Step 5。
