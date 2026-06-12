# Example Audit Output

This directory shows what you get when you run a playbook audit, so you can judge the output shape before running one yourself.

The example is the **Security Audit** (audit 2 from the [Production Readiness Playbook](../production-readiness-playbook.md)) run against **Brewlog**, a fictional coffee-brewing journal — the kind of small side project these playbooks target. Express 4 + SQLite + vanilla JS frontend, JWT auth, about 2,000 lines of code, built in evenings, two weeks from launch.

Every audit produces two documents:

| File | What it is |
|------|------------|
| [security-audit-report.md](security-audit-report.md) | The findings — grouped by severity, each with file, line, code snippet, explanation, and fix |
| [security-remediation-plan.md](security-remediation-plan.md) | The work plan — tasks ordered by severity, with verification steps and test commands |

In a real run, your AI assistant saves these to your project's `docs/plans/` directory as `YYYY-MM-DD-security-audit.md` and `YYYY-MM-DD-security-remediation.md`, then asks whether you want to execute the plan.

Brewlog's bugs are deliberately typical: every finding here is a pattern that shows up constantly in real side projects. If some of them look familiar, that's the point — run the audit.
