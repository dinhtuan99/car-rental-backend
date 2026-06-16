# Subscription Plans - Car Rental SaaS

## Mục lục
1. [Overview](#1-overview)
2. [Plan Comparison](#2-plan-comparison)
3. [Plan Details](#3-plan-details)
4. [Feature Descriptions](#4-feature-descriptions)
5. [Pricing Model](#5-pricing-model)
6. [Add-ons](#6-add-ons)
7. [Limits & Quotas](#7-limits--quotas)
8. [Upgrade/Downgrade Policy](#8-upgradedowngrade-policy)

---

## 1. Overview

### 1.1 Plan Tiers

| Plan | Target | Giá tham khảo | Phù hợp |
|------|--------|---------------|---------|
| **FREE** | Dùng thử | 0đ/tháng | Gara nhỏ, test |
| **BASIC** | Khởi đầu | 500,000đ/tháng | 1-2 chi nhánh |
| **PRO** | Phát triển | 1,500,000đ/tháng | 3-5 chi nhánh |
| **ENTERPRISE** | Quy mô lớn | 5,000,000đ/tháng | 5+ chi nhánh |

### 1.2 Core Concept

```
Tenant (Công ty thuê xe)
    │
    ├── Subscription Plan (FREE/BASIC/PRO/ENTERPRISE)
    │     │
    │     ├── Số lượng Branch tối đa
    │     ├── Số lượng Vehicle tối đa
    │     ├── Số lượng Booking/tháng
    │     └── Features được bật
    │
    ├── Branches (Chi nhánh)
    │     ├── Central Branch (Kho trung tâm)
    │     └── Satellite Branches (Chi nhánh con)
    │
    └── Add-ons (Tính năng mở rộng)
          ├── SMS notifications
          ├── Email notifications
          ├── Google Maps integration
          └── Advanced reporting
```

---

## 2. Plan Comparison

### 2.1 Feature Matrix

| Feature | FREE | BASIC | PRO | ENTERPRISE |
|---------|------|-------|-----|------------|
| **Core** |
| Số Branch tối đa | 1 | 2 | 5 | Unlimited |
| Số Vehicle tối đa | 5 | 20 | 100 | Unlimited |
| Booking/tháng | 20 | 100 | 500 | Unlimited |
| **Branch Management** |
| Central Branch | ✅ | ✅ | ✅ | ✅ |
| Satellite Branches | ❌ | ✅ | ✅ | ✅ |
| Vehicle Transfer | ❌ | ✅ | ✅ | ✅ |
| **Vehicle Management** |
| CRUD Vehicle | ✅ | ✅ | ✅ | ✅ |
| Vehicle Status Tracking | ✅ | ✅ | ✅ | ✅ |
| Vehicle Images | ❌ | 3/xe | 10/xe | Unlimited |
| **Booking** |
| Tạo Booking | ✅ | ✅ | ✅ | ✅ |
| Dynamic Pricing | ❌ | ✅ | ✅ | ✅ |
| Booking Calendar | ❌ | ✅ | ✅ | ✅ |
| **Payment** |
| Cash Payment | ✅ | ✅ | ✅ | ✅ |
| Bank Transfer | ❌ | ✅ | ✅ | ✅ |
| E-wallet | ❌ | ❌ | ✅ | ✅ |
| **Reporting** |
| Revenue Report | ❌ | Basic | Advanced | Advanced + Custom |
| Fleet Utilization | ❌ | ❌ | ✅ | ✅ |
| Customer Analytics | ❌ | ❌ | ✅ | ✅ |
| Export Excel/PDF | ❌ | ❌ | ✅ | ✅ |
| **Integrations** |
| SMS Notification | ❌ | Add-on | Add-on | ✅ |
| Email Notification | ❌ | Add-on | Add-on | ✅ |
| Google Maps | ❌ | ❌ | ✅ | ✅ |
| API Access | ❌ | Read-only | Full | Full |
| **Support** |
| Documentation | ✅ | ✅ | ✅ | ✅ |
| Email Support | ❌ | ✅ | ✅ | ✅ |
| Priority Support | ❌ | ❌ | ✅ | ✅ |
| Dedicated Support | ❌ | ❌ | ❌ | ✅ |
| **Security** |
| Multi-user accounts | 1 | 3 | 10 | Unlimited |
| Role-based access | ❌ | Basic | Full | Full |
| Audit Log | ❌ | ❌ | 7 days | 30 days |

### 2.2 Visual Comparison

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         Plan Hierarchy                                       │
│                                                                              │
│                          ENTERPRISE                                          │
│                    ┌─────────────────────┐                                   │
│                    │ • Unlimited branches│                                   │
│                    │ • Unlimited vehicles│                                   │
│                    │ • All features      │                                   │
│                    │ • Dedicated support│                                   │
│                    └─────────────────────┘                                   │
│                                   ▲                                          │
│                                   │                                          │
│                          ┌────────┴────────┐                                  │
│                          │      PRO       │                                  │
│                    ┌─────┴────────────────┴─────┐                             │
│                    │ • 5 branches             │                             │
│                    │ • 100 vehicles           │                             │
│                    │ • Advanced reporting    │                             │
│                    │ • Priority support      │                             │
│                    └─────────────────────────┘                               │
│                                   ▲                                          │
│                                   │                                          │
│                          ┌────────┴────────┐                                  │
│                          │     BASIC      │                                  │
│                    ┌─────┴────────────────┴─────┐                             │
│                    │ • 2 branches              │                             │
│                    │ • 20 vehicles             │                             │
│                    │ • Basic reporting        │                             │
│                    │ • Email support          │                             │
│                    └─────────────────────────┘                               │
│                                   ▲                                          │
│                                   │                                          │
│                          ┌────────┴────────┐                                  │
│                          │     FREE       │                                  │
│                    ┌─────┴────────────────┴─────┐                             │
│                    │ • 1 branch               │                             │
│                    │ • 5 vehicles             │                             │
│                    │ • Basic features        │                             │
│                    │ • Documentation only   │                             │
│                    └─────────────────────────┘                               │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. Plan Details

### 3.1 FREE Plan

**Đối tượng:** Gara nhỏ, cá nhân muốn dùng thử trước khi quyết định mua

| Aspect | Details |
|--------|---------|
| **Phí** | Miễn phí vĩnh viễn |
| **Thời hạn** | Không giới hạn |
| **Giới hạn** | 1 Branch, 5 Vehicles, 20 Bookings/tháng |
| **Payment methods** | Cash only |
| **Support** | Documentation (self-service) |

**Phù hợp khi:**
- Bạn mới bắt đầu, chưa có hệ thống
- Muốn trải nghiệm trước khi mua
- Fleet nhỏ (dưới 5 xe)

**Không phù hợp khi:**
- Cần nhiều hơn 5 xe
- Cần báo cáo doanh thu
- Cần thông báo SMS/Email cho khách

---

### 3.2 BASIC Plan

**Đối tượng:** Công ty thuê xe nhỏ, mới thành lập

| Aspect | Details |
|--------|---------|
| **Phí** | 500,000đ/tháng (5,000,000đ/năm - tiết kiệm 17%) |
| **Thời hạn** | Tháng hoặc Năm |
| **Giới hạn** | 2 Branches, 20 Vehicles, 100 Bookings/tháng |
| **Payment methods** | Cash, Bank Transfer |
| **Support** | Email support (response within 24h) |

**Phù hợp khi:**
- Bạn có 1-2 chi nhánh
- Fleet từ 5-20 xe
- Cần báo cáo doanh thu cơ bản
- Khách hàng thanh toán chủ yếu bằng tiền mặt/chuyển khoản

**Bao gồm thêm:**
- ✅ Basic Revenue Report
- ✅ Vehicle Transfer giữa các chi nhánh
- ✅ 3 images/vehicle
- ✅ 3 user accounts
- ✅ Email support

---

### 3.3 PRO Plan

**Đối tượng:** Công ty thuê xe đang phát triển, có nhiều chi nhánh

| Aspect | Details |
|--------|---------|
| **Phí** | 1,500,000đ/tháng (15,000,000đ/năm - tiết kiệm 17%) |
| **Thời hạn** | Tháng hoặc Năm |
| **Giới hạn** | 5 Branches, 100 Vehicles, 500 Bookings/tháng |
| **Payment methods** | Cash, Bank Transfer, E-wallet |
| **Support** | Priority email support (response within 4h) |

**Phù hợp khi:**
- Bạn có 3-5 chi nhánh
- Fleet từ 20-100 xe
- Cần báo cáo nâng cao, analytics
- Khách hàng thanh toán đa dạng (ví điện tử)
- Cần tích hợp Google Maps

**Bao gồm thêm:**
- ✅ Advanced Reporting (Fleet utilization, Customer analytics)
- ✅ Google Maps integration
- ✅ Full API Access
- ✅ Export Excel/PDF
- ✅ 10 images/vehicle
- ✅ 10 user accounts
- ✅ Role-based access control
- ✅ Booking Calendar view
- ✅ Dynamic Pricing (weekday/weekend/holiday)

---

### 3.4 ENTERPRISE Plan

**Đối tượng:** Chuỗi cho thuê xe lớn, cần custom và hỗ trợ cao cấp

| Aspect | Details |
|--------|---------|
| **Phí** | 5,000,000đ/tháng (50,000,000đ/năm - tiết kiệm 17%) |
| **Thời hạn** | Năm (bắt buộc) |
| **Giới hạn** | Unlimited Branches, Vehicles, Bookings |
| **Payment methods** | Tất cả + Invoice |
| **Support** | Dedicated account manager, Phone support |

**Phù hợp khi:**
- Bạn có 5+ chi nhánh
- Fleet trên 100 xe
- Cần custom reporting, API
- Cần SLA cam kết uptime
- Cần đào tạo onboarding cho nhân viên

**Bao gồm thêm:**
- ✅ Everything in PRO
- ✅ Unlimited scaling
- ✅ Custom API integrations
- ✅ Dedicated support manager
- ✅ Phone support
- ✅ On-site training (1x/year)
- ✅ Custom branding (logo, colors)
- ✅ Audit log 30 days
- ✅ SLA 99.9% uptime
- ✅ Invoice payment (NET 30)

---

## 4. Feature Descriptions

### 4.1 Branch Management

| Feature | Description |
|---------|-------------|
| **Central Branch** | Kho xe trung tâm - nơi quản lý fleet chính |
| **Satellite Branches** | Chi nhánh con - có thể nhận xe từ central |
| **Vehicle Transfer** | Chuyển xe giữa các chi nhánh khi cần |

### 4.2 Vehicle Management

| Feature | Description |
|---------|-------------|
| **CRUD Operations** | Thêm, sửa, xóa, xem xe |
| **Status Tracking** | available, rented, maintenance, transferred |
| **Image Gallery** | Hình ảnh xe (số lượng tùy plan) |
| **Vehicle Types** | Phân loại xe (4 chỗ, 7 chỗ, xe sang...) |

### 4.3 Booking Management

| Feature | Description |
|---------|-------------|
| **Create Booking** | Tạo đơn đặt xe mới |
| **Status Flow** | pending → confirmed → in_progress → completed/cancelled |
| **Booking Calendar** | Lịch đặt xe trực quan |
| **Pricing Calculation** | Tính giá tự động theo ngày |

### 4.4 Dynamic Pricing

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                     Dynamic Pricing Matrix                                 │
│                                                                              │
│  Base Price (vehicle_types.base_price)                                      │
│       │                                                                      │
│       ├── Weekday ──────► Multiplier 1.0 ──────► Price × 1.0                 │
│       │                                                                      │
│       ├── Weekend ──────► Multiplier 1.2 ──────► Price × 1.2                 │
│       │                                                                      │
│       └── Holiday ─────► Multiplier 1.5 ──────► Price × 1.5                 │
│                                                                            │
│  Season Adjustment:                                                         │
│       ├── Normal Season ────► Multiplier 1.0                               │
│       └── Peak Season ─────► Multiplier 1.3 (Tết, lễ)                      │
│                                                                            │
│  Final Price = Base × DayMultiplier × SeasonMultiplier × Days              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 4.5 Reporting

| Report Type | FREE | BASIC | PRO | ENTERPRISE |
|-------------|------|-------|-----|------------|
| **Revenue by Day** | ❌ | ✅ | ✅ | ✅ |
| **Revenue by Month** | ❌ | ✅ | ✅ | ✅ |
| **Revenue by Branch** | ❌ | ❌ | ✅ | ✅ |
| **Fleet Utilization** | ❌ | ❌ | ✅ | ✅ |
| **Customer Analytics** | ❌ | ❌ | ✅ | ✅ |
| **Custom Reports** | ❌ | ❌ | ❌ | ✅ |
| **Export Excel/PDF** | ❌ | ❌ | ✅ | ✅ |

---

## 5. Pricing Model

### 5.1 Monthly Pricing

| Plan | Monthly | Annual | Savings |
|------|---------|--------|---------|
| FREE | 0đ | 0đ | - |
| BASIC | 500,000đ | 5,000,000đ | 17% |
| PRO | 1,500,000đ | 15,000,000đ | 17% |
| ENTERPRISE | 5,000,000đ | 50,000,000đ | 17% |

### 5.2 Add-on Pricing

| Add-on | Price | Billing |
|--------|-------|---------|
| SMS Notifications | 200,000đ/1,000 SMS | Monthly |
| Email Notifications | 100,000đ/5,000 emails | Monthly |
| Additional Branch | 300,000đ/branch | Monthly |
| Additional Vehicle | 50,000đ/vehicle | Monthly |
| Additional User | 100,000đ/user | Monthly |
| Extended Audit Log | 200,000đ | Monthly |

### 5.3 Overage Pricing

Khi vượt giới hạn Booking/tháng:

| Plan | Overage Fee |
|------|-------------|
| FREE | 10,000đ/booking |
| BASIC | 5,000đ/booking |
| PRO | 3,000đ/booking |
| ENTERPRISE | Không có overage |

### 5.4 Payment Methods

| Method | Availability |
|--------|--------------|
| Cash | All plans |
| Bank Transfer (VNĐ) | BASIC+ |
| E-wallet (MoMo, ZaloPay, VNPay) | PRO+ |
| Credit Card (Visa, Mastercard) | PRO+ |
| Invoice (NET 30) | ENTERPRISE only |

---

## 6. Add-ons

### 6.1 SMS Notifications

| Feature | Description |
|---------|-------------|
| **OTP Verification** | Xác thực đăng nhập bằng SMS |
| **Booking Confirmation** | Gửi SMS xác nhận đặt xe cho khách |
| **Reminder** | Nhắc nhở khách trả xe đúng hẹn |
| **Status Update** | Thông báo trạng thái đơn hàng |

**Pricing:** 200,000đ/1,000 SMS

### 6.2 Email Notifications

| Feature | Description |
|---------|-------------|
| **Booking Confirmation** | Email xác nhận đặt xe |
| **Invoice** | Gửi hóa đơn cho khách |
| **Marketing** | Promotions, newsletters |

**Pricing:** 100,000đ/5,000 emails

### 6.3 Google Maps Integration

| Feature | Description |
|---------|-------------|
| **Branch Location** | Hiển thị vị trí chi nhánh trên bản đồ |
| **Route Planning** | Tính đường đi cho khách |
| **Distance Calculation** | Tính khoảng cách giữa các chi nhánh |

**Pricing:** Included in PRO+, Otherwise add-on

---

## 7. Limits & Quotas

### 7.1 Per-Plan Limits

| Resource | FREE | BASIC | PRO | ENTERPRISE |
|----------|------|-------|-----|------------|
| Branches | 1 | 2 | 5 | Unlimited |
| Vehicles | 5 | 20 | 100 | Unlimited |
| Bookings/month | 20 | 100 | 500 | Unlimited |
| Users | 1 | 3 | 10 | Unlimited |
| Images/vehicle | 0 | 3 | 10 | Unlimited |
| API calls/day | 0 | 100 | 1,000 | Unlimited |
| Storage (GB) | 1 | 5 | 20 | 100 |

### 7.2 Overage Handling

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        Overage Flow                                          │
│                                                                              │
│  Booking được tạo                                                           │
│       │                                                                     │
│       ▼                                                                     │
│  Kiểm tra quota (Bookings/month)                                            │
│       │                                                                     │
│       ├── Trong quota ────► Cho phép tạo ────► Hoàn tất                     │
│       │                                                                     │
│       └── Vượt quota ────► Kiểm tra plan                                    │
│                              │                                              │
│                              ├── FREE ──► Báo warning ──► Block sau 20      │
│                              │                                              │
│                              ├── BASIC ──► Tính phí 5,000đ/booking         │
│                              │                                              │
│                              ├── PRO ──► Tính phí 3,000đ/booking            │
│                              │                                              │
│                              └── ENTERPRISE ──► Không giới hạn             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 8. Upgrade/Downgrade Policy

### 8.1 Upgrade Policy

| From | To | Effect | Action |
|------|----|--------|--------|
| FREE | BASIC | Immediate | Upgrade now |
| FREE | PRO | Immediate | Upgrade now |
| FREE | ENTERPRISE | Immediate | Contact sales |
| BASIC | PRO | Immediate | Upgrade now |
| BASIC | ENTERPRISE | Immediate | Contact sales |
| PRO | ENTERPRISE | Immediate | Contact sales |

**Upgrade Benefits:**
- Immediate access to new features
- No downtime
- Prorated billing for current month

### 8.2 Downgrade Policy

| From | To | Effect | Timing |
|------|----|--------|--------|
| BASIC | FREE | Loss of features | End of billing cycle |
| PRO | BASIC | Loss of features | End of billing cycle |
| PRO | FREE | Loss of features | End of billing cycle |
| ENTERPRISE | Any | Loss of features | End of billing cycle |

**Downgrade Rules:**
- Downgrade chỉ có hiệu lực khi hết billing cycle hiện tại
- Nếu data vượt limit của plan mới → Không cho downgrade
- Khuyến nghị: Upgrade trước, test đầy đủ, sau đó mới downgrade nếu cần

### 8.3 Cancellation Policy

- Cancel anytime, no lock-in contract
- Service continues until end of paid period
- Data retained for 30 days after cancellation
- After 30 days, all data deleted permanently

---

## 9. Comparison with Competitors

| Feature | Car Rental SaaS | Competitor A | Competitor B |
|---------|-----------------|--------------|--------------|
| Free Plan | ✅ | ❌ | ✅ (limited) |
| Multi-branch | ✅ | ✅ | ✅ |
| Dynamic Pricing | ✅ | ❌ | ✅ |
| Mobile App | Future | ✅ | ❌ |
| API Access | PRO+ | Enterprise | ❌ |
| Price/month | 500K-5M | 1M-10M | 800K-8M |

---

## Appendix: SQL Schema for Subscription

```sql
-- Tenants table with subscription info
CREATE TABLE tenants (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(255) NOT NULL,
    domain VARCHAR(255) UNIQUE NOT NULL,
    plan_tier VARCHAR(20) NOT NULL DEFAULT 'FREE'
        CHECK (plan_tier IN ('FREE', 'BASIC', 'PRO', 'ENTERPRISE')),
    subscription_status VARCHAR(20) DEFAULT 'active'
        CHECK (subscription_status IN ('active', 'cancelled', 'suspended')),
    subscription_start_date TIMESTAMP WITH TIME ZONE,
    subscription_end_date TIMESTAMP WITH TIME ZONE,
    settings JSONB DEFAULT '{}',
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- Plan limits configuration
CREATE TABLE plan_limits (
    plan_tier VARCHAR(20) PRIMARY KEY,
    max_branches INTEGER,
    max_vehicles INTEGER,
    max_bookings_per_month INTEGER,
    max_users INTEGER,
    max_images_per_vehicle INTEGER,
    base_price_monthly DECIMAL(12, 2),
    base_price_yearly DECIMAL(12, 2)
);

-- Add-ons
CREATE TABLE tenant_addons (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID REFERENCES tenants(id),
    addon_type VARCHAR(50) NOT NULL,
    quantity INTEGER DEFAULT 1,
    price DECIMAL(12, 2),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);
```
