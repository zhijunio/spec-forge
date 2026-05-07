# Security Checklist

Companion to **`skills/sdd-review/SKILL.md`**. Quick reference for web application security. Use when a change touches trust boundaries, auth, persistence, uploads, callbacks, or external integrations — or when you need depth beyond the Security axis in the SKILL.

Normal **`sdd-review`** intent still comes from the **review packet** and **diff**; **`docs/features/`** / **`docs/plans/`** are not default authority unless a separate spec-alignment pass was commissioned.

## Table of Contents

- [Pre-Commit Checks](#pre-commit-checks)
- [Authentication](#authentication)
- [Authorization](#authorization)
- [Input Validation](#input-validation)
- [Security Headers](#security-headers)
- [CORS Configuration](#cors-configuration)
- [Data Protection](#data-protection)
- [Dependency Security](#dependency-security)
- [Error Handling](#error-handling)
- [OWASP Top 10 Quick Reference](#owasp-top-10-quick-reference)

## Pre-Commit Checks

- No secrets in code, logs, or staged diffs
- `.gitignore` covers `.env`, `.env.local`, `*.pem`, `*.key`
- `.env.example` uses placeholder values only
- Dependency changes reviewed for trust, maintenance, and necessity
- Sensitive config is not copied into tests, fixtures, or docs
- If the diff touches auth, data storage, or external integrations, do a deeper security pass

## Authentication

- Hash passwords with bcrypt, scrypt, or argon2
- Use `httpOnly`, `secure`, `sameSite` cookies
- Keep session lifetime and refresh policy explicit
- Rate limit login, reset, and verification flows
- Keep reset tokens short-lived and single-use
- Use MFA for high-value actions when the product warrants it
- Treat session material as sensitive, not regular application state

## Authorization

- Check auth on every protected route
- Verify ownership or role for every resource access
- Scope API keys to the minimum required permissions
- Treat IDOR as a default risk and check for it explicitly
- Fail closed when authorization context is missing
- Do not trust client-side visibility or disabled controls as enforcement

## Input Validation

- Validate at the trust boundary, not in the middle
- Prefer allowlists
- Constrain string length, numeric range, format, and file size
- Parameterize SQL and use ORM-safe APIs
- Escape output where the framework does not do it automatically
- Verify redirect targets, callback URLs, and webhook targets
- Treat all external data as untrusted until validated

### File Uploads

- Restrict type and size
- Check content, not just the extension
- Store uploads outside executable paths
- Rename or randomize stored filenames
- Scan or quarantine uploads if the product accepts high-risk file types

## Security Headers

Set the defaults early and keep exceptions rare.

```http
Content-Security-Policy: default-src 'self'; script-src 'self'
Strict-Transport-Security: max-age=31536000; includeSubDomains
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: camera=(), microphone=(), geolocation=()
```

## CORS Configuration

Use the narrowest origin set that still supports the product.

```typescript
cors({
  origin: ['https://yourdomain.com', 'https://app.yourdomain.com'],
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'PATCH', 'DELETE'],
  allowedHeaders: ['Content-Type', 'Authorization'],
})
```

- Never use wildcard origins in production when credentials are involved
- Keep `credentials: true` only when the browser session really needs it
- Re-check CORS after adding new subdomains or preview environments

## Data Protection

- Exclude secrets and sensitive fields from responses
- Avoid logging tokens, passwords, card data, and session material
- Use HTTPS everywhere
- Encrypt backups or regulated data at rest when required
- Minimize retention of personally identifiable data
- Make sure test fixtures do not contain real production secrets

## Dependency Security

- Run `npm audit` or the package manager equivalent
- Keep dependencies updated
- Investigate critical vulnerabilities before release
- Prefer the standard library when it is enough
- Prefer direct dependencies only when the package earns its keep
- Review transitive dependencies for high-risk changes

## Error Handling

- Return generic production errors
- Do not expose stack traces, SQL, or internal identifiers
- Log enough context to debug without leaking secrets
- Keep error messages specific enough for users, but not revealing internals

```typescript
res.status(500).json({
  error: {
    code: 'INTERNAL_ERROR',
    message: 'Something went wrong',
  },
})
```

## OWASP Top 10 Quick Reference

| # | Risk | What to check |
|---|---|---|
| 1 | Broken Access Control | Auth checks on every endpoint, ownership verification, fail-closed defaults |
| 2 | Cryptographic Failures | HTTPS, strong hashing, secure cookie settings, no secrets in code |
| 3 | Injection | Parameterized queries, input validation, output encoding |
| 4 | Insecure Design | Threat modeling, safe defaults, spec-driven security decisions |
| 5 | Security Misconfiguration | Headers, least privilege, restricted CORS, audit dependencies |
| 6 | Vulnerable Components | `npm audit`, version hygiene, minimal dependency footprint |
| 7 | Identification and Authentication Failures | Strong passwords, rate limiting, session management, MFA where needed |
| 8 | Software and Data Integrity Failures | Signed artifacts, verified updates, protected build and deploy paths |
| 9 | Security Logging and Monitoring Failures | Security event logging, redaction, alerting on suspicious activity |
| 10 | SSRF | Allowlist outbound URLs and destinations, restrict callbacks and fetches |

## Review Questions

- Could this be implemented with an existing utility instead of a new dependency?
- Does the code leak implementation details to the client?
- Are we validating at the trust boundary or after the fact?
- Does any new integration increase secret or token handling risk?
- Is any untrusted input crossing a trust boundary unchecked?
- Does any resource access rely on auth without authorization?
- Is there a safer built-in alternative to the new dependency?
