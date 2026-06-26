# Architecture Diagram - Car Rental SaaS

## Mục lục
1. [High-Level Architecture](#1-high-level-architecture)
2. [System Context](#2-system-context)
3. [Backend Architecture](#3-backend-architecture)
4. [Frontend Architecture](#4-frontend-architecture)
5. [Database Architecture](#5-database-architecture)
6. [Security Architecture](#6-security-architecture)
7. [Deployment Architecture](#7-deployment-architecture)

---

## 1. High-Level Architecture

```
┌───────────────────────────────────────────────────────────────────────────────────────┐
│                                       CLIENTS                                         │
│  ┌─────────────────┐   ┌─────────────┐   ┌─────────────┐   ┌─────────────┐            │
│  │   SaaS Admin    │   │   Admin     │   │   Staff     │   │  Customer   │            │
│  │  Portal (Super) │   │  Dashboard  │   │   Portal    │   │   Website   │            │
│  └────────┬────────┘   └──────┬──────┘   └──────┬──────┘   └──────┬──────┘            │
└───────────┼───────────────────┼─────────────────┼─────────────────┼───────────────────┘
            │                   │                 │                 │
            └───────────────────┴─────────────────┴─────────────────┘
                                        │
                                        ▼
┌───────────────────────────────────────────────────────────────────────────────────────┐
│                                   API GATEWAY LAYER                                   │
│  ┌─────────────────────────────────────────────────────────────────────────────────┐  │
│  │                                  Nginx / Gateway                                │  │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌───────────────────┐  │  │
│  │  │   JWT    │  │  Tenant   │  │   Rate   │  │   CORS   │  │  Super Admin RLS   │  │  │
│  │  │   Auth   │  │ Resolver  │  │  Limit   │  │  Handler │  │   Bypass Filter   │  │  │
│  │  └──────────┘  └──────────┘  └──────────┘  └──────────┘  └───────────────────┘  │  │
│  └─────────────────────────────────────────────────────────────────────────────────┘  │
└───────────────────────────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                            BACKEND SERVICES                                  │
│                                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐    │
│  │    Auth      │  │   Tenant     │  │   Branch     │  │   Vehicle     │    │
│  │   Service    │  │   Service    │  │   Service    │  │   Service     │    │
│  └──────────────┘  └──────────────┘  └──────────────┘  └──────────────┘    │
│                                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐    │
│  │  Customer    │  │   Booking    │  │   Payment    │  │   Pricing     │    │
│  │   Service    │  │   Service    │  │   Service    │  │   Service     │    │
│  └──────────────┘  └──────────────┘  └──────────────┘  └──────────────┘    │
│                                                                              │
│  ┌──────────────┐  ┌──────────────┐                                     │
│  │   Report     │  │   Email      │                                     │
│  │   Service    │  │   Service    │  (Transfer, SMS → Phase 2)          │
│  └──────────────┘  └──────────────┘                                     │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                           DATA LAYER                                         │
│                                                                              │
│  ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐          │
│  │   PostgreSQL     │  │      Redis       │  │    MinIO/S3      │          │
│  │  (Primary DB)    │  │  (Cache/Session) │  │  (File Storage)  │          │
│  └──────────────────┘  └──────────────────┘  └──────────────────┘          │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 2. System Context

```
                        ┌─────────────────────────┐
                        │   External Systems      │
                        └─────────────────────────┘
                                    │
        ┌──────────────────────────┼──────────────────────────┐
        │                          │                          │
        ▼                          ▼                          ▼
┌───────────────┐          ┌───────────────┐
│ Email Service │          │ Google Maps   │  (SMS → Phase 2)
│ (SMTP/SendGrid)│         │   API        │
└───────────────┘          └───────────────┘
        │                          │                          │
        └──────────────────────────┼──────────────────────────┘
                                   │
                                   ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                        Car Rental SaaS Platform                              │
│                                                                              │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │   Tenant A  │  │  Tenant B   │  │  Tenant C   │  │  Tenant N   │        │
│  │  (Công ty   │  │  (Công ty   │  │  (Công ty   │  │  (Công ty   │        │
│  │   thuê xe)  │  │   thuê xe)  │  │   thuê xe)  │  │   thuê xe)  │        │
│  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────┘        │
│        │                │                │                │                │
│        └────────────────┴────────────────┴────────────────┴────────────────┘
│                                   │                                          │
│                                   ▼                                          │
│                    ┌─────────────────────────┐                              │
│                    │  Shared PostgreSQL DB    │                              │
│                    │  (tenant_id isolation)   │                              │
│                    └─────────────────────────┘                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. Backend Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           Spring Boot Application                            │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                     API Layer (Controllers)                         │    │
│  │  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐        │    │
│  │  │  Auth   │ │ Tenant  │ │ Branch  │ │Vehicle  │ │Customer │  ...   │    │
│  │  │Controller│ │Controller│ │Controller│ │Controller│ │Controller│        │    │
│  │  └────┬────┘ └────┬────┘ └────┬────┘ └────┬────┘ └────┬────┘        │    │
│  └───────┼───────────┼───────────┼───────────┼───────────┼──────────────┘    │
│          │           │           │           │           │                   │
│          └───────────┴───────────┴───────────┴───────────┘                   │
│                              │                                               │
│                              ▼                                               │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                     Service Layer                                    │    │
│  │  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐        │    │
│  │  │  Auth   │ │ Tenant  │ │ Branch  │ │Vehicle  │ │Customer │  ...   │    │
│  │  │ Service │ │ Service │ │ Service │ │ Service │ │ Service │        │    │
│  │  └────┬────┘ └────┬────┘ └────┬────┘ └────┬────┘ └────┬────┘        │    │
│  └───────┼───────────┼───────────┼───────────┼───────────┼──────────────┘    │
│          │           │           │           │           │                   │
│          └───────────┴───────────┴───────────┴───────────┘                   │
│                              │                                               │
│                              ▼                                               │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                   Repository Layer (JPA)                            │    │
│  │  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐        │    │
│  │  │  Auth   │ │ Tenant  │ │ Branch  │ │Vehicle  │ │Customer │  ...   │    │
│  │  │Repository│ │Repository│ │Repository│ │Repository│ │Repository│        │    │
│  │  └────┬────┘ └────┬────┘ └────┬────┘ └────┬────┘ └────┬────┘        │    │
│  └───────┼───────────┼───────────┼───────────┼───────────┼──────────────┘    │
│          │           │           │           │           │                   │
│          └───────────┴───────────┴───────────┴───────────┘                   │
│                              │                                               │
│                              ▼                                               │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                     Database (PostgreSQL)                            │    │
│  │  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐        │    │
│  │  │tenants  │ │branches │ │vehicles │ │customers│ │bookings │  ...   │    │
│  │  └─────────┘ └─────────┘ └─────────┘ └─────────┘ └─────────┘        │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                     Security Layer                                   │    │
│  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐      │    │
│  │  │    JWT     │ │   Tenant   │ │    Role     │ │  Method     │      │    │
│  │  │   Filter   │ │  Context   │ │   Security  │ │  Security   │      │    │
│  │  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘      │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                     Exception Handling                                 │    │
│  │  ┌─────────────────────────────────────────────────────────────┐    │    │
│  │  │              GlobalExceptionHandler                          │    │    │
│  │  │  - ResourceNotFoundException                                │    │    │
│  │  │  - TenantAccessDeniedException                              │    │    │
│  │  │  - BookingConflictException                                 │    │    │
│  │  │  - ValidationException                                      │    │    │
│  │  └─────────────────────────────────────────────────────────────┘    │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 4. Frontend Architecture

Hệ thống gồm **3 frontend apps** chia làm 2 nhóm stack:

| App | Nhóm | Stack | Port (dev) | Người dùng |
|-----|------|-------|------------|------------|
| SaaS Admin Portal | Admin (Angular) | Angular 17+ | 4200 | Super Admin |
| Admin Dashboard | Admin (Angular) | Angular 17+ | 4201 | Tenant staff |
| Customer Website | Customer (Next.js) | Next.js 14+ | 3000 | End customer |

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                       FRONTEND LAYER (3 apps)                                │
│                                                                              │
│  ┌──────────────────────────────────────┐  ┌─────────────────────────────────┐
│  │   Admin Frontends (Angular 17+)      │  │  Customer Frontend (Next.js 14) │
│  │                                      │  │                                 │
│  │  ┌────────────────────────────────┐  │  │  ┌──────────────────────────┐  │
│  │  │  SaaS Admin Portal            │  │  │  │  Customer Website         │  │
│  │  │  /tenants, /subscriptions,    │  │  │  │  (Public + Portal)       │  │
│  │  │  /billing, /platform-reports  │  │  │  │  /, /booking, /profile,  │  │
│  │  └────────────────────────────────┘  │  │  │  /my-bookings            │  │
│  │                                      │  │  └──────────────────────────┘  │
│  │  ┌────────────────────────────────┐  │  │                                 │
│  │  │  Admin Dashboard              │  │  │  App Router (file-based):       │
│  │  │  /branches, /vehicles,        │  │  │  - (public)/  ← public site   │
│  │  │  /bookings, /customers,       │  │  │  - (portal)/  ← auth required │
│  │  │  /reports, /settings          │  │  │  - (auth)/    ← login/register│
│  │  └────────────────────────────────┘  │  │                                 │
│  │                                      │  │  React 18, Server/Client       │
│  │  Angular CLI, Standalone Components  │  │  Components, Server Actions,   │
│  │  Signals, RxJS, NgRx (nếu cần)     │  │  TanStack Query, TailwindCSS   │
│  │  Angular Material / PrimeNG          │  │                                 │
│  └──────────────────────────────────────┘  └─────────────────────────────────┘
│           │                                          │
│           └──────────────────┬───────────────────────┘
│                              ▼
│  ┌─────────────────────────────────────────────────────────────────────────┐
│  │                  Shared Frontend Layer (cross-cutting)                  │
│  │  ┌──────────────────────┐  ┌────────────────────┐  ┌──────────────────┐  │
│  │  │  HTTP Interceptors   │  │  Auth Guard        │  │  Shared Types     │  │
│  │  │  (JWT, Tenant-ID,    │  │  (Admin: route     │  │  (DTO contracts   │  │
│  │  │   Error handling)    │  │   guards; Customer │  │   từ backend      │  │
│  │  │                      │  │   : middleware)    │  │   OpenAPI gen)    │  │
│  │  └──────────────────────┘  └────────────────────┘  └──────────────────┘  │
│  │                                                                         │
│  │  ┌──────────────────────────────────────────────────────────────────┐  │
│  │  │  @car-rental/ui (shared component library, optional)             │  │
│  │  │  Button, Modal, Table, Form inputs, Toast, Date picker           │  │
│  │  │  - Publish dạng package nội bộ (npm workspace hoặc Verdaccio)    │  │
│  │  └──────────────────────────────────────────────────────────────────┘  │
│  └─────────────────────────────────────────────────────────────────────────┘
│                              │
│                              ▼ (HTTP/REST + JWT)
│                       API Gateway (Spring)
└─────────────────────────────────────────────────────────────────────────────┘
```

### 4.1 Admin Frontend (Angular)

Cấu trúc module chính:

```
src/app/
├── core/                      # Singleton services, guards, interceptors
│   ├── auth/                  # AuthService, AuthGuard, RoleGuard
│   ├── http/                  # HTTP interceptors (JWT, Tenant, Error)
│   ├── tenant/                # TenantContext, TenantResolver
│   └── api/                   # Generated từ OpenAPI (orval/ng-openapi)
├── shared/                    # Reusable components, pipes, directives
│   ├── ui/                    # Button, Input, Modal, Table, Toast
│   ├── forms/                 # ReactiveForm helpers, validators
│   └── layout/                # Header, Sidebar, Footer
├── features/                  # Feature modules (lazy-loaded)
│   ├── saas-admin/            # SaaS Admin Portal features
│   │   ├── tenants/
│   │   ├── subscriptions/
│   │   ├── billing/
│   │   └── platform-reports/
│   └── admin/                 # Admin Dashboard features
│       ├── branches/
│       ├── vehicles/
│       ├── bookings/
│       ├── customers/
│       ├── reports/
│       └── settings/
├── app.config.ts              # Standalone bootstrap, providers
├── app.routes.ts              # Top-level routes (lazy loading)
└── app.component.ts           # Root component
```

### 4.2 Customer Frontend (Next.js)

Cấu trúc App Router:

```
app/
├── (public)/                  # Public route group (SEO, SSR)
│   ├── layout.tsx
│   ├── page.tsx               # Home (vehicle catalog)
│   ├── search/page.tsx        # Search & filter
│   └── vehicles/[id]/page.tsx # Vehicle detail
├── (portal)/                  # Authenticated customer portal
│   ├── layout.tsx
│   ├── booking/page.tsx
│   ├── my-bookings/page.tsx
│   └── profile/page.tsx
├── (auth)/                    # Auth flow
│   ├── login/page.tsx
│   └── register/page.tsx
├── api/                       # Route handlers (BFF nếu cần)
├── layout.tsx                 # Root layout
└── page.tsx                   # Root page (redirect)
```

---

## 5. Database Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        PostgreSQL - Multi-tenant                            │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                        SCHEMA: public                               │    │
│  │                                                                      │    │
│  │  ┌─────────────────┐    ┌─────────────────┐                       │    │
│  │  │    tenants      │    │   vehicle_types │                       │    │
│  │  │  (Tenant chính) │    │ (Loại xe/mức giá)│                       │    │
│  │  └────────┬────────┘    └────────┬────────┘                       │    │
│  │           │                        │                                 │    │
│  │           │    ┌───────────────────┼───────────────────┐            │    │
│  │           │    │                   │                   │            │    │
│  │           ▼    ▼                   ▼                   ▼            │    │
│  │  ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐│    │
│  │  │    branches     │    │    vehicles     │    │  pricing_rules  ││    │
│  │  │  (Chi nhánh)    │    │     (Xe)        │    │   (Giá)         ││    │
│  │  └────────┬────────┘    └────────┬────────┘    └─────────────────┘│    │
│  │           │                        │                                 │    │
│  │           │              ┌─────────┴─────────┐                      │    │
│  │           │              │                   │                      │    │
│  │           ▼              ▼                   ▼                      │    │
│  │  ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐        │    │
│  │  │    bookings     │ │    customers    │ │     users       │        │    │
│  │  │   (Đặt xe)      │ │   (Khách hàng)   │ │   (Nhân viên)    │        │    │
│  │  └────────┬────────┘ └─────────────────┘ └─────────────────┘        │    │
│  │           │                                                        │    │
│  │           │    ┌─────────────────┐                                 │    │
│  │           └────│    payments      │                                 │    │
│  │                │   (Thanh toán)   │                                 │    │
│  │                └─────────────────┘                                 │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                     INDEX STRATEGY                                    │    │
│  │                                                                      │    │
│  │  tenant_id (Foreign Key) ──► B-tree Index                            │    │
│  │  status (Enum) ──────────► B-tree Index                              │    │
│  │  created_at (Timestamp) ─► B-tree Index                              │    │
│  │  license_plate (String) ─► Unique Index                              │    │
│  │                                                                      │    │
│  │  Composite Indexes:                                                  │    │
│  │  - idx_vehicles_tenant_status (tenant_id, status)                   │    │
│  │  - idx_bookings_tenant_dates (tenant_id, pickup_date, return_date)   │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 6. Security Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         Security Flow                                      │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                     1. Authentication                                │    │
│  │                                                                      │    │
│  │   User ──► Login Request ──► AuthController                         │    │
│  │                              │                                       │    │
│  │                              ▼                                       │    │
│  │                        AuthService                                   │    │
│  │                              │                                       │    │
│  │                   ┌──────────┴──────────┐                            │    │
│  │                   ▼                     ▼                            │    │
│  │            Validate Credentials    Generate JWT                      │    │
│  │                   │                     │                            │    │
│  │                   │            ┌────────┴────────┐                   │    │
│  │                   │            │ access_token    │                   │    │
│  │                   │            │ refresh_token   │                   │    │
│  │                   │            │ (expires: 1h)  │                   │    │
│  │                   │            └─────────────────┘                   │    │
│  │                   │                     │                            │    │
│  │                   └──────────┬──────────┘                            │    │
│  │                              │                                       │    │
│  │                              ▼                                       │    │
│  │                        Response ◄──── JWT sent to client            │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                     2. Request Authorization                         │    │
│  │                                                                      │    │
│  │   Request + JWT ──► JwtAuthFilter                                    │    │
│  │                          │                                           │    │
│  │                          ▼                                           │    │
│  │                    Extract JWT                                       │    │
│  │                          │                                           │    │
│  │                          ▼                                           │    │
│  │                    Validate JWT                                      │    │
│  │                          │                                           │    │
│  │                          ▼                                           │    │
│  │              ┌─────────────────────┐                                 │    │
│  │              │  Extract Claims:     │                                 │    │
│  │              │  - tenant_id        │                                 │    │
│  │              │  - user_id          │                                 │    │
│  │              │  - role             │                                 │    │
│  │              └─────────────────────┘                                 │    │
│  │                          │                                           │    │
│  │                          ▼                                           │    │
│  │                    TenantContext.set(tenant_id)                       │    │
│  │                          │                                           │    │
│  │                          ▼                                           │    │
│  │                    SecurityContext.set(auth)                          │    │
│  │                          │                                           │    │
│  │                          ▼                                           │    │
│  │                    Controller ◄──── Proceed                          │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                     3. Data Access (Tenant Isolation)                │    │
│  │                                                                      │    │
│  │   Service ──► Repository.findAll()                                   │    │
│  │                    │                                                 │    │
│  │                    ▼                                                 │    │
│  │   ┌──────────────────────────────────────────────┐                   │    │
│  │   │  BaseRepositoryInterceptor                   │                   │    │
│  │   │  Query: SELECT * FROM vehicles              │                   │    │
│  │   │  WHERE tenant_id = :currentTenantId         │                   │    │
│  │   └──────────────────────────────────────────────┘                   │    │
│  │                    │                                                 │    │
│  │                    ▼                                                 │    │
│  │              Filtered Results ◄──── Only tenant's data               │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 7. Deployment Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         Production Deployment                              │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                        CDN (CloudFlare/AWS CloudFront)              │    │
│  │              Static Assets, SSL Termination, Cache                   │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                    │                                        │
│                                    ▼                                        │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                    Load Balancer (AWS ALB / Nginx)                   │    │
│  │                    - Health Check                                    │    │
│  │                    - SSL Offload                                     │    │
│  │                    - Rate Limiting                                   │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                    │                                        │
│        ┌───────────────────────────┼───────────────────────────┐          │
│        │                           │                           │          │
│        ▼                           ▼                           ▼          │
│  ┌─────────────┐           ┌─────────────┐           ┌─────────────┐    │
│  │   API Pod   │           │   API Pod   │           │   API Pod   │    │
│  │  (Spring)  │           │  (Spring)  │           │  (Spring)  │    │
│  └─────────────┘           └─────────────┘           └─────────────┘    │
│        │                           │                           │          │
│        └───────────────────────────┼───────────────────────────┘          │
│                                    │                                        │
│                                    ▼                                        │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                      Docker Swarm / Kubernetes                       │    │
│  │                      - Auto-scaling                                 │    │
│  │                      - Service Discovery                            │    │
│  │                      - Config Maps / Secrets                        │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                    │                                        │
│        ┌───────────────────────────┼───────────────────────────┐          │
│        │                           │                           │          │
│        ▼                           ▼                           ▼          │
│  ┌─────────────┐           ┌─────────────┐           ┌─────────────┐    │
│  │ PostgreSQL  │           │    Redis    │           │    MinIO    │    │
│  │  Primary +  │           │   Cluster   │           │   (Files)   │    │
│  │  Replica    │           │             │           │             │    │
│  └─────────────┘           └─────────────┘           └─────────────┘    │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Component Legend

| Symbol | Meaning |
|--------|---------|
| `┌───┐` | Container/Pod |
| `│` | Data flow |
| `▼` | Direction (down) |
| `*` | Multiple instances |
