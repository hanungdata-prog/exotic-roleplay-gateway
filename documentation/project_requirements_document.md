# Project Requirements Document: Exotic Roleplay Gateway

## 1. Project Overview

Exotic Roleplay Gateway is a lightweight user-gating and verification service designed to sit in front of Discord roleplay servers and ensure only human users pass through. It presents a simple web form where a user completes a CAPTCHA challenge and submits their Discord ID. On the backend, this information is validated, encrypted, stored in a PostgreSQL database, and—in the case of successful verification—a webhook fires to your Discord channel, assigning roles or sending alerts.

We’re building this service to automate and secure the onboarding process for private or gated Discord communities. By handling human verification, data encryption, and Discord webhook integration in one containerized microservice, server owners can avoid spam, bot attacks, and manual role assignment. Key objectives include accuracy of CAPTCHA validation, data confidentiality (encrypted IP storage), reliable webhook delivery, and a developer‐friendly architecture that can be adapted to other frameworks or languages in the future.

## 2. In-Scope vs. Out-of-Scope

**In-Scope (First Version):**
- A static `verify.html` form that accepts:
  - Discord ID (text input)
  - CAPTCHA widget (Google reCAPTCHA or hCaptcha)
  - Submit button
- FastAPI backend with a single `/verify` POST endpoint to:
  1. Validate the CAPTCHA server-side
  2. Encrypt the user’s IP address using Fernet
  3. Persist `discord_id`, `ip_address`, `user_agent`, and timestamp in PostgreSQL via SQLModel
  4. Send a success or failure payload to a Discord webhook URL
- Dockerfile and `docker-compose.yml` for reproducible local/dev environment (FastAPI + Postgres)
- Environment variable support via `.env` (e.g., `DATABASE_URL`, `FERNET_KEY`, `DISCORD_WEBHOOK_URL`, `CAPTCHA_SECRET`)
- Basic rate limiting on `/verify` (e.g., 5 requests per minute per IP) using `slowapi` or similar
- Logging of all verification attempts (success/failure) to stdout in JSON format

**Out-of-Scope (Later Phases):**
- React or Next.js front-end rewrite—only a static HTML/JS page for v1
- Role assignment logic inside Discord (beyond firing the webhook)
- Multi-step or multi-factor authentication flows
- Analytics dashboard or admin interface
- Multi-language or theming support for the form
- User accounts, sign-in, or session management beyond request-level verification

## 3. User Flow

A new or unverified user navigates to `https://gateway.example.com/verify.html`. They see a simple form with two fields: “Discord ID” and the CAPTCHA widget. After entering their Discord tag or numeric ID and completing the CAPTCHA, they click **Verify**. The page shows a loading spinner while it sends a POST request to `/verify` with the form data and client metadata.

On the backend, FastAPI validates the CAPTCHA token with the provider’s API. If the token is valid, the service encrypts the client’s IP address, stores all relevant fields in PostgreSQL, and triggers a Discord webhook notifying the server or assigning a role. The endpoint returns a JSON response `{ success: true, message: 'Verified!' }`. If validation fails (invalid CAPTCHA, database error, rate limit exceeded), the response is `{ success: false, message: 'Verification failed: REASON' }`. The front-end displays this message and allows the user to retry if needed.

## 4. Core Features

- **Static Verification Form**: `verify.html` + vanilla JavaScript to call `/verify`
- **CAPTCHA Validation**: supports Google reCAPTCHA v2/v3 or hCaptcha
- **FastAPI Endpoint**: `/verify` handles request parsing, validation, encryption, DB insertion, webhook call
- **Encryption**: AES-128 via Fernet (Python `cryptography` library) to protect `ip_address`
- **Database**: PostgreSQL accessed using SQLModel (built on SQLAlchemy + Pydantic)
- **Webhook Integration**: HTTP POST to Discord webhook URL with customizable payload
- **Rate Limiting**: configurable limits per IP using `slowapi` or equivalent
- **Logging**: structured JSON logs at INFO and ERROR levels
- **Environment Configuration**: `.env` support for secrets and URLs
- **Containerization**: Docker + Docker Compose for both FastAPI and PostgreSQL

## 5. Tech Stack & Tools

- **Backend Framework**: FastAPI (Python 3.10+)
- **Data Models & Validation**: Pydantic via SQLModel
- **Database**: PostgreSQL 15
- **Encryption**: `cryptography` (Fernet symmetric encryption)
- **Rate Limiting**: `slowapi` or `starlette-limiter`
- **HTTP Client**: `httpx` for Discord webhook calls
- **Environment Management**: `python-dotenv` or Pydantic Settings
- **Containerization**: Docker, Docker Compose
- **Development IDEs/Plugins** (suggested):
  - VS Code with Pylance and Docker extensions
  - PyCharm Professional with Docker plugin
- **Testing**: `pytest` + `httpx` for endpoint tests

## 6. Non-Functional Requirements

- **Performance:** `/verify` endpoint must respond within 300ms under typical conditions (excluding third-party CAPTCHA API latency).
- **Scalability:** Able to handle bursts of 100 requests/minute with horizontal scaling.
- **Security:**
  - All secrets stored in environment variables.
  - HTTPS enforced at deployment (outside scope of service itself).
  - Rate limiting to prevent abuse.
  - Encrypted IP data at rest.
- **Reliability:** 99.9% uptime for the `/verify` endpoint.
- **Usability:** Single-page form must load in under 1 second on 3G networks.
- **Compliance:** GDPR-friendly (no personal data beyond Discord IDs and encrypted IPs).

## 7. Constraints & Assumptions

- We assume availability of a stable CAPTCHA service account (reCAPTCHA or hCaptcha) and valid site/secret keys.
- Discord webhook URL permits unlimited posts or is rate-limited beyond our expected usage.
- Hosting environment will provide TLS termination; the service can run on HTTP internally.
- PostgreSQL credentials and migrations will be managed separately by DevOps.
- No existing user management system; all state is per-request verification logs.

## 8. Known Issues & Potential Pitfalls

- **CAPTCHA Downtime or Latency:** If CAPTCHA provider is slow or down, `/verify` will fail. Mitigation: implement a short timeout (5s) and clear error messaging.
- **Discord Webhook Rate Limits:** Discord limits webhooks to ~30 requests/minute. Mitigation: queue retries with exponential backoff or batch notifications where possible.
- **Data Encryption Key Rotation:** Fernet keys may need rotation. Mitigation: plan for multiple active keys in env and write code to decrypt old entries.
- **Database Connection Pooling:** Under high load, too many connections can exhaust Postgres. Mitigation: configure SQLModel/SQLAlchemy pool size and timeouts.
- **Single Endpoint Bottleneck:** `/verify` handles multiple tasks. Mitigation: consider splitting encryption, DB write, and webhook into background tasks (e.g., Celery) in future phases.

---

This document fully defines the first-phase requirements and architecture for the Exotic Roleplay Gateway. It provides clear guidance on functionality, technology choices, and potential risks, enabling downstream teams or AI-driven documentation to generate implementation blueprints without ambiguity.