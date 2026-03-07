# Post-Launch Runbook

What to do in the first 72 hours after launching. This is active monitoring — not "set it and forget it." You're looking for problems that only appear with real users, real traffic, and real search engines.

Prerequisite: you've completed the [Pre-Launch Checklist](pre-launch-checklist.md) and decided GO.

---

## Hour 0: Immediately After Deploy

Do these within the first 15 minutes:

- [ ] **Verify the deployment succeeded** — Visit the production URL. Check that the correct version is live (look for a version indicator, recent content, or a known change).
- [ ] **Check for JavaScript errors** — Open browser devtools console on every major page. Any errors = investigate immediately.
- [ ] **Test critical user flows** — Walk through the primary paths a real user would take (homepage → key pages, signup/login if applicable, purchase flow if applicable).
- [ ] **Verify HTTPS** — Confirm no mixed content warnings. Check that HTTP redirects to HTTPS.
- [ ] **Check error tracking** — If Sentry/Bugsnag is set up, verify it's receiving events (trigger a test error if needed).
- [ ] **Check analytics** — Verify Google Analytics or equivalent is recording pageviews in the real-time dashboard.
- [ ] **Test cookie consent** — Accept and reject cookies. Verify non-essential scripts are blocked until consent is given. Check that the banner doesn't reappear after accepting.

### If something is broken

Don't panic. Refer to the [Rollback Decision Tree](rollback-decision-tree.md). If the issue is cosmetic, consider a hotfix. If it's data-affecting or security-related, roll back.

---

## Hours 1-4: Baseline Capture

Establish baselines while traffic is fresh. These numbers are your reference points for the next 72 hours.

### Performance Baselines

- [ ] **Run Lighthouse** on the 3 most important pages. Record scores:

| Page | Performance | Accessibility | SEO | Best Practices |
|------|-------------|---------------|-----|----------------|
| | | | | |
| | | | | |
| | | | | |

- [ ] **Record Core Web Vitals** from PageSpeed Insights (uses real Chrome user data once available, lab data initially):
  - LCP (Largest Contentful Paint): target < 2.5s
  - FID (First Input Delay): target < 100ms
  - CLS (Cumulative Layout Shift): target < 0.1

- [ ] **Check page load times** from a location different from your own (use WebPageTest or a VPN)

### Error Baselines

- [ ] **Error rate** — Check error tracking dashboard. Note the current rate (should be near zero). Any errors this early are likely real bugs, not noise.
- [ ] **Server logs** — Check for unexpected 4xx/5xx responses. Some 404s are normal (bots probing), but 500s are never OK.
- [ ] **Console warnings** — Note any warnings that aren't errors. Deprecation notices, missing resources, etc.

### SEO Baselines

- [ ] **Submit sitemap** to Google Search Console (if not already done pre-launch)
- [ ] **Request indexing** for the homepage and top pages in Search Console (URL Inspection → Request Indexing)
- [ ] **Test social cards** — Share the URL on Facebook, Twitter/X, and LinkedIn. Verify the card image, title, and description render correctly.
  - Facebook: [Sharing Debugger](https://developers.facebook.com/tools/debug/) — click "Scrape Again"
  - Twitter/X: [Card Validator](https://cards-dev.twitter.com/validator)
  - LinkedIn: [Post Inspector](https://www.linkedin.com/post-inspector/)
- [ ] **Check robots.txt** — Verify it's accessible at `https://yoursite.com/robots.txt` and isn't accidentally blocking anything

---

## Hours 4-24: Active Monitoring

Check these periodically throughout the first day. Set a timer for every 2-4 hours if needed.

### Watch For

- [ ] **Error rate trending** — Is it stable, increasing, or spiking? An upward trend means something is degrading under real usage.
- [ ] **New error types** — Are you seeing errors you didn't see in testing? Real users hit edge cases you didn't.
- [ ] **Performance degradation** — Are pages getting slower as traffic increases? Could indicate missing caching, unindexed queries, or resource exhaustion.
- [ ] **User reports** — Check support channels, social media mentions, app store reviews (if mobile). Real users are your best QA team.
- [ ] **Bot traffic** — Check server logs for crawlers. Google, Bing, and others should be finding your site. If they're not, check robots.txt and sitemap.
- [ ] **Resource usage** — CPU, memory, disk, and bandwidth on your hosting platform. Look for unexpected growth patterns.

### Compliance Checks

- [ ] **Cookie consent in the wild** — Test from a fresh browser (incognito). Does the banner appear? Do scripts actually wait for consent? Test from a EU IP if possible (VPN) to verify GDPR flow.
- [ ] **Privacy policy access** — Verify every page links to it. Click the links — don't assume they work.
- [ ] **Analytics data accuracy** — Compare analytics pageviews against server access logs. Large discrepancies could mean the tracking script is blocked or broken.

### What's Normal

Don't overreact to these:
- 404s for `/wp-admin`, `/.env`, `/xmlrpc.php` — bots probing for vulnerabilities. Normal.
- Googlebot crawling pages rapidly — it's indexing your site. Good.
- A few console warnings from third-party scripts — note them but don't panic.
- Traffic spikes if you posted to social media — expected.

### What's NOT Normal

Investigate these immediately:
- 500 errors on any user-facing page
- Error rate increasing over time (not spiking and recovering, but climbing)
- Core Web Vitals significantly worse than baseline (regression under real load)
- Cookie consent banner not appearing (compliance risk)
- Analytics showing zero traffic when you know people are visiting
- Unexpected outbound network requests (potential security issue)

---

## Hours 24-48: Settling In

The adrenaline has worn off. Now look at trends.

### Trend Analysis

- [ ] **24-hour error report** — Export errors from tracking service. Group by type. Prioritize:
  1. Errors affecting data integrity
  2. Errors blocking user flows
  3. Errors that are cosmetic but frequent
  4. Rare edge cases

- [ ] **Performance over 24 hours** — Has performance been stable or degrading? Check if there are time-of-day patterns (slow during peak hours = capacity issue).

- [ ] **Search Console status** — Check Coverage report. Are pages being indexed? Any errors? "Discovered — currently not indexed" is normal for the first few days.

- [ ] **Uptime report** — If you have uptime monitoring, check the 24-hour report. Any downtime periods you didn't notice?

### Fix or Backlog

For each issue found in the first 24 hours, decide:
- **Fix now** — Affecting multiple users, data integrity risk, or security issue
- **Fix this week** — Affecting some users, workaround exists
- **Backlog** — Cosmetic, rare edge case, or optimization opportunity

Document decisions and create tickets for backlogged items.

---

## Hours 48-72: Confidence Building

By now you should have enough data to know if the launch is solid.

### Final Checks

- [ ] **Error rate stable** — Should be flat or declining. If still climbing, something is wrong.
- [ ] **Performance stable** — Core Web Vitals holding at baseline or better.
- [ ] **No critical bugs reported** — If users found anything critical, it should be fixed by now.
- [ ] **Search indexing progressing** — Google Search Console should show pages being indexed (may still be in progress — that's OK).
- [ ] **Social cards confirmed** — All major platforms showing correct previews.
- [ ] **Analytics baseline established** — You have 3 days of data. Note daily active users, top pages, bounce rate, and traffic sources as your launch baseline.

### 72-Hour Report

Write a brief summary (save to `docs/plans/YYYY-MM-DD-launch-report.md`):

```markdown
# Launch Report — [Project Name]

**Launch date:** YYYY-MM-DD
**Launch version:** [version or commit hash]

## Results

- **Uptime:** ___% over 72 hours
- **Error rate:** ___ errors/hour (average)
- **Lighthouse scores:** Performance ___, A11y ___, SEO ___, Best Practices ___
- **Core Web Vitals:** LCP ___s, FID ___ms, CLS ___
- **Daily active users:** ___ (average)

## Issues Found

| # | Issue | Severity | Status |
|---|-------|----------|--------|
| 1 | | | Fixed / Backlogged / Monitoring |

## Follow-Up Items

- [ ] Item from backlog
- [ ] Scheduled re-audit (Quality/Security in 3 months)
```

---

## After 72 Hours

You're in steady-state operations now. Transition to regular monitoring:

- **Weekly:** Glance at error rates, performance trends, and Search Console
- **Monthly:** Review analytics trends, check for new dependency vulnerabilities
- **Quarterly:** Re-run Quality and Security audits from the [Production Readiness Playbook](production-readiness-playbook.md)
- **After major changes:** Re-run the relevant audit, then this runbook again for the deployment
