# Roadmap & Timeline - Car Rental SaaS

## Mục lục
1. [Tổng quan Timeline](#1-tổng-quan-timeline)
2. [Phase 0: Preparation ✅](#2-phase-0-preparation)
3. [Phase 1: Core Infrastructure](#3-phase-1-core-infrastructure-week-5-8)
4. [Phase 2: MVP Features](#4-phase-2-mvp-features-week-9-12)
5. [Phase 3: Polish & Testing](#5-phase-3-polish--testing-week-13-14)
6. [Phase 4: MVP Launch](#6-phase-4-mvp-launch-week-15)
7. [Task Breakdown cho Team 3 người](#7-task-breakdown-cho-team-3-người)
8. [Milestones](#8-milestones)
9. [Risk & Mitigation](#9-risk--mitigation)
10. [Documents Checklist](#10-documents-checklist)

---

## 1. Tổng quan Timeline

```
Week 1-2     │ Architecture & Design Documents ✅
Week 3-4     │ Tech Learning (Spring Boot + Next.js)
Week 5-8     │ Phase 1: Core Infrastructure
Week 9-12    │ Phase 2: MVP Features
Week 13-14   │ Phase 3: Polish & Testing
Week 15      │ MVP Launch
─────────────────────────────────────────────────────────────────
Total: ~4 tháng (full-time)
```

---

## 2. Phase 0: Preparation ✅

### Week 1-2: Architecture & Design (✅ Hoàn thành)

| Tuần | Công việc | Deliverables | Status |
|------|-----------|--------------|--------|
| 1 | Hoàn thiện SPEC.md | ✅ SPEC.md approved | ✅ Done |
| 1 | API Design | ✅ API-Specification.md | ✅ Done |
| 1 | Database Schema Design | ✅ Database-Schema.md | ✅ Done |
| 2 | Architecture Diagram | ✅ Architecture-Diagram.md | ✅ Done |
| 2 | Security Design | ✅ Security-Design.md | ✅ Done |
| 2 | Project Structure | ✅ Project-Structure.md | ✅ Done |

### Week 3-4: Tech Learning (🔄 Đang thực hiện)

| Tuần | Công việc | Deliverables | Ghi chú |
|------|-----------|--------------|---------|
| 3 | Spring Boot 3.x fundamentals | Spring Core, IoC, DI | Có Java cơ bản thì nhanh hơn |
| 3 | Spring Security + JWT | Auth flow, token handling | Quan trọng cho multi-tenant |
| 3 | Spring Data JPA | Repository pattern, migrations | Database access |
| 4 | Next.js 14 (App Router) | Server/Client Components, routing, data fetching | Frontend Lead |
| 4 | PostgreSQL deep dive | Multi-tenant patterns | Cần cho isolation |
| 4 | Docker basics | Containerize app | DevOps/Shared |

**Learning Resources:**
- Spring: [Baeldung Spring Boot Course](https://baeldung.com/spring-boot) - ~20h
- Next.js: [Next.js Learn Course](https://nextjs.org/learn) - ~15h
- PostgreSQL Multi-tenant: [PostgreSQL Documentation](https://www.postgresql.org/docs/) - ~5h
- Docker: [Docker Official Tutorial](https://docs.docker.com/get-started/) - ~8h

**Tracking:** [Learning-Progress.md](docs/Learning-Progress.md)

---

## 3. Phase 1: Core Infrastructure (Week 5-8)

| Week | Backend (Spring) | Frontend (Next.js) | Infrastructure |
|------|------------------|--------------------|----------------|
| 5 | Project setup, Docker compose | Project setup, Next.js config (App Router) | PostgreSQL, Redis |
| 5 | Multi-tenant middleware | Auth context, API service | JWT infrastructure |
| 6 | Tenant, Branch APIs | Branch management UI | - |
| 7 | Vehicle CRUD + Status | Vehicle list, detail UI | - |
| 8 | Customer CRUD | Customer management UI | - |

**Deliverables Phase 1:**
- [ ] Backend skeleton chạy được
- [ ] Frontend skeleton với routing
- [ ] Multi-tenant middleware hoạt động
- [ ] CRUD APIs: Tenant, Branch, Vehicle, Customer
- [ ] UI cơ bản: Dashboard, Branch, Vehicle, Customer

---

## 4. Phase 2: MVP Features (Week 9-12)

| Week | Backend | Frontend | Integrations |
|------|---------|----------|--------------|
| 9 | Booking API (create, update, cancel) | Booking flow UI | - |
| 10 | Pricing Engine (dynamic pricing) | Pricing display | - |
| 10 | Payment API | Payment recording UI | - |
| 11 | Vehicle Transfer API | Transfer request UI | - |
| 11 | Notification Service | - | SMS/Email setup |
| 12 | Reporting API | Report dashboard | - |

**Deliverables Phase 2:**
- [ ] Booking flow hoàn chỉnh
- [ ] Dynamic pricing tính đúng
- [ ] Payment recording đầy đủ
- [ ] Vehicle transfer flow
- [ ] SMS/Email notifications
- [ ] Basic reporting

---

## 5. Phase 3: Polish & Testing (Week 13-14)

| Week | Công việc | Deliverables |
|------|-----------|--------------|
| 13 | Unit tests (Backend: >70% coverage) | Test reports |
| 13 | Integration tests | API tests |
| 13 | Frontend testing (Vitest + Testing Library) | Component tests |
| 14 | Bug fixes | - |
| 14 | Performance optimization | - |
| 14 | Security audit | Security report |
| 14 | Documentation | Setup guide, API docs |

---

## 6. Phase 4: MVP Launch (Week 15)

| Công việc | Deliverables |
|-----------|--------------|
| Deploy lên staging | staging.carrental-saas.com |
| UAT với 1-2 khách hàng beta | Feedback |
| Bug fixes từ feedback | - |
| Deploy lên production | production.carrental-saas.com |
| Onboarding guide | Video + docs |

---

## 7. Task Breakdown cho Team 3 người

### Backend Lead (1 người)

```
Week 1-2:  Thiết kế API + Database Schema ✅
Week 3-4:  Học Spring Security + JPA 🔄
Week 5-8:  Implement: Tenant, Branch, Vehicle, Customer APIs
Week 9-12: Implement: Booking, Pricing, Payment, Transfer APIs
Week 13-14: Testing, Bug fixes, Security audit
Week 15:   Deploy, Support
```

### Frontend Lead (1 người)

```
Week 1-2:  UI/UX Design, Component library structure ✅
Week 3-4:  Học Next.js 14 (App Router) + TailwindCSS 🔄
Week 5-8:  Implement: Dashboard, Branch, Vehicle, Customer pages
Week 9-12: Implement: Booking flow, Payment UI, Reports
Week 13-14: Testing, Polish, Responsive design
Week 15:   Deploy, Support
```

### DevOps/Shared (1 người)

```
Week 1-2:  Architecture diagram, Project structure ✅
Week 3-4:  Học Docker, CI/CD basics 🔄
Week 5-8:  Docker compose, CI/CD pipeline, Database migrations
Week 9-12: Monitoring, Logging, SMS/Email integration setup
Week 13-14: Performance optimization, Security
Week 15:   Deploy, Documentation
```

---

## 8. Milestones

| Milestone | Target | Criteria | Status |
|-----------|--------|----------|--------|
| **M1: Design Done** | Week 2 | SPEC.md, API Spec, DB Schema approved | ✅ Done |
| **M2: Tech Learning Done** | Week 4 | Team có thể code base Spring + Next.js | 🔄 In Progress |
| **M3: Core Infrastructure** | Week 8 | CRUD APIs + UI hoạt động | ☐ Pending |
| **M4: MVP Features** | Week 12 | Full booking flow + payments | ☐ Pending |
| **M5: Testing Complete** | Week 14 | Tests passed, bugs fixed | ☐ Pending |
| **M6: MVP Launch** | Week 15 | Production deployment | ☐ Pending |

---

## 9. Risk & Mitigation

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| Spring learning curve cao | Medium | High | Bắt đầu học sớm Week 3, có mentor online |
| Multi-tenant isolation phức tạp | Medium | High | Thiết kế kỹ Week 1, review trước khi code |
| Integration delays (SMS/Email) | Low | Medium | Implement sau, mock data trước |
| Team coordination | Medium | Medium | Daily standup, shared docs |

---

## 10. Documents Checklist

### Phase 0 - Preparation ✅

| Tài liệu | Priority | Status |
|----------|----------|--------|
| SPEC.md | P0 | ✅ Done |
| API Specification | P0 | ✅ Done |
| Database Schema | P0 | ✅ Done |
| Project Structure | P0 | ✅ Done |
| Architecture Diagram | P1 | ✅ Done |
| Security Design | P1 | ✅ Done |
| Learning Progress | P1 | ✅ Done |
| Onboarding Guide | P1 | ✅ Done |
| Subscription Plans | P2 | ✅ Done |

### Phase 1 - Core Infrastructure

| Tài liệu | Priority | Status |
|----------|----------|--------|
| Setup Guide | P1 | ☐ Pending |
| Docker Compose | P1 | ☐ Pending |
| API Documentation (Swagger) | P1 | ☐ Pending |

### Phase 2 - MVP Features

| Tài liệu | Priority | Status |
|----------|----------|--------|
| CI/CD Pipeline | P2 | ☐ Pending |
| User Guide (Admin) | P2 | ☐ Pending |

### Phase 3 - Testing

| Tài liệu | Priority | Status |
|----------|----------|--------|
| Deployment Guide | P2 | ☐ Pending |
| Testing Guide | P2 | ☐ Pending |

### Phase 4 - Launch

| Tài liệu | Priority | Status |
|----------|----------|--------|
| Tenant Onboarding Guide | P2 | ☐ Pending |
| Admin User Manual | P2 | ☐ Pending |
| End User Guide | P3 | ☐ Pending |

---

## Appendix: Key Files Reference

### Documentation (docs/)

| File | Description |
|------|-------------|
| [SPEC.md](docs/plans/2026-06-12-spec.md) | Technical specification |
| [API-Specification.md](docs/API-Specification.md) | API endpoints |
| [Database-Schema.md](docs/Database-Schema.md) | Database design |
| [Project-Structure.md](docs/Project-Structure.md) | Code organization |
| [Architecture-Diagram.md](docs/Architecture-Diagram.md) | System architecture |
| [Security-Design.md](docs/Security-Design.md) | Security design |
| [Learning-Progress.md](docs/Learning-Progress.md) | Tech learning tracker |
| [Onboarding-Guide.md](docs/onboarding/Onboarding-Guide.md) | New member guide |
| [Subscription-Plans.md](docs/Subscription-Plans.md) | Pricing plans |
| [Roadmap.md](docs/Roadmap.md) | This file |

### Source Code Structure

```
car-rental-saas/
├── backend/              # Spring Boot (future)
├── frontend/            # Next.js (future)
├── docs/                # This documentation
└── docker-compose.yml   # Infrastructure
```

---

*Last updated: 2026-06-15*
