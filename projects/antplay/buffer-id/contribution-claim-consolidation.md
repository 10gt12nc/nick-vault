# buffer-id Contribution Claim Consolidation

日期: 2026-05-20

## 結論

`buffer-id` 目前不能列為 Nick / `10gt12nc` 真實開發過。Source git author 主要是 Clare / Chris，未掃到 Nick / `10gt12nc` direct commits。

這個 repo 可以保留為 code-backed 學習素材，用來理解平台 ID generator / serial buffer 的設計風險；不建議寫入正式履歷主成果。

## Evidence Summary

| 類別 | 判斷 | Evidence |
| --- | --- | --- |
| 直接開發 | 未見 direct evidence | `git log --all --author='10gt12nc|Nick|nick'` 無結果 |
| repo 類型 | 專案存在 / code-backed | Java library，`BufferIdService`、`MySQLIdManager`、`LocalIdManager` |
| 履歷價值 | learning-only | 可作 ID generator 設計素材，不作 Nick 實作成果 |

## Source Scan Record

來源 repo:

```text
/Users/nick/Git/antplay/buffer-id
```

遠端狀態:

- 已嘗試執行 `git fetch --all --prune`，但 remote refs 未能更新。
- local branch: `master`
- local HEAD: `015f44b2ad93852c7e50c0e5eadaaeed224f848c`
- 本機既有 `origin/master`: `015f44b2ad93852c7e50c0e5eadaaeed224f848c`
- local vs 本機既有 `origin/master`: `0 / 0`
- 重要限制：fetch 失敗，不能宣稱已看最新 remote。

本次掃描範圍:

- `git shortlog -sne --all`
- `git log --all --author='10gt12nc|Nick|nick'`
- `BufferIdService`
- `MySQLIdManager`
- source file list

## Code-Backed Notes

已確認 code path:

- `BufferIdService`: 提供 `next(id, prefix)`、`next(id, prefix, length)`、save / remove。
- `MySQLIdManager`: 用 MySQL `serial` table 做 number increment，再回傳 `@next`。
- `LocalIdManager`: local mode ID manager。

可面試講:

- serial row / prefix group 如何避免 ID collision。
- DB update + select `@next` 的 transaction / connection boundary。
- buffer ID 與 Snowflake 類 ID 的 trade-off。

不可放履歷:

- 不寫 Nick 開發或維護 `buffer-id`。
- 不寫主導 ID generator / distributed ID。

## Suggested Next

不建議優先深挖。若之後要補平台基礎設施面試小題，再做:

```text
antplay buffer-id Step 1
```
