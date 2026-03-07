# Production Readiness Playbook

Seven audits that take a project from "it works on my machine" to production-grade. Run them in order — each builds on the previous. Give each prompt to your AI coding assistant (Claude Code, Cursor, Copilot, Windsurf, etc.) in your project root — or use them as manual review checklists.

---

## Quick Reference

| # | Audit | What it answers | When to run |
|---|-------|----------------|-------------|
| 1 | [Quality](#1-quality-audit) | Is the code reliable, tested, and linted? | First. Quarterly. After major changes. |
| 2 | [Security](#2-security-audit) | Can someone exploit it? | Before launch. After auth/API changes. Quarterly. |
| 3 | [Compliance & Legal](#3-compliance--legal-audit) | Are we legally covered? | Before launch. After adding data collection, payments, or new markets. |
| 4 | [Accessibility](#4-accessibility-audit) | Can everyone use it? | Before launch. After UI changes. (Frontend only) |
| 5 | [Performance](#5-performance-audit) | Is it fast enough? | Before launch. After adding features. |
| 6 | [SEO & Discoverability](#6-seo--discoverability-audit) | Will people find it? | Before launch. After content/structure changes. (Web only) |
| 7 | [Operational Readiness](#7-operational-readiness-audit) | What happens when it breaks in prod? | Before launch. |

Start with **Quality** — the others build on having tests, linting, and CI in place.

---

## 1. Quality Audit

Covers: testing infrastructure, linting, formatting, type checking, pre-commit hooks, CI gates, code smells.

```
Audit the quality infrastructure and code health of this project. Detect the tech stack automatically from config files, package manifests, imports, and project structure.

## Part 1: Stack Detection

Identify:
- Language(s) and framework (TypeScript/JavaScript/Python/Dart/Swift/Rust/Go, React/Vue/Flutter/SwiftUI/Pygame, etc.)
- Build tool (Vite, webpack, Next.js, Cargo, go build, xcodebuild, etc.)
- Styling approach if applicable (Tailwind, CSS Modules, styled-components, etc.)
- State management if applicable (Zustand, Redux, Provider/Riverpod, ObservableObject, etc.)
- Data fetching if applicable (react-query, SWR, Apollo, http package, URLSession, etc.)
- Routing if applicable (React Router, GoRouter, NavigationStack, etc.)
- Test framework (Vitest, Jest, pytest, flutter test, XCTest, etc.)
- Test mocking (MSW, unittest.mock, mockito, etc.)
- E2E testing (Playwright, Cypress, integration_test, XCUITest, etc.)
- Any other significant libraries or patterns

## Part 2: Infrastructure Audit

For each of these, report what exists and what's missing:

1. **Testing framework** — What runner is configured? How many tests exist? What's the organization? Are there E2E tests?
2. **Test coverage** — Is coverage tracking configured? Current %? Which source files have no tests? Which critical paths are untested?
3. **Linting** — Is there a linter (ESLint, Biome, ruff, dart analyze, SwiftLint, clippy, etc.)? What rules are active?
4. **Formatting** — Is there a formatter (Prettier, Biome, black, dart format, swift-format, rustfmt, etc.)? Enforced or optional?
5. **Type checking** — Static type checking config and strictness (tsconfig strict, mypy strict, sound null safety, etc.)
6. **Pre-commit hooks** — Any hooks configured? What do they run?
7. **CI/CD quality gates** — Does CI run tests and linting before deploy? Or does it deploy blindly?
8. **Security scanning** — SAST tools, dependency vulnerability scanning?

Present a summary table: what exists vs what's missing.

## Part 3: Code Smell Audit

Generate a one-sentence-each bulleted list of code smells that linters miss, tailored to THIS project's detected stack. Use categories like these (skip any that don't apply):

1. Type safety (unsafe casts, type escape hatches, missing runtime validation)
2. Component lifecycle / hooks (stale closures, wrong deps, effects that should be events)
3. Data fetching (cache invalidation gaps, missing error/loading states, query key drift)
4. State management (god stores, missing selectors, stale subscriptions, redundant state)
5. Routing (hardcoded paths, unsafe param parsing, fragile navigation state)
6. Component keys (non-unique keys, index keys on reorderable lists)
7. Styling (utility soup, magic numbers, inconsistent design tokens)
8. Side effects (module-level mutations, import-time network calls, global state leaks)
9. Test mocks (happy-path-only handlers, fixtures drifting from schema, over-mocking)
10. E2E tests (brittle selectors, hardcoded waits, test interdependence)
11. Error handling (silent catches, swallowed exceptions, generic fallbacks hiding real errors)
12. Concurrency (race conditions, missing cancellation, unhandled async failures)
13. Resource management (unclosed connections, leaked listeners, missing cleanup)
14. Architecture (barrel file circular dependencies, components mixing too many concerns — fetching + state + routing + rendering in one file)
15. Type modeling (overuse of Partial<T> / optional-everything DTOs that allow invalid states at runtime, derived state stored instead of computed)
16. State modeling (multiple boolean flags representing a state machine — impossible states like isLoading + isError both true, props copied into useState that silently ignore prop updates after mount)
17. Component API (too many props indicating missing composition, direct DOM manipulation via querySelector/getElementById instead of refs)
18. Enum safety (numeric enums without explicit values — adding a member shifts all subsequent values, breaking serialized data)

Add stack-specific categories as needed (e.g., "Widget lifecycle" for Flutter, "Memory management" for Swift, "Unsafe blocks" for Rust).

Once you've identified the stack, search the web for "[framework] code smells that linters miss" and "[framework] common anti-patterns" to find additional stack-specific smells not listed above. Incorporate any relevant findings as extra categories before scanning.

Keep it to smells that static analysis genuinely cannot catch — things requiring understanding of intent, data flow, or runtime behavior.

Then find ALL examples of every smell across the codebase. For each finding:

1. Cite the exact file path and line numbers
2. Show the offending code snippet
3. Rate severity: CRITICAL (causes bugs in production), HIGH (fragile, breaks under pressure), MEDIUM (maintenance burden), LOW (cosmetic)
4. Briefly explain why it's a problem

## Part 4: Output

Produce two documents:

**Audit Report** (save to `docs/plans/YYYY-MM-DD-quality-audit.md`):
- Detected stack summary
- Infrastructure table (exists vs missing)
- Infrastructure recommendations, prioritized:
  1. CI quality gates (don't deploy broken code)
  2. Linting + formatting (catch bugs + enforce consistency)
  3. Pre-commit hooks (catch issues before push)
  4. Coverage tracking (find blind spots)
  5. Type checking strictness
  6. Missing tests for critical untested code
  7. Security scanning
- Code smell findings grouped by severity, then category
- Statistics table at the end
- Note any categories that came back clean

**Remediation Plan** (save to `docs/plans/YYYY-MM-DD-quality-remediation.md`):
- Infrastructure tasks first (CI gates, linting, hooks)
- Then code smell fixes, ordered by severity (critical first)
- Each task: files to modify, what to change, how to test
- Mark potential false positives as "verify first"
- Include test/verification commands

After both documents are saved, ask me whether I want to execute the plan.
```

---

## 2. Security Audit

Covers: input validation, authentication, authorization, secrets, dependencies, headers, cryptography.

```
Perform a security audit of this project. Detect the tech stack automatically, then systematically check every category below. This is NOT a penetration test — it's a source code review for security anti-patterns.

## Part 1: Stack Detection

Identify the language, framework, auth mechanism (JWT, sessions, OAuth, API keys), database layer, and any security-relevant middleware or libraries.

## Part 2: Security Review

Check each category. For each finding, cite file path, line numbers, code snippet, and severity.

### Input Validation & Injection
- User input rendered without sanitization (XSS via innerHTML, dangerouslySetInnerHTML, v-html, [innerHTML], etc.)
- SQL queries built with string concatenation or template literals instead of parameterized queries
- Command injection via unsanitized input passed to exec, spawn, os.system, subprocess, etc.
- Path traversal via user-controlled file paths (../../../etc/passwd)
- Regex denial of service (ReDoS) from user-controlled patterns
- Deserialization of untrusted data (pickle, eval, JSON.parse of unvalidated input used as code)

### Authentication
- Tokens stored in localStorage instead of httpOnly cookies (XSS can steal them)
- Missing token expiration or refresh rotation
- Passwords compared with == instead of constant-time comparison
- Session fixation (session ID not regenerated after login)
- Missing rate limiting on login/signup/password reset endpoints
- Hardcoded credentials, default passwords, or backdoor accounts
- Missing CSRF protection on state-changing requests

### Authorization
- Client-side-only role checks without server enforcement
- IDOR vulnerabilities (user can access other users' resources by changing IDs in URLs/params)
- Missing authorization checks on API endpoints (any authenticated user can access admin routes)
- Privilege escalation paths (user can modify their own role)
- Overly broad API scopes or permissions

### Secrets & Configuration
- API keys, tokens, or passwords in source code
- Secrets in git history (even if removed from current files)
- .env files committed to the repo
- Debug/development modes enabled in production config
- Verbose error messages that leak stack traces, file paths, or internal state to users
- Default or weak secret keys for signing (JWT secret = "secret")

### Dependencies
- Known vulnerable dependencies (check lockfile against advisory databases)
- Unpinned dependency versions that could be supply-chain attacked
- Dependencies from untrusted or abandoned sources
- Unnecessary dependencies that increase attack surface

### Transport & Headers
- Missing HTTPS enforcement
- Missing or permissive CORS configuration (Access-Control-Allow-Origin: *)
- Missing security headers (Content-Security-Policy, X-Frame-Options, Strict-Transport-Security, X-Content-Type-Options)
- Sensitive data in URL query parameters (tokens, passwords visible in logs and browser history)

### Cryptography
- Weak hashing algorithms for passwords (MD5, SHA1 without salt — should be bcrypt/scrypt/argon2)
- Homegrown authentication or encryption schemes
- Insecure random number generation for security-sensitive values (Math.random instead of crypto.getRandomValues)
- Hardcoded IVs, salts, or encryption keys

### Data Exposure
- Sensitive data logged (passwords, tokens, PII in console.log or server logs)
- API responses returning more data than the client needs (full user objects including password hashes)
- Sensitive data in client-side state visible in browser devtools
- Missing data sanitization on error responses

## Part 3: Output

**Severity levels:**
- CRITICAL — Exploitable now, data loss or unauthorized access possible
- HIGH — Exploitable with moderate effort or specific conditions
- MEDIUM — Defense-in-depth gap, exploitable if combined with another issue
- LOW — Best practice violation, minimal direct risk

Produce two documents:

**Security Audit Report** (save to `docs/plans/YYYY-MM-DD-security-audit.md`):
- Stack and auth architecture summary
- Findings grouped by severity, then category
- For each finding: file, line, code snippet, explanation, recommended fix
- Statistics table
- Note clean categories

**Security Remediation Plan** (save to `docs/plans/YYYY-MM-DD-security-remediation.md`):
- Tasks ordered by severity (critical first)
- Each task: files to modify, what to change, how to verify the fix
- Mark findings that need manual verification as "verify first"
- Include commands to test fixes

After both documents are saved, ask me whether I want to execute the plan.
```

---

## 3. Compliance & Legal Audit

Covers: privacy policies, cookie consent, terms of service, EULA, accessibility statements, legal acceptance tracking, data protection regulations (GDPR/CCPA/COPPA), AI disclosures, payment processing, copyright, age restrictions, and platform-specific marketplace requirements. Skip categories that don't apply to the project's type (e.g., skip EULA for web-only apps, skip payment processing if there's no checkout).

```
Perform a compliance and legal audit of this project. Detect the tech stack automatically, then systematically check every applicable category below. This is a source code review for legal and regulatory requirements — not legal advice.

## Part 1: Stack Detection

Identify:
- Language, framework, and deployment target (web, iOS, Android, desktop, CLI)
- Auth mechanism and user data collected (registration, analytics, cookies, PII)
- Payment processing (Stripe, PayPal, in-app purchases, none)
- Third-party services that receive user data (analytics, AI APIs, ad networks, CDNs)
- Target audience (general, children, specific regions)
- Distribution platforms (App Store, Google Play, Steam, itch.io, Twitch, web hosting)

## Part 2: Compliance Review

Check each category. For each finding, cite file path, line numbers, code snippet, and severity. Skip entire categories that don't apply (e.g., EULA for web-only, payment for free apps).

### Privacy & Data Protection
- Privacy policy exists, is accessible from every page/screen, and covers all required disclosures
- Privacy policy accurately lists all data collected, how it's used, who it's shared with, and how long it's retained
- GDPR compliance (if serving EU users):
  - Lawful basis identified for each data processing activity
  - Data subject rights implemented: access, rectification, erasure, portability, objection
  - DPO contact info if required (>250 employees or core activity is large-scale monitoring/sensitive data)
  - 72-hour breach notification procedure documented
  - Data processing agreements with all third-party processors
- CCPA/CPRA compliance (if serving California users):
  - Right to know, delete, and opt-out of sale/sharing
  - "Do Not Sell or Share My Personal Information" link visible
  - Symmetric opt-in/opt-out UI (declining must be as easy as accepting)
  - Global Privacy Control (GPC) signal detection and response
  - Visible opt-out confirmation (2026 CPRA requirement)
- US state privacy laws: check applicability of Delaware, Iowa, Minnesota, Nebraska, New Hampshire, New Jersey, Maryland, Tennessee, Indiana, Kentucky, Rhode Island privacy acts
- International privacy laws (check applicability based on where users are located):
  - LGPD (Brazil): consent requirements, DPO appointment, data subject rights similar to GDPR
  - PIPEDA (Canada): meaningful consent, breach reporting, right of access
  - PIPL (China): separate consent for cross-border transfers, data localization requirements
  - APPI (Japan): purpose limitation, opt-out of third-party sharing, cross-border transfer rules
  - PDPA (Singapore/Thailand): consent and notification requirements, Do Not Call registry (Singapore)
  - POPIA (South Africa): lawful processing conditions, Information Officer registration
- COPPA compliance (if app could attract under-13 users): no data collection without verifiable parental consent
- Data retention policy documented and enforced
- Third-party data sharing disclosures accurate (analytics, payment processors, AI services, ad networks)

### Cookie Consent
- Cookie consent banner exists with symmetric accept/reject buttons (equal size, color, and visual weight)
- Consent stored with version tracking (re-prompt when policy changes)
- Non-essential cookies and scripts blocked until consent is granted (no pre-checked boxes)
- Do Not Track (DNT) and Global Privacy Control (GPC) signal detection and response
- Cookie policy page listing all cookies: name, purpose, duration, category (essential/analytics/marketing), provider
- Consent withdrawal mechanism accessible at any time (not just on first visit)

### Terms of Service
- Terms page exists and is linked from registration, checkout, and footer
- Covers: acceptable use, intellectual property ownership, user content licensing, disclaimers, liability limitations
- Dispute resolution mechanism specified (arbitration clause, governing jurisdiction)
- Service availability and modification rights (right to change terms, deprecate features)
- Termination conditions (what causes account suspension, data handling after termination)

### End User License Agreement (EULA)
- EULA exists if distributing downloadable software (mobile apps, desktop apps, CLI tools)
- License scope and restrictions defined (no reverse-engineering, no redistribution)
- Apple App Store: 10 required EULA sections (scope, maintenance, warranty, product claims, IP, legal compliance, contact info, third-party terms, third-party beneficiary, no App Store liability)
- Google Play: compliance provisions for Play Developer Program Policies
- Open source component acknowledgments and license compatibility verified (no GPL in proprietary, LGPL handled correctly)

### Accessibility Statement
- Accessibility statement page exists and is linked from footer
- WCAG version and conformance level declared (minimum AA)
- Known limitations documented honestly (not claiming perfection)
- Feedback/contact mechanism for reporting accessibility issues
- European Accessibility Act (EAA) compliance if serving EU users (effective June 2025)

### Legal Acceptance Tracking
- Registration and purchase flows require explicit agreement (unchecked checkbox, not pre-checked)
- Acceptance records stored: user ID, document accepted, document version, timestamp, IP address, user agent
- Document version bumps trigger re-acceptance for existing users
- Acceptance audit trail queryable for legal disputes
- Separate consent for each document (privacy policy, terms, marketing — not bundled into one checkbox)

### AI & Automated Decision-Making
- AI-generated content disclosed to users where required (EU AI Act transparency obligations, effective 2026)
- Automated decision-making transparency if using AI for decisions affecting users (hiring, credit, content moderation)
- AI service provider terms reviewed: data usage policies, training opt-out status, data retention
- User data sent to AI APIs disclosed in privacy policy

### Payment Processing
- PCI DSS compliance: using hosted payment fields (Stripe Elements, PayPal hosted buttons) — never handling raw card data
- Refund policy clearly stated and linked from checkout
- Price display includes taxes/fees or explicitly states "excluding tax"
- Receipt/confirmation emails sent after purchase
- Subscription auto-renewal disclosed with cancellation instructions

### Copyright & Attribution
- Copyright notice in footer with current year and correct entity name
- All third-party assets properly licensed (fonts, images, icons, sound effects, music)
- Open source licenses compatible and attributed (LICENSE file, NOTICES file, or SBOM)
- No assets used under expired or personal-use-only licenses

### Age Restrictions
- Age-gating implemented if content requires it (gambling, alcohol, adult content)
- Age verification mechanism appropriate to jurisdiction and content type
- COPPA-compliant if any features could attract children under 13

### Platform & Marketplace Compliance
- **Apple App Store:** App Review Guidelines compliance, required EULA sections, privacy nutrition labels accurate, App Tracking Transparency (ATT) prompt if tracking across apps/websites
- **Google Play:** Developer Program Policies compliance, Data Safety section accurate, target API level meets current requirements
- **Twitch:** Extensions and integrations comply with Developer Agreement, stream content within Community Guidelines
- **itch.io:** Content ratings accurate, payment splits comply with itch.io creator terms, content warnings present if applicable
- **Steam:** Steamworks partner requirements met, content ratings (ESRB/PEGI) accurate, regional pricing compliance
- **Web hosting (AWS/Vercel/Netlify):** Acceptable Use Policy compliance, DMCA takedown procedure documented

## Part 3: Output

**Severity levels:**
- CRITICAL — Illegal or exposes the project to regulatory enforcement (missing privacy policy with data collection, COPPA violation)
- HIGH — Significant legal risk or user rights violation (missing cookie consent with tracking cookies, no opt-out mechanism)
- MEDIUM — Best practice gap that could become a legal issue (outdated privacy policy, missing accessibility statement)
- LOW — Improvement opportunity, minimal legal risk (copyright year outdated, missing SBOM)

Produce two documents:

**Compliance Audit Report** (save to `docs/plans/YYYY-MM-DD-compliance-audit.md`):
- Stack, data collection, and distribution platform summary
- Applicable regulations identified (GDPR, CCPA, COPPA, EAA, EU AI Act, platform-specific)
- Findings grouped by severity, then category
- For each finding: file (if applicable), explanation, regulatory reference, recommended fix
- Categories that don't apply to this project (noted as "N/A — [reason]")
- Statistics table
- Note clean categories

**Compliance Remediation Plan** (save to `docs/plans/YYYY-MM-DD-compliance-remediation.md`):
- Tasks ordered by severity (critical first)
- Legal document creation tasks (privacy policy, terms, EULA, accessibility statement)
- Code changes for consent tracking, cookie blocking, acceptance logging
- Platform-specific submission requirements
- Each task: what to create/change, regulatory basis, how to verify
- Flag tasks requiring legal review as "legal review recommended"
- Flag tasks requiring platform action (App Store submission, GSC verification) as "manual — user action required"

After both documents are saved, ask me whether I want to execute the plan.
```

---

## 4. Accessibility Audit

Covers: semantic HTML, keyboard navigation, screen readers, color contrast, ARIA, focus management. Skip this for backend-only or CLI projects.

```
Perform an accessibility (a11y) audit of this project's user interface. Detect the tech stack automatically.

## Part 1: Automated Checks

Identify if any a11y tooling exists (eslint-plugin-jsx-a11y, axe-core, pa11y, Lighthouse CI, etc.). If a test runner exists, check whether any a11y tests are written.

## Part 2: Manual Code Review

Check each category across all UI components and pages. For each finding, cite file path, line numbers, code snippet, and severity.

### Semantic HTML
- Divs and spans used where semantic elements exist (nav, main, article, section, header, footer, button, etc.)
- Click handlers on non-interactive elements (div onClick without role="button" and keyboard handling)
- Links with no meaningful href (href="#" or href="javascript:void(0)")
- Missing landmark regions (no main, no nav)
- Heading hierarchy violations (h1 → h3, skipping h2)

### Keyboard Navigation
- Interactive elements not reachable via Tab (custom dropdowns, modals, menus)
- Missing focus trap in modals and dialogs (Tab escapes the modal into background content)
- No visible focus indicator (outline: none without a replacement style)
- Focus not returned to trigger element when modal/popup closes
- Keyboard shortcuts that conflict with screen reader shortcuts

### Screen Readers
- Images without alt text (or decorative images without alt="")
- Form inputs without associated labels (missing htmlFor/id connection or aria-label)
- Icons used as buttons without accessible names
- Dynamic content updates not announced (missing aria-live regions for toasts, loading states, errors)
- Tables without headers or caption

### Color & Visual
- Color as the only means of conveying information (red = error without text/icon)
- Insufficient color contrast (text vs background below WCAG AA 4.5:1 for normal text, 3:1 for large)
- Animations without prefers-reduced-motion support
- Text that doesn't resize properly (fixed px sizes preventing browser zoom)

### ARIA
- Incorrect ARIA roles (role="button" on a link, role="link" on a button)
- Missing aria-expanded on expandable elements (dropdowns, accordions)
- Missing aria-label or aria-labelledby on elements that need them
- aria-hidden="true" on elements that should be visible to screen readers
- Redundant ARIA (aria-label on an element that already has visible text label)

### Forms & Errors
- Error messages not programmatically associated with inputs (no aria-describedby or role="alert")
- Required fields not indicated to screen readers (missing aria-required or required attribute)
- Form validation errors that only appear visually without screen reader announcement
- Autocomplete attributes missing on common fields (name, email, password, address)

## Part 3: Output

**Severity levels:**
- CRITICAL — Content or functionality completely inaccessible to some users
- HIGH — Significant barrier requiring workaround
- MEDIUM — Usability degradation, not a complete barrier
- LOW — Best practice, improves experience but not blocking

Produce two documents:

**Accessibility Audit Report** (save to `docs/plans/YYYY-MM-DD-a11y-audit.md`):
- Current a11y tooling summary
- Findings grouped by severity, then category
- Statistics table
- Note clean categories
- Recommend a11y tooling to add (eslint-plugin-jsx-a11y, axe-core integration tests, etc.)

**Accessibility Remediation Plan** (save to `docs/plans/YYYY-MM-DD-a11y-remediation.md`):
- Tooling setup tasks first (add linting, add axe-core to test suite)
- Then fixes ordered by severity
- Each task: files to modify, what to change, how to verify
- Include commands to test fixes

After both documents are saved, ask me whether I want to execute the plan.
```

---

## 5. Performance Audit

Covers: bundle size, rendering efficiency, data fetching patterns, asset optimization, memory leaks.

```
Perform a performance audit of this project. Detect the tech stack automatically. This is a source code review for performance anti-patterns, not a runtime profiling session.

## Part 1: Build & Bundle

- What's the production bundle size? Is there a build analyzer configured (webpack-bundle-analyzer, vite-plugin-visualizer, etc.)?
- Are there large dependencies that could be replaced with lighter alternatives?
- Is code splitting / lazy loading used for routes and heavy components?
- Is tree shaking working? (Check for barrel files re-exporting entire modules)
- Are source maps configured appropriately for production?
- Are assets optimized? (Images compressed, SVGs optimized, fonts subset)

## Part 2: Rendering & UI (skip for backend-only projects)

Check each category. Cite file, lines, code snippet, and severity.

### Unnecessary re-renders
- Components re-rendering on every parent render without memoization where expensive
- Object/array literals created inline in JSX props (new reference every render)
- Context providers with value objects created on every render
- Missing useMemo/useCallback where computation is expensive (NOT premature optimization — only flag genuinely expensive cases)

### List rendering
- Large lists rendered without virtualization (rendering 1000+ DOM nodes)
- Missing or unstable keys causing full list re-renders
- Filtering/sorting done on every render without memoization

### Data fetching
- Waterfall requests (sequential fetches that could be parallel)
- N+1 query patterns (fetching detail for each item in a list)
- Missing pagination on large datasets (loading all records at once)
- No caching strategy (refetching data that rarely changes)
- Missing loading skeletons (layout shift when data arrives)

### Images & Media
- Images without explicit width/height (causes layout shift)
- Large images served without responsive srcset
- Images loaded eagerly that are below the fold (missing lazy loading)
- Unoptimized image formats (PNG where WebP/AVIF would work)

## Part 3: Runtime

### Memory leaks
- Event listeners added without cleanup
- Timers (setInterval, setTimeout) not cleared on unmount
- Subscriptions (WebSocket, EventSource, observers) not unsubscribed
- Closures holding references to large objects unnecessarily

### Network
- Missing request deduplication (same API called multiple times simultaneously)
- No request cancellation on navigation (stale requests completing after leaving a page)
- Missing retry/backoff strategy for failed requests
- Polling without visibility check (fetching data while tab is hidden)

## Part 4: Backend / API (skip for frontend-only projects)

- Unindexed database queries on user-facing endpoints
- N+1 query patterns in ORM usage
- Missing response compression (gzip/brotli)
- Large JSON responses without pagination
- Synchronous operations that should be async/queued
- Missing connection pooling

## Part 5: Output

**Severity levels:**
- CRITICAL — Causes visible lag, timeouts, or crashes for users
- HIGH — Noticeable slowness under normal usage
- MEDIUM — Degrades with scale (more data, more users)
- LOW — Optimization opportunity, not currently impactful

Produce two documents:

**Performance Audit Report** (save to `docs/plans/YYYY-MM-DD-performance-audit.md`):
- Build/bundle analysis
- Findings grouped by severity, then category
- Statistics table
- Recommend performance tooling to add (bundle analyzer, Lighthouse CI, etc.)

**Performance Remediation Plan** (save to `docs/plans/YYYY-MM-DD-performance-remediation.md`):
- Quick wins first (lazy loading, image optimization)
- Then architectural changes (virtualization, code splitting)
- Each task: files to modify, what to change, expected impact, how to verify
- Include measurement commands (build size before/after, Lighthouse scores)

After both documents are saved, ask me whether I want to execute the plan.
```

---

## 6. SEO & Discoverability Audit

Covers: meta tags, Open Graph/social cards, structured data (JSON-LD), crawlability, favicons, content structure, image optimization, and performance factors that affect search ranking. Skip this for backend-only, CLI, mobile-only, or non-web projects.

This section incorporates the full SEO playbook — audit checks, implementation templates, and gotchas. The prompt is kept clean for copy-paste; reference templates and gotchas follow it in separate sections.

```
Perform an SEO and discoverability audit of this project. Detect the tech stack automatically. This is a source code review for SEO best practices — flag any manual tasks (Search Console, Rich Results Test, social card validators) for the user to handle.

Refer to the "SEO Implementation Templates" and "SEO Gotchas & Lessons Learned" sections that follow this prompt for reference material (HTML templates, JSON-LD examples, commands, and known pitfalls).

## Part 1: Stack Detection

Identify:
- Language, framework, and rendering strategy (static HTML, SSR, SPA with hydration, client-only SPA)
- Hosting/deployment (AWS Amplify, Vercel, Netlify, GitHub Pages, custom server)
- Whether a blog or content section exists
- Existing SEO tooling (sitemap generators, meta tag libraries, structured data plugins)

## Part 2: SEO Review

Check each category. For each finding, cite file path, line numbers, code snippet, and severity.

### Meta Tags (check every public page)
- <title> exists, is unique per page, under 60 characters, includes primary keyword
- <meta name="description"> exists, is unique per page, under 160 characters
- <link rel="canonical"> exists and is self-referencing (absolute URL)
- <meta name="viewport" content="width=device-width, initial-scale=1.0"> present
- <meta name="theme-color"> present
- <meta name="author"> present
- Google/Bing site verification meta tags present (if applicable — flag for user if missing)

### Open Graph & Social Cards (check every public page)
- og:title, og:description, og:image, og:url, og:type, og:site_name all present
- twitter:card, twitter:title, twitter:description, twitter:image all present
- og:image is an absolute URL (including protocol), dimensions 1200x630px, with og:image:width and og:image:height meta tags
- Social card image file exists and is current
- Cache-busting version parameter on og:image and twitter:image URLs (?v=N, incremented when image changes)

### Structured Data (JSON-LD)
- Appropriate schema types used for content (WebSite, BlogPosting, Organization, Product, BreadcrumbList, FAQ, etc.)
- datePublished in full ISO 8601 with timezone (e.g., 2026-01-28T00:00:00-08:00 — NOT just 2026-01-28)
- image field present on articles (required for Google Article rich results)
- Breadcrumbs: last item has name but NO item URL (it's the current page)
- Structured data validates (flag for user to check manually in Google Rich Results Test — reCAPTCHA blocks automation)

### Crawlability & Indexing
- robots.txt exists at site root, allows indexing of public pages, references sitemap
- sitemap.xml exists at site root, includes all public indexable pages, excludes noindex pages
- <lastmod> dates in sitemap are current (not stale placeholder dates)
- No orphan pages (every page reachable from navigation or internal links)
- No broken internal links (href to pages that don't exist)
- No redirect chains (301 -> 301 -> final destination)
- Pages with <meta name="robots" content="noindex"> excluded from sitemap and skip OG/Twitter/structured data

### Favicons & Web Manifest
- favicon-16x16.png, favicon-32x32.png, apple-touch-icon.png (180x180) exist
- icon-192.png, icon-512.png exist (for PWA/manifest)
- site.webmanifest exists with name, short_name, icons, theme_color, background_color
- Favicon link tags use absolute paths (work correctly from nested pages)

### Content & Internal Linking
- Semantic HTML used (<main>, <nav>, <article>, <header>, <footer>)
- Skip-to-main link present for screen readers
- Alt text on all images (descriptive, not filename)
- ARIA labels on interactive elements without visible text
- Heading hierarchy correct (h1 -> h2 -> h3, no level skips)
- Descriptive anchor text on internal links (not "click here" or bare URLs)
- Natural cross-links between related content pages (mentions of topics covered elsewhere linked inline)

### Images
- WebP format used with PNG/JPG fallback (<picture> element)
- Explicit width and height attributes on all images (prevents CLS)
- loading="lazy" on below-the-fold images
- Images sized to display dimensions (not serving oversized originals)

### Performance (SEO-impacting factors)
- Lighthouse scores: Performance > 90, SEO > 95, Accessibility > 95
- Core Web Vitals: LCP < 2.5s, INP < 200ms, CLS < 0.1
- Render-blocking resources minimized (async/defer on scripts, preloaded critical fonts)
- No unused JavaScript or CSS increasing page weight

### RSS Feed (if blog exists)
- rss.xml exists and is valid
- <link rel="alternate" type="application/rss+xml"> in <head> of blog pages

### Manual Tasks (flag for the user — these require browser login)
- Google Search Console: property verified, sitemap submitted
- Bing Webmaster Tools: imported from GSC
- Rich Results Test: each page with structured data validated (reCAPTCHA blocks automation)
- Social card previews: tested with Facebook Sharing Debugger, Twitter Card Validator, LinkedIn Post Inspector

## Part 3: Output

**Severity levels:**
- CRITICAL — Page not indexable, or major SEO signals missing site-wide (no title tags, no sitemap, blocked by robots.txt)
- HIGH — Significant discoverability gap (missing meta descriptions, broken structured data, no Open Graph tags)
- MEDIUM — Optimization opportunity with measurable impact (missing image alt text, no lazy loading, stale sitemap dates)
- LOW — Nice-to-have improvement (missing theme-color, RSS feed, minor structured data enhancements)

Produce two documents:

**SEO Audit Report** (save to `docs/plans/YYYY-MM-DD-seo-audit.md`):
- Stack, rendering strategy, and hosting summary
- Findings grouped by severity, then category
- For each finding: file, line, explanation, recommended fix
- Manual tasks flagged for user (Search Console, Rich Results Test, social card validators)
- Statistics table
- Note clean categories

**SEO Remediation Plan** (save to `docs/plans/YYYY-MM-DD-seo-remediation.md`):
- Code changes ordered by severity (critical first)
- Asset creation tasks (favicons, social card, manifest)
- Content improvements (alt text, internal linking, heading hierarchy)
- Each task: files to modify, what to change, how to verify
- Manual tasks listed separately with instructions for the user
- Include verification commands (Lighthouse CLI, link checkers)

After both documents are saved, ask me whether I want to execute the plan.
```

### SEO Implementation Templates

Reference templates for implementing SEO fixes. Use these when creating or updating meta tags, structured data, sitemaps, favicons, and images.

#### Meta Tags

```html
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta name="google-site-verification" content="YOUR_VERIFICATION_TOKEN">
<title>Page Title - Site Name</title>

<meta name="description" content="Under 160 chars, include primary keyword">
<meta name="author" content="Author Name">
<meta name="theme-color" content="#hex">
<link rel="canonical" href="https://site.com/this-page">
```

#### Open Graph & Twitter Cards

```html
<meta property="og:type" content="website">  <!-- "article" for blog posts -->
<meta property="og:url" content="https://site.com/this-page">
<meta property="og:title" content="Page Title">
<meta property="og:description" content="Same as meta description or slightly longer">
<meta property="og:site_name" content="Site Name">
<meta property="og:image" content="https://site.com/social-card.png?v=1">
<meta property="og:image:width" content="1200">
<meta property="og:image:height" content="630">

<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:title" content="Page Title">
<meta name="twitter:description" content="Same as meta description">
<meta name="twitter:image" content="https://site.com/social-card.png?v=1">
```

#### Social Card Image

- Create a `social-card-template.html` that renders a 1200x630 card matching the site's visual identity (same fonts, colors, background effects)
- Use inline styles, relative font/image paths so it works when opened locally
- Screenshot with Playwright at 1200x630 viewport, save as `social-card.png` in site root
- When updating, increment the `?v=N` parameter in ALL HTML files that reference the image

#### BlogPosting (JSON-LD)

```json
{
  "@context": "https://schema.org",
  "@type": "BlogPosting",
  "headline": "Post Title",
  "description": "Post description",
  "url": "https://site.com/blog/slug",
  "image": "https://site.com/post-image.png",
  "datePublished": "2026-01-28T00:00:00-08:00",
  "author": {
    "@type": "Person",
    "name": "Author Name",
    "url": "https://site.com/about"
  },
  "publisher": {
    "@type": "Organization",
    "name": "Site Name",
    "url": "https://site.com"
  }
}
```

#### BreadcrumbList (JSON-LD)

```json
{
  "@context": "https://schema.org",
  "@type": "BreadcrumbList",
  "itemListElement": [
    { "@type": "ListItem", "position": 1, "name": "Home", "item": "https://site.com/" },
    { "@type": "ListItem", "position": 2, "name": "Blog", "item": "https://site.com/blog" },
    { "@type": "ListItem", "position": 3, "name": "Post Title" }
  ]
}
```

**Key rules:**
- `datePublished` MUST be full ISO 8601 with timezone (NOT just `2026-01-28`)
- `image` is REQUIRED for Google Article rich results
- Last breadcrumb item has NO `item` URL (it's the current page)

#### robots.txt

```
User-agent: *
Allow: /
Sitemap: https://site.com/sitemap.xml
```

#### sitemap.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://site.com/</loc>
    <lastmod>2026-01-15</lastmod>
    <priority>1.0</priority>
  </url>
</urlset>
```

Do NOT include pages with `<meta name="robots" content="noindex">`. Update `<lastmod>` when page content changes.

#### Favicons

Generate from a source PNG:

```bash
sips -z 16 16 source.png --out favicon-16x16.png
sips -z 32 32 source.png --out favicon-32x32.png
sips -z 180 180 source.png --out apple-touch-icon.png
sips -z 192 192 source.png --out icon-192.png
sips -z 512 512 source.png --out icon-512.png
```

Link in every page (use absolute paths so nested pages resolve correctly):

```html
<link rel="icon" type="image/png" sizes="32x32" href="/favicon-32x32.png">
<link rel="icon" type="image/png" sizes="16x16" href="/favicon-16x16.png">
<link rel="apple-touch-icon" sizes="180x180" href="/apple-touch-icon.png">
<link rel="manifest" href="/site.webmanifest">
```

#### Web Manifest

```json
{
  "name": "Site Name",
  "short_name": "Site",
  "icons": [
    { "src": "/icon-192.png", "sizes": "192x192", "type": "image/png" },
    { "src": "/icon-512.png", "sizes": "512x512", "type": "image/png" }
  ],
  "theme_color": "#hex",
  "background_color": "#hex",
  "display": "browser"
}
```

Use `"display": "browser"` for content sites (not PWAs).

#### Image Optimization

```bash
# Convert PNG to WebP (quality 80)
cwebp -q 80 image.png -o image.webp
```

```html
<picture>
  <source srcset="/image.webp" type="image/webp">
  <img src="/image.png" alt="Description" width="600" height="400" loading="lazy">
</picture>
```

Always set explicit `width` and `height` to prevent CLS. Use `loading="lazy"` for below-the-fold images.

#### Manual Task Instructions

When flagging manual tasks for the user, include these instructions:

- **Google Search Console:** "Go to https://search.google.com/search-console, add your site as a URL prefix property, verify, then submit sitemap.xml under Sitemaps."
- **Bing Webmaster Tools:** "Go to https://www.bing.com/webmasters and click 'Import from Google Search Console'."
- **Rich Results Test:** "Go to https://search.google.com/test/rich-results and test each page with structured data. reCAPTCHA blocks automation — must be done manually."
- **Social card cache clearing:** After deploying updated og:image tags, scrape each platform to refresh cached previews:

| Platform | Tool | Action |
|----------|------|--------|
| Facebook/LinkedIn | [Sharing Debugger](https://developers.facebook.com/tools/debug/) | Paste URL, click "Scrape Again" |
| Twitter/X | [Card Validator](https://cards-dev.twitter.com/validator) | Paste URL, click "Preview card" |
| LinkedIn | [Post Inspector](https://www.linkedin.com/post-inspector/) | Paste URL, click "Inspect" |

Wait for deployment to complete before scraping — scraping before the new meta tags are live re-caches the old image.

### SEO Gotchas & Lessons Learned

Consult these before making SEO changes:

- **CSP headers:** Adding external services (analytics, fonts, APIs) requires updating Content Security Policy directives. CSP does NOT do subdomain matching — `example.com` does NOT cover `api.example.com`. Need explicit entries or wildcards.
- **Deployment pipelines:** If using a build system (Amplify, Netlify, Vercel), new files at the site root must be included in the build config or they won't deploy.
- **noindex pages:** Skip OG/Twitter tags and structured data on pages with `<meta name="robots" content="noindex">` (404 pages, privacy policies). They won't appear in search results or social shares.
- **CollectionPage schema:** Google does NOT generate rich results from `CollectionPage`. It passes validation but shows "No items detected" in Rich Results Test. Expected — it still helps Google understand page structure.
- **Private GitHub repos:** Can't use as backlink sources since search engines can't crawl them.
- **Rich Results Test:** Blocked by reCAPTCHA — cannot be automated.
- **datePublished format:** Must be full ISO 8601 with timezone (`2026-01-28T00:00:00-08:00`), not just a date. Google's validator flags date-only as "invalid datetime."
- **og:image URLs:** Must be absolute including protocol (`https://site.com/image.png`), not relative paths.
- **og:image caching:** Social platforms cache images by URL. Replacing the file at the same URL won't update previews — add a `?v=N` query parameter and increment it. Update ALL HTML files that reference the image.
- **Social card template:** Keep a `social-card-template.html` alongside site source for easy regeneration when branding changes.
- **Playwright for screenshots:** Viewport screenshot at 1200x630 produces a clean OG image without browser chrome.

---

## 7. Operational Readiness Audit

Covers: error tracking, logging, monitoring, health checks, graceful degradation, deployment safety. This is what separates "deployed" from "production-grade."

```
Audit this project's operational readiness — everything needed to run reliably in production and recover when things go wrong. Detect the tech stack automatically.

## Part 1: Observability

### Error Tracking
- Is there an error tracking service integrated (Sentry, Bugsnag, Rollbar, etc.)?
- Are unhandled exceptions caught globally (window.onerror, process.on('uncaughtException'), panic recovery)?
- Are errors reported with useful context (user ID, request ID, stack trace, breadcrumbs)?
- Are sensitive data fields scrubbed from error reports (passwords, tokens, PII)?

### Logging
- Is there structured logging (JSON logs with consistent fields) or just console.log/print?
- Are log levels used appropriately (debug vs info vs warn vs error)?
- Is there a correlation/request ID for tracing requests across services?
- Are logs shipped to a central system (CloudWatch, Datadog, ELK, etc.) or only on the server's disk?
- Is sensitive data excluded from logs?

### Monitoring & Alerting
- Are there health check endpoints? What do they verify (just "alive" vs. database connectivity, external service health)?
- Is there uptime monitoring (external ping)?
- Are there alerts for error rate spikes, latency degradation, or resource exhaustion?
- Are there dashboards for key metrics (request rate, error rate, latency, CPU/memory)?

## Part 2: Resilience

### Graceful Degradation
- What happens when an external dependency is down (database, third-party API, auth provider)?
- Are there timeouts on all external calls (HTTP, database, DNS)?
- Are there circuit breakers or retry-with-backoff for flaky dependencies?
- Does the frontend show meaningful error states or just blank screens when API calls fail?

### Data Integrity
- Are database migrations reversible (down migrations exist and tested)?
- Is there a backup strategy? Are backups tested (can you actually restore from one)?
- Are destructive operations (delete, bulk update) behind confirmation or soft-delete?
- Is there an audit trail for important state changes?

### Rate Limiting & Abuse Prevention
- Are public endpoints rate-limited (login, signup, password reset, API)?
- Is there protection against automated abuse (CAPTCHA, bot detection)?
- Are file uploads validated (size limits, type checking, malware scanning)?

## Part 3: Deployment Safety

### Rollback
- Can you roll back a deployment in under 5 minutes?
- Are database migrations forward-compatible (can old code run against new schema during rolling deploys)?
- Is there blue-green or canary deployment capability?

### Configuration
- Are environment-specific settings externalized (not hardcoded)?
- Is there a clear separation between dev, staging, and production configs?
- Are secrets managed through a secrets manager (not .env files on the server)?
- Are feature flags available for risky rollouts?

### Documentation
- Is there a runbook for common operational tasks (restart, scale, rollback, clear cache)?
- Is the deployment process documented (not just in someone's head)?
- Is there an incident response plan (who gets paged, escalation path)?

## Part 4: Output

**Severity levels:**
- CRITICAL — No way to detect or recover from production failures
- HIGH — Can detect failures but recovery is slow or manual
- MEDIUM — Missing defense-in-depth, single points of failure
- LOW — Best practice, would improve operational confidence

Produce two documents:

**Operational Readiness Report** (save to `docs/plans/YYYY-MM-DD-ops-readiness-audit.md`):
- Current operational capabilities summary
- Findings grouped by severity, then category
- Recommended tooling (error tracking, monitoring, log aggregation)
- Statistics table

**Operational Readiness Plan** (save to `docs/plans/YYYY-MM-DD-ops-readiness-remediation.md`):
- Tasks ordered by severity
- Each task: what to implement, which tools/services to use, how to verify
- Distinguish between "code changes" and "infrastructure/service setup"
- Include verification steps

After both documents are saved, ask me whether I want to execute the plan.
```

---

## Running Order for a New Project

1. **Quality** — Get tests, linting, and CI in place first. Everything else depends on this.
2. **Security** — Fix exploitable issues before exposing the app to users.
3. **Compliance & Legal** — Ensure legal docs and consent mechanisms exist before launch.
4. **Accessibility** — Legal and ethical requirement. Harder to retrofit later.
5. **Performance** — Optimize once the app is correct, secure, and compliant.
6. **SEO & Discoverability** — Depends on performance (Core Web Vitals) and content structure being solid.
7. **Operational Readiness** — Final go-live gate: monitoring, alerting, and recovery in place.

## Running Order for an Existing Project

Start with whichever area has the most risk. Usually: Security → Quality → Compliance → Ops → Performance → Accessibility → SEO.

## Notes

- Each audit is independent — run one at a time and fix before moving to the next.
- Works on any stack. Each prompt detects the tech stack automatically.
- Run from the project root directory.
- Re-run quarterly or after major changes to the relevant area.
- All outputs go to `docs/plans/` with date-stamped filenames so you can track progress over time.
- The SEO audit incorporates the full SEO playbook (previously `seo-playbook.md`). All templates, gotchas, and manual task instructions are inline.
- Several audits flag manual tasks that require browser login (Search Console, Rich Results Test, social card validators, App Store submissions). These are marked "manual — user action required" in remediation plans.
