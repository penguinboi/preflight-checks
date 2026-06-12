# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/).

## [1.1.0] - 2026-06-11

### Fixed
- Rollback tree: Amplify CLI rollback now uses `RETRY` with a previous job ID — `RELEASE` rebuilds the latest (broken) commit
- GDPR DPO requirement corrected to the actual Article 37 triggers (there is no employee-count threshold)
- Removed the defunct Twitter/X Card Validator; X card previews now go through the post composer
- `datePublished` guidance: date-only ISO 8601 is valid; full datetime with timezone is preferred, not required
- noindex pages keep OG/Twitter tags when shareable — social scrapers ignore robots meta
- CORS check reframed to overly permissive configuration (absent CORS is secure by default)
- Password hashing check: salted fast hashes are still inadequate; recommend argon2id/scrypt/bcrypt/PBKDF2
- Search Console "Coverage report" updated to its current name, the Page indexing report
- US state privacy law check no longer omits earlier-effective laws (VA, CO, CT, UT, TX, OR, MT, FL)
- Do Not Track demoted to its CalOPPA disclosure-only status; GPC remains the required signal

### Added
- Database migration branch and mobile app rollback section in the rollback tree
- Breach-notification triggers in the incident path and a data-exposure field in the post-mortem template
- AI/LLM security category (prompt injection, untrusted model output, spend caps) with a conditional checklist row
- Revoke-and-rotate requirement for any secret found in source or git history
- Operational coverage: billing alarms, TLS certificate and domain expiry, launch capacity/load testing, transactional email deliverability
- Quality audit severity levels block; manual-verification flags in the SEO and Ops audit prompts
- Open-Source Readiness option in the content-suggestion issue template
- README Contributing section, Check Links badge, and the Post-Launch Runbook named in the flow diagram
- Weekly scheduled link-check run, SHA-pinned workflow actions, and Dependabot for GitHub Actions

### Changed
- Audit prompts degrade gracefully when copy-pasted without the surrounding playbook (skip clauses, web-search fallback, placeholder guidance)
- CONTRIBUTING audit-structure description matches the actual audits; PR review expectations documented

## [1.0.0] - 2026-03-08

### Added
- Production Readiness Playbook with 8 audits (Quality, Security, Compliance & Legal, Accessibility, Performance, SEO & Discoverability, Operational Readiness, Open-Source Readiness)
- Pre-Launch Checklist with MUST / SHOULD / NICE priority tiers
- Post-Launch Runbook with 72-hour monitoring guide and platform-specific sections
- Rollback Decision Tree for incident triage
- Start Here prompt for AI coding assistants
- Contributing guide, code of conduct, and security policy
- Issue and PR templates
- CI link checker
