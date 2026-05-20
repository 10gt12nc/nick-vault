# platform-mock Contribution Claim Consolidation

日期: 2026-05-20

## 結論

`platform-mock` 有 Nick / `10gt12nc` direct commits，但範圍非常局部，主要是 bet / money_inout API 的暫時故障注入與 revert。它可以作 AntPlay / provider integration 測試 supporting evidence，不建議寫成正式履歷主成果。

履歷若需要只能非常保守:

> 曾配合 provider integration 測試，在 mock platform 調整 bet / money_inout failure injection，用於驗證上游 rollback / compensation 行為。

## Evidence Summary

| 類別 | 判斷 | Evidence |
| --- | --- | --- |
| 直接開發 | 局部真實開發過 | `10gt12nc` 約 6 筆 commits |
| 範圍 | 測試 / fault injection | commits 是 `tmp: always fail for bet / money_inout api` 與 revert |
| 履歷價值 | supporting evidence | 支撐 provider failure / rollback 測試，不作主成果 |

## Source Scan Record

來源 repo:

```text
/Users/nick/Git/antplay/platform-mock
```

遠端狀態:

- 已嘗試執行 `git fetch --all --prune`，但 remote refs 未能更新。
- local branch: `deploy/dev`
- local HEAD: `6b3838980b492fc0f670c64e21aecfbc6521e6de`
- 本機既有 `origin/master`: `6b3838980b492fc0f670c64e21aecfbc6521e6de`
- local vs 本機既有 `origin/master`: `0 / 0`
- 重要限制：fetch 失敗，不能宣稱已看最新 remote。

本次掃描範圍:

- `git shortlog -sne --all`
- `git log --all --author='10gt12nc|Nick|nick'`
- key commits: `ca1d558`、`8d88648`、`5ea71b0` 與 revert commits
- `SlotAntplayV1Controller`

## Important Evidence

重要 commits:

- `8d88648`: `tmp: always fail for bet /money_inout api`。
- `ca1d558`: `tmp: always fail for money_inout api`。
- `5ea71b0`: reapply always fail for bet api。
- 後續有對應 revert commits。

已確認 code path:

- `SlotAntplayV1Controller`: `/slot/antplay/v1/link`、`info`、`balance`、`bet`、`bet/settle`、`money_inout`。
- mock wallet 用於下注扣款、派彩與 all-in-one money_inout 測試。

可面試講:

- 用 mock provider 強制 bet / money_inout fail，驗證 game-api / game-job 的 rollback / compensation / retry。
- mock 只支撐測試場景，不是交易 source of truth。

不可誇大:

- 不寫 production runtime owner。
- 不寫完整 mock platform owner。
- 不寫 chaos engineering / full failure platform。

## Suggested Next

不建議優先深挖。若之後要補 provider failure injection case，再做:

```text
antplay platform-mock Step 1
```
