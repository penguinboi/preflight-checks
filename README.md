# Playbooks

Reusable guides for shipping software. Stack-agnostic, tool-agnostic — works with any project and any AI coding assistant (Claude Code, Cursor, Copilot, Windsurf, etc.). The audit prompts can also be run manually without AI.

## What's Here

| File | When to use | Time |
|------|-------------|------|
| [Start Here](start-here.md) | Drop into Claude Code to run the full process on a project | 5 min setup |
| [Production Readiness Playbook](production-readiness-playbook.md) | Weeks before launch — 7 systematic audits | Hours per audit |
| [Pre-Launch Checklist](pre-launch-checklist.md) | Day before launch — go/no-go decision | 15 minutes |
| [Post-Launch Runbook](post-launch-runbook.md) | First 72 hours after launch — active monitoring | Ongoing |
| [Rollback Decision Tree](rollback-decision-tree.md) | When something breaks in production — triage | 5 minutes |

## Workflow

```
Audits (weeks before)  →  Checklist (day before)  →  Launch  →  Monitoring (72 hrs)
                                                         ↓
                                                   Problem? → Rollback Decision Tree
```

## Quick Start

Point your AI coding assistant at any project and run:

```
Read /path/to/playbooks/start-here.md and follow its instructions for this project.
```

Or copy the prompt from `start-here.md` directly into your assistant. The individual audit prompts in the playbook also work standalone — copy any one into a conversation with your project open.

## Re-running

- **Quarterly:** Re-run Quality and Security audits
- **After major changes:** Re-run the relevant audit (UI changes → Accessibility, new data collection → Compliance, etc.)
- **New launch:** Full cycle — all 7 audits, checklist, monitoring
