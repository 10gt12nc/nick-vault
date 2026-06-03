# *-math Contribution Claim Consolidation

日期: 2026-05-20
Refresh: 2026-05-21（五條代表 flow 全部 Step 5 後的 project-level claim refresh）
Workspace alignment: 2026-06-03（對齊 `/Users/nick/Git/antplay/math-workspace` 最新開發 / 驗證 KB）

## 結論

`*-math` 群組有明確 Nick / `10gt12nc` direct evidence。71 個 `*-math` repo 中，本輪掃到 49 個有 Nick / `10gt12nc` commits；其中 `sph-math`、`spn-math`、`sfm-math`、`setl-math`、`sdt-math`、`slc-math` direct commits 特別多，可保守列為真實開發 / 維護過的 slot math module。

2026-05-21 refresh 後，`*-math` Step 2 本批五條代表 flow 已全部完成 Step 5：

- `fixed-multi-bet-currency-math-core-compatibility`
- `rtp-reel-strip-simulation-validation`
- `buy-free-scatter-rtp3-result-contract`
- `jackpot-symbol-hit-and-prize-scaling`
- `special-wild-feature-state-transform`

這讓 `*-math` 的 project-level claim 從「rolling / grouped」升級為「本批代表 flows 已回填的 refreshed grouped consolidation」。它仍不是 71 個 repo 全量 Level 3 final consolidation，也不是完整遊戲數學 owner 結論。

2026-06-03 對齊 `math-workspace` 後，結論不變但驗證口徑更清楚：`projects/antplay/star-math/flows/*` 是履歷 / 面試 flow 主體；`math-workspace` 是 cross-math KB / validation workflow supporting source。它補強的是 GDD / 規格書、相似成熟底包、固定盤面驗算、result contract、RTP / reel strip / optimizer / final valid 交互驗證、child repo commit 與 workspace KB 回填方法，不反向升級為完整 math platform、完整 simulator / certification 或完整 RTP 策略 owner。

履歷可保守寫:

> 參與 AntPlay slot math core 與多個 slot math module 維護與驗證，處理 SlotMath contract、debug bet、fixedMultiBet、currency、RTP / reel strip、buy free / purchasable free spin、jackpot / symbol、特殊 feature result contract 與模擬驗證調整。

不要寫:

- 主導全部 `*-math` module。
- 不得寫成主導完整遊戲數學模型 / RTP 策略 / 派彩設計。
- 不得寫成主導完整 math release / simulator / certification platform。
- 不得寫成主導完整 jackpot pool、buy free、Special Wild 或單一遊戲完整 feature owner。

## Evidence Summary

| 類別 | 判斷 | Evidence |
| --- | --- | --- |
| 群組掃描 | refreshed / grouped | 掃描 `/Users/nick/Git/antplay/*-math` 共 71 個 repo；2026-05-21 已吸收五條代表 flow Step 5 evidence |
| direct evidence | 真實開發過 + code-backed | 49 個 repo 有 Nick / `10gt12nc` commits |
| strongest modules | 真實開發過 + code-backed | `sph-math:125`、`spn-math:116`、`sfm-math:80`、`setl-math:69`、`sdt-math:68`、`slc-math:66` |
| light-touch modules | 局部真實維護 | 多數 repo 是 1-5 筆，常見為 debug amount、purchasable free spin、RTP 110 reel strip、VND currency 等小調整 |
| no direct evidence | 不放 Nick claim | 22 個 repo 未掃到 Nick / `10gt12nc` author |

## Representative Flow Refresh

| Flow | Step 5 結論 | 對 project-level claim 的影響 | 不可誇大 |
| --- | --- | --- | --- |
| `fixed-multi-bet-currency-math-core-compatibility` | 可作 Senior Backend 主案例 | 強化 SlotMath contract、fixedMultiBet、currency、debug result、money-like correctness | 不說完整 math platform / RTP owner |
| `rtp-reel-strip-simulation-validation` | 可作高差異化 validation case | 強化 RTP / reel strip / simulation validation | 不說設計完整 RTP 策略或 certification |
| `buy-free-scatter-rtp3-result-contract` | 可面試講 feature result contract | 強化 buy free / scatter / RTP_3 / result contract | 不說完整 buy free owner 或 wallet / settlement owner |
| `jackpot-symbol-hit-and-prize-scaling` | 只併入 grouped bullet | 強化 jackpot / symbol、fixedMultiBet、prize scaling、result contract | 不說 jackpot pool / provider pool / settlement owner |
| `special-wild-feature-state-transform` | 只併入 grouped bullet | 強化特殊 feature state transform / result contract | 不說主導完整 Special Wild、LuckyClover 或單一遊戲 feature |

## 2026-05-21 Source Refresh

本輪為 contribution claim refresh，實際重讀：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/antplay/star-math/README.md`
- `projects/antplay/star-math/step1-candidate-flows.md`
- `projects/antplay/star-math/step2-flow-comparison.md`
- 五條代表 flow 的 `flow.md`、`career-interview.md` 與 claim / evidence 摘要
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`
- `senior-owner-playbook/06-todo.md`

代表 source repo 本輪嘗試 fetch：

| Repo | Branch | Local HEAD | Local upstream ref | ahead / behind | 遠端最新性 |
| --- | --- | --- | --- | --- | --- |
| `math-core` | `master` | `7f1533b` | `origin/master` = `7f1533b` | `0 / 0` | fetch 失敗，依本地 refs |
| `sdt-math` | `master` | `146e256` | `origin/master` = `146e256` | `0 / 0` | fetch 失敗，依本地 refs |
| `sfm-math` | `master` | `ea458d5` | `origin/master` = `ea458d5` | `0 / 0` | fetch 失敗，依本地 refs |
| `slc-math` | `master` | `1d8a137` | `origin/master` = `1d8a137` | `0 / 0` | fetch 失敗，依本地 refs |
| `sph-math` | `master` | `17996c5` | `origin/master` = `17996c5` | `0 / 0` | fetch 失敗，依本地 refs |
| `spn-math` | `master` | `73f32d4` | `origin/master` = `73f32d4` | `0 / 0` | fetch 失敗，依本地 refs |
| `antplay-slot-game-api` | `develop` | `079aa66` | `origin/develop` = `079aa66` | `0 / 0` | fetch 失敗，依本地 refs；只作 runtime caller context |

本輪沒有修改 source repo。fetch 失敗代表未確認最新遠端；依 KB 不反覆重試，也不把內網 remote 細節寫入 vault。

## 2026-06-03 math-workspace Alignment

已重新 fetch `/Users/nick/Git/antplay/math-workspace`，local `main` 與 `origin/main` 均為 `43d520dc9954a946a2a671e0f82f147a3dc3aabb`，ahead / behind `0 / 0`；本輪只讀 workspace，未 pull / checkout / merge / 改外部 repo。

已重讀：

- `README.md`
- `INDEX.md`
- `docs/INDEX.md`
- `docs/AI-OPERATING-PROTOCOL.md`
- `docs/projects/NEW-GAME-EVALUATION.md`
- `docs/relations/all-math-code-kb-audit.md`

可採 / 轉譯到 `star-math` 的重點：

- `math-workspace` 的新遊戲流程可作五條 representative flows 的驗證背景：先讀 GDD / 規格書與外層 KB，再選 3-5 個相似成熟底包，接著用固定盤面 / debug RNG / TestNew / result JSON 驗玩法，最後才跑 RTP / reel strip / optimizer / final valid。
- `fixed-multi-bet-currency-math-core-compatibility` 要對齊 `math-workspace` 的 contract 檢查：core method、module input、operator service、calculation、result output、debug path 與 jackpot scaling 必須吃同一組下注上下文。
- `rtp-reel-strip-simulation-validation` 要對齊 workspace 的兩段驗證：optimizer / `Simulate` 看 Base RTP、FG trigger、Free multiplier；production final valid / `TestNew` 看 `valid -> doRound -> processGameSpinResult` 的真實 result。兩段差太多時先查 code flow，不先調 ratio。
- `buy-free-scatter-rtp3-result-contract` 要對齊 workspace 的 routing / result contract 檢查：buy free odds、`RTP_3` routing、free result、scatter state、`RoundResult` / `FreeBetResult` / result JSON 必須一致。
- `jackpot-symbol-hit-and-prize-scaling` 要對齊 workspace 的分層驗證：先用固定盤面強制 trigger 驗 jackpot show / type / reward list，再到 factory / wrapper 驗 jackpot amount 是否進最終 result；不能用偶發 JP 或 debug-only state 當 production evidence。
- `special-wild-feature-state-transform` 要對齊 workspace 的 transform 檢查：停輪原盤、算分盤、ExtraData / parent-child position、FG state 都要能說清楚；只看金額會漏前端演出與 result contract 風險。

不可採 / 不升級的重點：

- `math-workspace` 的 `docs/agent-roles/` 與 Karpathy guidelines 是 guardrail / checkpoint，不是 `nick-vault` 的新 flow Step，也不取代五條 `star-math` flow。
- `sdt-lab-math` 的安全補強、debug gate、fixed board property gate、單元測試與重構節奏只能作 lab / testing reference；不能自動套用到所有 `*-math` module，也不能寫成 Nick 建立完整 math testing platform。
- `math-workspace` 仍不是 production runtime service；正式履歷主成果仍回到 `math-core` / `*-math` source repo direct evidence 與本檔五條代表 flow。

## Source Scan Record

來源 repo:

```text
/Users/nick/Git/antplay/*-math
```

遠端狀態:

- 已對 71 個 `*-math` repo 嘗試 `git fetch --all --prune`。
- 部分 repo fetch 成功，部分 repo remote refs 未能更新。
- 本輪 consolidation 依本機既有 refs / 成功更新的 refs 判斷；fetch 失敗的 repo 不宣稱已看最新 remote。
- source repo 自查發現 `setl-math`、`szs-math` 本機工作樹已有未提交修改；本輪未改公司 repo，這兩個 repo 的 claim 只依 git history / 已讀檔案保守判斷。

本次掃描範圍:

- `git shortlog -sne --all`
- `git log --all --author='10gt12nc|Nick|nick'`
- per repo branch / HEAD / file count / commit count
- key direct evidence modules: `sph-math`、`spn-math`、`sdt-math`、`sfm-math`
- key commits: SPH JP / histogram / trigger log、SPN RTP_3 / buy free / scatter、SDT fixedMultiBet / JP test、SFM fixedMultiBet with math-core

## Direct Evidence Inventory

強 evidence module:

| Module | Nick / `10gt12nc` commits | 可用口徑 |
| --- | ---: | --- |
| `sph-math` | 125 | 鳳凰轉生、JP / trigger log、RTP / reel strip / simulation / histogram 類調整 |
| `spn-math` | 116 | 神偷怪盜、RTP_3、buyFreeWinInfo、scatter win、payline / reel strip / optimizer |
| `sfm-math` | 80 | fixedMultiBet with math-core、operator service / abstract math / optimizer 類維護 |
| `setl-math` | 69 | VND currency、調整類 commits；需後續單 repo 深挖 |
| `sdt-math` | 68 | fixedMultiBet、JP test、slot result / factory / round result 類調整 |
| `slc-math` | 66 | debugBet、reel strip table、初始化 / 不產輪帶等調整 |

局部 direct evidence module:

```text
salc-math:4, sam-math:3, saw-math:1, sba-math:1, sbc-math:2,
sbh-math:1, sbo-math:1, sbs-math:1, scf-math:2, sch-math:2,
scir-math:4, scl-math:1, scp-math:3, scs-math:4, scy-math:3,
sdbs-math:4, sdw-math:1, sed-math:1, sft-math:2, sge-math:4,
sgw-math:1, shc-math:2, shf-math:5, sjb-math:2, sjcs-math:4,
sjj-math:3, sjt-math:1, sml-math:4, smm-math:1, smy-math:1,
snd-math:4, spd-math:1, spm-math:1, spp-math:2, spr-math:1,
spt-math:3, sqft-math:4, srf-math:2, sss-math:4, stp-math:3,
stt-math:2, swb-math:3, szs-math:1
```

未掃到 direct evidence module:

```text
dpszs-math, s7-math, saa-math, sbq-math, sbr-math, sco-math,
sct-math, sdi-math, sep-math, sftt-math, sgb-math, sgd-math,
sgm-math, sgs-math, slw-math, smw-math, soe-math, srg-math,
srm-math, stc-math, swv-math, szb-math
```

## Important Evidence

### SPH / SPN / SDT / SFM

已確認重要 diff:

- `sph-math`: `498c8fd` 調整 JP、觸發 log、初版分布柱狀圖，涉及 `SlotReelStripTable`、`SPHMathFactory`、`AbstractSlotMath`、test、optimizer、simulate 與 sparkline utils。
- `spn-math`: `ec1d9ca` 調整 RTP_3 / buyFreeWinInfo / scatter win，涉及 `SlotReelStripTable`、test、optimizer ratio、simulate。
- `sdt-math`: `146e256` fixedMultiBet 給前端，涉及 factory、test、slot bet result；`82243c1` JP test。
- `sfm-math`: `697302b` fixedMultiBet with math-core，涉及 abstract math、test、optimizer、operator service。

可放履歷:

- 參與 AntPlay slot math core 與多個 slot math module 的 RTP / reel strip / debug bet / fixedMultiBet / currency / jackpot / buy free / feature result contract 維護與驗證。
- 參與 math-core contract 和 module 實作的相容性調整，處理 debug / front-end result / simulation 類支援。

可面試講:

- Slot math module 和 `math-core` contract 如何互相影響。
- RTP / reel strip / optimizer / simulation 的迭代方式。
- debugBet / fixedMultiBet / currency / buy free 改動如何跨 core 和 game module 對齊。
- jackpot / symbol / feature state transform 如何影響 result contract 與前端顯示。
- 為什麼只能說參與維護 / 驗證，不能說完整數學策略 owner。

不可誇大:

- 不說全部 `*-math` 都是 Nick 主導。
- 不說設計完整遊戲機率模型。
- 不說完整上線 certified math owner。
- 不說改善 RTP / hit rate 數字，除非後續補 GDD / validation / metric。

## 05 / 08 更新口徑

建議補入履歷 / 自傳的保守句:

> 參與 AntPlay slot math core 與多個 slot math module 維護與驗證，處理 SlotMath contract、debug bet、fixedMultiBet、currency、RTP / reel strip、buy free / purchasable free spin、jackpot / symbol、特殊 feature result contract 與模擬驗證調整。

## Suggested Next

`*-math` contribution claim consolidation 已 refresh 完成，五條代表 flow 都已回填到 grouped 履歷口徑。`antplay-slot-game-api`、`antplay-slot-game-job`、`antplay-slot-admin-api` 與 AntPlay system map v1 後續也已收斂；目前沒有預設下一步。
