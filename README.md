<div align="center">

![Typing SVG](https://readme-typing-svg.demolab.com?font=Fira+Code&size=26&duration=3400&pause=1000&color=3B82F6&center=true&vCenter=true&width=440&lines=CTO+at+Nnayas+eSports;Systems+Architecture;Platform+Engineering;Process+Automation)

<p>
  <a href="https://www.linkedin.com/in/nykolaskauan/"><img src="https://img.shields.io/badge/LinkedIn-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white" alt="LinkedIn" /></a>
  <a href="mailto:nykolaskauansilva@gmail.com"><img src="https://img.shields.io/badge/Email-EA4335?style=for-the-badge&logo=gmail&logoColor=white" alt="Email" /></a>
  <a href="https://www.nnayas.com"><img src="https://img.shields.io/badge/Nnayas-3B82F6?style=for-the-badge&logo=googlechrome&logoColor=white" alt="Nnayas" /></a>
  <img src="https://komarev.com/ghpvc/?username=NykolasK&style=for-the-badge&color=3B82F6&label=PROFILE+VIEWS" alt="Profile views" />
</p>

</div>

---

# Nykolas Kauan

Chief Technology Officer of [Nnayas eSports](https://www.nnayas.com), a Brazilian esports organization operating competitive teams, influencer management, marketing, and a digital solutions practice.

I have held technical ownership since the company's inception: architecture, infrastructure, engineering process, and technical roadmap. I run the technical organization as a single engineer, which sets the design constraint for everything below. Systems have to be operable by one person, so correctness is enforced by tooling and tests rather than by headcount.

---

## Internal Business Platform

**Company-wide ERP, built in-house.** In active development, running in production.

A modular management system covering internal operations end to end, with an external portal for client accounts. The platform is proprietary, so what follows describes architecture and engineering practice rather than product detail.

**Architecture**

- **NestJS + Prisma + PostgreSQL 16** backend. 123 domain models, 85 versioned migrations, 69 feature modules, 458 routes.
- **Next.js 15 + React + Tailwind 4** frontend. 145 routed pages.
- **Turborepo monorepo** with shared `types` and `config` packages, enforcing a single source of truth for DTOs across API and web.
- **Authorization** combines role (6 roles) with a per-user, per-module permission matrix (`NONE | VIEW | EDIT | MANAGE`, with delete as an independent grant). Route policies are generated into an inventory document by a script, and a unit test fails the build when the inventory and the runtime policies diverge. Authorization drift cannot pass CI silently.
- **Event-driven automation** via the Outbox Pattern. Domain events are written in the same transaction as the business change, then published by a worker as a signed envelope carrying `HMAC-SHA256` over the payload with key-id rotation support. The consumer verifies the signature in constant time, enforces a delivery window, and reserves `eventId` in PostgreSQL for idempotency before routing. At-least-once delivery with exactly-once effects.
- **10 domain event types** feeding 8 automation flows in a self-hosted workflow engine, split between event-triggered and scheduled.
- **Object storage** through S3-compatible presigned URLs, keeping file payloads off the API process.
- **Hardening**: Helmet, request throttling and rate limiting, runtime DTO validation, and a client-bundle boundary check that fails the build if a server-only environment variable reaches browser code.

**Verification and operations**

- 230 test files across unit, database-integration, and Playwright end-to-end suites.
- Dedicated test database with a guard script that refuses to run destructive suites against a non-test connection string.
- Data integrity and relational auditing scripts run as their own quality gate.
- Secret scanning wired into the security verification step.
- Containerized deployment with build-triggered releases, automated migrations, managed TLS, and a database exposed only on the internal network.

---

## Nayatsu

**Minecraft server network.** Beta, 50+ players onboarded.

A multi-server network running **50+ plugins in production**, combining plugins written in-house with third-party plugins forked and patched where upstream behavior did not fit the network.

- **Java 21**. A **Velocity** proxy plugin coordinates the network, with **Paper** plugins running gameplay.
- Systems maintained across the network: matchmaking queue, cosmetics, minigames, Skywars, leaderboards, dialogue engine, and party games.
- The Skywars engine carries **17 NMS compatibility modules spanning Minecraft 1.8 through 1.21**, isolating version-specific server internals behind a common interface so gameplay code stays version-agnostic.
- **Redis Pub/Sub** as the cross-process message bus between the web layer and the game network, carrying VIP entitlements, in-game currency, and account linking on dedicated channels. The subscriber runs on a supervised daemon thread with an automatic reconnect loop, because a dropped Redis connection must not require a server restart.
- **MySQL** for persistence, **Maven** and **Gradle** builds, self-managed **VPS** provisioning and tuning.

---

## Selected Client and Product Work

**WhatsApp ticketing and customer service platform.** Deployed a customer support system built on a fork of an open-source ticketing engine, modified for the client's workflow. Containerized with Docker Compose: PostgreSQL 16, Redis, and a background worker service, separated into backend and frontend deployments.

**Nnayas corporate website.** Zero-trust edge architecture. Stateless **Vite + React** frontend holding no credentials, with all business logic, validation, and authorization enforced in a **Hono** API running on **Cloudflare Workers**, backed by **PostgreSQL** through **Drizzle ORM**.

**WordPress e-commerce.** Complete storefront delivered for a large client, including theme, catalog, and checkout.

**Clinic management system.** Multi-package monorepo MVP for practice management.

**Discord bots.** `discord.py` bots using slash commands, modal forms, and interactive component views for community operations.

---

## Stack

```
Languages      TypeScript, Java 21, Python, JavaScript, SQL
Backend        NestJS, Node.js, Hono, Express, Django-style REST design
Frontend       Next.js 15, React, Tailwind CSS 4, Vite
Data           PostgreSQL, MySQL, Prisma, Drizzle
Messaging      Redis Pub/Sub, Outbox Pattern, HMAC-signed webhooks, n8n
Minecraft      Velocity, Paper, NMS multi-version support, Maven, Gradle
Infrastructure Docker, Docker Compose, Coolify, Cloudflare, Nginx, TLS, VPS
CI/CD          GitHub Actions, automated migrations, quality gates
Testing        Vitest, Playwright, database-integration suites
Auth           better-auth, RBAC with per-module permission matrices
Tooling        pnpm, Turborepo, ESLint, Git
```

---

## Engineering Principles

- **Authorization is generated and tested, never remembered.** A permission model that lives only in reviewers' heads regresses. Route policies are emitted as an artifact and asserted by a test.
- **Events are transactional.** An outbox write in the same transaction as the business change is the difference between an automation that is reliable and one that is merely usually correct.
- **Delivery guarantees are explicit.** At-least-once transport plus consumer-side idempotency, stated in the design rather than assumed.
- **Recurring manual work becomes a service.** Any operation performed by hand on a regular cadence is a candidate for automation.
- **Leverage compensates for scale.** A single-engineer organization requires tooling and process to carry the load that headcount otherwise would.
- **Decisions are documented.** Operational runbooks, environment catalogs, and architecture records exist so the next engineer starts from a known state.

---

## Currently Deepening

- Distributed systems: consistency models, failure modes, partition behavior
- Message broker internals: delivery semantics, dead-letter strategies, consumer backpressure
- System design patterns: CQRS, Saga, circuit breaker
- Multi-tenant data isolation and row-level authorization models

---

## Activity

<div align="center">

<img height="165" src="https://github-readme-stats.vercel.app/api/top-langs/?username=NykolasK&layout=compact&langs_count=8&hide_border=true&theme=tokyonight&bg_color=0D1117&title_color=3B82F6" alt="Top languages" />

<img src="https://github-readme-activity-graph.vercel.app/graph?username=NykolasK&bg_color=0D1117&color=3B82F6&line=3B82F6&point=FFFFFF&area=true&hide_border=true" alt="Contribution activity" width="98%" />

</div>

> Nearly all of the work described above lives in private repositories. Architecture and technical decisions are available for discussion directly.

---

## Contact

Open to discussion on systems architecture, engineering leadership, esports and creator-economy technology, and technical partnerships.

**LinkedIn:** [nykolaskauan](https://www.linkedin.com/in/nykolaskauan/)
**Email:** [nykolaskauansilva@gmail.com](mailto:nykolaskauansilva@gmail.com)
**Location:** Cascavel, Paraná, Brazil

<div align="center">

<br>

> *"Simplicity is a prerequisite for reliability."*
> Edsger W. Dijkstra

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:3B82F6,100:1E40AF&height=110&section=footer" alt="" />

</div>
