# Evidence - fixedMultiBet / Currency / Math-Core Compatibility

## Scan Scope

- Vault branch: `main`
- Vault path: `projects/antplay/star-math`
- Source root: `/Users/nick/Git/antplay`
- Depth: Level 2 deep scan + Step 5 claim gate
- 本輪沒有重複 retry 內網 GitLab fetch；依本地 refs / local branch / local log 判斷。
- 2026-05-20 Step 4 補充：重讀 Step 3 `flow.md`、`career-interview.md`、`materials/evidence.md`、`materials/interview.md`、`materials/decision-notes.md`、`materials/claim-boundary.md`；未新增公司 repo 掃描，不改公司 repo。
- 2026-05-20 Step 5 補充：重讀 Step 3/4 flow package、project README、Step 1/2、inventory、todo 與 interview casebook；本輪只做單條 flow claim gate，不更新 05 / 08。

## Source Repo Status

| Repo | Local branch / HEAD | Remote status | Dirty | 掃描用途 |
| --- | --- | --- | --- | --- |
| `math-core` | `master` / `7f1533b` | local 與 `origin/master` 先前檢查一致；本輪未重複 fetch | clean | core contract、DebugBetVO、result base |
| `sdt-math` | `master` / `146e256` | 內網 remote 未確認最新；依本地 refs | clean | fixedMultiBet + currency 最完整代表 module |
| `sfm-math` | `master` / `ea458d5` | 內網 remote 未確認最新；依本地 refs | clean | currency multiplier + math-core 相容修正代表 |
| `slc-math` | `master` / `1d8a137` | 內網 remote 未確認最新；依本地 refs | clean | fixedMultiBet + jackpot scaling 旁證 |
| `setl-math` | local dirty | 未採用 | dirty | 本輪不採為正式 evidence |

## Direct Commit Evidence

### `math-core`

| Commit | Author | Evidence |
| --- | --- | --- |
| `274c1ea fixedMultiBet` | `10gt12nc` | `DebugBetVO` 增加 fixedMultiBet |
| `a45d6a5 debugBet VO 加 fixedMultiBet` | `10gt12nc` | `SlotMathOperatorService` debug / fixedMultiBet 相容修正 |
| `d08109b debugBet VO 加 currency` | `10gt12nc` | `DebugBetVO` 增加 currency |
| `ef7ff33 fix debugBet VO` | `10gt12nc` | debug VO 修正 |

### `sdt-math`

| Commit | Author | Evidence |
| --- | --- | --- |
| `146e256 fixedMultiBet 給前端` | `10gt12nc` | `SDTMathFactory`、`P21014SlotMathTestNew`、`SlotBetResult` |
| `dcda532 debugBet VO 加 currency` | `10gt12nc` | `SDTOperatorService` |
| `2d89d9f debugBet VO 加 fixedMultiBet` | `10gt12nc` | `SDTOperatorService` |
| `7e65d15 debugBet 倍數` | `10gt12nc` | debug bet 倍數修正 |
| `f6f5add debug 預設使用 usd` | `10gt12nc` | default currency / factory / service / simulate |

### `sfm-math`

| Commit | Author | Evidence |
| --- | --- | --- |
| `697302b fix: fixedMulit with math core` | `10gt12nc` | `AbstractSlotMath`、`SFMOperatorService`、tests / simulation |
| `82b8cbc fix: fixedMulit with math core` | `10gt12nc` | `SFMOperatorService` 與 test 調整 |
| `af41db6 fix: fixedMulit with math core` | `10gt12nc` | `AbstractSlotMath` 相容修正 |

### `slc-math`

| Commit | Author | Evidence |
| --- | --- | --- |
| `966de18 debugBet` | `10gt12nc` | `SLCOperatorService` debug bet |
| `2c09920 微調` | `10gt12nc` | `GameSetting`、`SLCMathFactory`、`AbstractSlotMath`、`SLCOperatorService` |

## Code Paths Read

### `math-core`

- `src/main/java/com/ps/math/core/SlotMath.java`
- `src/main/java/com/ps/math/core/SlotMathOperatorService.java`
- `src/main/java/com/ps/math/core/vo/DebugBetVO.java`
- `src/main/java/com/ps/math/core/vo/AbstractBetResult.java`

### `sdt-math`

- `src/main/java/com/ps/math/sdt/game/AbstractSlotMath.java`
- `src/main/java/com/ps/math/sdt/factory/SDTMathFactory.java`
- `src/main/java/com/ps/math/sdt/service/SDTOperatorService.java`
- `src/main/java/com/ps/math/sdt/bo/InputData.java`
- `src/main/java/com/ps/math/sdt/vo/SlotBetResult.java`

### `sfm-math`

- `src/main/java/com/ps/math/sfm/game/AbstractSlotMath.java`
- `src/main/java/com/ps/math/sfm/service/SFMOperatorService.java`
- `src/main/java/com/ps/math/sfm/bo/InputData.java`
- `src/main/java/com/ps/math/sfm/vo/SlotBetResult.java`

### `slc-math`

- `src/main/java/com/ps/math/slc/game/AbstractSlotMath.java`
- `src/main/java/com/ps/math/slc/vo/SlotBetResult.java`

## Confirmed

- `math-core` contract 採 default fallback，避免所有 module 被強制一次改完。
- `DebugBetVO` 已有 `fixedMultiBet` 與 `currency`。
- `sdt-math` 有完整 path：factory input -> currency multiplier -> totalBet -> jackpot scaling -> result fixedMultiBet。
- `sfm-math` 有 currency multiplier 與 math-core 相容調整，但 fixedMultiBet 落地程度與 `sdt` 不同。
- `slc-math` 有 `fixedMultiBet` 參與 totalBet 與 jackpot scaling，可作旁證。

## Inferred

- 上游 caller 應該是 game-api / runtime，但本輪未深掃上游呼叫端；只把它標成待確認。
- fixedMultiBet / currency 屬 money-like correctness，因為 totalBet / win / jackpot amount 會影響上游扣款、派彩與對帳語意。

## Not Scanned / To Confirm

- 沒有掃全部 71 個 `*-math` repo。
- 沒有驗證內網 GitLab 最新遠端。
- 沒有跑 Java tests。
- 沒有深掃上游 game-api caller。
- 沒有採用 dirty 的 `setl-math` 工作樹內容作正式 evidence。

## Step 4 Output Evidence

- `career-interview.md` 已改為 Step 4 面試主稿，包含 30 秒、2 分鐘、5 分鐘講法、Senior / Lead / Architect 追問與履歷句。
- `materials/interview.md` 已補完整追問答法、強回答句、弱回答避雷與 evidence-backed claim matrix。
- Step 4 沒有新增履歷 master / 自傳正式 claim；正式更新仍要等 Step 5 或 project-level contribution consolidation 回填。

## Step 5 Claim Gate Evidence

- Step 5 判定：可作為 `*-math` grouped 履歷 bullet 的強化 evidence。
- Step 5 判定：可作 Senior Backend 面試主案例，主軸是 contract compatibility、money-like correctness、multi-module rollout trade-off。
- Step 5 限制：不代表完整 `*-math` final consolidation；不代表全部 71 個 repo 已完成相同 flow 深掃。
- Step 5 限制：不直接更新 `05-resume-master-zh.md` / `08-application-autobiography-zh.md`，後續由 project-level consolidation / rolling resume package 回填。
