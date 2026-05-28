# math-core

`math-core` 是 AntPlay slot math 共用核心 library，提供 `SlotMath` contract、RTP / game state / symbol 常數、debug bet VO、random 與共用 VO。

## Status

| 項目 | 狀態 |
| --- | --- |
| contribution claim consolidation | 已完成 / rolling / 2026-05-20 |
| Flow Track | 未開始；目前不建議單獨開新主線，已由 `*-math` grouped flows 與 AntPlay system map 吸收為 supporting evidence |
| 履歷判斷 | 真實開發過 + code-backed，可保守放 slot math contract / RTP / jackpot symbol / debug bet VO 維護 |
| 下一步 | 已收斂；沒有預設下一步 |

## Claim Boundary

可保守說:

- 參與 `math-core` slot math 共用 contract / debug tooling / RTP enum / symbol 類維護。
- 參與 `fixedMultiBet`、currency、debugBet VO、jackpot symbol 與 RTP type 類跨 math module 相容調整。

不可誇大:

- 不寫完整 slot math framework owner。
- 不寫主導遊戲數學模型 / RTP 策略。
- 不寫完整 math runtime / simulator / validation platform owner。

## Files

- [contribution-claim-consolidation.md](/Users/nick/Git/nick/nick-vault/projects/antplay/math-core/contribution-claim-consolidation.md)
