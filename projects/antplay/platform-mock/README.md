# platform-mock

`platform-mock` 是 AntPlay / slot provider 對接測試用 mock platform，包含 login / balance / bet / settle / money_inout 類 mock API。

## Status

| 項目 | 狀態 |
| --- | --- |
| contribution claim consolidation | 已完成 / rolling / 2026-05-20 |
| Flow Track | 不建議優先 |
| 履歷判斷 | 局部真實開發過 + code-backed，但只作測試 / 故障注入 supporting evidence |
| 下一步 | 不建議優先做 Flow Track，除非要補 provider failure injection case |

## Claim Boundary

可保守說:

- 參與 `platform-mock` 的 bet / money_inout failure injection 測試調整。
- 可作 AntPlay / provider integration 測試與 failure scenario supporting evidence。

不可誇大:

- 不寫主導 mock platform。
- 不寫 production provider / wallet owner。
- 不寫完整測試平台或 chaos engineering。

## Files

- [contribution-claim-consolidation.md](/Users/nick/Git/nick/nick-vault/projects/antplay/platform-mock/contribution-claim-consolidation.md)
