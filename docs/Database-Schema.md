# Database Schema - Car Rental SaaS

> **Cập nhật:** 24/06/2026 — Đơn giản hóa pricing_rules, đánh dấu vehicle_transfers là Phase 2.

## Mục lục
1. [ER Diagram](#1-er-diagram)
2. [Tables](#2-tables)
3. [Indexes](#3-indexes)
4. [Multi-tenant Strategy](#4-multi-tenant-strategy)
5. [Phase 2 Additions](#5-phase-2-additions)

---

## 1. ER Diagram

```
┌──────────────┐       ┌──────────────┐       ┌──────────────┐
│   tenants    │       │  branches    │       │  customers   │
├──────────────┤       ├──────────────┤       ├──────────────┤
│ id (PK)      │───┐   │ id (PK)      │       │ id (PK)      │
│ name         │   │   │ tenant_id(FK)│───┐   │ tenant_id(FK)│
│ domain       │   │   │ name         │   │   │ name         │
│ plan_tier    │   │   │ address      │   │   │ phone        │
│ created_at   │   │   │ phone        │   │   │ email        │
└──────────────┘   │   │ is_central   │   │   │ id_card (enc)│
                  │   │ created_at   │   │   │ driver_lic   │
                  │   └──────────────┘   │   │ created_at   │
                  │          │          │   └──────────────┘
                  │          │          │          │
                  │    ┌─────┴─────┐    │          │
                  │    │           │    │          │
                  │    ▼           ▼    ▼          ▼
┌──────────────┐ ┌─┴──────────────┐ ┌──────────────┐ ┌──────────────┐
│vehicle_types │ │   vehicles     │ │  bookings    │ │  payments    │
├──────────────┤ ├───────────────┤ ├─────────────┤ ├──────────────┤
│ id (PK)      │ │ id (PK)       │ │ id (PK)     │ │ id (PK)      │
│ tenant_id(FK)│ │ tenant_id(FK) │ │ tenant_id   │ │ tenant_id(FK)│
│ name         │ │ branch_id(FK) │ │ branch_id   │ │ booking_id   │
│ base_price   │ │ vehicle_type  │ │ customer_id │ │ method       │
│ created_at   │ │ license_plate │ │ vehicle_id  │ │ amount       │
└──────────────┘ │ model         │ │ pickup_date │ │ transaction  │
                 │ color         │ │ return_date │ │ paid_at       │
                 │ status        │ │ status      │ │ created_at   │
                 │ created_at    │ │ total_amount│ └──────────────┘
                 └───────────────┘ │ deposit_paid│
                                  │ created_at  │
                                  └─────────────┘
                                         │
                                         │
                                         ▼
                                  ┌────────────┐
                                  │pricing_rules│
                                  ├────────────┤
                                  │ id (PK)    │
                                  │ tenant_id  │
                                  │ vehicle_type│
                                  │ day_type   │  ← weekday / weekend
                                  │ multiplier │  ← 1.0 / 1.2
                                  │ is_active  │
                                  └────────────┘

  Phase 2 bổ sung: vehicle_transfers (điều phối xe)
```

---

## 2. Tables

### 2.1 tenants

```sql
CREATE TABLE tenants (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(255) NOT NULL,
    domain VARCHAR(255) UNIQUE NOT NULL,
    plan_tier VARCHAR(20) NOT NULL DEFAULT 'FREE'
        CHECK (plan_tier IN ('FREE', 'BASIC', 'PRO', 'ENTERPRISE')),
    logo_url VARCHAR(500),
    contact_email VARCHAR(255),
    contact_phone VARCHAR(20),
    settings JSONB DEFAULT '{}',
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE UNIQUE INDEX idx_tenants_domain ON tenants(domain);
```

### 2.2 branches

```sql
CREATE TABLE branches (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id) ON DELETE CASCADE,
    name VARCHAR(255) NOT NULL,
    address TEXT,
    phone VARCHAR(20),
    email VARCHAR(255),
    latitude DECIMAL(10, 8),
    longitude DECIMAL(11, 8),
    is_central BOOLEAN DEFAULT FALSE,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_branches_tenant_id ON branches(tenant_id);
CREATE INDEX idx_branches_is_central ON branches(tenant_id, is_central);
```

### 2.3 vehicle_types

```sql
CREATE TABLE vehicle_types (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id) ON DELETE CASCADE,
    name VARCHAR(50) NOT NULL,
    description TEXT,
    base_price DECIMAL(12, 2) NOT NULL,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_vehicle_types_tenant_id ON vehicle_types(tenant_id);
```

### 2.4 vehicles

```sql
CREATE TABLE vehicles (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id) ON DELETE CASCADE,
    branch_id UUID REFERENCES branches(id) ON DELETE SET NULL,
    vehicle_type_id UUID NOT NULL REFERENCES vehicle_types(id),
    license_plate VARCHAR(20) NOT NULL,
    model VARCHAR(100),
    color VARCHAR(30),
    year INTEGER,
    description TEXT,
    status VARCHAR(20) NOT NULL DEFAULT 'available'
        CHECK (status IN ('available', 'rented', 'maintenance', 'transferred')),
    current_km INTEGER DEFAULT 0,
    fuel_level VARCHAR(20) DEFAULT 'full',
    images JSONB DEFAULT '[]',
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_vehicles_tenant_id ON vehicles(tenant_id);
CREATE INDEX idx_vehicles_branch_id ON vehicles(branch_id);
CREATE INDEX idx_vehicles_status ON vehicles(tenant_id, status);
CREATE INDEX idx_vehicles_license_plate ON vehicles(tenant_id, license_plate);
CREATE UNIQUE INDEX idx_vehicles_tenant_license ON vehicles(tenant_id, license_plate);
```

### 2.5 customers

```sql
CREATE TABLE customers (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id) ON DELETE CASCADE,
    name VARCHAR(255) NOT NULL,
    phone VARCHAR(20),
    email VARCHAR(255),
    address TEXT,
    id_card VARCHAR(20),
    driver_license VARCHAR(20),
    notes TEXT,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_customers_tenant_id ON customers(tenant_id);
CREATE INDEX idx_customers_phone ON customers(tenant_id, phone);
CREATE INDEX idx_customers_email ON customers(tenant_id, email);
```

### 2.6 bookings

```sql
CREATE TABLE bookings (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id) ON DELETE CASCADE,
    branch_id UUID NOT NULL REFERENCES branches(id),
    customer_id UUID NOT NULL REFERENCES customers(id),
    vehicle_id UUID REFERENCES vehicles(id),
    booking_code VARCHAR(20) UNIQUE NOT NULL,
    pickup_date DATE NOT NULL,
    return_date DATE NOT NULL,
    pickup_time TIME,
    return_time TIME,
    status VARCHAR(20) NOT NULL DEFAULT 'pending'
        CHECK (status IN ('pending', 'confirmed', 'in_progress', 'completed', 'cancelled')),
    initial_km INTEGER,
    final_km INTEGER,
    initial_fuel VARCHAR(20),
    final_fuel VARCHAR(20),
    notes TEXT,
    total_amount DECIMAL(12, 2) DEFAULT 0,
    deposit_amount DECIMAL(12, 2) DEFAULT 0,
    deposit_paid BOOLEAN DEFAULT FALSE,
    late_fee DECIMAL(12, 2) DEFAULT 0,
    damage_fee DECIMAL(12, 2) DEFAULT 0,
    cancellation_reason TEXT,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_bookings_tenant_id ON bookings(tenant_id);
CREATE INDEX idx_bookings_branch_id ON bookings(branch_id);
CREATE INDEX idx_bookings_customer_id ON bookings(customer_id);
CREATE INDEX idx_bookings_vehicle_id ON bookings(vehicle_id);
CREATE INDEX idx_bookings_status ON bookings(tenant_id, status);
CREATE INDEX idx_bookings_dates ON bookings(tenant_id, pickup_date, return_date);
CREATE INDEX idx_bookings_code ON bookings(booking_code);
```

### 2.7 payments

```sql
CREATE TABLE payments (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id) ON DELETE CASCADE,
    booking_id UUID NOT NULL REFERENCES bookings(id) ON DELETE CASCADE,
    method VARCHAR(20) NOT NULL
        CHECK (method IN ('cash', 'bank_transfer', 'e_wallet')),
    amount DECIMAL(12, 2) NOT NULL,
    transaction_id VARCHAR(100),
    payment_type VARCHAR(20) DEFAULT 'deposit'
        CHECK (payment_type IN ('deposit', 'full', 'refund', 'late_fee', 'damage_fee')),
    paid_at TIMESTAMP WITH TIME ZONE,
    notes TEXT,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_payments_tenant_id ON payments(tenant_id);
CREATE INDEX idx_payments_booking_id ON payments(booking_id);
CREATE INDEX idx_payments_method ON payments(tenant_id, method);
```

### 2.8 pricing_rules (đơn giản hóa MVP)

```sql
CREATE TABLE pricing_rules (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id) ON DELETE CASCADE,
    vehicle_type_id UUID NOT NULL REFERENCES vehicle_types(id),
    day_type VARCHAR(20) NOT NULL
        CHECK (day_type IN ('weekday', 'weekend')),
    multiplier DECIMAL(4, 2) NOT NULL DEFAULT 1.00,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_pricing_rules_tenant_id ON pricing_rules(tenant_id);
CREATE INDEX idx_pricing_rules_type_day ON pricing_rules(tenant_id, vehicle_type_id, day_type);
```

> **MVP:** Chỉ hỗ trợ weekday (1.0) và weekend (1.2). Season/Holiday multipliers → Phase 2.

### 2.9 vehicle_transfers — Lùi Phase 2

> Bảng `vehicle_transfers` (điều phối xe giữa các chi nhánh) sẽ được thêm vào Phase 2 khi hệ thống phục vụ các chuỗi 50+ xe có nhu cầu điều phối liên chi nhánh. MVP không cần bảng này.

### 2.10 users (for staff/admin)

```sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID REFERENCES tenants(id) ON DELETE CASCADE, -- Nullable for SUPER_ADMIN
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    name VARCHAR(255),
    role VARCHAR(20) NOT NULL
        CHECK (role IN ('SUPER_ADMIN', 'TENANT_ADMIN', 'BRANCH_MANAGER', 'STAFF', 'CENTRAL_MANAGER')),
    branch_id UUID REFERENCES branches(id),
    is_active BOOLEAN DEFAULT TRUE,
    last_login_at TIMESTAMP WITH TIME ZONE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_users_tenant_id ON users(tenant_id) WHERE tenant_id IS NOT NULL;
CREATE INDEX idx_users_email ON users(email);
```

---

## 3. Indexes

### 3.1 Summary of Indexes

| Table | Index Name | Columns | Type |
|-------|------------|----------|------|
| tenants | idx_tenants_domain | domain | UNIQUE |
| branches | idx_branches_tenant_id | tenant_id | |
| branches | idx_branches_is_central | tenant_id, is_central | |
| vehicle_types | idx_vehicle_types_tenant_id | tenant_id | |
| vehicles | idx_vehicles_tenant_id | tenant_id | |
| vehicles | idx_vehicles_branch_id | branch_id | |
| vehicles | idx_vehicles_status | tenant_id, status | |
| vehicles | idx_vehicles_license_plate | tenant_id, license_plate | |
| vehicles | idx_vehicles_tenant_license | tenant_id, license_plate | UNIQUE |
| customers | idx_customers_tenant_id | tenant_id | |
| customers | idx_customers_phone | tenant_id, phone | |
| customers | idx_customers_email | tenant_id, email | |
| bookings | idx_bookings_tenant_id | tenant_id | |
| bookings | idx_bookings_branch_id | branch_id | |
| bookings | idx_bookings_customer_id | customer_id | |
| bookings | idx_bookings_vehicle_id | vehicle_id | |
| bookings | idx_bookings_status | tenant_id, status | |
| bookings | idx_bookings_dates | tenant_id, pickup_date, return_date | |
| bookings | idx_bookings_code | booking_code | UNIQUE |
| payments | idx_payments_tenant_id | tenant_id | |
| payments | idx_payments_booking_id | booking_id | |
| payments | idx_payments_method | tenant_id, method | |
| pricing_rules | idx_pricing_rules_tenant_id | tenant_id | |
| pricing_rules | idx_pricing_rules_type_day | tenant_id, vehicle_type_id, day_type | |
| users | idx_users_tenant_id | tenant_id | |

> `vehicle_transfers` — lùi Phase 2.
| users | idx_users_email | email | UNIQUE |

---

## 4. Multi-tenant Strategy

### 4.1 Row-Level Security (RLS)

```sql
-- Enable RLS for all tables
ALTER TABLE vehicles ENABLE ROW LEVEL SECURITY;
ALTER TABLE bookings ENABLE ROW LEVEL SECURITY;
ALTER TABLE customers ENABLE ROW LEVEL SECURITY;
-- ... apply to all tenant-scoped tables

-- Create policy
CREATE POLICY tenant_isolation ON vehicles
    USING (
        -- Allow SUPER_ADMIN to bypass isolation filter
        (current_setting('app.current_user_role', true) = 'SUPER_ADMIN') OR
        (tenant_id = current_setting('app.current_tenant', true)::uuid)
    );
```

### 4.2 Application-Level Filtering

```java
// BaseRepository with automatic tenant filtering
public interface BaseRepository<T> {
    @Query("SELECT e FROM #{#entityName} e WHERE e.tenantId = :tenantId")
    List<T> findAllByTenantId(@Param("tenantId") UUID tenantId);
}

// Every query automatically includes tenant_id
```

### 4.3 Tenant Context Setup

```java
// TenantContext.java
public class TenantContext {
    private static final ThreadLocal<UUID> currentTenant = new ThreadLocal<>();

    public static void setTenantId(UUID tenantId) {
        currentTenant.set(tenantId);
    }

    public static UUID getTenantId() {
        return currentTenant.get();
    }

    public static void clear() {
        currentTenant.remove();
    }
}

// JwtAuthenticationFilter.java
public class JwtAuthenticationFilter extends OncePerRequestFilter {
    @Override
    protected void doFilterInternal(HttpServletRequest request, ...) {
        String tenantId = extractTenantId(request); // from JWT or subdomain
        TenantContext.setTenantId(UUID.fromString(tenantId));
        // ... continue filter chain
    }
}
```

### 4.4 Migration Path

```
Phase 1: Shared Database + tenant_id (Current)
    └── Simple, cost-effective
    └── Good for up to ~1000 tenants

Phase 2: Schema-per-tenant (Future)
    └── Better isolation
    └── Good for ~1000-10000 tenants

Phase 3: Database-per-tenant (Future)
    └── Maximum isolation
    └── Good for enterprise/critical tenants
```

---

## 5. Phase 2 Additions

Các bảng sẽ được thêm ở Phase 2 khi scale lên phục vụ chuỗi lớn:

| Bảng | Mục đích | Trigger |
|------|----------|---------|
| `vehicle_transfers` | Điều phối xe liên chi nhánh | Tenant có >50 xe, >3 chi nhánh |
| `pricing_rules` (mở rộng) | Thêm season, holiday multipliers | Tenant yêu cầu định giá theo mùa/lễ |
| `notification_logs` | Lịch sử SMS/Email/Push | Khi tích hợp đa kênh thông báo |
| `driver_records` | Quản lý tài xế (xe có lái) | Khi mở rộng sang vertical xe có lái |
