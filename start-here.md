# Start Here

Drop this prompt into Claude Code from any project root. It assesses your project and walks you through the production readiness process.

---

## The Prompt

Replace `<PLAYBOOKS_PATH>` with the absolute path to your local clone of this repo, then copy everything below this line and paste it into Claude Code:

---

```
I want to run a production readiness assessment on this project. The playbooks are at <PLAYBOOKS_PATH>.

## Step 1: Assess This Project

Detect the tech stack automatically, then answer these questions:

1. **What type of project is this?** (web app, mobile app, CLI tool, API/backend, game, library)
2. **What's the deployment target?** (AWS Amplify, App Runner, Vercel, App Store, itch.io, npm, etc.)
3. **What's the maturity level?** (greenfield, pre-launch, launched but needs hardening, established)
4. **Does it have a frontend?** (determines if Accessibility and SEO audits apply)
5. **Does it collect user data?** (determines Compliance audit scope)
6. **Does it process payments?** (determines Payment Processing checks)
7. **What platforms does it distribute through?** (App Store, Google Play, Steam, itch.io, Twitch, web only)

Present your assessment as a summary table and confirm with me before proceeding.

## Step 2: Recommend an Audit Order

Based on the assessment, recommend which of the 7 audits to run and in what order. Use these rules:

**For a new/pre-launch project:**
Quality → Security → Compliance → Accessibility → Performance → SEO → Ops

**For an existing/launched project:**
Security → Quality → Compliance → Ops → Performance → Accessibility → SEO

**Skip these when they don't apply:**
- Accessibility: skip for backend-only, CLI, or API projects
- SEO: skip for backend-only, CLI, mobile-only, or non-web projects
- Compliance (EULA section): skip for web-only apps
- Compliance (Payment section): skip for free apps
- Compliance (Platform section): skip if not distributed through app stores or marketplaces

Present the recommended order and confirm with me before running the first audit.

## Step 3: Run Audits

For each audit, read the corresponding section from <PLAYBOOKS_PATH>/production-readiness-playbook.md and execute the prompt. Wait for my go-ahead between each audit.

After each audit:
1. Save the audit report and remediation plan to docs/plans/
2. Summarize findings: how many CRITICAL, HIGH, MEDIUM, LOW
3. Ask me: "Fix issues now, or continue to the next audit?"

If I choose to fix, work through the remediation plan by severity (critical first). When done fixing, re-run the relevant checks to confirm, then move to the next audit.

## Step 4: Pre-Launch Checklist

After all audits are complete (or after fixing critical issues), read <PLAYBOOKS_PATH>/pre-launch-checklist.md and walk me through it. Fill in each item based on the audit results and any fixes applied.

Present the final go/no-go summary.

## Step 5: What's Next

Based on the results, tell me:
- If we're GO: "Read the post-launch runbook at <PLAYBOOKS_PATH>/post-launch-runbook.md after deploying."
- If we're NO-GO: List the remaining MUST items and estimate what it takes to fix them.
- When to re-run: Suggest a quarterly schedule based on the project type.
```
