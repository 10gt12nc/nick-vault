# Manual Discovery Prompt

狀態：手動執行用 prompt，不是 automation prompt。

## Prompt

```text
Run one Company Discovery for nick-vault.

Mission:
Do NOT summarize existing KB.
Your primary goal is to discover information Nick does NOT already know.
Assume existing flow documents already describe the happy path, interview version, trade-off, and evidence boundary.

Read only as needed:
- AGENTS.md
- senior-owner-playbook/company-discovery/README.md
- senior-owner-playbook/company-discovery/project-index.md
- senior-owner-playbook/company-discovery/topic-list.md
- senior-owner-playbook/company-discovery/discovery-template.md
- senior-owner-playbook/company-discovery/discovery-log.md
- relevant existing KB only to avoid duplicating known material
- relevant local code / git history / SQL / config only for the selected project and scope

Select exactly one project from project-index.md and exactly one discovery target from topic-list.md.

Search for 0-3 NEW high-value discoveries:
- hidden architecture decisions
- unexpected data flow
- unusual SQL / index / table routing
- retry logic
- rollback handling
- compensation
- edge cases
- production hacks
- legacy constraints
- historical refactors
- important git history
- TODOs / FIXMEs
- technical debt
- duplicated logic
- dead code
- configuration risks
- mismatch between code assumptions and DB / MQ / Redis constraints

Forbidden:
- Do not summarize callback flow / wallet flow / bet flow / MQ flow / architecture happy path unless NEW evidence is found.
- Do not produce a beautiful packet if there is no discovery.
- Do not scan all repos.
- Do not scan all modules.
- Do not modify resume, autobiography, story drafts, production flow files, 04 / 05 / 08 / 17 / 19, or project claim files.
- Do not store company secrets, merchants, tokens, internal URLs, or real transaction data.
- Do not turn code-backed / team-context / learning-only material into Nick direct owner claim.
- Do not create backlog or learning debt.

Output using discovery-template.md.

If no A/B-level discovery is found, explicitly output:
No new high-value knowledge discovered.

Only if A/B-level discovery is found and safe, save it under:
senior-owner-playbook/company-discovery/projects/{project}/YYYY-MM-DD-{discovery-slug}.md

If saved or checked, update:
- senior-owner-playbook/company-discovery/discovery-log.md

Run Relationship Check and git diff --check before commit.
Commit local KB updates when safe.
Do not push unless Nick explicitly asks.
```

## 使用方式

指定專案與 target：

```text
用 Company Discovery 跑一次。
Project: iwin
Discovery target: callback failure window / git history / TODO FIXME
照 manual-discovery-prompt.md。
不要總結既有 flow；只找新的高價值發現。找不到就說沒有。
```

讓 Codex 自己選：

```text
用 Company Discovery 跑一次。
照 project-index.md 的優先順序，選一個最可能挖到新 production risk / git history / technical debt 的 project 和 target。
不要全掃，不要改正式面試材料。
```
