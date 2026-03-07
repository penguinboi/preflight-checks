# Pre-Launch Checklist

Go/no-go gate to run the day before launch. This is the compressed version of the [Production Readiness Playbook](production-readiness-playbook.md) — if you haven't run the full audits yet, do those first.

Each item is binary: done or not done. If any **MUST** item is unchecked, you're not ready to launch.

---

## How to Use

1. Copy this checklist into a new file for your project: `docs/plans/YYYY-MM-DD-pre-launch-checklist.md`
2. Go through each item and mark it `[x]` or `[N/A]`
3. If any MUST item is unchecked, fix it or postpone launch
4. Items marked **MUST\*** are conditional — they're required only if the condition in parentheses applies to your project. Mark `[N/A]` if the condition doesn't apply.
5. SHOULD items are strong recommendations — document why if you're skipping any
6. NICE items can wait for a fast-follow

---

## 1. Quality

| Priority | Check | Status |
|----------|-------|--------|
| MUST | All tests pass on the branch being deployed | [ ] |
| MUST | CI pipeline passes (tests + linting + type checking) | [ ] |
| MUST | No CRITICAL or HIGH code smells unresolved | [ ] |
| MUST | Pre-commit hooks are active and enforced | [ ] |
| SHOULD | Test coverage above project threshold (document what it is: ___%) | [ ] |
| SHOULD | No `// TODO` or `// FIXME` items related to launch features | [ ] |
| NICE | Code reviewed by someone other than the author | [ ] |

## 2. Security

| Priority | Check | Status |
|----------|-------|--------|
| MUST | No secrets in source code or git history | [ ] |
| MUST | No CRITICAL or HIGH security findings unresolved | [ ] |
| MUST | All dependencies up to date (no known vulnerabilities) | [ ] |
| MUST | Authentication and authorization working correctly | [ ] |
| MUST | HTTPS enforced (no mixed content) | [ ] |
| MUST | Security headers configured (CSP, HSTS, X-Frame-Options, X-Content-Type-Options) | [ ] |
| SHOULD | CORS restricted to expected origins | [ ] |
| SHOULD | Rate limiting on auth and public API endpoints | [ ] |
| SHOULD | Error responses don't leak internal details | [ ] |

## 3. Compliance & Legal

| Priority | Check | Status |
|----------|-------|--------|
| MUST | Privacy policy exists and is accurate | [ ] |
| MUST | Privacy policy linked from every page/screen | [ ] |
| MUST* | Cookie consent banner works — blocks scripts until accepted (if using non-essential cookies) | [ ] |
| MUST* | Terms of service exist (if users create accounts or purchase) | [ ] |
| MUST | Copyright notice in footer with correct year and entity | [ ] |
| MUST | All third-party assets properly licensed | [ ] |
| MUST* | EULA exists (if distributing downloadable software) | [ ] |
| MUST* | COPPA compliance verified (if app could attract under-13 users) | [ ] |
| SHOULD | Accessibility statement exists | [ ] |
| SHOULD | Legal acceptance tracking records who agreed to what and when | [ ] |
| SHOULD | AI-generated content disclosed where required | [ ] |
| SHOULD | Platform-specific requirements met (App Store, Google Play, etc.) | [ ] |
| NICE | Open source licenses documented in NOTICES/SBOM | [ ] |

## 4. Accessibility

| Priority | Check | Status |
|----------|-------|--------|
| MUST | All images have alt text | [ ] |
| MUST | All form inputs have associated labels | [ ] |
| MUST | Keyboard navigation works for all interactive elements | [ ] |
| MUST | Color is not the only way to convey information | [ ] |
| MUST | Heading hierarchy is correct (no skipped levels) | [ ] |
| SHOULD | Color contrast meets WCAG AA (4.5:1 normal, 3:1 large) | [ ] |
| SHOULD | Focus indicators visible on all interactive elements | [ ] |
| SHOULD | Modals trap focus and return it on close | [ ] |
| SHOULD | `prefers-reduced-motion` respected | [ ] |
| SHOULD | Skip-to-main link present | [ ] |
| NICE | axe-core or Lighthouse accessibility score > 95 | [ ] |

## 5. Performance

| Priority | Check | Status |
|----------|-------|--------|
| MUST | Pages load without visible lag on average connection | [ ] |
| MUST | No JavaScript errors in browser console on any page | [ ] |
| MUST | Images have explicit width and height (no layout shift) | [ ] |
| SHOULD | Lighthouse Performance score > 90 | [ ] |
| SHOULD | Core Web Vitals passing (LCP < 2.5s, INP < 200ms, CLS < 0.1) | [ ] |
| SHOULD | Below-the-fold images use lazy loading | [ ] |
| SHOULD | Images in modern format (WebP) with fallback | [ ] |
| SHOULD | No render-blocking resources | [ ] |
| NICE | Bundle size documented and reasonable for the app type | [ ] |

## 6. SEO & Discoverability

| Priority | Check | Status |
|----------|-------|--------|
| MUST | Every page has a unique `<title>` (under 60 chars) | [ ] |
| MUST | Every page has a unique `<meta name="description">` (under 160 chars) | [ ] |
| MUST | `sitemap.xml` exists and lists all public pages | [ ] |
| MUST | `robots.txt` exists and references sitemap | [ ] |
| MUST | Canonical URLs set on all pages | [ ] |
| SHOULD | Open Graph tags on all public pages | [ ] |
| SHOULD | Social card image exists (1200x630) and displays correctly | [ ] |
| SHOULD | Structured data (JSON-LD) on content pages | [ ] |
| SHOULD | Favicons present (16x16, 32x32, apple-touch-icon) | [ ] |
| SHOULD | `site.webmanifest` present | [ ] |
| NICE | Google Search Console verified and sitemap submitted | [ ] |
| NICE | Rich Results Test passed on structured data pages | [ ] |
| NICE | RSS feed exists (if blog) | [ ] |

## 7. Operational Readiness

| Priority | Check | Status |
|----------|-------|--------|
| MUST | Deployment process documented and tested | [ ] |
| MUST | Rollback procedure documented and tested (< 5 min) | [ ] |
| MUST | Environment variables and secrets configured for production | [ ] |
| MUST | Production database backed up (and backup tested) | [ ] |
| SHOULD | Error tracking service integrated (Sentry, etc.) | [ ] |
| SHOULD | Uptime monitoring configured (external ping) | [ ] |
| SHOULD | Health check endpoint exists | [ ] |
| SHOULD | Alerting configured for error rate spikes | [ ] |
| SHOULD | Structured logging with log aggregation | [ ] |
| NICE | Runbook for common ops tasks (restart, scale, clear cache) | [ ] |
| NICE | Incident response plan documented (who gets paged) | [ ] |
| NICE | Feature flags available for risky rollouts | [ ] |

---

## Final Decision

Count your results:

| Category | MUST (all checked?) | SHOULD (how many?) | NICE (how many?) |
|----------|--------------------|--------------------|------------------|
| Quality | | | |
| Security | | | |
| Compliance | | | |
| Accessibility | | | |
| Performance | | | |
| SEO | | | |
| Ops | | | |

**GO** — All MUSTs checked. Most SHOULDs checked. Remaining items documented as fast-follow.

**NO-GO** — Any MUST unchecked. Fix it first, then re-run this checklist.

---

## Post-Decision

- If GO: proceed to [Post-Launch Runbook](post-launch-runbook.md)
- If NO-GO: document what needs fixing, estimate time, set a new launch date
- Save this completed checklist in `docs/plans/` for the project's records
