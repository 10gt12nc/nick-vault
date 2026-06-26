# Manual Run Prompt

狀態：手動執行用 prompt，不是 automation prompt。

## Prompt

```text
Run one Company System Study packet for nick-vault.

Read only as needed:
- AGENTS.md
- senior-owner-playbook/company-system-study/README.md
- senior-owner-playbook/company-system-study/project-index.md
- senior-owner-playbook/company-system-study/topic-list.md
- senior-owner-playbook/company-system-study/packet-template.md
- senior-owner-playbook/company-system-study/study-log.md
- relevant existing KB / flow files only if they directly support the selected project/topic

Select exactly one project from project-index.md and exactly one topic from topic-list.md.

Produce one small study packet using packet-template.md:
1. Project
2. Topic
3. What was observed
4. Why it might be designed this way
5. Production risk / pain
6. Decision / trade-off
7. Git history signal, only if checked
8. If redesigning today
9. Interview takeaway
10. Evidence boundary
11. Non-goal
12. KB suggestion

Rules:
- Do not scan all repos.
- Do not scan all modules.
- Do not modify resume, autobiography, story drafts, production flow files, 04 / 05 / 08 / 17 / 19, or project claim files.
- Do not store company secrets, merchants, tokens, internal URLs, or real transaction data.
- Do not turn code-backed / team-context / learning-only material into Nick direct owner claim.
- Do not create backlog or learning debt.
- Mention modern alternatives only as trade-off analysis, not as an automatic migration task.

If useful and safe, save the packet under:
senior-owner-playbook/company-system-study/projects/{project}/YYYY-MM-DD-{topic-slug}.md

If saved, update:
- senior-owner-playbook/company-system-study/study-log.md

Run Relationship Check and git diff --check before commit.
Commit local KB updates when safe.
Do not push unless Nick explicitly asks.
```

## 使用方式

如果 Nick 想指定專案：

```text
用 Company System Study 跑一包。
Project: iwin
Topic: Provider callback
照 manual-run-prompt.md，先不要改正式履歷 / flow。
```

如果 Nick 想讓 Codex 自己選：

```text
用 Company System Study 跑一包。
照 project-index.md 的優先順序，選本週最有面試 / production thinking 價值的一個 project/topic。
不要全掃，不要改正式面試材料。
```
