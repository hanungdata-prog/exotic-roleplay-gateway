# Security Guidelines for Exotic Roleplay Gateway

This document outlines security best practices and requirements for the Exotic Roleplay Gateway, a full-stack Next.js starter template for user verification and gating. It covers secure design, implementation, and maintenance across authentication, data handling, infrastructure, and DevOps.

---

## 1. Authentication & Access Control

### 1.1 Secure Authentication Flows
- Use a battle-tested library (Better Auth / NextAuth) with:
  - Strong password policies (minimum length, complexity, rotation).
  - Secure password hashing (Argon2 or bcrypt) with per-user salts.
  - Account lockout and CAPTCHA after repeated failures.
- Enforce Multi-Factor Authentication (MFA) for privileged or high-risk accounts.

### 1.2 Session Management
- Generate cryptographically strong, unpredictable session IDs.
- Store session tokens in `HttpOnly`, `Secure`, and `SameSite=Strict` cookies.
- Enforce both idle and absolute session timeouts (e.g., 15 min idle, 8 h absolute).
- Invalidate sessions on logout or password change.
- Protect against session fixation: regenerate session ID upon privilege elevation.

### 1.3 Role-Based Access Control (RBAC)
- Define roles (e.g., `user`, `moderator`, `admin`) and map permissions explicitly.
- Enforce authorization checks server-side on every protected route and API endpoint.
- Deny by default; grant only the minimum necessary privileges.

---

## 2. Input Handling & Processing

### 2.1 Validate & Sanitize All Inputs
- Treat all external input (forms, API requests, Webhooks) as untrusted.
- Frontend: use Zod or built-in Next.js validation for immediate feedback.
- Backend: use Pydantic (Python) or Zod/TypeScript types to validate request bodies, query parameters, headers.
- Enforce strict schemas: reject unexpected fields.

### 2.2 Prevent Injection Attacks
- Use parameterized queries or a reputable ORM (DrizzleORM, SQLAlchemy, SQLModel).
- Never concatenate user input into SQL or shell commands.
- For any raw queries, strictly escape all inputs.

### 2.3 Mitigate XSS & HTML Injection
- Escape or encode user-supplied content before rendering in React components.
- Enforce a strict Content Security Policy (CSP) that only allows trusted script sources.
- Sanitize any rich text or HTML inputs with a vetted library (DOMPurify).

### 2.4 Secure Redirects & File Uploads
- Validate redirect targets against an allow-list; disallow open redirects.
- For file uploads:
  - Restrict file types, extensions, and sizes.
  - Store uploads outside the webroot or on a dedicated object storage (S3) with presigned URLs.
  - Scan uploads for viruses/malware.
  - Defend against path traversal by sanitizing file names.

---

## 3. Data Protection & Privacy

### 3.1 Encryption & Secure Storage
- Enforce HTTPS (TLS 1.2+) for all traffic. Redirect HTTP to HTTPS.
- Encrypt sensitive fields at rest (e.g., IP addresses, PII) using AES-256 or a similar standard.
- Use field-level encryption (e.g., Python’s `cryptography.Fernet`) where required.
- Store secrets (API keys, encryption keys, DB credentials) in a secrets manager (AWS Secrets Manager, Vault), not in code or plaintext .env files.

### 3.2 Database Security
- Use a dedicated least-privileged database user account for the application.
- Enforce encrypted connections (SSL) between the app and the database.
- Regularly rotate DB credentials.
- Mask or truncate sensitive data in logs and error traces.

### 3.3 Privacy Compliance
- Adhere to GDPR/CCPA principles:
  - Collect only necessary PII.
  - Implement data retention and deletion policies.
  - Provide users with data access/export and deletion mechanisms.

---

## 4. API & Service Security

### 4.1 Secure API Endpoints
- Prefix all APIs under a versioned namespace (e.g., `/api/v1/verify`).
- Enforce HTTPS and reject insecure requests.
- Authenticate every protected endpoint; avoid “unprotected” catch-alls.
- Validate and sanitize request payloads rigorously.

### 4.2 Rate Limiting & Throttling
- Implement rate limiting (e.g., slowapi for FastAPI or rate-limiting middleware for Next.js).
- Throttle high-risk endpoints (login, register, verify) to defend against brute force.

### 4.3 CORS & CSRF Protection
- Configure CORS to allow only trusted origins (e.g., your domain).
- Use anti-CSRF tokens (`sync` token pattern) for state-changing requests if you rely on cookies.

### 4.4 Minimizing Data Exposure
- Return only necessary fields in API responses.
- Avoid leaking internal implementation details or stack traces.

---

## 5. Web Application Security Hygiene

### 5.1 Security HTTP Headers
- Enforce the following headers via Next.js `next.config.js` or a middleware:
  - Strict-Transport-Security: `max-age=63072000; includeSubDomains; preload`
  - X-Frame-Options: `DENY`
  - X-Content-Type-Options: `nosniff`
  - Referrer-Policy: `strict-origin-when-cross-origin`
  - Content-Security-Policy: define scripts, styles, and frame sources.

### 5.2 Secure Cookies & Storage
- Set `Secure`, `HttpOnly`, `SameSite=Strict` on session/auth cookies.
- Avoid storing any secrets in `localStorage` or `sessionStorage`.

### 5.3 Clickjacking & Subresource Integrity
- Use `X-Frame-Options: DENY` or CSP `frame-ancestors 'none'`.
- Add Subresource Integrity (SRI) hashes for any external scripts/styles.

---

## 6. Infrastructure & Configuration Management

### 6.1 Container & Server Hardening
- Use minimal, up-to-date base images (Node.js / Python slim).
- Run application processes as non-root inside containers.
- Disable unnecessary OS services and ports.
- Apply OS and library security patches promptly.

### 6.2 Secure Configuration
- Avoid default credentials; change all examples.
- Store runtime configuration in environment variables or a secrets manager.
- Keep debug modes and verbose error reporting disabled in production.

### 6.3 TLS/SSL Best Practices
- Use strong cipher suites and TLS 1.2+ only.
- Disable legacy protocols (SSLv3, TLS 1.0/1.1).
- Automate certificate renewal (Let’s Encrypt or managed CA).

---

## 7. Dependency & DevOps Security

### 7.1 Dependency Management
- Lock versions with `package-lock.json`, `yarn.lock`, or `Pipfile.lock`.
- Regularly scan dependencies for known vulnerabilities (Dependabot, Snyk, OWASP Dependency-Check).
- Remove unused packages to shrink the attack surface.

### 7.2 CI/CD & Code Quality
- Integrate automated security scans in CI (ESLint, Bandit, static analysis for secrets).
- Enforce code reviews with security-focused checklists.
- Store CI secrets in secure vault or platform-managed secrets store.
- Limit CI/CD service permissions following the principle of least privilege.

---

## 8. Ongoing Security Practices

- Schedule periodic penetration tests and code audits.
- Monitor logs and alerts for suspicious activity.
- Maintain an incident response plan and rotate keys immediately upon compromise.
- Keep security processes documented and train the team regularly.

---

By adhering to these guidelines, the Exotic Roleplay Gateway will be built with security by design, ensuring resilience against common threats and compliance with industry-standard best practices.