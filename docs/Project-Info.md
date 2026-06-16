# Car Rental SaaS - Project Information

## 1. Tên Dự án
**Car Rental SaaS Platform**

---

## 2. Mục đích

Hệ thống quản lý và cho thuê xe du lịch tới khách hàng lẻ, hỗ trợ multi-branch (đa chi nhánh).

### Đối tượng sử dụng

| Đối tượng | Mô tả | Fleet Size |
|-----------|-------|------------|
| Công ty cho thuê xe nhỏ | Gara địa phương | 5-20 xe |
| Chuỗi cho thuê xe vừa | Chain có vài chi nhánh | 20-100+ xe |
| Chuỗi cho thuê xe lớn | Enterprise | 100+ xe |

---

## 3. Các ứng dụng liên quan

| Ứng dụng | Mô tả | Người dùng |
|----------|-------|------------|
| **SaaS Admin Portal** | Trang quản trị toàn hệ thống SaaS: Quản lý tenant, subscription, cấu hình dùng chung và báo cáo tổng thể doanh thu nền tảng. | Đội ngũ vận hành hệ thống (Super Admin, Support, Billing Operations) |
| **Admin Dashboard** | Trang quản lý cho nhà xe | Nhân viên, quản lý nhà xe |
| **Customer Website** | Trang đặt xe công khai kết hợp cổng thông tin cá nhân (quản lý lịch sử thuê xe, hồ sơ cá nhân). | Khách hàng thuê xe (Vãng lai & Đã có tài khoản) |

---

## 4. Thành phần tham gia dự án

### 4.1 Team Structure

| Role | Trách nhiệm | Tech Stack |
|------|-------------|------------|
| **Backend Lead** | Spring Boot APIs, Security, Database | Java 17, Spring Boot 3.x, PostgreSQL |
| **Frontend Lead** | React UI/UX, Components, Pages | React 18, TypeScript, Vite |
| **DevOps/Shared** | Docker, CI/CD, Infrastructure | Docker, Docker Compose |

### 4.2 Stakeholders

| Stakeholder | Interest |
|------------|----------|
| Product Owner | Quản lý roadmap, priorities |
| Development Team | Xây dựng và ship sản phẩm |
| Customers (Tenants) | Sử dụng platform để quản lý thuê xe |
| End Customers | Đặt xe qua public website |

---

## 5. Chức năng dự án

### 5.1 Module chính

| Module | Chức năng | Priority |
|--------|-----------|----------|
| **SaaS Platform Operations** | Quản lý tenants, duyệt subscription, cấu hình tham số hệ thống dùng chung, xem báo cáo doanh thu platform. | P0 |
| **Tenant Management** | Đăng ký, đăng nhập, quản lý subscription (FREE/BASIC/PRO/ENTERPRISE) | P0 |
| **Branch Management** | CRUD chi nhánh, đánh dấu central branch | P0 |
| **Vehicle Management** | CRUD xe, theo dõi trạng thái (available/rented/maintenance) | P0 |
| **Customer Management** | Lưu thông tin khách hàng, lịch sử thuê xe | P1 |
| **Booking Management** | Tạo/cập nhật/hủy đơn thuê, theo dõi trạng thái | P0 |
| **Dynamic Pricing** | Giá theo ngày thường, cuối tuần, ngày lễ, mùa cao điểm | P0 |
| **Payment Management** | Tiền mặt, chuyển khoản, ví điện tử | P1 |
| **Vehicle Transfer** | Chuyển xe từ central đến chi nhánh thiếu | P2 |
| **Reporting** | Doanh thu theo ngày/tháng/chi nhánh | P1 |
| **Notifications** | SMS/Email notification (OTP, xác nhận, nhắc trả xe) | P2 |

### 5.2 Subscription Plans

| Plan | Giá/tháng | Giới hạn |
|------|-----------|----------|
| **FREE** | 0đ | 1 Branch, 5 Vehicles, 20 Bookings |
| **BASIC** | 500,000đ | 2 Branches, 20 Vehicles, 100 Bookings |
| **PRO** | 1,500,000đ | 5 Branches, 100 Vehicles, 500 Bookings |
| **ENTERPRISE** | 5,000,000đ | Unlimited |

### 5.3 Pricing Engine

```
Giá/ngày = BasePrice × DayMultiplier × SeasonMultiplier

Ví dụ (xe base_price = 500,000đ/ngày):
├── Thứ 2 (weekday, normal): 500,000 × 1.0 × 1.0 = 500,000đ
├── Thứ 7 (weekend, normal): 500,000 × 1.2 × 1.0 = 600,000đ
├── Chủ nhật (weekend, normal): 500,000 × 1.2 × 1.0 = 600,000đ
└── Tết (holiday, peak): 500,000 × 1.5 × 1.3 = 975,000đ
```

---

## 6. Phạm vi triển khai dự án

### 6.1 Technical Scope

| Aspect | Chi tiết |
|--------|----------|
| **Mô hình** | Multi-tenant SaaS (1 app, nhiều khách hàng) |
| **Architecture** | Monolithic (MVP), có thể chuyển Microservices sau |
| **Database** | Shared PostgreSQL với tenant_id isolation |
| **Authentication** | JWT with access/refresh tokens. Tenant users isolated under `tenant_id`. Super Admin has global scope (tenant_id = null). |
| **Deployment** | Docker containers, Cloud deployment |
| **Scalability** | 10 → 1000+ tenants without code change |

### 6.2 Platform Scope

| Platform | Support |
|----------|---------|
| **Web (Desktop)** | ✅ Full support |
| **Web (Mobile)** | ✅ Responsive design |
| **Mobile App** | Future (Phase 2+) |

### 6.3 Deployment Scope

| Environment | URL | Purpose |
|-------------|-----|---------|
| Local Development | localhost:8080 (API), localhost:5173 (UI) | Development |
| Staging | staging.carrental-saas.com | UAT, Testing |
| Production | production.carrental-saas.com | Live users |

### 6.4 Timeline

| Phase | Timeline | Deliverables |
|-------|----------|--------------|
| Phase 0: Preparation | Week 1-4 | Design docs, Tech learning |
| Phase 1: Core Infrastructure | Week 5-8 | CRUD APIs + Admin UI |
| Phase 2: MVP Features | Week 9-12 | Booking flow + Public website |
| Phase 3: Polish & Testing | Week 13-14 | Tests, Bug fixes |
| Phase 4: MVP Launch | Week 15 | Production deployment |

---

## 7. Success Criteria

- [ ] Tenant có thể đăng ký và bắt đầu sử dụng trong 5 phút
- [ ] 1 chi nhánh có thể quản lý 100+ xe mà không chậm
- [ ] Booking flow hoàn chỉnh trong < 30 giây
- [ ] Data isolation 100% giữa các tenant
- [ ] Có thể scale từ 10 → 1000 tenant without code change

---

## 8. Related Documents

| Document | Location | Purpose |
|----------|----------|---------|
| Technical Spec | [SPEC.md](plans/2026-06-12-spec.md) | Chi tiết kỹ thuật |
| API Spec | [API-Specification.md](../API-Specification.md) | API endpoints |
| Database Schema | [Database-Schema.md](../Database-Schema.md) | Database design |
| Roadmap | [Roadmap.md](../Roadmap.md) | Project timeline |
| Subscription Plans | [Subscription-Plans.md](../Subscription-Plans.md) | Pricing details |

---

*Last updated: 2026-06-15*
