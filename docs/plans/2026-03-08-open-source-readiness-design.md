# Open-Source Release Readiness — Design Doc

**Date:** 2026-03-08
**Status:** Approved
**Scope:** preflight-checks + claude-scrub (template for all Penguinboi Software open-source projects)

## Goal

Establish open-source release readiness for both projects:
- **Community adoption** — make the repos inviting and easy to contribute to
- **Portfolio/credibility** — professional presentation
- **Living projects** — set up for ongoing contributions and active maintenance

## Section 1: Community Files

### CONTRIBUTING.md
- How to contribute (issues first, then PRs)
- Types of contributions welcome
- Style guide for content (preflight-checks) or code (claude-scrub)
- Per-project specifics:
  - preflight-checks: new checks, audit improvements, typo fixes, translations
  - claude-scrub: new detection patterns, run tests, code style

### CODE_OF_CONDUCT.md
- Contributor Covenant v2.1 (industry standard)

### SECURITY.md
- How to report security issues privately
- preflight-checks: lightweight (report via email for bad advice in guide)
- claude-scrub: full responsible disclosure process, GitHub private vulnerability reporting

### PR Template (.github/pull_request_template.md)
- What does this change? Why?
- Per-project checklist:
  - preflight-checks: "Does this follow the existing audit format?"
  - claude-scrub: "Tests pass? New patterns have tests?"

## Section 2: Issue Templates

Three templates via `.github/ISSUE_TEMPLATE/`:

1. **Bug Report** — steps to reproduce, expected vs actual, environment
2. **Feature Request** — problem description, proposed solution, alternatives
3. **Content Suggestion** (preflight-checks) / **Pattern Request** (claude-scrub) — what's missing, references

Plus `config.yml` to redirect questions to GitHub Discussions.

## Section 3: Versioning & Releases

- Semantic versioning for both projects
- Tag v1.0.0 as the initial public release
- CHANGELOG.md in Keep a Changelog format
- Retroactively summarize current state as v1.0.0
- GitHub Releases with release notes (also provides RSS for free)

### Version bump rules:
- preflight-checks: minor for new checks/content, major for structural changes
- claude-scrub: minor for new patterns/features, major for breaking CLI changes

## Section 4: Repo Discoverability

- **GitHub topics** — relevant tags for search discovery
- **README badges** — license, latest release, last commit (keep it clean, not too many)
- **GitHub Discussions** — enable with categories:
  - General (Q&A)
  - Show and Tell ("I used this on my project")
  - Ideas (feature discussion)

## Section 5: CI/Automation

### preflight-checks:
- GitHub Action: markdown link checker (catch broken links in guides)
- Nothing else — it's a docs project

### claude-scrub:
- Already has test workflow ✅
- Future: lint check, release automation on tag push (separate session)

## Section 6: Monitoring & Feedback Loop

### Weekly check-ins:
- GitHub Insights → Traffic tab (views, clones, referrers)
- Issues and Discussions — respond within a few days
- Star count trend

### Feedback collection:
- Discussions "Show and Tell" — encourage users to share results
- Issue templates funnel structured feedback
- Blog post comments (existing via website)

### Signals that things are working:
- Stars trending up
- Issues/discussions from non-maintainers
- Forks (people adapting it)
- Referrer traffic from unexpected sources

### Signals to act on:
- Same question asked repeatedly → improve docs
- Feature requests clustering → next version focus
- No engagement after a month → revisit discoverability (topics, cross-posting to dev communities)

## Section 7: New Playbook Content — Open-Source Readiness Audit

Add an 8th audit to the production-readiness-playbook for open-source projects.

**Applies when:** Project is public / open-source.
**Skip when:** Project is private / internal-only.

### Audit categories:
1. **Community files** — CONTRIBUTING, CODE_OF_CONDUCT, SECURITY.md, issue/PR templates
2. **Licensing** — LICENSE exists, compatible with dependencies, attribution correct
3. **Versioning & releases** — semantic versioning, CHANGELOG, GitHub Releases/tags
4. **Discoverability** — repo topics, README badges, Discussions enabled
5. **Contributor experience** — can someone clone, build, test, and contribute in under 15 minutes?
6. **Monitoring** — traffic insights, issue response time, feedback mechanisms

Follows the same format as other audits: stack detection → review → severity ratings → output (report + remediation plan).

## Per-Project Differences

| Item | preflight-checks (guide) | claude-scrub (CLI tool) |
|------|------------------------|----------------------|
| CONTRIBUTING.md | how to suggest checks | how to add patterns, run tests |
| SECURITY.md | lightweight | critical (security tool) |
| GitHub Releases | tag versions of the guide | tag releases, install references them |
| CI/CD | markdown link linting | already has test workflow |
| CHANGELOG | tracks audit additions | tracks features/fixes |
| Issue template #3 | Content Suggestion | Pattern Request |

## Implementation Order

1. Community files (CONTRIBUTING, CODE_OF_CONDUCT, SECURITY, templates)
2. Tag v1.0.0 + CHANGELOG + GitHub Release
3. Repo discoverability (topics, badges, Discussions)
4. CI for preflight-checks (link checker)
5. Write the Open-Source Readiness Audit for the playbook
6. Repeat 1-3 for claude-scrub (adapt, don't duplicate effort)
