# Rollback Decision Tree

When something goes wrong in production, use this to decide: rollback, hotfix, or accept and monitor.

Speed matters. Don't spend 30 minutes debating — follow the tree, make a call, and act. You can always do a more thorough analysis after the fire is out.

---

## Step 1: Assess Severity (2 minutes max)

Answer these questions about the issue:

### Is data being corrupted or lost?
**YES** → **ROLLBACK IMMEDIATELY.** Don't investigate further. Every minute is more data damage. Go to [How to Roll Back](#how-to-roll-back).

### Is there a security vulnerability being actively exploited?
**YES** → **ROLLBACK IMMEDIATELY.** If rollback isn't possible, take the affected service offline. Go to [How to Roll Back](#how-to-roll-back).

### Is a critical user flow completely broken? (login, signup, checkout, core functionality)
**YES** → Go to Step 2.

### Is it a partial degradation? (slow performance, broken non-critical feature, cosmetic issue)
**YES** → Go to Step 3.

### Is it only affecting a small subset of users? (specific browser, region, edge case)
**YES** → Go to Step 3.

---

## Step 2: Broken Critical Flow

A critical user flow is completely broken. You need to restore it, but you have two options.

### Can you identify and fix the bug in under 15 minutes?

**YES, and:**
- The fix is a small, isolated change (1-3 lines)
- You understand the root cause (not just suppressing the symptom)
- You can test the fix before deploying

→ **HOTFIX.** Go to [How to Hotfix](#how-to-hotfix).

**NO, or:**
- The fix touches multiple files or systems
- You're not sure of the root cause
- You can't test it quickly

→ **ROLLBACK.** Don't spend time debugging while users are stuck. Roll back, then investigate at your own pace. Go to [How to Roll Back](#how-to-roll-back).

---

## Step 3: Partial Degradation

Something is wrong but the app is still usable for most users.

### How many users are affected?

**Most/all users** (even if it's minor):
- If you can fix it in under 30 minutes → **HOTFIX**
- If not → **ROLLBACK** (minor issues affecting everyone erode trust quickly)

**Small subset of users:**
- Is there a workaround? (different browser, clearing cache, etc.)
  - **YES** → **ACCEPT AND MONITOR.** Document the workaround. Fix in the next regular release. Go to [How to Accept](#how-to-accept-and-monitor).
  - **NO** → Can you fix it in under 30 minutes?
    - **YES** → **HOTFIX**
    - **NO** → **ROLLBACK** if severity warrants it, otherwise **ACCEPT AND MONITOR**

### Is performance degraded?

- **Page load > 2x slower than baseline** → **ROLLBACK** (this affects SEO, bounce rates, and user experience)
- **Page load 1.2-2x slower** → **ACCEPT AND MONITOR** if stable. Fix in next release. **ROLLBACK** if it's getting progressively worse.
- **Occasional slow requests** → **ACCEPT AND MONITOR.** Investigate the pattern.

---

## Decision Summary

```
                    Data loss / Security exploit?
                           │
                    YES ───┤──── NO
                     │           │
                 ROLLBACK    Critical flow broken?
                 (NOW)            │
                           YES ──┤──── NO
                            │          │
                     Fix < 15min?   Partial degradation
                      │                │
               YES ──┤── NO      Most users affected?
                │      │            │
            HOTFIX  ROLLBACK   YES ─┤── NO
                                │       │
                         Fix < 30min? Workaround exists?
                          │              │
                   YES ──┤── NO    YES ─┤── NO
                    │      │        │       │
                HOTFIX  ROLLBACK  ACCEPT  Fix < 30min?
                                          │
                                   YES ──┤── NO
                                    │       │
                                HOTFIX   ACCEPT
```

---

## How to Roll Back

### Pre-flight (1 minute)
1. Announce to the team: "Rolling back [service] to [previous version]. Reason: [one sentence]."
2. If you have a status page, post an incident.

### Execute (target: under 5 minutes)
Choose the method that matches your deployment:

**AWS Amplify:**
```bash
# Redeploy previous build from Amplify console
# Hosting → Deployments → select previous successful build → Redeploy
# Or via CLI:
aws amplify start-job --app-id $APP_ID --branch-name main --job-type RELEASE
```

**Container-based (App Runner, ECS, Kubernetes):**
```bash
# Point to the previous image tag
# App Runner: update service to previous image
# ECS: update task definition to previous revision
# K8s: kubectl rollout undo deployment/[name]
```

**Git-based (Vercel, Netlify, any CI/CD that deploys on push):**
```bash
# Option A: Revert the commit and push
git revert HEAD --no-edit
git push

# Option B: Redeploy previous commit from dashboard
# Most platforms have a "Redeploy" button on previous deployments
```

**Static hosting (S3, manual upload):**
```bash
# Restore previous version from backup or rebuild from previous commit
git checkout [previous-commit-hash]
# Run build and deploy commands
```

### Post-rollback (5 minutes)
1. Verify the rollback succeeded — test the critical flow that was broken.
2. Update the team: "Rollback complete. [Service] is back on [previous version]."
3. Update the status page if applicable.
4. **Do NOT investigate the root cause now.** The fire is out. Schedule investigation for after you've confirmed stability.

---

## How to Hotfix

### Rules
- A hotfix is a **small, targeted fix** deployed outside the normal release cycle.
- It MUST be the minimum change needed. No drive-by refactoring. No "while I'm here" improvements.
- It MUST go through CI (tests, linting). If CI is slow, that's a problem to fix later — not a reason to skip it now.

### Execute
1. Create a branch from the deployed commit:
   ```bash
   git checkout -b hotfix/[description] [deployed-commit]
   ```
2. Make the fix. Keep it minimal.
3. Test locally. Test again.
4. Push and let CI run.
5. Deploy via your normal deployment process.
6. Verify the fix in production.
7. Merge the hotfix branch back to main (so it's not lost).

### If the hotfix doesn't work
**ROLLBACK.** You've already spent time trying to fix it forward. Don't spend more. Roll back and investigate properly.

---

## How to Accept and Monitor

You've decided the issue is low-severity enough to live with temporarily.

### Document (5 minutes)
1. Create a ticket/issue with:
   - What's broken
   - Who's affected
   - Workaround (if any)
   - Root cause theory
   - Target fix date
2. Add monitoring for the specific issue if possible (error tracking filter, custom alert).

### Monitor
- Check the issue every few hours for the first day.
- If it's getting worse (more users affected, error rate climbing), re-evaluate using this decision tree.
- Fix in the next regular release. Don't let it linger.

### Communicate
- If users might notice: post to status page or support channels.
- If internal only: update the team in your communication channel.
- Include the workaround in any communication.

---

## After the Incident

Regardless of which path you took, do a brief retrospective within 48 hours:

### 5-Minute Post-Mortem Template

```markdown
# Post-Mortem — [Date] [Brief Description]

**Duration:** [Time detected] → [Time resolved]
**Impact:** [Who was affected and how]
**Resolution:** [Rollback / Hotfix / Accepted]

## Timeline
- HH:MM — Issue detected (how: monitoring alert / user report / manual check)
- HH:MM — Decision made: [rollback/hotfix/accept]
- HH:MM — Resolution deployed
- HH:MM — Verified working

## Root Cause
[One paragraph. What actually caused the issue.]

## What Went Well
- [Something that worked in your response process]

## What Could Improve
- [Something that would have prevented or shortened this]

## Action Items
- [ ] [Specific improvement to prevent recurrence]
- [ ] [Process improvement if applicable]
```

Save to `docs/plans/YYYY-MM-DD-postmortem-[description].md`.

### Common Retrospective Insights

| Pattern | Typical Fix |
|---------|-------------|
| "We didn't know it was broken for 2 hours" | Add uptime monitoring and error alerting |
| "Rollback took 20 minutes" | Document and practice the rollback procedure |
| "The fix introduced a second bug" | Enforce CI on hotfix branches, add regression test |
| "We couldn't reproduce it locally" | Improve staging environment parity |
| "We didn't know which commit caused it" | Smaller, more frequent deploys |
| "The same bug happened before" | Add a regression test for the specific case |
