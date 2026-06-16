# Project Structure - Car Rental SaaS

## Mục lục
1. [Directory Structure](#1-directory-structure)
2. [Backend Structure (Spring Boot)](#2-backend-structure-spring-boot)
3. [Frontend Structure (React)](#3-frontend-structure-react)
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
├── frontend/                       # React + Vite Application
│   ├── src/
│   │   ├── components/             # Reusable Components
│   │   │   ├── ui/                 # Base UI components
│   │   │   ├── forms/             # Form components
│   │   │   └── layout/            # Layout components
│   │   ├── pages/                 # Page Components
│   │   │   ├── admin/             # Admin pages
│   │   │   ├── customer/          # Customer pages
│   │   │   └── auth/              # Auth pages
│   │   ├── hooks/                 # Custom Hooks
│   │   ├── services/              # API Services
│   │   ├── context/               # React Context
│   │   ├── utils/                 # Utilities
│   │   ├── types/                 # TypeScript types
│   │   └── App.tsx
│   ├── public/
│   ├── package.json
│   ├── vite.config.ts
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

## 3. Frontend Structure (React)

```
frontend/src/
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
│       ├── AdminLayout.tsx
│       ├── CustomerLayout.tsx
│       ├── Header.tsx
│       └── Sidebar.tsx
│
├── pages/                          # Page Components
│   ├── admin/                      # Admin pages
│   │   ├── Dashboard.tsx
│   │   ├── BranchList.tsx
│   │   ├── VehicleList.tsx
│   │   ├── BookingList.tsx
│   │   ├── CustomerList.tsx
│   │   ├── ReportList.tsx
│   │   └── Settings.tsx
│   ├── customer/                  # Customer pages
│   │   ├── Home.tsx
│   │   ├── BookingPage.tsx
│   │   ├── MyBookings.tsx
│   │   └── Profile.tsx
│   └── auth/                      # Auth pages
│       ├── Login.tsx
│       ├── Register.tsx
│       └── ForgotPassword.tsx
│
├── hooks/                          # Custom Hooks
│   ├── useAuth.ts
│   ├── useTenant.ts
│   ├── useBooking.ts
│   └── useToast.ts
│
├── services/                       # API Services
│   ├── api.ts                      # Axios instance
│   ├── authService.ts
│   ├── tenantService.ts
│   ├── branchService.ts
│   ├── vehicleService.ts
│   ├── bookingService.ts
│   └── paymentService.ts
│
├── context/                        # React Context
│   ├── AuthContext.tsx
│   ├── TenantContext.tsx
│   └── ToastContext.tsx
│
├── utils/                          # Utilities
│   ├── dateUtils.ts
│   ├── priceUtils.ts
│   └── validationUtils.ts
│
├── types/                          # TypeScript types
│   ├── tenant.ts
│   ├── vehicle.ts
│   ├── booking.ts
│   └── api.ts
│
├── App.tsx
├── main.tsx
└── index.css
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

### 4.2 Frontend (React/TypeScript)

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
// 2. Use functional components with hooks
// 3. Use absolute imports
import { Button } from '@/components/ui/Button';

// 4. Follow single responsibility
// Bad: VehicleForm.tsx (handles form + API calls + validation)
// Good: VehicleForm.tsx (form only) + useVehicleForm.ts (logic)

// 5. Use React Query for data fetching
// 6. Use Zustand or Context for state management
// 7. Write unit tests for components
```

### 5.3 Git Rules

| Rule | Convention |
|------|------------|
| Branch | `feature/booking-flow`, `fix/payment-bug` |
| Commit | `feat: add booking flow`, `fix: payment validation` |
| PR | Title: `feat: implement booking flow`, Description: What & Why |
