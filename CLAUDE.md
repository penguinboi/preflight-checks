# Preflight Checks

Production readiness playbooks for shipping software with confidence. Eight systematic audits, a go/no-go checklist, 72 hours of post-launch monitoring, and a rollback decision tree. Stack-agnostic, tool-agnostic, designed for AI coding assistants or manual use.

## Repository

- **Repo:** `penguinboi/preflight-checks` (public, GitHub)
- **License:** MIT
- **Version:** 1.0.0

## Project Structure

```
production-readiness-playbook.md   # 8 audits — the core of the project
pre-launch-checklist.md            # Binary go/no-go gate (MUST / SHOULD / NICE tiers)
post-launch-runbook.md             # Hour-by-hour monitoring for the first 72 hours
rollback-decision-tree.md          # Severity triage: rollback, hotfix, or accept
start-here.md                      # Single prompt that runs the full process end-to-end
start-here-local.md                # Same as start-here.md with hardcoded local paths (gitignored)
CONTRIBUTING.md                    # Contribution guide and content style rules
SECURITY.md                        # Vulnerability reporting policy
CODE_OF_CONDUCT.md                 # Contributor Covenant
CHANGELOG.md                       # Keep a Changelog format
.lychee.toml                       # Link checker config (excludes placeholder URLs)
.github/workflows/links.yml        # CI: lychee broken-link checker on push/PR to main
.github/ISSUE_TEMPLATE/            # Bug report, feature request, content suggestion templates
```

## The 8 Audits

Each audit in `production-readiness-playbook.md` is a self-contained prompt designed to be copy-pasted into an AI coding assistant. Audits can also be used as manual review checklists.

1. **Quality** — Tests, CI, code health, pre-commit hooks, code smells
2. **Security** — Secrets, dependencies, auth, headers, input validation
3. **Compliance & Legal** — Privacy policies, cookie consent, GDPR/CCPA, EULA, platform rules
4. **Accessibility** — WCAG AA, keyboard nav, screen readers, color contrast (frontend only)
5. **Performance** — Core Web Vitals, Lighthouse, bundle size, caching
6. **SEO & Discoverability** — Meta tags, structured data, sitemaps, social cards (web only)
7. **Operational Readiness** — Deployment, rollback, monitoring, alerting, backups
8. **Open-Source Readiness** — Community files, licensing, versioning, contributor experience (public repos only)

## Severity Levels

Used consistently across all audits and checklists:

- **CRITICAL** — Causes real harm (bugs in prod, exploitable vulnerabilities, legal exposure)
- **HIGH** — Fragile, breaks under pressure or edge cases
- **MEDIUM** — Maintenance burden, tech debt
- **LOW** — Cosmetic, minor improvements

## Content Conventions

- Audit prompts go inside fenced code blocks so they can be copy-pasted into AI assistants
- Checks are stack-agnostic where possible (specific tools listed as examples, not requirements)
- All code and config files start with a 2-line `ABOUTME:` comment
- Markdown follows consistent patterns: tables for summaries, numbered lists for steps, bulleted lists for findings

## CI

One GitHub Actions workflow: `links.yml` runs lychee link checker against all markdown files on push/PR to main. Configuration in `.lychee.toml` excludes placeholder URLs (localhost, yoursite.com, etc.).

## Git Configuration

- **Name:** Skyler Lister Aley
- **Email:** skylerlisteraley@gmail.com
- **GitHub:** penguinboi
