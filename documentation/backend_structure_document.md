# Backend Structure Document

This document outlines the backend setup for the "Exotic Roleplay Gateway" starter template. It covers the overall architecture, database management, API design, hosting, infrastructure components, security, monitoring, and maintenance.

## 1. Backend Architecture

**Overview**
- Built on Next.js using the App Router.
- All backend logic lives in the `app/api` folder as Next.js API routes.
- Authentication and database access are handled through separate modules for clear separation of concerns.

**Design Patterns and Frameworks**
- **API Routes**: Each REST endpoint is a file under `app/api/*/route.ts`, following Next.js conventions.
- **Modular Structure**: Business logic is split into:
  - `app/api` for route handlers
  - `lib` for utilities (e.g., `auth.ts`)
  - `db` for schema definitions and connection
- **ORM**: Drizzle ORM provides type-safe database operations and migrations.

**Scalability, Maintainability, Performance**
- **Serverless-Friendly**: Next.js API routes can be deployed as serverless functions, scaling on demand.
- **Type Safety**: TypeScript + Drizzle reduces runtime errors, making it easier to extend and refactor.
- **Containerization**: Docker ensures consistent environments across development and production.
- **Cacheable Endpoints**: Next.js settings allow caching of API responses where applicable.

## 2. Database Management

**Technology**
- PostgreSQL (relational SQL database).
- Drizzle ORM for type-safe queries and schema definitions.

**Data Flow**
- Connection configuration in `db/index.ts` reads environment variables for host, port, user, password, and database name.
- Schemas defined in `db/schema/*.ts` map directly to database tables.
- Drizzle handles query building, migrations, and type inference.

**Data Practices**
- **Migrations**: Manage schema changes over time using Drizzle’s migration tooling.
- **Environment Variables**: Store sensitive database credentials in `.env` files, not in source control.
- **Pooling**: Use connection pooling (configured in Drizzle) to manage concurrent database connections.

## 3. Database Schema

**Human-Readable Description**
- **users**: Stores user credentials and profile details.
  - `id`: Unique identifier
  - `email`: User email, unique
  - `hashed_password`: Securely hashed password
  - `created_at`: Timestamp of account creation

- **sessions**: Tracks active user sessions.
  - `id`: Session ID
  - `user_id`: References `users.id`
  - `expires_at`: When the session expires

- **verifications** (example extension for Discord verification)
  - `id`: Unique record ID
  - `discord_id`: User’s Discord ID
  - `ip_address`: Encrypted IP address
  - `status`: Verification status (`pending`, `success`, `failed`)
  - `created_at`, `updated_at`: Timestamps

**SQL Schema (PostgreSQL)**
```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  hashed_password TEXT NOT NULL,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

CREATE TABLE sessions (
  id UUID PRIMARY KEY,
  user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  expires_at TIMESTAMP WITH TIME ZONE NOT NULL,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

CREATE TABLE verifications (
  id SERIAL PRIMARY KEY,
  discord_id VARCHAR(50) NOT NULL,
  ip_address BYTEA NOT NULL,
  status VARCHAR(20) NOT NULL DEFAULT 'pending',
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
```

## 4. API Design and Endpoints

**Approach**
- RESTful API using Next.js API routes.
- Request and response bodies validated with TypeScript types and optionally Zod.

**Key Endpoints**
- **POST /api/auth/sign-up**
  - Purpose: Register a new user.
  - Input: `{ email, password }`
  - Actions: Validate input, hash password, store user in DB.
  - Response: Success or error message.

- **POST /api/auth/sign-in**
  - Purpose: Authenticate existing user.
  - Input: `{ email, password }`
  - Actions: Verify credentials, create session.
  - Response: Session token or error.

- **GET /api/auth/session**
  - Purpose: Retrieve current session info.
  - Input: Session cookie or header.
  - Actions: Validate session, fetch user.
  - Response: User profile or unauthorized.

- **POST /api/auth/sign-out**
  - Purpose: End user session.
  - Actions: Destroy session record.
  - Response: Confirmation.

- **POST /api/verify** (optional extension)
  - Purpose: Handle Discord/CAPTCHA verification.
  - Input: `{ discord_id, captcha_token }`
  - Actions: Validate CAPTCHA, encrypt IP, insert into `verifications`, send webhook.
  - Response: Verification status.

## 5. Hosting Solutions

**Primary Environment**
- **Vercel**
  - Serverless deployment of Next.js frontend and API routes.
  - Built-in CDN, automatic SSL, and global edge network.
  - Benefit: Zero-configuration deploys, instant scaling, and minimal operational overhead.

**Secondary / Local Development**
- **Docker & Docker Compose**
  - Services:
    - `app`: Next.js application container
    - `postgres`: Official PostgreSQL container
  - Benefit: Reproducible environments on any machine, easy onboarding.

## 6. Infrastructure Components

- **Load Balancer**: Handled by Vercel’s edge network for global traffic distribution.
- **Caching**:
  - **CDN**: Vercel’s built-in CDN for static assets and cached SSR pages.
  - **API Caching**: Configurable via Next.js `cache` settings.
- **Container Network**: Docker Compose network isolates app and database.
- **Content Delivery**: Static assets and images are served through Vercel’s asset optimizer.

## 7. Security Measures

- **Authentication & Sessions**
  - Better Auth library for secure sign-in/up flows.
  - HTTP-only, secure cookies for session tokens.
- **Password Handling**
  - Passwords hashed with a strong algorithm (e.g., bcrypt).
- **Data Encryption**
  - Sensitive fields (e.g., IP addresses) can be encrypted before storage using a library like `crypto`.
- **Transport Security**
  - HTTPS enforced by Vercel.
- **Environment Secrets**
  - All API keys, database credentials, and encryption keys stored in environment variables (`.env`).
- **Rate Limiting** (recommended extension)
  - Use a middleware or third-party service to prevent abuse of endpoints like `/verify`.

## 8. Monitoring and Maintenance

- **Logging**
  - Server-side logs captured via Next.js’s built-in logging or a library like `winston`.
- **Error Tracking**
  - Integrate Sentry or LogRocket for real-time error reporting.
- **Performance Metrics**
  - Vercel Analytics for monitoring response times and traffic patterns.
- **Health Checks**
  - Docker health checks for PostgreSQL container.
- **Updates & Patches**
  - Regular dependency audits (e.g., `npm audit`).
  - Schema migrations managed via Drizzle’s migration tool.

## 9. Conclusion and Overall Backend Summary

The backend of "Exotic Roleplay Gateway" combines Next.js API routes, TypeScript, Better Auth, and PostgreSQL (via Drizzle ORM) to deliver a scalable, maintainable, and secure foundation. Containerization with Docker ensures consistent development environments, while deployment on Vercel provides global edge performance and minimal ops overhead. The clear separation of concerns—from API handlers and authentication logic to database schemas—makes this starter kit a strong blueprint for building user-gated applications, including custom verification flows such as Discord or CAPTCHA checks.