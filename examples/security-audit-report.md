# Security Audit Report — Brewlog

**Date:** 2026-06-11
**Audit:** Production Readiness Playbook, audit 2 (Security)
**Project:** Brewlog — coffee-brewing journal (fictional example project)

## Stack & Auth Architecture Summary

- **Backend:** Node.js 22, Express 4.21, better-sqlite3
- **Frontend:** Vanilla JS + HTML served from `public/`, no framework
- **Auth:** Email + password login issuing JWTs (`jsonwebtoken` 9.x), bcrypt password hashing
- **Database:** Single SQLite file at `data/brewlog.db`
- **Deployment target:** Single VPS behind nginx (per `deploy/notes.md`)
- **AI/LLM features:** None — AI/LLM category skipped

---

## Findings

### CRITICAL

#### C1. SQL injection in brew search

**File:** `src/routes/brews.js:42`

```js
router.get('/search', auth, (req, res) => {
  const rows = db.prepare(
    `SELECT * FROM brews WHERE user_id = ${req.user.id} AND notes LIKE '%${req.query.q}%'`
  ).all();
```

The search term is interpolated directly into the SQL string. `?q=' OR '1'='1` returns every user's brews; stacked expressions can read the entire `users` table, including password hashes. Exploitable now by any logged-in user with one crafted URL.

**Fix:** Use parameterized queries: `db.prepare('SELECT * FROM brews WHERE user_id = ? AND notes LIKE ?').all(req.user.id, '%' + q + '%')`.

#### C2. IDOR — any user can read any brew by ID

**File:** `src/routes/brews.js:67`

```js
router.get('/:id', auth, (req, res) => {
  const brew = db.prepare('SELECT * FROM brews WHERE id = ?').get(req.params.id);
  res.json(brew);
```

The query checks that *a* user is logged in but never that the brew belongs to them. Brew IDs are sequential integers, so `/api/brews/1`, `/2`, `/3`… walks every record in the database. Unauthorized access to other users' data, exploitable now.

**Fix:** Add the ownership condition: `WHERE id = ? AND user_id = ?`, and return 404 (not 403) on miss to avoid confirming the ID exists.

#### C3. Live SMTP credentials committed in `.env`

**File:** `.env:4` (committed; present in git history since the initial commit)

```
SMTP_PASS=<redacted in this report — value is live>
```

The repository is about to go public with working mail credentials in it. Anyone who clones the repo can send mail as the app. Removing the file or rewriting history does **not** fix this — the credential must be treated as compromised.

**Fix:** Revoke and rotate the SMTP password first. Then remove `.env` from tracking, add it to `.gitignore`, and provide a `.env.example` with placeholder values.

---

### HIGH

#### H1. JWT signing secret falls back to a hardcoded default

**File:** `src/auth/token.js:7`

```js
const SECRET = process.env.JWT_SECRET || 'brewlog-dev';
```

If `JWT_SECRET` is ever unset in production (fresh server, missed env config — and `deploy/notes.md` doesn't mention it), every token is signed with a string that's in the public repo, letting anyone forge a session for any user. Exploitable under a likely misconfiguration.

**Fix:** Throw on startup when `JWT_SECRET` is missing instead of falling back: `if (!process.env.JWT_SECRET) throw new Error('JWT_SECRET is required')`.

#### H2. JWT stored in localStorage

**File:** `public/js/auth.js:18`

```js
localStorage.setItem('token', data.token);
```

Any XSS anywhere in the app can read localStorage and exfiltrate the token. Combined with C1's data access this is a full account-takeover chain waiting on a single script injection.

**Fix:** Issue the JWT as an `httpOnly`, `Secure`, `SameSite=Lax` cookie and drop the localStorage handling.

#### H3. No rate limiting on login or signup

**File:** `src/routes/auth.js:15-31`

`POST /api/auth/login` and `/signup` accept unlimited attempts. Credential stuffing and password brute-forcing are unthrottled; signup is open to bot floods.

**Fix:** Add `express-rate-limit` on the auth routes (e.g., 10 attempts per 15 minutes per IP) plus a small constant delay on failed logins.

---

### MEDIUM

#### M1. CORS allows any origin

**File:** `src/app.js:12`

```js
app.use(cors());
```

The default reflects any origin. Brewlog's API is same-origin in practice, so this mostly widens the attack surface for no benefit — a defense-in-depth gap rather than a direct exploit.

**Fix:** Either remove the `cors()` middleware entirely (same-origin needs none) or restrict it: `cors({ origin: 'https://localhost:3000' })` with the real origin in production config.

#### M2. Stack traces returned to clients

**File:** `src/app.js:38`

```js
app.use((err, req, res, next) => {
  res.status(500).json({ error: err.message, stack: err.stack });
```

Error responses leak file paths, dependency versions, and query fragments — reconnaissance material that makes every other attack easier.

**Fix:** Log the full error server-side; return a generic message and a request ID to the client.

#### M3. No security headers

**File:** `src/app.js` (absent)

No CSP, HSTS, `X-Frame-Options`, or `X-Content-Type-Options`. Each is a mitigation layer that would blunt XSS, clickjacking, and MIME confusion.

**Fix:** Add `helmet` with a CSP allowing only self-hosted scripts; enable HSTS once HTTPS is confirmed stable.

---

### LOW

#### L1. Login emails written to server logs

**File:** `src/routes/auth.js:19`

```js
console.log(`login attempt: ${email}`);
```

PII accumulating in plaintext logs — a liability if logs are ever shared or shipped to a third-party aggregator.

**Fix:** Log a user ID after successful auth instead of the email; drop the attempt log or hash the identifier.

#### L2. Logout doesn't invalidate the token server-side

**File:** `public/js/auth.js:31`

Logout only deletes the client's copy; the JWT stays valid until expiry (24 h). Acceptable for stateless JWTs, worth noting as a best-practice gap.

**Fix:** Shorten token lifetime (≤ 1 h) with silent refresh, or keep a small denylist of logged-out token IDs.

---

## Statistics

| Category | CRITICAL | HIGH | MEDIUM | LOW | Total |
|----------|----------|------|--------|-----|-------|
| Input Validation & Injection | 1 | — | — | — | 1 |
| Authentication | — | 2 | — | — | 2 |
| Authorization | 1 | — | — | — | 1 |
| Secrets & Configuration | 1 | 1 | — | — | 2 |
| Transport & Headers | — | — | 2 | — | 2 |
| Data Exposure | — | — | 1 | 2 | 3 |
| **Total** | **3** | **3** | **3** | **2** | **11** |

## Clean Categories

- **Dependencies** — Lockfile present; no known vulnerabilities in `npm audit`; all six runtime dependencies actively maintained.
- **Cryptography** — Passwords hashed with bcrypt (cost 12) ✓; no homegrown crypto; `crypto.randomUUID()` used for reset tokens.
- **Input Validation (XSS)** — Frontend renders user content via `textContent` throughout; no `innerHTML` sinks found.
- **AI / LLM Features** — Skipped; the project does not call an LLM.
