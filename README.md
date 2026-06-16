# Car Rental SaaS Platform

A multi-tenant SaaS platform for car rental businesses — from single-branch local garages to multi-branch enterprise chains. Provides tenant onboarding, branch/vehicle/booking management, dynamic pricing, payments, and a public booking website.

> **Status:** Phase 0 — Design & Learning. Design documents are complete; source code is in development. See [Roadmap](docs/Roadmap.md).

---

## Features

- **Multi-tenant** — 1 deployment serves many rental companies, with strict `tenant_id` data isolation
- **Multi-branch** — central + satellite branches, with vehicle transfers between them
- **Vehicle management** — CRUD, status tracking (available / rented / maintenance)
- **Booking flow** — create, update, cancel; conflict detection
- **Dynamic pricing** — `BasePrice × DayMultiplier × SeasonMultiplier` (weekday / weekend / holiday / peak season)
- **Subscriptions** — FREE / BASIC / PRO / ENTERPRISE plans with per-plan limits
- **Payments** — cash, bank transfer, e-wallet
- **Reports** — revenue by day / month / branch
- **Notifications** — SMS & email (OTP, confirmations, return reminders)

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Backend | Java 17, Spring Boot 3.x, Spring Security, Spring Data JPA |
| Frontend | React 18, TypeScript, Vite |
| Database | PostgreSQL (shared, `tenant_id` isolation) |
| Cache / Session | Redis |
| File storage | MinIO / S3 |
| Auth | JWT (access + refresh) |
| Container | Docker, Docker Compose |
| License | MIT |

---

## Repository Layout

```
car-rental-backend/
├── docs/          # Design documents (spec, API, schema, security, roadmap, …)
├── backend/       # Spring Boot application (in development)
├── frontend/      # React + Vite application (in development)
├── docker-compose.yml
├── docker-compose.prod.yml
├── LICENSE
└── README.md
```

The intended directory layout, naming conventions, and module structure are documented in [Project Structure](docs/Project-Structure.md).

---

## Documentation

All design documents live under [`docs/`](docs/):

| Document | Description |
|----------|-------------|
| [Project Info](docs/Project-Info.md) | Scope, audience, modules, success criteria |
| [Roadmap](docs/Roadmap.md) | Phase-by-phase plan and milestones |
| [API Specification](docs/API-Specification.md) | REST endpoints, request/response shapes |
| [Database Schema](docs/Database-Schema.md) | Tables, relationships, indexes |
| [Architecture Diagram](docs/Architecture-Diagram.md) | System, backend, deployment views |
| [Security Design](docs/Security-Design.md) | Auth, JWT, tenant isolation, threats |
| [Project Structure](docs/Project-Structure.md) | Code layout, naming, coding rules |
| [Multi-Tenant & Multi-Branch](docs/Multi-Tenant-Multi-Branch.md) | Tenant & branch model |
| [Subscription Plans](docs/Subscription-Plans.md) | Plan tiers and limits |
| [User Flows](docs/User-Flows.md) | Admin, staff, and customer flows |
| [Learning Progress](docs/Learning-Progress.md) | Team tech-learning tracker |

---

## Quick Start

> Source code is not yet committed. Once Phase 1 lands (target: Week 8), this section will include:
> 1. `docker-compose up` to start PostgreSQL + Redis + backend + frontend
> 2. Default admin credentials
> 3. `localhost:8080` (API) and `localhost:5173` (UI)

For now, see [Roadmap](docs/Roadmap.md) for the build plan.

---

## Team

3-person team, 3 roles — see [Project Info](docs/Project-Info.md#4-thành-phần-tham-gia-dự-án).

| Role | Focus |
|------|-------|
| Backend Lead | Spring Boot APIs, security, database |
| Frontend Lead | React UI, components, pages |
| DevOps / Shared | Docker, CI/CD, infrastructure |

---

## License

[MIT](LICENSE) — Copyright (c) 2026 dinhtuan99
