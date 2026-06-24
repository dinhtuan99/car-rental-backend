# Car Rental SaaS - Project Information

> **Cập nhật:** 24/06/2026 — Sync với phân tích thị trường & roadmap tái cấu trúc.

## Yêu cầu
Xây dựng hệ thống quản lý, cho thuê xe du lịch tới khách hàng lẻ. Yêu cầu đi theo hướng SaaS (B2B SaaS). Hệ thống theo mô hình kiến trúc Multi-tenant, mô hình nghiệp vụ Multi-branch. Sản phẩm phải phù hợp với xu hướng hiện tại.

**Định hướng thị trường Phase 1:** Cho thuê xe tự lái (self-drive rental) — xe máy & ô tô tại các điểm du lịch. Phase 2 mở rộng sang xe có lái. [Phân tích chi tiết](analysis/03-business-model-comparison.md)

---

## 1. Tên Dự án
**Car Rental SaaS Platform**

---

## 2. Mục đích

Hệ thống quản lý và cho thuê xe tới khách hàng lẻ, hỗ trợ multi-branch (đa chi nhánh), triển khai dạng SaaS (1 codebase, nhiều tenant).

### Đối tượng sử dụng

| Đối tượng | Mô tả | Fleet Size |
|-----------|-------|------------|
| Shop cho thuê xe nhỏ | Shop địa phương, điểm du lịch | 5-20 xe |
| Chuỗi cho thuê xe vừa | Chain có vài chi nhánh | 20-100 xe |
| Chuỗi cho thuê xe lớn | Enterprise | 100+ xe |

---

## 3. Các ứng dụng liên quan

| Ứng dụng | Mô tả | Người dùng | Tech Stack |
|----------|-------|------------|------------|
| **SaaS Admin Portal** | Quản trị toàn hệ thống: Quản lý tenant, subscription, báo cáo doanh thu nền tảng. | Super Admin, Billing Operations | Angular 17+ |
| **Admin Dashboard** | Quản lý nhà xe: xe, booking, khách hàng, thanh toán. | Nhân viên, quản lý nhà xe | Angular 17+ |
| **Customer Website** | Web đặt xe công khai + cổng thông tin cá nhân (lịch sử thuê, hồ sơ). | Khách thuê xe (Guest & Member) | Next.js 14+ |

---

## 4. Thành phần tham gia dự án

### 4.1 Team Structure

| Role | Trách nhiệm | Tech Stack |
|------|-------------|------------|
| **Backend Lead** | Spring Boot APIs, Security, Database | Java 17, Spring Boot 3.x, PostgreSQL |
| **Frontend Lead (Admin)** | SaaS Admin Portal + Admin Dashboard (Angular) | Angular 17+, TypeScript, RxJS |
| **Frontend Lead (Customer)** | Customer Website (Next.js) | Next.js 14, React 18, TypeScript |
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
| **SaaS Platform Operations** | Quản lý tenants, duyệt subscription, báo cáo doanh thu platform. | P0 |
| **Tenant Management** | Đăng ký, đăng nhập, quản lý subscription (FREE/PAID). | P0 |
| **Branch Management** | CRUD chi nhánh, đánh dấu central branch. | P0 |
| **Vehicle Management** | CRUD xe, theo dõi trạng thái (available/rented/maintenance). | P0 |
| **Customer Management** | Lưu thông tin khách hàng, lịch sử thuê xe, mã hóa CCCD/GPLX. | P1 |
| **Booking Management** | Tạo/cập nhật/hủy đơn thuê, giao/nhận xe, chống double-booking. | P0 |
| **Dynamic Pricing** | Giá theo ngày thường, cuối tuần (basePrice × dayMultiplier). | P0 |
| **Payment Management** | Tiền mặt, chuyển khoản, VietQR. | P1 |
| **Reporting** | Doanh thu theo ngày, fleet status, booking hôm nay. | P1 |

### 5.2 Tính năng lùi Phase 2

| Tính năng | Lý do |
|-----------|-------|
| Vehicle Transfer (điều phối xe) | Nhà xe <50 xe không dùng, gọi điện là xong |
| SMS Notification | Tốn chi phí, ROI thấp. Giữ Email. |
| Pricing Season/Holiday | Nhà xe tự chỉnh basePrice mùa cao điểm |
| Subscription đa bậc (BASIC/PRO/ENTERPRISE) | Phân mảnh gói cước khi có 50+ tenant |
| Upload ảnh CCCD/GPLX | Giữ AES encrypt, upload ảnh để Phase 2 |

### 5.3 Subscription Plans

| Plan | Giá/tháng | Giới hạn |
|------|-----------|----------|
| **FREE** | 0đ | 1 Branch, 10 Vehicles, 30 Bookings, 30 ngày dùng thử |
| **PAID** | 1,500,000đ | 5 Branches, 100 Vehicles, 500 Bookings |

> Chi tiết: [Subscription-Plans.md](Subscription-Plans.md)

### 5.4 Pricing Engine (đơn giản hóa)

```
Giá/ngày = BasePrice × DayMultiplier

DayMultiplier:
├── Weekday (T2-T6): 1.0
└── Weekend (T7-CN): 1.2

Ví dụ (xe base_price = 500,000đ/ngày):
├── Thứ 2 (weekday): 500,000 × 1.0 = 500,000đ
├── Thứ 7 (weekend): 500,000 × 1.2 = 600,000đ
└── Chủ nhật (weekend): 500,000 × 1.2 = 600,000đ
```

---

## 6. Phạm vi triển khai dự án

### 6.1 Technical Scope

| Aspect | Chi tiết |
|--------|----------|
| **Mô hình** | Multi-tenant SaaS (1 app, nhiều khách hàng) |
| **Architecture** | Monolithic Modular (MVP), có thể chuyển Microservices sau |
| **Database** | Shared PostgreSQL với tenant_id isolation + RLS |
| **Authentication** | JWT with access/refresh tokens. Tenant users isolated. Super Admin bypass RLS. |
| **Deployment** | Docker containers |
| **Scalability** | 10 → 1000+ tenants without code change |

### 6.2 Platform Scope

| Platform | Support |
|----------|---------|
| **Web (Desktop)** | Full support |
| **Web (Mobile)** | Responsive design |
| **Mobile App** | Phase 2+ |

### 6.3 Deployment Scope

| Environment | Purpose |
|-------------|---------|
| Local Development | localhost:8080 (API), localhost:4200 (Angular Admin), localhost:3000 (Next.js Customer) |
| Staging | UAT, Testing |
| Production | Live users |

### 6.4 Timeline

| Phase | Timeline | Deliverables |
|-------|----------|--------------|
| Phase A: Nền móng | Week 1-6 | Auth, Tenant Isolation, DB Schema, Docker |
| Phase B: MVP Core | Week 7-14 | Quản lý nội bộ nhà xe (xe, khách, booking, thanh toán) |
| Phase C: MVP Public | Week 15-18 | Web công cộng + Guest checkout + Customer Portal |
| Phase D: Go-Live | Week 19-21 | Test, Audit, Production Deploy |

> Chi tiết: [Roadmap.md](Roadmap.md)

---

## 7. Success Criteria

- [ ] Tenant có thể đăng ký và có web riêng trong 5 phút
- [ ] 1 chi nhánh quản lý 100+ xe không chậm
- [ ] Booking flow hoàn chỉnh < 30 giây (online), < 3 phút (tại quầy)
- [ ] Data isolation 100% giữa các tenant
- [ ] 0 double-booking (kể cả khi nhiều request đồng thời)
- [ ] Scale từ 10 → 1000 tenant without code change

---

## 8. Related Documents

| Document | Location | Purpose |
|----------|----------|---------|
| API Spec | [API-Specification.md](API-Specification.md) | API endpoints |
| Database Schema | [Database-Schema.md](Database-Schema.md) | Database design |
| Roadmap | [Roadmap.md](Roadmap.md) | Project timeline |
| Subscription Plans | [Subscription-Plans.md](Subscription-Plans.md) | Pricing details |
| Mindmap | [Mindmap.md](Mindmap.md) | Visual overview |
| Phân tích | [analysis/](analysis/) | Market & feature analysis |

---

*Last updated: 2026-06-24*
