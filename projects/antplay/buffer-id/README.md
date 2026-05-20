# buffer-id

`buffer-id` 是 Primestar / AntPlay 相關的 Java ID buffer library，提供 MySQL / local ID manager、序號緩衝與字串 ID 格式化能力。

## Status

| 項目 | 狀態 |
| --- | --- |
| contribution claim consolidation | 已完成 / rolling / 2026-05-20 |
| Flow Track | 未開始 |
| 履歷判斷 | 未見 Nick / `10gt12nc` direct commits；只作 platform ID generator code-backed 學習素材 |
| 下一步 | 不建議優先做 Flow Track，除非要補 ID generator / serial safety 面試題 |

## Claim Boundary

可保守說:

- 分析過 `buffer-id` 的 ID buffer / serial generator 設計。
- 可用作面試補充題材：MySQL serial row、buffer range、prefix + padded number、ID collision / DB transaction 風險。

不可誇大:

- 不寫 Nick 真實開發過。
- 不寫主導 ID generator、分散式 ID 或平台基礎設施。
- 不寫 production incident / performance improvement。

## Files

- [contribution-claim-consolidation.md](/Users/nick/Git/nick/nick-vault/projects/antplay/buffer-id/contribution-claim-consolidation.md)
