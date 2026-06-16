# Learning Progress Tracker - Car Rental SaaS

## Mục lục
1. [Overview](#1-overview)
2. [Team Members](#2-team-members)
3. [Learning Tracks](#3-learning-tracks)
4. [Weekly Progress](#4-weekly-progress)
5. [Resources](#5-resources)
6. [Certification](#6-certification)

---

## 1. Overview

| Item | Details |
|------|---------|
| **Project** | Car Rental SaaS |
| **Phase** | Phase 0: Tech Learning (Week 3-4) |
| **Start Date** | 2026-06-08 |
| **End Date** | 2026-06-19 |
| **Total Duration** | 2 weeks |

### Learning Goals

| Track | Goal | Deadline |
|-------|------|----------|
| Spring Boot 3.x | Can implement basic CRUD APIs | Week 3 |
| Spring Security + JWT | Understand auth flow, implement JWT | Week 3 |
| Spring Data JPA | Repository pattern, migrations | Week 3 |
| Next.js 14 (App Router) | Server/Client Components, routing, data fetching | Week 4 |
| PostgreSQL Multi-tenant | RLS, isolation patterns | Week 4 |
| Docker | Containerize app | Week 4 |

---

## 2. Team Members

| Name | Role | Primary Track | Secondary Track |
|------|------|---------------|-----------------|
| Backend Lead | Backend | Spring Boot, Security, JPA | Docker |
| Frontend Lead | Frontend | Next.js 14, React 18, TypeScript | API Integration |
| DevOps/Shared | DevOps | Docker, CI/CD | PostgreSQL |

---

## 3. Learning Tracks

### Track 1: Spring Boot 3.x Backend

**Assigned to:** Backend Lead
**Status:** 🔄 In Progress

#### Week 3 Goals

| Topic | Sub-topics | Estimated Time | Status |
|-------|------------|----------------|--------|
| Spring Core | IoC, Dependency Injection, Bean lifecycle | 4 hours | ☐ |
| Spring MVC | Controllers, Request/Response, Exception handling | 4 hours | ☐ |
| Spring Data JPA | Repository pattern, Entity mapping, Queries | 4 hours | ☐ |
| Spring Security | Authentication, Authorization, Filters | 4 hours | ☐ |
| Building REST APIs | CRUD, Validation, Pagination | 4 hours | ☐ |

#### Week 3 Deliverables

- [ ] Spring Boot project setup with dependencies
- [ ] Implement 1 sample CRUD API (e.g., Vehicle API)
- [ ] Unit tests for service layer
- [ ] API documentation with Swagger/OpenAPI

#### Progress Checklist

```
Spring Core
  ☐ IoC Container
  ☐ Dependency Injection (Constructor, Setter, Field)
  ☐ Bean Scopes (Singleton, Prototype, etc.)
  ☐ Bean Lifecycle
  ☐ @Configuration and @Bean

Spring MVC
  ☐ @Controller and @RestController
  ☐ @RequestMapping, @GetMapping, @PostMapping, etc.
  ☐ @RequestBody, @ResponseBody
  ☐ @Valid and Validation
  ☐ Exception handling (@ControllerAdvice)

Spring Data JPA
  ☐ JpaRepository, CrudRepository
  ☐ @Entity, @Table, @Column
  ☐ @Id, @GeneratedValue
  ☐ Relationships (@OneToMany, @ManyToOne, etc.)
  ☐ Custom queries (@Query)
  ☐ Pagination and Sorting

Spring Security
  ☐ Spring Security architecture
  ☐ Authentication vs Authorization
  ☐ SecurityFilterChain
  ☐ Password encoding (BCrypt)
  ☐ JWT integration
```

---

### Track 2: Spring Security + JWT

**Assigned to:** Backend Lead
**Status:** 🔄 In Progress

#### Week 3 Goals

| Topic | Sub-topics | Estimated Time | Status |
|-------|------------|----------------|--------|
| JWT Fundamentals | Token structure, Claims, Signing | 2 hours | ☐ |
| JWT Implementation | Generation, Validation, Refresh | 3 hours | ☐ |
| Spring Security Integration | Filter chain, Security context | 3 hours | ☐ |
| Multi-tenant Security | Tenant context, Row-level security | 2 hours | ☐ |

#### Progress Checklist

```
JWT Fundamentals
  ☐ JWT structure (Header, Payload, Signature)
  ☐ Access token vs Refresh token
  ☐ Token expiration (exp, iat)
  ☐ Token claims (sub, tenant_id, role)

JWT Implementation
  ☐ Generate access token with claims
  ☐ Generate refresh token
  ☐ Validate token signature
  ☐ Check token expiration
  ☐ Extract claims from token
  ☐ Token refresh flow

Spring Security Integration
  ☐ UsernamePasswordAuthenticationFilter
  ☐ JwtAuthenticationFilter
  ☐ OncePerRequestFilter
  ☐ SecurityContextHolder
  ☐ ThreadLocal for tenant context

Multi-tenant Security
  ☐ TenantContext (ThreadLocal)
  ☐ Tenant filter in repository
  ☐ Cross-tenant access prevention
  ☐ RLS (Row Level Security) in PostgreSQL
```

---

### Track 3: Next.js 14 (App Router)

**Assigned to:** Frontend Lead
**Status:** 🔄 In Progress

#### Week 4 Goals

| Topic | Sub-topics | Estimated Time | Status |
|-------|------------|----------------|--------|
| Next.js Fundamentals | App Router, file-based routing, layouts | 4 hours | ☐ |
| Server vs Client Components | When to use 'use client', data fetching patterns | 4 hours | ☐ |
| Next.js Project Setup | `create-next-app`, `next.config.mjs`, TailwindCSS, TS config | 2 hours | ☐ |
| TypeScript | Types, Interfaces, Generics | 3 hours | ☐ |
| Data Fetching | Server Components fetch, Server Actions, TanStack Query (Client) | 3 hours | ☐ |
| State Management | React Context (Client Components), Zustand | 4 hours | ☐ |

#### Progress Checklist

```
Next.js Fundamentals
  ☐ App Router vs Pages Router
  ☐ File-based routing (page.tsx, layout.tsx)
  ☐ Route groups (group)/ and parallel routes
  ☐ Root layout vs nested layout
  ☐ Loading and error UI (loading.tsx, error.tsx)

Server vs Client Components
  ☐ Server Components by default
  ☐ 'use client' directive
  ☐ Composing Server + Client Components
  ☐ Passing server data to client components
  ☐ When NOT to use Server Components

Next.js Project Setup
  ☐ create-next-app with App Router + TypeScript + TailwindCSS
  ☐ next.config.mjs (images, redirects, env)
  ☐ Path aliases (@/)
  ☐ Environment variables (.env.local)
  ☐ Build and run scripts

TypeScript
  ☐ Basic types
  ☐ Interfaces and Types
  ☐ Generics
  ☐ Utility types
  ☐ Type guards

Data Fetching
  ☐ fetch in Server Components
  ☐ Caching and revalidation
  ☐ Server Actions for mutations
  ☐ TanStack Query for client-side
  ☐ Optimistic updates

State Management
  ☐ React Context (Client Components)
  ☐ useContext patterns
  ☐ Zustand for global state
  ☐ Server-side data as state
  ☐ Form state with Server Actions
```

---

### Track 4: PostgreSQL Multi-tenant

**Assigned to:** DevOps/Shared
**Status:** 🔄 In Progress

#### Week 4 Goals

| Topic | Sub-topics | Estimated Time | Status |
|-------|------------|----------------|--------|
| PostgreSQL Basics | Queries, Joins, Indexes | 3 hours | ☐ |
| Multi-tenant Patterns | Shared DB, Schema per tenant | 3 hours | ☐ |
| Row Level Security | RLS policies, Enable RLS | 3 hours | ☐ |
| Performance | Indexing strategy, Query optimization | 3 hours | ☐ |
| Migrations | Flyway/Liquibase, Version control | 2 hours | ☐ |

#### Progress Checklist

```
PostgreSQL Basics
  ☐ SELECT, WHERE, ORDER BY
  ☐ JOINs (INNER, LEFT, RIGHT)
  ☐ GROUP BY, HAVING
  ☐ Indexes (B-tree, Hash)
  ☐ EXPLAIN for query analysis

Multi-tenant Patterns
  ☐ Shared database with tenant_id column
  ☐ Schema per tenant
  ☐ Database per tenant
  ☐ Trade-offs analysis

Row Level Security
  ☐ Enable RLS on tables
  ☐ Create RLS policies
  ☐ Set current tenant setting
  ☐ Policy examples

Performance
  ☐ Composite indexes
  ☐ Partial indexes
  ☐ Query optimization
  ☐ Connection pooling (PgBouncer)

Migrations
  ☐ Flyway setup
  ☐ Migration scripts
  ☐ Rollback strategies
  ☐ Seed data
```

---

### Track 5: Docker

**Assigned to:** DevOps/Shared
**Status:** 🔄 In Progress

#### Week 4 Goals

| Topic | Sub-topics | Estimated Time | Status |
|-------|------------|----------------|--------|
| Docker Basics | Images, Containers, Dockerfile | 2 hours | ☐ |
| Docker Compose | Multi-container setup | 2 hours | ☐ |
| Backend Dockerization | Spring Boot + PostgreSQL | 2 hours | ☐ |
| Frontend Dockerization | Next.js (standalone output) | 2 hours | ☐ |
| CI/CD Basics | GitHub Actions | 2 hours | ☐ |

#### Progress Checklist

```
Docker Basics
  ☐ Docker architecture
  ☐ Images vs Containers
  ☐ Dockerfile syntax
  ☐ docker build, run, ps, exec
  ☐ docker-compose basics

Docker Compose
  ☐ Service definition
  ☐ Port mapping
  ☐ Volume mounting
  ☐ Environment variables
  ☐ Network configuration

Backend Dockerization
  ☐ Multi-stage build for Spring Boot
  ☐ JAR file optimization
  ☐ PostgreSQL container
  ☐ Redis container
  ☐ Health checks

Frontend Dockerization
  ☐ Node.js build stage
  ☐ Nginx serving stage
  ☐ SPA routing configuration
  ☐ Environment variables

CI/CD Basics
  ☐ GitHub Actions workflow
  ☐ Build and test
  ☐ Docker build and push
  ☐ Deployment trigger
```

---

## 4. Weekly Progress

### Week 3 Progress (2026-06-08 to 2026-06-12)

| Team Member | Track | Monday | Tuesday | Wednesday | Thursday | Friday |
|-------------|-------|--------|---------|-----------|----------|--------|
| Backend Lead | Spring Boot | ☐ | ☐ | ☐ | ☐ | ☐ |
| Backend Lead | Security + JWT | ☐ | ☐ | ☐ | ☐ | ☐ |
| Frontend Lead | Next.js 14 | ☐ | ☐ | ☐ | ☐ | ☐ |
| DevOps/Shared | Docker | ☐ | ☐ | ☐ | ☐ | ☐ |

### Week 4 Progress (2026-06-15 to 2026-06-19)

| Team Member | Track | Monday | Tuesday | Wednesday | Thursday | Friday |
|-------------|-------|--------|---------|-----------|----------|--------|
| Backend Lead | Spring Boot | ☐ | ☐ | ☐ | ☐ | ☐ |
| Backend Lead | Security + JWT | ☐ | ☐ | ☐ | ☐ | ☐ |
| Frontend Lead | Next.js 14 | ☐ | ☐ | ☐ | ☐ | ☐ |
| DevOps/Shared | Docker | ☐ | ☐ | ☐ | ☐ | ☐ |

---

## 5. Resources

### Spring Boot Learning Resources

| Resource | URL | Time | Status |
|----------|-----|------|--------|
| Spring Boot Official Docs | https://spring.io/projects/spring-boot | 2h | ☐ |
| Baeldung Spring Boot | https://www.baeldung.com/spring-boot | 8h | ☐ |
| Spring Security Guide | https://www.baeldung.com/spring-security | 4h | ☐ |
| Spring Data JPA | https://www.baeldung.com/spring-data-jpa | 4h | ☐ |
| Building REST APIs | https://www.baeldung.com/rest-with-spring-series | 4h | ☐ |

### Next.js Learning Resources

| Resource | URL | Time | Status |
|----------|-----|------|--------|
| Next.js Learn Course | https://nextjs.org/learn | 4h | ☐ |
| Next.js App Router Docs | https://nextjs.org/docs/app | 4h | ☐ |
| React Official Tutorial (foundation) | https://react.dev/learn | 2h | ☐ |
| TypeScript Handbook | https://www.typescriptlang.org/docs/ | 4h | ☐ |
| TanStack Query Guide | https://tanstack.com/query/latest | 3h | ☐ |

### Docker Learning Resources

| Resource | URL | Time | Status |
|----------|-----|------|--------|
| Docker Official Tutorial | https://docs.docker.com/get-started/ | 4h | ☐ |
| Docker Compose Guide | https://docs.docker.com/compose/ | 3h | ☐ |
| Spring Boot + Docker | https://spring.io/guides/containers/ | 2h | ☐ |

### PostgreSQL Multi-tenant Resources

| Resource | URL | Time | Status |
|----------|-----|------|--------|
| PostgreSQL RLS | https://www.postgresql.org/docs/current/ddl-rowsecurity.html | 3h | ☐ |
| Multi-tenant Patterns | https://www.postgresql.org/docs/current/ddl-rowsecurity.html | 2h | ☐ |

---

## 6. Certification

### Completion Criteria

| Track | Criteria | Certification |
|-------|----------|---------------|
| Spring Boot 3.x | Complete sample CRUD API + tests | Spring Boot Certificate |
| Spring Security + JWT | Implement auth flow with JWT | Security Certificate |
| Next.js 14 (App Router) | Build 1 feature page with component library | Next.js Certificate |
| PostgreSQL Multi-tenant | Implement RLS for 1 table | PostgreSQL Certificate |
| Docker | Containerize backend + frontend | Docker Certificate |

### Assessment

| Assessment | Format | Passing Score |
|------------|--------|---------------|
| Spring Boot | Coding challenge (2 hours) | 70% |
| Spring Security | Security audit (1 hour) | 80% |
| Next.js | Build a page from mockup (3 hours) | 70% |
| PostgreSQL | Design multi-tenant schema (1 hour) | 80% |
| Docker | Dockerize a sample app (2 hours) | 70% |

---

## How to Update This Document

1. Update daily progress in Weekly Progress section
2. Mark completed topics in Progress Checklists
3. Update status in Learning Tracks table
4. Add notes/comments as needed
5. Update timestamps for completed items

### Update Log

| Date | Updated By | Changes |
|------|-----------|---------|
| 2026-06-12 | Team | Initial document created |
