# *-math

`*-math` 是 AntPlay 多個 slot game math module 的群組 consolidation。因 repo 數量很多，本資料夾用 `star-math` 作安全路徑名稱，避免在檔名中使用 `*`。

## Status

| 項目 | 狀態 |
| --- | --- |
| contribution claim consolidation | 已完成 / refreshed grouped / 2026-05-21；本批五條代表 flows 已全 Step 5 並已回填 project-level claim |
| Flow Track | `fixed-multi-bet-currency-math-core-compatibility` Step 5 已完成；`rtp-reel-strip-simulation-validation` Step 5 已完成；`buy-free-scatter-rtp3-result-contract` Step 5 已完成；`jackpot-symbol-hit-and-prize-scaling` Step 5 已完成；`special-wild-feature-state-transform` Step 5 已完成 |
| math-workspace 對齊 | 2026-06-03 已重查 `/Users/nick/Git/antplay/math-workspace`；workspace 補強 GDD / 相似底包 / 固定盤面 / result contract / RTP 長測 / optimizer / final valid / KB 回填驗證鏈，作 supporting evidence，不取代本資料夾五條 flow |
| 履歷判斷 | 多個 module 有 Nick / `10gt12nc` direct commits；可保守放 slot math core / math module 維護、RTP / reel strip、debug / fixedMultiBet / currency、buy free、jackpot / symbol、特殊 feature result contract |
| 下一步 | 已收斂；沒有預設下一步 |

## Claim Boundary

可保守說:

- 參與 AntPlay slot math core 與多個 slot math module 的維護與驗證，處理 RTP / reel strip、debug bet、fixedMultiBet、currency、buy free / purchasable free spin、jackpot / symbol、特殊 feature result contract 類調整。
- 強 evidence module 包含 `sph-math`、`spn-math`、`sfm-math`、`setl-math`、`sdt-math`、`slc-math`。

不可誇大:

- 不寫主導全部 `*-math`。
- 不寫完整遊戲數學 / RTP 策略 owner。
- 不寫完整 math release / certification / simulator owner。
- 不寫完整 jackpot pool、buy free、Special Wild 或單一遊戲 feature owner。
- 不把 `math-workspace` 的 `sdt-lab-math` 安全 / 重構 / 單測 lab 經驗，直接升級成所有 `*-math` flow 的預設開發規則；它只能作測試設計與 guardrail 參考，正式 flow 仍以 GDD、相似成熟包、source code、commit history、固定盤面與長測證據為準。

## Files

- [flows/fixed-multi-bet-currency-math-core-compatibility/flow.md](/Users/nick/Git/nick/nick-vault/projects/antplay/star-math/flows/fixed-multi-bet-currency-math-core-compatibility/flow.md)
- [flows/fixed-multi-bet-currency-math-core-compatibility/career-interview.md](/Users/nick/Git/nick/nick-vault/projects/antplay/star-math/flows/fixed-multi-bet-currency-math-core-compatibility/career-interview.md)
- [flows/rtp-reel-strip-simulation-validation/flow.md](/Users/nick/Git/nick/nick-vault/projects/antplay/star-math/flows/rtp-reel-strip-simulation-validation/flow.md)
- [flows/rtp-reel-strip-simulation-validation/career-interview.md](/Users/nick/Git/nick/nick-vault/projects/antplay/star-math/flows/rtp-reel-strip-simulation-validation/career-interview.md)
- [flows/buy-free-scatter-rtp3-result-contract/flow.md](/Users/nick/Git/nick/nick-vault/projects/antplay/star-math/flows/buy-free-scatter-rtp3-result-contract/flow.md)
- [flows/buy-free-scatter-rtp3-result-contract/career-interview.md](/Users/nick/Git/nick/nick-vault/projects/antplay/star-math/flows/buy-free-scatter-rtp3-result-contract/career-interview.md)
- [flows/jackpot-symbol-hit-and-prize-scaling/flow.md](/Users/nick/Git/nick/nick-vault/projects/antplay/star-math/flows/jackpot-symbol-hit-and-prize-scaling/flow.md)
- [flows/jackpot-symbol-hit-and-prize-scaling/career-interview.md](/Users/nick/Git/nick/nick-vault/projects/antplay/star-math/flows/jackpot-symbol-hit-and-prize-scaling/career-interview.md)
- [flows/special-wild-feature-state-transform/flow.md](/Users/nick/Git/nick/nick-vault/projects/antplay/star-math/flows/special-wild-feature-state-transform/flow.md)
- [flows/special-wild-feature-state-transform/career-interview.md](/Users/nick/Git/nick/nick-vault/projects/antplay/star-math/flows/special-wild-feature-state-transform/career-interview.md)
- [step2-flow-comparison.md](/Users/nick/Git/nick/nick-vault/projects/antplay/star-math/step2-flow-comparison.md)
- [step1-candidate-flows.md](/Users/nick/Git/nick/nick-vault/projects/antplay/star-math/step1-candidate-flows.md)
- [contribution-claim-consolidation.md](/Users/nick/Git/nick/nick-vault/projects/antplay/star-math/contribution-claim-consolidation.md)
- [../math-workspace/contribution-claim-consolidation.md](/Users/nick/Git/nick/nick-vault/projects/antplay/math-workspace/contribution-claim-consolidation.md)
