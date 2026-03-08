# Open-Source Release Readiness — Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Add community infrastructure, versioning, discoverability, CI, and a new Open-Source Readiness Audit to both preflight-checks and claude-scrub.

**Architecture:** All changes are documentation and GitHub configuration. No application code. The new audit follows the exact format of existing audits in production-readiness-playbook.md.

**Tech Stack:** Markdown, GitHub Actions (YAML), GitHub CLI (`gh`)

**Repos:**
- preflight-checks: `/Users/saley/Code/preflight-checks` → `github.com/penguinboi/preflight-checks`
- claude-scrub: `/Users/saley/Code/claude-scrub` → `github.com/penguinboi/claude-scrub`

---

### Task 1: Add CODE_OF_CONDUCT.md to preflight-checks

**Files:**
- Create: `CODE_OF_CONDUCT.md`

**Step 1: Create CODE_OF_CONDUCT.md**

Use Contributor Covenant v2.1. Set contact email to the Penguinboi Software contact.

Content: Standard Contributor Covenant v2.1 text. Replace `[INSERT CONTACT METHOD]` with the appropriate contact for Penguinboi Software (confirm with Skyler if unclear).

**Step 2: Commit**

```bash
git add CODE_OF_CONDUCT.md
git commit -m "Add Contributor Covenant code of conduct"
```

---

### Task 2: Add SECURITY.md to preflight-checks

**Files:**
- Create: `SECURITY.md`

**Step 1: Create SECURITY.md**

For a documentation project, this is lightweight. Cover:
- How to report inaccurate security guidance in the playbook (could lead someone to ship insecure code)
- How to report vulnerabilities in the repo itself (unlikely but possible — e.g., malicious link injection)
- Contact method: GitHub private vulnerability reporting (preferred) or email
- Response time commitment (e.g., "we'll acknowledge within 72 hours")

Keep it short — under 40 lines.

**Step 2: Commit**

```bash
git add SECURITY.md
git commit -m "Add security reporting policy"
```

---

### Task 3: Add CONTRIBUTING.md to preflight-checks

**Files:**
- Create: `CONTRIBUTING.md`

**Step 1: Create CONTRIBUTING.md**

Sections:
1. **Welcome** — Brief intro, what the project is, that contributions are welcome
2. **Ways to contribute** — Report issues, suggest new checks, improve existing audits, fix typos, translations
3. **Before you start** — Open an issue first for non-trivial changes, check existing issues
4. **Content style guide:**
   - Follow the existing audit format (Parts: Stack Detection → Review → Output)
   - Severity levels: CRITICAL → HIGH → MEDIUM → LOW (match existing definitions)
   - Checks should be stack-agnostic where possible
   - Use the same markdown formatting (headers, code blocks, tables)
   - Pre-launch checklist items use MUST / SHOULD / NICE tiers
5. **Submitting changes** — Fork, branch, make changes, open PR
6. **Code of conduct** — Link to CODE_OF_CONDUCT.md

Keep practical, not bureaucratic. Under 80 lines.

**Step 2: Commit**

```bash
git add CONTRIBUTING.md
git commit -m "Add contributing guide"
```

---

### Task 4: Add issue and PR templates to preflight-checks

**Files:**
- Create: `.github/ISSUE_TEMPLATE/bug-report.yml`
- Create: `.github/ISSUE_TEMPLATE/feature-request.yml`
- Create: `.github/ISSUE_TEMPLATE/content-suggestion.yml`
- Create: `.github/ISSUE_TEMPLATE/config.yml`
- Create: `.github/pull_request_template.md`

**Step 1: Create issue templates**

Use YAML form format (`.yml` not `.md`) for structured input.

**bug-report.yml** — For reporting incorrect advice or broken links in the guides:
- Name: "Bug Report"
- Description: "Report incorrect advice, broken links, or formatting issues"
- Fields: Which document, what's wrong, what it should say, how you found it

**feature-request.yml** — For suggesting new audits or major features:
- Name: "Feature Request"
- Description: "Suggest a new audit, checklist section, or major addition"
- Fields: What's missing, why it matters, any references/sources

**content-suggestion.yml** — For suggesting individual checks or improvements:
- Name: "Content Suggestion"
- Description: "Suggest a new check, improve an existing one, or flag a gap"
- Fields: Which audit, what to add/change, severity suggestion, references

**config.yml** — Redirect questions to Discussions:
```yaml
blank_issues_enabled: false
contact_links:
  - name: Question or Discussion
    url: https://github.com/penguinboi/preflight-checks/discussions
    about: Ask questions or start a discussion here
```

**Step 2: Create PR template**

`.github/pull_request_template.md`:
- What does this change?
- Why is this needed?
- Checklist: follows existing format, links work, severity levels correct

Keep the template short — 15-20 lines.

**Step 3: Commit**

```bash
git add .github/
git commit -m "Add issue and PR templates"
```

---

### Task 5: Add CHANGELOG.md and tag v1.0.0

**Files:**
- Create: `CHANGELOG.md`

**Step 1: Create CHANGELOG.md**

Use [Keep a Changelog](https://keepachangelog.com) format. Summarize the current state as v1.0.0:

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/).

## [1.0.0] - 2026-03-08

### Added
- Production Readiness Playbook with 7 audits (Quality, Security, Compliance, Accessibility, Performance, SEO, Ops)
- Pre-Launch Checklist with MUST/SHOULD/NICE tiers
- Post-Launch Runbook with 72-hour monitoring guide and platform-specific sections
- Rollback Decision Tree for incident triage
- Start Here prompt for AI coding assistants
```

**Step 2: Commit**

```bash
git add CHANGELOG.md
git commit -m "Add changelog for v1.0.0"
```

**Step 3: Tag and create GitHub Release**

```bash
git tag -a v1.0.0 -m "v1.0.0 — Initial public release"
git push origin main --tags
```

Then create a GitHub Release:
```bash
gh release create v1.0.0 --title "v1.0.0 — Initial Public Release" --notes "$(cat <<'EOF'
## Preflight Checks v1.0.0

Production readiness assessment system for any software project.

### Included
- **Production Readiness Playbook** — 7 audits: Quality, Security, Compliance & Legal, Accessibility, Performance, SEO & Discoverability, Operational Readiness
- **Pre-Launch Checklist** — Go/no-go gate with MUST/SHOULD/NICE priority tiers
- **Post-Launch Runbook** — 72-hour monitoring guide with platform-specific sections (web, mobile, API, games)
- **Rollback Decision Tree** — Severity triage for production incidents
- **Start Here** — One prompt that runs the full process in any AI coding assistant

### How to use
Clone the repo, open your project in an AI coding assistant, and paste the start-here prompt. See [README](https://github.com/penguinboi/preflight-checks#quick-start) for details.
EOF
)"
```

---

### Task 6: Update README with release badge and repo discoverability

**Files:**
- Modify: `README.md` (line 3 — add release badge next to license badge)

**Step 1: Add release badge to README**

Add next to the existing license badge on line 3:
```markdown
[![GitHub release](https://img.shields.io/github/v/release/penguinboi/preflight-checks)](https://github.com/penguinboi/preflight-checks/releases)
```

**Step 2: Set GitHub repo topics**

```bash
gh repo edit penguinboi/preflight-checks --add-topic "production-readiness,launch-checklist,security-audit,code-quality,devops,accessibility,seo,compliance,ai-coding-assistant,preflight-checks"
```

**Step 3: Enable GitHub Discussions**

```bash
gh repo edit penguinboi/preflight-checks --enable-discussions
```

**Step 4: Commit README change**

```bash
git add README.md
git commit -m "Add release badge to README"
git push origin main
```

---

### Task 7: Add CI link checker

**Files:**
- Create: `.github/workflows/links.yml`

**Step 1: Create link checker workflow**

```yaml
name: Check Links

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  check-links:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: lycheeverse/lychee-action@v2
        with:
          args: --verbose --no-progress '**/*.md'
          fail: true
```

Note: lychee is fast and handles markdown well. May need a `.lychee.toml` config if there are URLs that need to be excluded (e.g., localhost examples).

**Step 2: Verify locally if possible**

```bash
# If lychee is installed:
lychee --verbose '**/*.md'
# Otherwise, just commit and let CI run
```

**Step 3: Commit**

```bash
git add .github/workflows/links.yml
git commit -m "Add CI link checker for markdown files"
git push origin main
```

---

### Task 8: Write Open-Source Readiness Audit for the playbook

**Files:**
- Modify: `production-readiness-playbook.md` — Add Audit 8 before the "Running Order" section (insert before line 962)
- Modify: `pre-launch-checklist.md` — Add section 8 before "Final Decision" (insert before line 129)
- Modify: `start-here.md` — Update references from "7 audits" to "8 audits"
- Modify: `README.md` — Add audit 8 to the list

**Step 1: Write Audit 8 in the playbook**

Add after the Operational Readiness audit (after line 959, before the `---` and "Running Order" section). Follow the exact format of existing audits:

```markdown
## 8. Open-Source Readiness Audit

Covers: community files, licensing, versioning, contributor experience, discoverability, and project governance. **Skip for private or internal-only projects.**

(Full audit prompt in code block, with Parts:)

Part 1: Project Detection
- Is the project public on a forge (GitHub, GitLab, etc.)?
- What license is used?
- What's the primary language/framework?
- Is this a library, tool, application, or documentation project?
- Is it published to a registry (npm, PyPI, crates.io, etc.)?

Part 2: Community Infrastructure
1. Repository essentials — README (clear purpose, install/usage, contributing link, license), LICENSE file, CONTRIBUTING.md, CODE_OF_CONDUCT.md, SECURITY.md
2. Issue management — Issue templates (bug report, feature request), PR template, labels configured, stale issue policy
3. Discussion channels — GitHub Discussions or linked forum/Discord/Slack, clear place for questions vs bugs

Part 3: Licensing & Legal
1. License compatibility — LICENSE file exists, license compatible with all dependencies, attribution requirements met (NOTICES/SBOM if needed)
2. Copyright headers — Consistent copyright in LICENSE and any file headers
3. CLA/DCO — Contributor license agreement or Developer Certificate of Origin (for larger projects)

Part 4: Versioning & Releases
1. Version strategy — Semantic versioning or calendar versioning, documented in README or CONTRIBUTING
2. Changelog — CHANGELOG.md exists and follows a standard format (Keep a Changelog)
3. Release process — Tagged releases on GitHub/registry, release notes describe changes, binary/package artifacts if applicable
4. Upgrade guidance — Breaking changes documented, migration guides for major versions

Part 5: Contributor Experience
1. First contribution path — Can someone clone, build, test, and contribute in under 15 minutes?
2. Development setup — Dev environment documented (dependencies, build steps, test commands)
3. Good first issues — Issues labeled for newcomers, clear scope, mentorship available
4. Review process — PR review expectations documented (turnaround time, who reviews)
5. Recognition — Contributors acknowledged (README, CHANGELOG, releases)

Part 6: Discoverability
1. Repository metadata — Topics/tags set, description filled in, website URL linked
2. README quality — Clear value proposition, install/quickstart, badges (build status, version, license), screenshots/demos if applicable
3. Registry presence — Published to appropriate registry, package description and keywords set
4. Search optimization — Descriptive repo name, consistent naming across registries

Part 7: Project Health Signals
1. Maintenance indicators — Recent commits, issues responded to, PRs reviewed, dependencies updated
2. Documentation — API docs, architecture docs, or guides beyond README (proportional to project complexity)
3. CI/CD — Automated tests on PRs, build verification, release automation
4. Security — Dependabot/Renovate enabled, security policy published, vulnerability disclosure process

Part 8: Output
- Severity levels match other audits (CRITICAL → LOW)
- Two documents: audit report + remediation plan
- Save to docs/plans/YYYY-MM-DD-open-source-audit.md and docs/plans/YYYY-MM-DD-open-source-remediation.md
```

**Step 2: Add Open-Source section to pre-launch checklist**

Add section 8 before "Final Decision" in `pre-launch-checklist.md`:

```markdown
## 8. Open-Source Readiness

*Skip this section for private or internal-only projects.*

| Priority | Check | Status |
|----------|-------|--------|
| MUST | LICENSE file exists and is compatible with dependencies | [ ] |
| MUST | README has clear purpose, install/usage instructions, and license | [ ] |
| MUST | No secrets, credentials, or internal URLs in repository | [ ] |
| SHOULD | CONTRIBUTING.md exists | [ ] |
| SHOULD | CODE_OF_CONDUCT.md exists | [ ] |
| SHOULD | SECURITY.md with vulnerability reporting process | [ ] |
| SHOULD | Issue templates configured | [ ] |
| SHOULD | CHANGELOG.md exists | [ ] |
| SHOULD | Tagged release with release notes | [ ] |
| NICE | GitHub Discussions or community channel linked | [ ] |
| NICE | Good first issues labeled for newcomers | [ ] |
| NICE | Dependabot or Renovate enabled | [ ] |
```

**Step 3: Update start-here.md**

- Line 35: Change "7 audits" to "8 audits"
- Line 45: Add skip rule: "Open-Source Readiness: skip for private or internal-only projects"
- Update audit order recommendations to include Open-Source Readiness

**Step 4: Update README.md**

- Add audit 8 to "The 7 Audits" section (rename to "The 8 Audits"):
  `8. **Open-Source Readiness** — Community files, licensing, versioning, contributor experience, discoverability`

**Step 5: Update playbook Quick Reference table and Running Order**

- Add row 8 to the Quick Reference table at the top of the playbook
- Update Running Order sections to mention audit 8

**Step 6: Update Final Decision table in pre-launch-checklist.md**

- Add "Open-Source" row to the category summary table

**Step 7: Verify internal consistency**

Check that all documents agree on the number of audits and the new audit name:
```bash
grep -rn "7 audits\|seven audits\|The 7" *.md
```
Fix any remaining references to "7 audits."

**Step 8: Commit**

```bash
git add production-readiness-playbook.md pre-launch-checklist.md start-here.md README.md
git commit -m "Add Open-Source Readiness as 8th audit"
git push origin main
```

---

### Task 9: Adapt and apply community files to claude-scrub

**Files (all in `/Users/saley/Code/claude-scrub/`):**
- Create: `CODE_OF_CONDUCT.md` — Same as preflight-checks (copy)
- Create: `SECURITY.md` — **Different**: full responsible disclosure for a security tool, enable GitHub private vulnerability reporting
- Create: `CONTRIBUTING.md` — **Different**: how to add detection patterns, run tests, code style, Python conventions
- Create: `.github/ISSUE_TEMPLATE/bug-report.yml` — Adapted for CLI bugs (include command run, expected output, actual output, OS/Python version)
- Create: `.github/ISSUE_TEMPLATE/feature-request.yml` — Adapted (new pattern requests, new commands, etc.)
- Create: `.github/ISSUE_TEMPLATE/pattern-request.yml` — claude-scrub specific: request a new secret detection pattern
- Create: `.github/ISSUE_TEMPLATE/config.yml` — Redirect questions to Discussions
- Create: `.github/pull_request_template.md` — Adapted (tests pass? new patterns have tests?)

**Step 1: Create all community files**

Adapt from preflight-checks where possible, customize for a Python CLI security tool.

SECURITY.md differences for claude-scrub:
- This is a security tool — vulnerabilities here could mean secrets aren't detected
- Report via GitHub private vulnerability reporting (preferred) or email
- Scope: false negatives (patterns that miss real secrets), false positives (patterns that flag non-secrets), any way the tool could leak or expose secrets it finds
- Response time: acknowledge within 48 hours

CONTRIBUTING.md differences for claude-scrub:
- How to run tests: `python -m pytest tests/`
- How to add a new pattern: explain the pattern dict format, which tier to add it to, require a test
- Code style: match existing style (no external linters required since zero-dependency)
- Testing requirements: every new pattern needs a test with a realistic example and a non-matching example

**Step 2: Commit**

```bash
cd /Users/saley/Code/claude-scrub
git add CODE_OF_CONDUCT.md SECURITY.md CONTRIBUTING.md .github/
git commit -m "Add community files, issue templates, and PR template"
```

---

### Task 10: Add CHANGELOG and tag v1.0.0 for claude-scrub

**Files (in `/Users/saley/Code/claude-scrub/`):**
- Create: `CHANGELOG.md`

**Step 1: Create CHANGELOG.md**

Review git log to summarize features for v1.0.0. Key features:
- scan, scrub, stats, sessions commands
- 40+ built-in detection patterns in tiered system
- Custom pattern support via config
- Extended patterns from secrets-patterns-db
- Interactive session browser

**Step 2: Commit and tag**

```bash
git add CHANGELOG.md
git commit -m "Add changelog for v1.0.0"
git tag -a v1.0.0 -m "v1.0.0 — Initial public release"
git push origin main --tags
```

**Step 3: Create GitHub Release**

```bash
gh release create v1.0.0 --title "v1.0.0 — Initial Public Release" --notes "..."
```

Include install instructions in release notes.

---

### Task 11: Repo discoverability for claude-scrub

**Step 1: Set GitHub topics**

```bash
gh repo edit penguinboi/claude-scrub --add-topic "security,secrets-detection,claude-code,credential-scanning,python,cli,privacy,devtools"
```

**Step 2: Enable Discussions**

```bash
gh repo edit penguinboi/claude-scrub --enable-discussions
```

**Step 3: Update README badge if needed**

claude-scrub already has a CI badge. Add release badge if not present:
```markdown
[![GitHub release](https://img.shields.io/github/v/release/penguinboi/claude-scrub)](https://github.com/penguinboi/claude-scrub/releases)
```

**Step 4: Commit and push**

```bash
git add README.md
git commit -m "Add release badge to README"
git push origin main
```

---

## Execution Order & Dependencies

```
Task 1 (CODE_OF_CONDUCT) ─┐
Task 2 (SECURITY.md)      ├─ independent, can run in parallel
Task 3 (CONTRIBUTING.md)   ┘
         │
Task 4 (issue/PR templates) ─── depends on community files existing for links
         │
Task 5 (CHANGELOG + v1.0.0 tag) ─── should run after community files so v1.0.0 includes them
         │
Task 6 (README badge + topics + Discussions) ─── depends on v1.0.0 release existing
         │
Task 7 (CI link checker) ─── independent, can run anytime
         │
Task 8 (Open-Source Readiness Audit) ─── independent of infra tasks, but should be last for preflight-checks so v1.0.0 doesn't need to include it (or bump to v1.1.0)
         │
Task 9 (claude-scrub community files) ──┐
Task 10 (claude-scrub CHANGELOG + tag)  ├─ sequential, after preflight-checks is done
Task 11 (claude-scrub discoverability)  ┘
```

**Note on versioning:** Tasks 1-7 should be committed and included in the v1.0.0 release. Task 8 (new audit) is a content addition that could be v1.1.0, or included in v1.0.0 if we tag after all work is done. Recommend: do Tasks 1-4, 7, 8 first, then tag v1.0.0 (Task 5-6) so the initial release includes everything.

**Revised order:**
1. Tasks 1-4 (community files + templates)
2. Task 7 (CI)
3. Task 8 (new audit content)
4. Tasks 5-6 (CHANGELOG, tag, release, discoverability) — now v1.0.0 includes all the good stuff
5. Tasks 9-11 (claude-scrub)
