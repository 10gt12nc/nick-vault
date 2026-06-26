# Company Discovery Template

> 每次只選一個 project 的一個小 scope。目標是找未知，不是整理已知。

## 1. Run Scope

- Project:
- Local path:
- Discovery target:
- Scope searched:
- Sources checked:

## 2. Result

選一個：

```text
A-level discovery found
B-level discovery found
No new high-value knowledge discovered
```

## 3. Discoveries

最多 3 個。沒有就刪掉本段，只保留 `No new high-value knowledge discovered`。

### Discovery 1

- Level: A / B
- What surprised me:
- Evidence:
- Why it matters:
- Risk / opportunity:
- How to verify next, if needed:
- KB action:

### Discovery 2

- Level: A / B
- What surprised me:
- Evidence:
- Why it matters:
- Risk / opportunity:
- How to verify next, if needed:
- KB action:

### Discovery 3

- Level: A / B
- What surprised me:
- Evidence:
- Why it matters:
- Risk / opportunity:
- How to verify next, if needed:
- KB action:

## 4. No New Knowledge Case

如果沒有新發現，只寫：

```text
No new high-value knowledge discovered.

Checked:
-

Reason:
- The checked material only repeated existing KB.
- No new production risk, hidden decision, edge case, git history signal, SQL / config issue, or technical debt was found.
```

## 5. Forbidden Summary Check

確認是否踩雷：

- Did I summarize existing KB? Yes / No
- Did I repeat happy path without new evidence? Yes / No
- Did I create backlog? Yes / No
- Did I expose secret / merchant / internal URL / real transaction data? Yes / No
- Did I exaggerate code-backed or team context into Nick direct owner claim? Yes / No

如果任何一項是 Yes，不能保存 discovery。
