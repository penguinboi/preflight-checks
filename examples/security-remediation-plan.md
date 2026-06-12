# Security Remediation Plan — Brewlog

**Date:** 2026-06-11
**Source:** [security-audit-report.md](security-audit-report.md) — 11 findings (3 CRITICAL, 3 HIGH, 3 MEDIUM, 2 LOW)

Work top to bottom. Each task lists the files to modify, the change, and how to verify it. Don't batch unrelated fixes into one commit.

---

## 0. Rotate the leaked credential first (C3 — do this before touching code)

The committed SMTP password must be treated as compromised. Removing it from the repo is not remediation.

1. Log in to the mail provider and rotate the SMTP password now.
2. Put the new value in the server's environment only — never in a tracked file.

**Verify:** Old password rejected by the SMTP server; app sends mail with the new one.

---

## 1. C3 (continued) — stop tracking `.env`

**Files:** `.env`, `.gitignore`, new `.env.example`

- `git rm --cached .env` and add `.env` to `.gitignore`
- Create `.env.example` with placeholder values and a comment per variable

**Verify:** `git ls-files | grep .env` returns only `.env.example`. *Note: the old value remains in git history — that's why step 0 rotated it.*

## 2. C1 — parameterize the search query

**Files:** `src/routes/brews.js:42`

Replace the interpolated SQL with a prepared statement: `WHERE user_id = ? AND notes LIKE ?`, passing `'%' + q + '%'` as the parameter.

**Verify:**
```bash
curl -s -H "Authorization: Bearer $TOKEN" \
  "http://localhost:3000/api/brews/search?q='%20OR%20'1'='1"
# Expect: empty result set, not other users' rows
```
Add a regression test asserting the injection string returns zero rows.

## 3. C2 — enforce ownership on brew reads

**Files:** `src/routes/brews.js:67` (and audit `PUT`/`DELETE` on the same router — **verify first** whether they share the bug)

Add `AND user_id = ?` to the lookup; return 404 on miss.

**Verify:**
```bash
# As user B, request a brew belonging to user A:
curl -s -o /dev/null -w "%{http_code}" \
  -H "Authorization: Bearer $TOKEN_B" http://localhost:3000/api/brews/1
# Expect: 404
```

## 4. H1 — require `JWT_SECRET` at startup

**Files:** `src/auth/token.js:7`, `deploy/notes.md`

Remove the `|| 'brewlog-dev'` fallback; throw at startup when unset. Document the variable in the deploy notes.

**Verify:** `JWT_SECRET= node src/server.js` exits with the error; starts normally when set.

## 5. H2 — move the JWT to an httpOnly cookie

**Files:** `src/routes/auth.js`, `src/middleware/auth.js`, `public/js/auth.js`

Set the token as an `httpOnly`, `Secure`, `SameSite=Lax` cookie on login; read it from the cookie in the auth middleware; delete the localStorage read/write in the client.

**Verify:** In devtools, `localStorage.getItem('token')` is null after login while authenticated requests still succeed; the cookie shows `HttpOnly` in the Application tab.

## 6. H3 — rate-limit auth endpoints

**Files:** `src/routes/auth.js`, `package.json` (add `express-rate-limit`)

10 requests / 15 min / IP on `/login` and `/signup`.

**Verify:**
```bash
for i in $(seq 1 12); do curl -s -o /dev/null -w "%{http_code}\n" \
  -X POST http://localhost:3000/api/auth/login \
  -H 'Content-Type: application/json' -d '{"email":"x@yoursite.com","password":"wrong"}'; done
# Expect: 401 x10, then 429
```

## 7. M1–M3 — transport hardening

**Files:** `src/app.js`, `package.json` (add `helmet`)

- Remove `cors()` (same-origin app) — **verify first** that no separate origin consumes the API
- Replace the error handler: log server-side, return `{ error: 'Internal error', requestId }`
- Add `helmet` with a self-only CSP; enable HSTS after 48 h of stable HTTPS

**Verify:** `curl -sI http://localhost:3000 | grep -i -e content-security-policy -e x-frame-options` shows the headers; a thrown test error returns no stack trace.

## 8. L1–L2 — logging and session polish

**Files:** `src/routes/auth.js:19`, `src/auth/token.js`, `public/js/auth.js:31`

- Drop the email from the login log line
- Reduce token lifetime to 1 h; client refreshes silently on 401

**Verify:** Grep the log after a test login — no email present. A token older than 1 h is rejected.

---

## Suggested commit sequence

One commit per numbered task (tasks 0–1 combine into one), each with its verification run before commit. Re-run the full audit's injection and authorization checks after task 7 to confirm no regressions.
