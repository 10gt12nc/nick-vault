# *-math Contribution Claim Consolidation

日期: 2026-05-20

## 結論

`*-math` 群組有明確 Nick / `10gt12nc` direct evidence。71 個 `*-math` repo 中，本輪掃到 49 個有 Nick / `10gt12nc` commits；其中 `sph-math`、`spn-math`、`sfm-math`、`setl-math`、`sdt-math`、`slc-math` direct commits 特別多，可保守列為真實開發 / 維護過的 slot math module。

履歷可保守寫:

> 參與多個 AntPlay slot math module 維護與驗證，處理 RTP / reel strip、debug bet、fixedMultiBet、buy free / purchasable free spin、jackpot / symbol、currency 與模擬驗證調整。

不要寫:

- 主導全部 `*-math` module。
- 主導完整遊戲數學模型 / RTP 策略 / 派彩設計。
- 主導完整 math release / simulator / certification platform。

## Evidence Summary

| 類別 | 判斷 | Evidence |
| --- | --- | --- |
| 群組掃描 | rolling / grouped | 掃描 `/Users/nick/Git/antplay/*-math` 共 71 個 repo |
| direct evidence | 真實開發過 + code-backed | 49 個 repo 有 Nick / `10gt12nc` commits |
| strongest modules | 真實開發過 + code-backed | `sph-math:125`、`spn-math:116`、`sfm-math:80`、`setl-math:69`、`sdt-math:68`、`slc-math:66` |
| light-touch modules | 局部真實維護 | 多數 repo 是 1-5 筆，常見為 debug amount、purchasable free spin、RTP 110 reel strip、VND currency 等小調整 |
| no direct evidence | 不放 Nick claim | 22 個 repo 未掃到 Nick / `10gt12nc` author |

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

- 參與多個 slot math module 的 RTP / reel strip / debug bet / fixedMultiBet / jackpot / buy free 維護與驗證。
- 參與 math-core contract 和 module 實作的相容性調整，處理 debug / front-end result / simulation 類支援。

可面試講:

- Slot math module 和 `math-core` contract 如何互相影響。
- RTP / reel strip / optimizer / simulation 的迭代方式。
- debugBet / fixedMultiBet / currency / buy free 改動如何跨 core 和 game module 對齊。
- 為什麼只能說參與維護 / 驗證，不能說完整數學策略 owner。

不可誇大:

- 不說全部 `*-math` 都是 Nick 主導。
- 不說設計完整遊戲機率模型。
- 不說完整上線 certified math owner。
- 不說改善 RTP / hit rate 數字，除非後續補 GDD / validation / metric。

## 05 / 08 更新口徑

建議補入履歷 / 自傳的保守句:

> 參與 AntPlay 多個 slot math module 維護與驗證，處理 RTP / reel strip、debug bet、fixedMultiBet、buy free / purchasable free spin、jackpot / symbol、currency 與模擬驗證調整。

## Suggested Next

`*-math` 已有很強 career evidence，第一條代表 flow `fixed-multi-bet-currency-math-core-compatibility` 已完成 Step 5，第二條代表 flow `rtp-reel-strip-simulation-validation` 已完成 Step 3；下一步繼續同一條 flow 做 Step 4，不要平均深挖 71 個 repo。

```text
antplay *-math rtp-reel-strip-simulation-validation Step 4
```
