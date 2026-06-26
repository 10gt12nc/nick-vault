# Manual Deep Dive Prompt

狀態：手動執行用 prompt，不是 automation prompt。

## Prompt

```text
Run Nick's Company System Deep Dive for nick-vault.

Goal:
Use real company systems/code as learning material to understand production flows, architecture, data flow, integration design, failure handling, technology trade-offs, and possible future refactoring paths.

This is NOT an external learning packet.
This is NOT a summary of existing KB.
This is a focused system deep dive.
Discovery is only the final section, not the whole goal.

Read only as needed:
- AGENTS.md
- senior-owner-playbook/README.md
- senior-owner-playbook/22-career-industry-kb-evolution-plan.md
- senior-owner-playbook/company-system-deep-dive/README.md
- senior-owner-playbook/company-system-deep-dive/curriculum.md
- senior-owner-playbook/company-system-deep-dive/project-value-map.md
- senior-owner-playbook/company-system-deep-dive/project-index.md
- senior-owner-playbook/company-system-deep-dive/topic-list.md
- senior-owner-playbook/company-system-deep-dive/deep-dive-template.md
- senior-owner-playbook/company-system-deep-dive/deep-dive-log.md
- relevant existing company/system notes in nick-vault only to avoid duplication
- relevant local code / git history only for the selected topic

Scope:
Before selecting a topic, read project-value-map.md.
Pick by learning value first, not by resume/autobiography usefulness.
Pick ONE high-value target only:
- production flow
- module
- provider integration
- payment / wallet / settle flow
- MQ / async flow
- DB table group
- Redis / cache pattern
- scheduler / batch / reconciliation
- auth / permission flow
- incident / bug pattern
- refactor / migration candidate

Priority:
A first:
- money flow
- order state
- callback
- wallet mutation
- MQ retry
- idempotency
- transaction boundary
- consistency
- incident / production risk
- cross-system integration

B second:
- performance
- DB index
- Redis key design
- permission
- API design
- module boundary
- observability

C only if no A/B:
- utility
- enum
- config
- DTO
- mapper

Output one focused deep-dive packet using deep-dive-template.md:
1. Topic
2. System Context
3. Runtime Flow
4. Code Reading Map
5. Data Flow / Data Structure
6. Engineering Thinking
7. Production Risk
8. Technology Comparison
9. If Redesigning Today
10. Learning Level Check
11. Interview Value
12. New Findings
13. KB Update

Rules:
- Do not scan all repos.
- Do not scan all modules.
- Do not produce only a discovery list.
- Do not merely repackage existing flow / interview KB.
- Do not default to Payment callback just because existing KB is rich; choose it only when this run will add code / DB / git / architecture evidence.
- If an existing KB already explains a section, summarize it briefly and add code map, data structure, alternatives, redesign thinking, or learning-level assessment.
- Do not modify resume, autobiography, story drafts, production flow files, 04 / 05 / 08 / 17 / 19, or project claim files.
- Do not treat the deep dive as resume material unless Nick explicitly asks for claim gate/backfill.
- Do not store company secrets, merchants, tokens, internal URLs, or real transaction data.
- Do not turn code-backed / team-context / learning-only material into Nick direct owner claim.
- Do not create backlog or learning debt.

New Findings:
List 0-3 genuinely new findings from code / git / system notes.
If none, say:
No new A-level finding this run.
Do not invent findings.

If useful and safe, save it under:
senior-owner-playbook/company-system-deep-dive/projects/{project}/YYYY-MM-DD-{topic-slug}.md

If saved or checked, update:
- senior-owner-playbook/company-system-deep-dive/deep-dive-log.md

Run Relationship Check and git diff --check before commit.
Commit local KB updates when safe.
Do not push unless Nick explicitly asks.
```

## 使用方式

指定專案與 topic：

```text
用 Company System Deep Dive 跑一次。
Project: antplay
Topic: Slot bet-settle-rollback
照 manual-deep-dive-prompt.md。
先看 project-value-map.md。
不要只做 discovery，也不要只重包既有 KB，也不要預設回填履歷。
```

讓 Codex 自己選：

```text
用 Company System Deep Dive 跑一次。
照 curriculum.md、project-value-map.md 和 project-index.md 的優先順序，選一個最值得學 production / architecture / refactor thinking 的 project/topic。
不要全掃，不要改正式面試材料。
```
