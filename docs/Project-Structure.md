# Project Structure - Car Rental SaaS

## Mục lục
1. [Directory Structure](#1-directory-structure)
2. [Backend Structure (Spring Boot)](#2-backend-structure-spring-boot)
3. [Frontend Structure (Next.js)](#3-frontend-structure-nextjs)
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
├── frontend/                       # Next.js 14 Application (App Router)
│   ├── app/                        # App Router (file-based routing)
│   │   ├── (super-admin)/          # Super-admin route group
│   │   │   ├── layout.tsx
│   │   │   ├── dashboard/page.tsx
│   │   │   ├── tenants/page.tsx
│   │   │   ├── subscriptions/page.tsx
│   │   │   ├── billing/page.tsx
│   │   │   └── settings/page.tsx
│   │   ├── (admin)/                # Admin route group
│   │   │   ├── layout.tsx
│   │   │   ├── dashboard/page.tsx
│   │   │   ├── branches/page.tsx
│   │   │   ├── vehicles/page.tsx
│   │   │   ├── bookings/page.tsx
│   │   │   ├── customers/page.tsx
│   │   │   ├── reports/page.tsx
│   │   │   └── settings/page.tsx
│   │   ├── (customer)/             # Customer route group (public + portal)
│   │   │   ├── layout.tsx
│   │   │   ├── page.tsx            # Home (public catalog)
│   │   │   ├── booking/page.tsx    # Booking form
│   │   │   ├── my-bookings/page.tsx
│   │   │   └── profile/page.tsx
│   │   ├── (auth)/                 # Auth route group
│   │   │   ├── login/page.tsx
│   │   │   ├── register/page.tsx
│   │   │   └── forgot-password/page.tsx
│   │   ├── api/                    # Route handlers (e.g., auth callback)
│   │   ├── layout.tsx              # Root layout
│   │   ├── page.tsx                # Root page (redirects to /login or dashboard)
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
│   └── Dockerfile
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
│   └── VehicleTransferRepository.java
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
│   └── VehicleTransfer.java
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

## 3. Frontend Structure (Next.js)

```
frontend/
│
├── app/                            # App Router (file-based routing)
│   ├── (super-admin)/              # Super-admin route group (no URL segment)
│   │   ├── layout.tsx              # SuperAdminLayout
│   │   ├── dashboard/
│   │   │   └── page.tsx            # /super-admin/dashboard
│   │   ├── tenants/
│   │   │   └── page.tsx            # /super-admin/tenants
│   │   ├── subscriptions/
│   │   │   └── page.tsx            # /super-admin/subscriptions
│   │   ├── billing/
│   │   │   └── page.tsx            # /super-admin/billing
│   │   └── settings/
│   │       └── page.tsx            # /super-admin/settings
│   │
│   ├── (admin)/                    # Admin route group (no URL segment)
│   │   ├── layout.tsx              # AdminLayout
│   │   ├── dashboard/
│   │   │   └── page.tsx            # /admin/dashboard
│   │   ├── branches/
│   │   │   └── page.tsx            # /admin/branches
│   │   ├── vehicles/
│   │   │   └── page.tsx            # /admin/vehicles
│   │   ├── bookings/
│   │   │   └── page.tsx            # /admin/bookings
│   │   ├── customers/
│   │   │   └── page.tsx            # /admin/customers
│   │   ├── reports/
│   │   │   └── page.tsx            # /admin/reports
│   │   └── settings/
│   │       └── page.tsx            # /admin/settings
│   │
│   ├── (customer)/                 # Customer route group (public + portal)
│   │   ├── layout.tsx              # CustomerLayout
│   │   ├── page.tsx                # / (Home — public catalog)
│   │   ├── booking/
│   │   │   └── page.tsx            # /booking
│   │   ├── my-bookings/
│   │   │   └── page.tsx            # /my-bookings
│   │   └── profile/
│   │       └── page.tsx            # /profile
│   │
│   ├── (auth)/                     # Auth route group
│   │   ├── login/
│   │   │   └── page.tsx            # /login
│   │   ├── register/
│   │   │   └── page.tsx            # /register
│   │   └── forgot-password/
│   │       └── page.tsx            # /forgot-password
│   │
│   ├── api/                        # Route handlers (e.g., /api/auth/[...nextauth])
│   │
│   ├── layout.tsx                  # Root layout (shared chrome, providers)
│   ├── page.tsx                    # Root page (redirects based on auth)
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
│   │   ├── VehicleForm.tsx
│   │   └── CustomerForm.tsx
│   └── layout/                     # Layout components
│       ├── Header.tsx
│       └── Sidebar.tsx
│
├── lib/                            # Utilities, services, hooks
│   ├── hooks/                      # Custom Hooks
│   │   ├── useAuth.ts
│   │   ├── useTenant.ts
│   │   ├── useBooking.ts
│   │   └── useToast.ts
│   ├── services/                   # API Services
│   │   ├── api.ts                  # Axios instance
│   │   ├── authService.ts
│   │   ├── tenantService.ts
│   │   ├── branchService.ts
│   │   ├── vehicleService.ts
│   │   ├── bookingService.ts
│   │   └── paymentService.ts
│   ├── context/                    # React Context (Client Components only)
│   │   ├── AuthContext.tsx
│   │   ├── TenantContext.tsx
│   │   └── ToastContext.tsx
│   ├── utils/                      # Utilities
│   │   ├── dateUtils.ts
│   │   ├── priceUtils.ts
│   │   └── validationUtils.ts
│   └── types/                      # TypeScript types
│       ├── tenant.ts
│       ├── vehicle.ts
│       ├── booking.ts
│       └── api.ts
│
├── public/
├── next.config.mjs
├── tailwind.config.ts
├── package.json
└── Dockerfile
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

### 4.2 Frontend (Next.js/TypeScript)

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

### 5.2 Frontend Rules

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
