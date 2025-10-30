# Tech Stack Document for Exotic Roleplay Gateway

This document explains, in everyday language, the technology choices behind the Exotic Roleplay Gateway starter template. Our goal is to clarify why each tool or framework was picked and how it helps build a secure, reliable, and easy-to-use web application.

## Frontend Technologies

We chose a modern set of tools to build the part of the app you see and interact with.

• **Next.js (App Router)**
  - Provides both server-side rendering (SSR) and static site generation (SSG).  
  - Lets us build pages and APIs in the same project, simplifying development.

• **TypeScript**  
  - Adds clear “types” to JavaScript to catch mistakes early.  
  - Makes the code easier to understand and maintain over time.

• **React & React Hooks**  
  - Powers all the interactive parts of the UI.  
  - Hooks (like `useState` and `useEffect`) let us manage data and side effects in a simple, organized way.

• **shadcn/ui**  
  - A ready-made collection of React components (buttons, inputs, cards, etc.) that look good out of the box.  
  - Fully accessible and customizable so we can match any design quickly.

• **Tailwind CSS v4**  
  - A “utility-first” styling framework that keeps our CSS small and consistent.  
  - Lets us tweak layouts, colors, and spacing fast, without writing a lot of custom CSS.

• **Adaptive Theming (Light & Dark Mode)**  
  - Automatically follows the user’s system preference for light or dark.  
  - Ensures a comfortable experience day or night.

These choices ensure the interface is fast, responsive, and easy to evolve as needs change.

## Backend Technologies

Behind the scenes, these tools handle data, security, and business logic.

• **Next.js API Routes**  
  - Built-in endpoints that let us write server code alongside the frontend.  
  - Ideal for handling form submissions and protected dashboard routes without a separate server.

• **Better Auth**  
  - Manages user sign-up, sign-in, and session storage.  
  - Offers configurable flows so we can add extra steps (CAPTCHA, email checks) down the line.

• **PostgreSQL**  
  - A reliable, open-source relational database for storing user records, sessions, and any custom data.  
  - Well supported and battle-tested for production workloads.

• **Drizzle ORM**  
  - A type-safe layer on top of PostgreSQL, so database queries look and feel like TypeScript code.  
  - Catches schema mismatches early and helps us write safer database logic.

• **TypeScript on the Server**  
  - Extends the benefits of type safety into our backend code.  
  - Puts request and response structures under compile-time checks, reducing runtime errors.

Together, these components ensure users are authenticated securely and their data is stored and retrieved reliably.

## Infrastructure and Deployment

We designed the infrastructure for consistency, scalability, and easy hand-off between development and production.

• **Docker & Docker Compose**  
  - Wraps the application and database in containers, so everyone runs the same setup locally and in production.  
  - Simplifies environment setup—no more “But it works on my machine” problems.

• **Vercel Hosting**  
  - Seamlessly deploys Next.js projects with zero-config.  
  - Provides automatic global CDN, continuous deploys on each Git push, and built-in caching for speed.

• **Git & Version Control**  
  - All code is tracked in Git, ensuring a clear history of changes and easy collaboration.  
  - Branching workflows allow us to test new features safely before merging to production.

• **Environment Variables**  
  - Secrets (API keys, database URLs) live in `.env` files locally and in Vercel’s dashboard in production.  
  - Keeps sensitive data out of source control and fine-grained per-environment configuration.

These infrastructure choices make onboarding new developers quick, keep deployments predictable, and allow the app to grow without major rework.

## Third-Party Integrations

A few key services plug into our system to handle specialized tasks.

• **Better Auth**  
  - Outsources the complexity of secure authentication and session management.  
  - Easily configurable for future multi-factor or third-party login providers.

• **shadcn/ui**  
  - Accelerates UI development with polished, accessible components maintained by an active community.

• **Vercel Platform**  
  - Provides analytics, automatic rollbacks, and preview URLs on each pull request.  
  - Integrates with Git providers for smooth CI/CD.

• **Drizzle ORM**  
  - Connects to PostgreSQL under the hood, giving us the benefits of a full-featured ORM with modern TypeScript support.

These integrations let us focus on delivering unique features instead of building every piece from scratch.

## Security and Performance Considerations

We’ve baked in best practices to keep data safe and pages snappy.

• **Secure Authentication**  
  - Better Auth handles password hashing, session cookies, and CSRF protection.  
  - Route protection ensures only logged-in users can access the dashboard.

• **End-to-End Type Safety**  
  - TypeScript and Drizzle ORM catch type mismatches before they hit production, reducing bugs.

• **Server-Side Rendering & Static Generation**  
  - Next.js pre-renders pages when possible.  
  - Reduces time-to-first-byte and improves SEO and accessibility.

• **Container Isolation**  
  - Docker containers sandbox services, limiting the blast radius of any misconfiguration.

• **Environment-Based Secrets**  
  - Credentials and API keys never live in code, minimizing exposure risk.

Together, these measures protect user data and keep the experience smooth, even under load.

## Conclusion and Overall Tech Stack Summary

Exotic Roleplay Gateway brings together a set of proven, developer-friendly technologies to deliver a secure, performant, and easy-to-maintain web application starter:

- Frontend: Next.js, React, TypeScript, Tailwind CSS, shadcn/ui  
- Backend: Next.js API Routes, Better Auth, PostgreSQL, Drizzle ORM, TypeScript  
- Infrastructure: Docker, Docker Compose, Vercel, Git, environment variables  
- Integrations: Better Auth, shadcn/ui, Drizzle ORM, Vercel services  
- Security & Performance: SSR/SSG, type safety, containerization, secrets management

This carefully chosen stack ensures rapid development, consistent environments, strong security, and an excellent user experience. It also serves as a flexible blueprint—whether you’re building a simple verification page or a full-featured dashboard, you’ll have a solid foundation to build on.