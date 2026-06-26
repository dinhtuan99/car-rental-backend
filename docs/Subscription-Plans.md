# Subscription Plans - Car Rental SaaS

**Cập nhật:** 24/06/2026 — Đơn giản hóa MVP còn 2 bậc. Phân mảnh gói cước để Phase 2 (Phase 2).

---

## 1. Overview

### 1.1 MVP: 2 Bậc

| Plan | Target | Giá | Phù hợp |
|------|--------|-----|---------|
| **FREE** | Dùng thử | 0đ/tháng | Shop nhỏ, test hệ thống |
| **PAID** | Chính thức | 1,500,000đ/tháng | Nhà xe đi vào vận hành thật |

Multi-tier (BASIC/PRO/ENTERPRISE) sẽ triển khai ở Phase 2 khi đạt 50+ tenant trả phí.

### 1.2 Core Concept

```
Tenant (Nhà xe)
    │
    ├── Subscription Plan: FREE hoặc PAID
    │     ├── Số lượng Branch tối đa
    │     ├── Số lượng Vehicle tối đa
    │     └── Features được bật
    │
    ├── Branches (Chi nhánh)
    │     ├── Central Branch
    │     └── Satellite Branches
    │
    └── Tính năng theo plan
```

---

## 2. Plan Comparison

### 2.1 Feature Matrix

| Feature | FREE | PAID |
|---------|------|------|
| **Core** |
| Số Branch tối đa | 1 | 5 |
| Số Vehicle tối đa | 10 | 100 |
| Booking/tháng | 30 | 500 |
| Thời hạn dùng thử | 30 ngày | Không giới hạn |
| **Branch Management** |
| CRUD Branch | ✅ | ✅ |
| **Vehicle Management** |
| CRUD Vehicle | ✅ | ✅ |
| Vehicle Status Tracking | ✅ | ✅ |
| Vehicle Images | ❌ | 5/xe |
| **Booking** |
| Tạo Booking tại quầy | ✅ | ✅ |
| Dynamic Pricing (weekday/weekend) | ✅ | ✅ |
| Guest Booking Online | ✅ | ✅ |
| Customer Portal | ❌ | ✅ |
| **Payment** |
| Cash Payment | ✅ | ✅ |
| Bank Transfer | ❌ | ✅ |
| VietQR | ❌ | ✅ |
| **Reporting** |
| Revenue Report (ngày) | ❌ | ✅ |
| Fleet Status Report | ❌ | ✅ |
| **Security** |
| Multi-user accounts | 1 | 10 |
| Role-based access | ❌ | ✅ |
| **Support** |
| Documentation | ✅ | ✅ |
| Email Support | ❌ | ✅ |

### 2.2 Visual Comparison

```
┌─────────────────────────────────────────────────────────┐
│                    PLAN HIERARCHY (MVP)                  │
│                                                          │
│                    ┌─────────────────┐                   │
│                    │     PAID        │                   │
│                    │  • 5 branches   │                   │
│                    │  • 100 vehicles │                   │
│                    │  • 500 bookings │                   │
│                    │  • Full features│                   │
│                    │  • Email support│                   │
│                    └────────┬────────┘                   │
│                             ▲                            │
│                             │ Upgrade                    │
│                             │                            │
│                    ┌────────┴────────┐                   │
│                    │     FREE        │                   │
│                    │  • 1 branch     │                   │
│                    │  • 10 vehicles  │                   │
│                    │  • 30 bookings  │                   │
│                    │  • Dùng thử 30d │                   │
│                    │  • Tự support   │                   │
│                    └─────────────────┘                   │
└─────────────────────────────────────────────────────────┘
```

---

## 3. Plan Details

### 3.1 FREE Plan

| Aspect | Details |
|--------|---------|
| **Phí** | Miễn phí |
| **Thời hạn** | 30 ngày dùng thử |
| **Giới hạn** | 1 Branch, 10 Vehicles, 30 Bookings/tháng |
| **Payment** | Cash only |
| **Support** | Documentation (self-service) |

**Phù hợp khi:**
- Mới bắt đầu, cần trải nghiệm
- Fleet dưới 10 xe
- Chưa có nhu cầu trả phí

---

### 3.2 PAID Plan

| Aspect | Details |
|--------|---------|
| **Phí** | 1,500,000đ/tháng (15,000,000đ/năm - tiết kiệm 17%) |
| **Thời hạn** | Tháng hoặc Năm |
| **Giới hạn** | 5 Branches, 100 Vehicles, 500 Bookings/tháng |
| **Payment** | Cash, Bank Transfer, VietQR |
| **Support** | Email support (response within 24h) |

**Khác biệt so với FREE:**
- ✅ Customer Portal (khách hàng xem lịch sử, đặt lại)
- ✅ Bank Transfer + VietQR
- ✅ Revenue Report + Fleet Status Report
- ✅ 5 images/vehicle
- ✅ 10 user accounts
- ✅ Role-based access control
- ✅ Email support

---

## 4. Pricing Model

### 4.1 MVP Pricing

| Plan | Monthly | Annual | Savings |
|------|---------|--------|---------|
| FREE | 0đ | — | — |
| PAID | 1,500,000đ | 15,000,000đ | 17% |

### 4.2 Overage Handling

```
Booking vượt quota:
├── FREE: Cảnh báo → Khóa booking khi hết 30 booking/tháng
└── PAID: Cảnh báo → Đề xuất nâng cấp (Phase 2 multi-tier)
```

### 4.3 Payment Methods (cho tenant trả phí SaaS)

| Method | Availability |
|--------|--------------|
| Bank Transfer (VNĐ) | PAID |
| MoMo / ZaloPay | PAID |

---

## 5. Upgrade/Downgrade

### 5.1 Upgrade FREE → PAID

- Immediate access to all PAID features
- Không gián đoạn dịch vụ
- Data được giữ nguyên

### 5.2 Downgrade PAID → FREE

- Chỉ hiệu lực khi hết billing cycle
- Nếu data vượt limit FREE → Không cho downgrade
- Data retained 30 days sau khi hủy

### 5.3 Cancellation

- Hủy bất kỳ lúc nào
- Service tiếp tục đến hết chu kỳ đã thanh toán
- Data giữ 30 ngày sau khi hủy → xóa vĩnh viễn

---

## 6. Phase 2: Multi-tier Plans

Sau khi đạt 50+ tenant trả phí, triển khai phân mảnh:

| Plan | Giá/tháng | Giới hạn | Target |
|------|-----------|----------|--------|
| **FREE** | 0đ | 1 Branch, 10 Vehicles, 30 Bookings | Dùng thử |
| **BASIC** | 500,000đ | 2 Branches, 20 Vehicles, 100 Bookings | Shop nhỏ |
| **PRO** | 1,500,000đ | 5 Branches, 100 Vehicles, 500 Bookings | Đang phát triển |
| **ENTERPRISE** | 5,000,000đ | Unlimited | Chuỗi lớn |

Chi tiết sẽ được thiết kế sau khi có data sử dụng thực tế từ Phase 1 MVP.

---

## 7. SQL Schema

```sql
-- MVP Schema (đơn giản hóa)
CREATE TABLE tenants (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(255) NOT NULL,
    domain VARCHAR(255) UNIQUE NOT NULL,
    plan_tier VARCHAR(20) NOT NULL DEFAULT 'FREE'
        CHECK (plan_tier IN ('FREE', 'PAID')),
    subscription_status VARCHAR(20) DEFAULT 'active'
        CHECK (subscription_status IN ('active', 'cancelled', 'suspended')),
    trial_ends_at TIMESTAMP WITH TIME ZONE,
    subscription_end_date TIMESTAMP WITH TIME ZONE,
    settings JSONB DEFAULT '{}',
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_tenants_plan ON tenants(plan_tier);
CREATE INDEX idx_tenants_status ON tenants(subscription_status);
```
