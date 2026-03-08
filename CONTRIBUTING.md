# Contributing to Preflight Checks

Thanks for your interest in improving Preflight Checks! This is a documentation project — a production readiness playbook with systematic audits — and contributions of all sizes are welcome.

## Ways to Contribute

- **Report issues** — Broken links, unclear wording, outdated advice
- **Suggest new checks** — Missing items within existing audits, or entirely new audit categories
- **Improve existing audits** — Better explanations, more stack-specific examples, edge cases
- **Fix typos and formatting** — Always appreciated, no issue needed
- **Translations** — Help make the playbook accessible in other languages

## Before You Start

- **Typos and small fixes**: Just open a PR.
- **Everything else**: Open an issue first to discuss your idea. This saves everyone time if the change doesn't fit the project's direction.
- Check [existing issues](https://github.com/penguinboi/preflight-checks/issues) before creating a new one.

## Content Style Guide

The playbook follows consistent patterns. Please match them:

**Audit structure** — Each audit has three parts:
1. **Stack Detection** — Identify the project's tech stack from config files and imports
2. **Review categories** — Check each category, citing file paths, line numbers, and severity
3. **Output** — Findings grouped by severity, then prioritized fix list

**Severity levels** — Used consistently across all audits:
- **CRITICAL** — Causes real harm now (bugs in prod, exploitable vulnerabilities, legal exposure)
- **HIGH** — Fragile, breaks under pressure or edge cases
- **MEDIUM** — Maintenance burden, tech debt
- **LOW** — Cosmetic, minor improvements

**Pre-launch checklist tiers:**
- **MUST** — Required for launch. If unchecked, you're not ready.
- **SHOULD** — Strong recommendation. Document why if skipping.
- **NICE** — Can wait for a fast-follow.

**General formatting rules:**
- Keep checks stack-agnostic where possible (list specific tools as examples, not requirements)
- Audit prompts go inside fenced code blocks (`` ``` ``) so they can be copy-pasted into AI assistants
- Use the same markdown patterns as existing audits: tables for summaries, numbered lists for steps, bulleted lists for findings

## Submitting Changes

1. Fork the repo and create a branch (`fix/broken-link`, `add/data-audit`, etc.)
2. Make your changes, keeping PRs focused on one thing
3. Open a pull request with a clear description of what changed and why
4. Link the relevant issue if one exists

## Code of Conduct

This project follows the [Contributor Covenant Code of Conduct](CODE_OF_CONDUCT.md). By participating, you agree to uphold it.
