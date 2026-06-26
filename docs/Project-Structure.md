# Project Structure - Car Rental SaaS

## Mục lục
1. [Directory Structure](#1-directory-structure)
2. [Backend Structure (Spring Boot)](#2-backend-structure-spring-boot)
3. [Frontend Structure (Angular + Next.js)](#3-frontend-structure-angular--nextjs)
   - 3.1 [Admin Frontend (Angular)](#31-admin-frontend-angular)
   - 3.2 [Customer Frontend (Next.js)](#32-customer-frontend-nextjs)
4. [Naming Conventions](#4-naming-conventions)
5. [Coding Rules](#5-coding-rules)

---

## 1. Directory Structure

```
car-rental-saas/
├── docs/                           # Documentation
│   ├── plans/
│   │   └── 2026-06-12-spec.md
│   ├── User-Flows.md
│   ├── Tech-Trends.md
│   ├── Roadmap.md
│   ├── Project-Structure.md
│   ├── API-Specification.md
│   └── Database-Schema.md
│
├── backend/                        # Spring Boot Application
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/
│   │   │   │   └── com/carrental/
│   │   │   │       ├── CarRentalApplication.java
│   │   │   │       ├── config/           # Configuration
│   │   │   │       ├── controller/       # REST Controllers
│   │   │   │       ├── service/          # Business Logic
│   │   │   │       ├── repository/       # Data Access
│   │   │   │       ├── model/            # JPA Entities
│   │   │   │       ├── dto/              # Data Transfer Objects
│   │   │   │       ├── exception/       # Exception Handling
│   │   │   │       ├── security/        # Security Config
│   │   │   │       └── util/            # Utilities
│   │   │   └── resources/
│   │   │       ├── application.yml
│   │   │       ├── application-dev.yml
│   │   │       └── application-prod.yml
│   │   └── test/
│   │       └── java/
│   │           └── com/carrental/
│   ├── pom.xml
│   └── Dockerfile
│
├── frontend-saas-admin/            # Angular 17+ - SaaS Admin Portal (Super Admin)
│   ├── src/
│   │   ├── app/
│   │   │   ├── core/              # Auth, HTTP interceptors, guards
│   │   │   │   ├── auth/
│   │   │   │   ├── http/
│   │   │   │   ├── tenant/
│   │   │   │   └── api/           # Generated clients (orval)
│   │   │   ├── shared/            # Reusable UI, pipes, directives
│   │   │   │   ├── ui/
│   │   │   │   ├── forms/
│   │   │   │   └── layout/
│   │   │   ├── features/          # Feature modules (lazy)
│   │   │   │   ├── tenants/
│   │   │   │   ├── subscriptions/
│   │   │   │   ├── billing/
│   │   │   │   ├── platform-reports/
│   │   │   │   └── settings/
│   │   │   ├── app.component.ts
│   │   │   ├── app.config.ts      # Standalone bootstrap, providers
│   │   │   └── app.routes.ts      # Top-level routes
│   │   ├── assets/
│   │   ├── environments/
│   │   │   ├── environment.ts
│   │   │   └── environment.prod.ts
│   │   ├── styles.scss
│   │   ├── index.html
│   │   └── main.ts
│   ├── angular.json
│   ├── tsconfig.json
│   ├── package.json
│   ├── Dockerfile                 # Multi-stage: ng build → nginx
│   └── nginx.conf
│
├── frontend-admin/                # Angular 17+ - Admin Dashboard (Tenant staff)
│   ├── src/
│   │   ├── app/
│   │   │   ├── core/              # (tương tự SaaS Admin)
│   │   │   ├── shared/
│   │   │   ├── features/          # Feature modules
│   │   │   │   ├── branches/
│   │   │   │   ├── vehicles/
│   │   │   │   ├── bookings/
│   │   │   │   ├── customers/
│   │   │   │   ├── pricing/
│   │   │   │   ├── reports/
│   │   │   │   └── settings/
│   │   │   ├── app.component.ts
│   │   │   ├── app.config.ts
│   │   │   └── app.routes.ts
│   │   ├── assets/
│   │   ├── environments/
│   │   ├── styles.scss
│   │   ├── index.html
│   │   └── main.ts
│   ├── angular.json
│   ├── tsconfig.json
│   ├── package.json
│   └── Dockerfile
│
├── frontend-customer/             # Next.js 14+ - Customer Website (Public + Portal)
│   ├── app/                        # App Router (file-based routing)
│   │   ├── (public)/              # Public route group (SSR, SEO)
│   │   │   ├── layout.tsx
│   │   │   ├── page.tsx            # Home (vehicle catalog)
│   │   │   ├── search/page.tsx
│   │   │   └── vehicles/[id]/page.tsx
│   │   ├── (portal)/              # Authenticated customer portal
│   │   │   ├── layout.tsx
│   │   │   ├── booking/page.tsx
│   │   │   ├── my-bookings/page.tsx
│   │   │   └── profile/page.tsx
│   │   ├── (auth)/                 # Auth flow
│   │   │   ├── login/page.tsx
│   │   │   ├── register/page.tsx
│   │   │   └── forgot-password/page.tsx
│   │   ├── api/                    # Route handlers (BFF nếu cần)
│   │   ├── layout.tsx              # Root layout
│   │   └── globals.css
│   ├── components/                 # Reusable Components
│   │   ├── ui/                     # Base UI components
│   │   ├── forms/                  # Form components
│   │   └── layout/                 # Layout components
│   ├── lib/                        # Utilities, services, hooks
│   │   ├── hooks/                  # Custom Hooks
│   │   ├── services/               # API Services
│   │   ├── context/                # React Context (Client Components)
│   │   ├── utils/                  # Utilities
│   │   └── types/                  # TypeScript types
│   ├── public/
│   ├── next.config.mjs
│   ├── tailwind.config.ts
│   ├── package.json
│   └── Dockerfile                 # Multi-stage: next build → standalone
│
├── docker-compose.yml              # Docker Compose for local dev
├── docker-compose.prod.yml        # Docker Compose for production
└── README.md
```

---

## 2. Backend Structure (Spring Boot)

```
backend/src/main/java/com/carrental/
│
├── CarRentalApplication.java
│
├── config/                         # Configuration
│   ├── SecurityConfig.java        # Spring Security config
│   ├── JwtConfig.java             # JWT configuration
│   ├── TenantConfig.java          # Multi-tenant config
│   ├── WebConfig.java             # Web config
│   └── OpenApiConfig.java         # Swagger/OpenAPI config
│
├── controller/                     # REST Controllers
│   ├── AuthController.java
│   ├── TenantController.java
│   ├── BranchController.java
│   ├── VehicleController.java
│   ├── CustomerController.java
│   ├── BookingController.java
│   ├── PaymentController.java
│   ├── PricingController.java
│   └── ReportController.java
│
├── service/                        # Business Logic
│   ├── AuthService.java
│   ├── TenantService.java
│   ├── BranchService.java
│   ├── VehicleService.java
│   ├── CustomerService.java
│   ├── BookingService.java
│   ├── PaymentService.java
│   ├── PricingService.java
│   └── ReportService.java
│
├── repository/                     # Data Access
│   ├── TenantRepository.java
│   ├── BranchRepository.java
│   ├── VehicleRepository.java
│   ├── CustomerRepository.java
│   ├── BookingRepository.java
│   ├── PaymentRepository.java
│   ├── PricingRuleRepository.java
│   └── VehicleTransferRepository.java *(Phase 2)*
│
├── model/                          # JPA Entities
│   ├── Tenant.java
│   ├── Branch.java
│   ├── Vehicle.java
│   ├── VehicleType.java
│   ├── Customer.java
│   ├── Booking.java
│   ├── Payment.java
│   ├── PricingRule.java
│   └── VehicleTransfer.java *(Phase 2)*
│
├── dto/                            # Data Transfer Objects
│   ├── request/                    # Request DTOs
│   │   ├── LoginRequest.java
│   │   ├── CreateBookingRequest.java
│   │   └── CreateVehicleRequest.java
│   └── response/                   # Response DTOs
│       ├── ApiResponse.java
│       ├── BookingResponse.java
│       └── VehicleResponse.java
│
├── exception/                      # Exception Handling
│   ├── GlobalExceptionHandler.java
│   ├── ResourceNotFoundException.java
│   ├── TenantNotFoundException.java
│   └── BookingConflictException.java
│
├── security/                       # Security
│   ├── JwtTokenProvider.java
│   ├── JwtAuthenticationFilter.java
│   ├── TenantContext.java          # ThreadLocal for tenant
│   └── UserDetailsServiceImpl.java
│
└── util/                          # Utilities
    ├── DateUtils.java
    └── PricingCalculator.java
```

---

## 3. Frontend Structure (Angular + Next.js)

Hệ thống gồm **3 frontend apps** trên 2 stack:

| App | Folder | Stack |
|-----|--------|-------|
| SaaS Admin Portal | `frontend-saas-admin/` | Angular 17+ |
| Admin Dashboard | `frontend-admin/` | Angular 17+ |
| Customer Website | `frontend-customer/` | Next.js 14+ |

### 3.1 Admin Frontend (Angular)

Cấu trúc này dùng chung cho `frontend-saas-admin/` và `frontend-admin/`. Điểm khác biệt chính nằm ở folder `features/`.

```
frontend-admin/                    # (tương tự cho frontend-saas-admin/)
│
├── src/
│   ├── app/
│   │   ├── core/                  # Singleton services, guards, interceptors
│   │   │   ├── auth/
│   │   │   │   ├── auth.service.ts
│   │   │   │   ├── auth.guard.ts
│   │   │   │   ├── role.guard.ts
│   │   │   │   └── token.storage.ts
│   │   │   ├── http/
│   │   │   │   ├── jwt.interceptor.ts
│   │   │   │   ├── tenant.interceptor.ts
│   │   │   │   ├── error.interceptor.ts
│   │   │   │   └── api.config.ts
│   │   │   ├── tenant/
│   │   │   │   └── tenant.service.ts
│   │   │   └── api/               # Generated API clients (orval / ng-openapi)
│   │   │       ├── tenant.api.ts
│   │   │       ├── branch.api.ts
│   │   │       ├── vehicle.api.ts
│   │   │       ├── booking.api.ts
│   │   │       └── ...
│   │   ├── shared/                # Reusable UI, pipes, directives
│   │   │   ├── ui/                # Button, Input, Modal, Table, Toast, Card
│   │   │   ├── forms/             # ReactiveForm helpers, custom validators
│   │   │   ├── pipes/             # CurrencyVnd, DateVi, StatusLabel
│   │   │   └── layout/            # Header, Sidebar, Footer, Shell
│   │   ├── features/              # Feature modules (lazy-loaded)
│   │   │   ├── dashboard/
│   │   │   │   ├── dashboard.component.ts
│   │   │   │   ├── dashboard.routes.ts
│   │   │   │   └── widgets/
│   │   │   ├── branches/          # CRUD chi nhánh
│   │   │   ├── vehicles/          # CRUD xe, status
│   │   │   ├── bookings/          # Booking flow
│   │   │   ├── customers/         # Quản lý khách
│   │   │   ├── pricing/           # Pricing rules
│   │   │   ├── reports/           # Báo cáo
│   │   │   └── settings/          # Cấu hình tenant
│   │   ├── app.component.ts       # Root standalone component
│   │   ├── app.config.ts          # provideRouter, provideHttpClient, ...
│   │   └── app.routes.ts          # Top-level routes (lazy load)
│   ├── assets/                    # Static assets (images, i18n)
│   ├── environments/
│   │   ├── environment.ts         # Dev: apiBaseUrl=http://localhost:8080
│   │   └── environment.prod.ts
│   ├── styles.scss                # Global styles (Tailwind, Angular Material)
│   ├── index.html
│   └── main.ts
│
├── angular.json
├── tsconfig.json
├── tsconfig.app.json
├── package.json
├── Dockerfile                     # Multi-stage: ng build → nginx alpine
├── nginx.conf                     # SPA fallback, gzip, cache headers
└── README.md
```

Với **SaaS Admin Portal** (`frontend-saas-admin/`), `features/` chứa:
- `tenants/` - Quản lý tenants
- `subscriptions/` - Plans, billing
- `billing/` - Invoices, payment ops
- `platform-reports/` - Doanh thu nền tảng
- `settings/` - Cấu hình toàn cục

### 3.2 Customer Frontend (Next.js)

```
frontend-customer/
│
├── app/                            # App Router (file-based routing)
│   ├── (public)/                   # Public route group (SSR-friendly, SEO)
│   │   ├── layout.tsx
│   │   ├── page.tsx                # / — Home (vehicle catalog)
│   │   ├── search/page.tsx          # /search
│   │   └── vehicles/[id]/page.tsx   # /vehicles/:id
│   │
│   ├── (portal)/                   # Authenticated customer portal
│   │   ├── layout.tsx              # CustomerLayout (header, footer)
│   │   ├── booking/page.tsx         # /booking
│   │   ├── my-bookings/page.tsx     # /my-bookings
│   │   └── profile/page.tsx         # /profile
│   │
│   ├── (auth)/                     # Auth flow
│   │   ├── login/page.tsx           # /login
│   │   ├── register/page.tsx        # /register
│   │   └── forgot-password/page.tsx
│   │
│   ├── api/                        # Route handlers (BFF nếu cần)
│   │
│   ├── layout.tsx                  # Root layout (shared chrome, providers)
│   └── globals.css                 # Tailwind base styles
│
├── components/                     # Reusable Components
│   ├── ui/                         # Base UI components
│   │   ├── Button.tsx
│   │   ├── Input.tsx
│   │   ├── Select.tsx
│   │   ├── Modal.tsx
│   │   ├── Table.tsx
│   │   ├── Card.tsx
│   │   └── Badge.tsx
│   ├── forms/                      # Form components
│   │   ├── BookingForm.tsx
│   │   ├── VehicleSearchForm.tsx
│   │   └── ProfileForm.tsx
│   └── layout/                     # Layout components
│       ├── Header.tsx
│       └── Footer.tsx
│
├── lib/                            # Utilities, services, hooks
│   ├── hooks/                      # Custom Hooks
│   │   ├── useAuth.ts
│   │   ├── useBooking.ts
│   │   └── useToast.ts
│   ├── services/                   # API Services
│   │   ├── api.ts                  # Axios instance
│   │   ├── authService.ts
│   │   ├── vehicleService.ts
│   │   ├── bookingService.ts
│   │   └── paymentService.ts
│   ├── context/                    # React Context (Client Components only)
│   │   ├── AuthContext.tsx
│   │   └── ToastContext.tsx
│   ├── utils/                      # Utilities
│   │   ├── dateUtils.ts
│   │   ├── priceUtils.ts
│   │   └── validationUtils.ts
│   └── types/                      # TypeScript types
│       ├── vehicle.ts
│       ├── booking.ts
│       └── api.ts
│
├── public/
├── next.config.mjs
├── tailwind.config.ts
├── package.json
└── Dockerfile                     # Multi-stage: next build → standalone
```

---

## 4. Naming Conventions

### 4.1 Backend (Java)

| Type | Convention | Example |
|------|------------|---------|
| Class | PascalCase | `BookingService` |
| Method | camelCase | `createBooking()` |
| Variable | camelCase | `bookingId` |
| Constant | UPPER_SNAKE | `MAX_RETRY_COUNT` |
| Package | lowercase | `com.carrental.service` |
| Table | snake_case | `booking_records` |
| Column | snake_case | `created_at` |
| Entity | PascalCase | `Booking` |
| DTO | PascalCase + Suffix | `BookingRequest`, `BookingResponse` |

### 4.2 Frontend - Admin (Angular/TypeScript)

| Type | Convention | Example |
|------|------------|---------|
| Component class | PascalCase | `BookingListComponent` |
| Component file | kebab-case | `booking-list.component.ts` |
| Selector | `app-` prefix, kebab-case | `app-booking-list` |
| Service | PascalCase + Service suffix | `BookingService` |
| Service file | kebab-case | `booking.service.ts` |
| Guard | PascalCase + Guard suffix | `AuthGuard`, `RoleGuard` |
| Interceptor | PascalCase + Interceptor suffix | `JwtInterceptor` |
| Pipe | PascalCase + Pipe suffix | `CurrencyVndPipe` |
| Module/Route file | kebab-case | `booking.routes.ts` |
| Interface/Type | PascalCase | `Booking`, `BookingDto` |
| Constant | UPPER_SNAKE | `API_BASE_URL` |
| Observable variable | `$` suffix | `bookings$: Observable<Booking[]>` |

### 4.3 Frontend - Customer (Next.js/TypeScript)

| Type | Convention | Example |
|------|------------|---------|
| Component | PascalCase | `BookingList.tsx` |
| Hook | camelCase + use prefix | `useBooking.ts` |
| Service | camelCase | `bookingService.ts` |
| Type/Interface | PascalCase | `BookingType` |
| CSS Class | kebab-case | `booking-list` |
| File | kebab-case | `booking-list.tsx` |
| Constant | UPPER_SNAKE | `API_BASE_URL` |

### 4.3 Database (PostgreSQL)

| Type | Convention | Example |
|------|------------|---------|
| Table | snake_case, plural | `tenants`, `bookings` |
| Column | snake_case | `tenant_id`, `created_at` |
| Primary Key | id | `id` (UUID) |
| Foreign Key | `table_id` | `branch_id`, `tenant_id` |
| Index | `idx_table_column` | `idx_vehicles_tenant_id` |
| Unique | `uq_table_column` | `uq_tenants_domain` |

---

## 5. Coding Rules

### 5.1 Backend Rules

```java
// 1. Always use dependency injection (constructor injection)
@Service
public class BookingService {
    private final BookingRepository bookingRepository;
    private final VehicleService vehicleService;

    public BookingService(BookingRepository bookingRepository,
                          VehicleService vehicleService) {
        this.bookingRepository = bookingRepository;
        this.vehicleService = vehicleService;
    }
}

// 2. Always filter by tenant_id
@GetMapping("/vehicles")
public List<Vehicle> getVehicles(HttpServletRequest request) {
    String tenantId = getTenantId(request);
    return vehicleRepository.findByTenantId(tenantId);
}

// 3. Use DTOs for request/response
// 4. Always validate input
// 5. Use global exception handler
// 6. Write unit tests for services
```

### 5.2 Frontend Rules (Admin - Angular)

```typescript
// 1. Use TypeScript strict mode
// 2. Standalone components (no NgModule), signals cho local state
// 3. Use RxJS cho async data flow, OnPush change detection
@Component({
  selector: 'app-booking-list',
  standalone: true,
  changeDetection: ChangeDetectionStrategy.OnPush,
  imports: [CommonModule, TableModule],
  template: `...`
})
export class BookingListComponent {
  private bookingService = inject(BookingService);
  bookings$ = this.bookingService.getAll(); // Observable + async pipe
}

// 4. HTTP qua interceptors (JWT, Tenant, Error) - KHÔNG gọi fetch/HttpClient trực tiếp từ component
// 5. API clients generated từ OpenAPI (orval) - không viết tay
// 6. Lazy-load mọi feature module qua loadChildren / loadComponent
// 7. Reactive Forms cho mọi form; dùng FormBuilder.nonGrouped
// 8. State management: Signals (mặc định) + NgRx (chỉ khi state phức tạp, cross-feature)
// 9. Unit tests với Jest + Testing Library (Angular)
```

### 5.3 Frontend Rules (Customer - Next.js)

```typescript
// 1. Use TypeScript strict mode
// 2. Server Components by default; add 'use client' only when needed (state, effects, browser APIs)
// 3. Use absolute imports
import { Button } from '@/components/ui/Button';

// 4. Follow single responsibility
// Bad: VehicleForm.tsx (handles form + API calls + validation)
// Good: VehicleForm.tsx (form only) + useVehicleForm.ts (logic)

// 5. Use TanStack Query (React Query) for client-side data fetching
// 6. Use Server Components + Server Actions for mutations when possible
// 7. Use React Context (Client Components) or Zustand for client state
// 8. Write unit tests for components (Vitest + Testing Library)
```

### 5.3 Git Rules

| Rule | Convention |
|------|------------|
| Branch | `feature/booking-flow`, `fix/payment-bug` |
| Commit | `feat: add booking flow`, `fix: payment validation` |
| PR | Title: `feat: implement booking flow`, Description: What & Why |
