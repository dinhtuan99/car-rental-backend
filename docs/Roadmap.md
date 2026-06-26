# LỘ TRÌNH PHÁT TRIỂN CAR RENTAL SAAS 2.0

**Cập nhật:** 24/06/2026 — Tái cấu trúc dựa trên phân tích thị trường & ưu tiên tính năng.
**Tham chiếu phân tích:** [docs/analysis/](analysis/)
**Đặc tả API:** [API-Specification.md](API-Specification.md)

---

## Tổng Quan

Lộ trình 21 tuần chia làm **4 Phase**, mỗi phase kết thúc = có sản phẩm chạy được. Team 3 người part-time (30-40%). Sản phẩm hướng đến **xe tự lái** (self-drive rental) trước, mở rộng xe có lái ở Phase 2+.

### Nguyên tắc thiết kế

1. **Mỗi phase có thứ chạy được** — Demo được cho khách hàng, không phải "làm dần bao giờ xong"
2. **Public + Admin đồng thời** — Không tách "admin trước, public sau"
3. **Key features được đầu tư nhiều nhất** — Availability, Tenant Isolation, Counter Ops
4. **Gate chất lượng bắt buộc** — Không đạt → không qua phase tiếp

### Tổng quan Timeline

```
 PHASE A (W1-W6)    PHASE B (W7-W14)    PHASE C (W15-W18)   PHASE D (W19-W21)
┌──────────────────┬───────────────────┬───────────────────┬──────────────────┐
│   NỀN MÓNG       │   MVP CORE        │   MVP PUBLIC      │   GO-LIVE        │
│                  │                   │                   │                  │
│ • Bootcamp Java  │ • Branch & Vehicle│ • Public Website  │ • Test & Audit   │
│ • Auth + Tenant  │ • Customer & Price│ • Guest Checkout  │ • Security       │
│ • DB Schema      │ • Booking & Counter│ • Customer Portal │ • Deploy Prod    │
│ • Docker         │ • Payment & Report│ • Domain per Tenant│ • Docs           │
└──────────────────┴───────────────────┴───────────────────┴──────────────────┘
       6 tuần              8 tuần              4 tuần             3 tuần
```

---

## PHASE A: NỀN MÓNG (Weeks 1-6)

**Mục tiêu:** Xây dựng nền tảng kỹ thuật cốt lõi — auth, multi-tenant, DB schema. Sẵn sàng cho nghiệp vụ.

**Câu hỏi cần trả lời:** "Hệ thống đã cô lập được dữ liệu giữa các nhà xe chưa?"

### Week 1-3: Bootcamp Backend

| Tuần | Nội dung | Kết quả |
|------|----------|---------|
| **W1** | Java Core, OOP, Spring Boot Core, RESTful API | Tự code được CRUD đơn giản |
| **W2** | PostgreSQL, Spring Data JPA, Entity Mapping, Pagination | Thiết kế được entity + quan hệ |
| **W3** | Spring Security, JWT, RBAC, PostgreSQL RLS, Docker Compose | Code được API có JWT bảo vệ |

### Week 4-6: Base System

| Tuần | Nhiệm vụ |
|------|----------|
| **W4** | Khởi tạo boilerplate BE (Spring Boot 3.x, Exception Handler, Swagger/OpenAPI). Khởi tạo boilerplate FE (Angular 17 Admin + Next.js 14 Customer). |
| **W5** | Auth system: Login/Register/Refresh + JWT + RBAC (SUPER_ADMIN, TENANT_ADMIN, STAFF, CUSTOMER). Next.js Middleware route guarding. |
| **W6** | Multi-tenant Engine: TenantContext (ThreadLocal), TenantInterceptor (subdomain/header), Postgres RLS activation, DB migration (Flyway) với tenant_id trên tất cả bảng nghiệp vụ. |

### Deliverables Phase A

| # | Deliverable | Tiêu chuẩn đạt |
|---|-------------|-----------------|
| A1 | Login/Register flow (FE + BE) | Đăng nhập → nhận JWT → redirect đúng role |
| A2 | DB Schema đầy đủ + Migration scripts | Tất cả bảng có tenant_id, RLS enabled |
| A3 | Tenant Isolation Test (tự động) | Tenant A không đọc được data Tenant B |
| A4 | Docker Compose one-command setup | `docker compose up` chạy toàn bộ stack |
| A5 | Swagger API docs đầy đủ | FE mock được tất cả endpoint |

### Quality Gate A

```
☐ Mọi thành viên tự code được API CRUD có JWT bảo vệ
☐ Tenant Isolation Test PASS (có unit test tự động)
☐ Docker Compose hoạt động trên máy cả 3 thành viên
```

---

## PHASE B: MVP CORE — QUẢN LÝ NỘI BỘ NHÀ XE (Weeks 7-14)

**Mục tiêu:** Nhà xe quản lý được nội bộ: xe, khách, booking tại quầy, thu tiền.

**Câu hỏi cần trả lời:** "Một nhà xe có thể bỏ Excel/sổ tay và dùng phần mềm này không?"

### Sprint 1 (W7-8): Branch & Vehicle Management

**API tham chiếu:** [Branch APIs](API-Specification.md#5-branch-apis), [Vehicle APIs](API-Specification.md#6-vehicle-apis)

| Week | BE | FE (Admin) | Public |
|------|-----|-----------|--------|
| **W7** | CRUD Branch, CRUD Vehicle, Vehicle status workflow | Trang quản lý chi nhánh + danh sách xe | GET /public/vehicles (xe trống) |
| **W8** | Redis cache availability theo branch+date. Integration test. | Tích hợp API thật, validate, loading/error states | Responsive mobile |

### Sprint 2 (W9-10): Customer & Pricing Engine

**API tham chiếu:** [Customer APIs](API-Specification.md#7-customer-apis), [Pricing APIs](API-Specification.md#10-pricing-apis)

| Week | BE | FE (Admin) | Public |
|------|-----|-----------|--------|
| **W9** | CRUD Customer + AES encrypt CCCD/GPLX | Trang quản lý khách hàng + lịch sử thuê | — |
| **W10** | Pricing engine: basePrice × dayMultiplier (weekday=1.0, weekend=1.2) | Trang cấu hình bảng giá | GET /public/pricing |

### Sprint 3 (W11-12): Booking & Counter Operations

**API tham chiếu:** [Booking APIs](API-Specification.md#8-booking-apis)

| Week | BE | FE (Admin) | Public |
|------|-----|-----------|--------|
| **W11** | CRUD Booking, status workflow, Pessimistic locking (SELECT FOR UPDATE) | Kanban/Table quản lý booking | — |
| **W12** | Start rental (giao xe), Complete rental (nhận xe + tính tiền tự động: phụ phí trễ, hư hỏng) | Form giao/nhận xe (tối đa 3 bước) | POST /public/bookings (guest) |

### Sprint 4 (W13-14): Payment & Basic Reports

**API tham chiếu:** [Payment APIs](API-Specification.md#9-payment-apis), [Report APIs](API-Specification.md#11-report-apis)

| Week | BE | FE (Admin) | Public |
|------|-----|-----------|--------|
| **W13** | CRUD Payment (cash, bank_transfer), VietQR generator | Trang hóa đơn + thanh toán | — |
| **W14** | 3 báo cáo: doanh thu ngày, fleet status, booking hôm nay | Dashboard với biểu đồ cơ bản | — |

### Deliverables Phase B

| # | Deliverable | Tiêu chuẩn đạt |
|---|-------------|-----------------|
| B1 | Fleet Dashboard | Load < 200ms với 50 xe, trạng thái chính xác 100% |
| B2 | Booking Flow Tại Quầy | Tạo → confirm → giao → nhận → thu tiền < 3 phút/boking |
| B3 | Public Vehicle Search | Kết quả khớp DB 100%, response < 200ms |
| B4 | Guest Booking | Booking xuất hiện ngay trên dashboard staff |
| B5 | VietQR Payment | Hiển thị đúng số tiền + nội dung chuyển khoản |
| B6 | Basic Reports | Số liệu khớp DB, load < 500ms |

### Quality Gate B

```
☐ Double-booking test: 10 request đồng thời → chỉ 1 thành công
☐ Full flow test: Guest book → Staff confirm → Giao xe → Nhận xe → Thanh toán
☐ Cache hit rate > 80% cho availability queries
☐ FE hoạt động trên Chrome, Firefox, Safari (desktop + mobile)
```

---

## PHASE C: MVP PUBLIC (Weeks 15-18)

**Mục tiêu:** Khách hàng cuối tự đặt xe online qua web công cộng. Nhà xe có web riêng với domain riêng.

**Câu hỏi cần trả lời:** "Một khách du lịch ở Đà Nẵng vào web nhà xe, tìm xe, đặt xe, ra quầy lấy xe — được không?"

### Sprint 5 (W15-16): Public Website Hoàn Chỉnh

**API tham chiếu:** [Public & Customer Portal APIs](API-Specification.md#13-public--customer-portal-apis)

| Week | BE | FE (Customer Website - Next.js) |
|------|-----|--------------------------------|
| **W15** | Hoàn thiện public API, rate limiting, multi-tenant domain routing | Trang chủ + tìm kiếm xe (branch, ngày, loại) + chi tiết xe + bảng giá |
| **W16** | Email xác nhận booking (template riêng/t tenant) | Form đặt xe guest checkout + xác nhận QR + responsive mobile |

### Sprint 6 (W17-18): Customer Portal

| Week | BE | FE (Customer Portal - Next.js) |
|------|-----|--------------------------------|
| **W17** | Customer login/register, Guest→Member tự động | Customer dashboard: lịch sử booking, trạng thái hiện tại |
| **W18** | 1-click rebook, cập nhật hồ sơ | Tự động điền form + quản lý CCCD/GPLX |

### Deliverables Phase C

| # | Deliverable | Tiêu chuẩn đạt |
|---|-------------|-----------------|
| C1 | Public Website hoàn thiện | Khách tìm → đặt → nhận email < 5 phút |
| C2 | Guest → Member flow | Tỷ lệ chuyển đổi > 15% [MT] |
| C3 | Customer Portal | Load lịch sử < 1s, rebook 1 chạm |
| C4 | Multi-tenant Domain | 2 tenant, 2 domain khác nhau, data cô lập |

### Quality Gate C

```
☐ UAT: 5 người dùng thật hoàn thành booking không cần hướng dẫn
☐ Mobile responsive: Toàn bộ flow hoạt động trên iPhone/Android
☐ Domain routing: 2 tenant khác domain, đúng brand, đúng data
☐ Email deliverability > 95%
```

---

## PHASE D: POLISH & GO-LIVE (Weeks 19-21)

**Mục tiêu:** Hệ thống sẵn sàng cho khách hàng thật. Deploy production. Có test + audit.

**Câu hỏi cần trả lời:** "Có thể mời nhà xe đầu tiên đăng ký dùng thử không?"

### Sprint 7 (W19-20): Testing & Hardening

| Week | Nhiệm vụ |
|------|----------|
| **W19** | Unit test BE coverage > 60%. Integration test full flow (Cypress/Selenium). Bug fixing. |
| **W20** | Security audit (OWASP Top 10, IDOR, SQL injection). Tenant isolation penetration test. Performance test: 100 tenant, 1000 xe, 1000 booking. |

### Sprint 8 (W21): Deployment & Docs

| Week | Nhiệm vụ |
|------|----------|
| **W21** | Deploy production (VPS/Cloud), SSL/TLS, monitoring, user guide, SaaS landing page. Onboard tenant đầu tiên. |

### Deliverables Phase D

| # | Deliverable | Tiêu chuẩn đạt |
|---|-------------|-----------------|
| D1 | Production Deployment | Uptime 99%+, SSL tất cả domain |
| D2 | Test Coverage > 60% | Tất cả critical path có test |
| D3 | Security Audit Report | 0 lỗi Critical/High |
| D4 | Tenant Isolation Certification | 0 cross-tenant data leak |
| D5 | User Guide | Nhà xe tự setup trong 1 giờ |
| D6 | SaaS Landing Page | Khách hàng đăng ký dùng thử được |

### Quality Gate D (Go-Live Gate)

```
☐ Production deploy không lỗi critical
☐ Tất cả gate A, B, C vẫn PASS
☐ Load test: 100 concurrent users, response < 500ms
☐ Security: 0 critical, 0 high vulnerabilities
☐ Ít nhất 1 tenant thật đăng ký dùng thử thành công
```

---

## Các Mốc Milestones & Quyết Định Kinh Doanh

| Milestone | Tuần | Sản phẩm demo được | Hành động kinh doanh |
|-----------|------|---------------------|----------------------|
| **M1: Bootcamp** | W3 | API CRUD có JWT | Team sẵn sàng code nghiệp vụ |
| **M2: Base Ready** | W6 | Auth + Tenant Isolation | Test cô lập dữ liệu đạt |
| **M3: Fleet Live** | W8 | Quản lý xe + xem xe trống | **Demo 2-3 nhà xe, lấy feedback** |
| **M4: Counter Ops** | W12 | Booking + giao/nhận xe | **Cho 1 nhà xe dùng thử nội bộ** |
| **M5: Payment Done** | W14 | Thanh toán + báo cáo | Sẵn sàng thu tiền thật |
| **M6: Public Web** | W18 | Web công cộng hoàn thiện | **Mở đăng ký dùng thử (free trial)** |
| **M7: GO-LIVE** | W21 | Production system | **Bắt đầu thu phí tenant đầu tiên** |

---

## Phân Bổ Nhân Sự

| Phase | BE Lead | FE Angular | FE Next.js | DevOps |
|-------|---------|------------|------------|--------|
| **A (W1-6)** | Code base + Auth + Tenant | Setup base + Auth UI | Setup base + Auth UI | Docker + DB + RLS |
| **B (W7-14)** | API nghiệp vụ (entity→service→controller→test) | Admin Dashboard từng module | Public Web từng module | CI/CD + Monitoring |
| **C (W15-18)** | Hoàn thiện public API + domain routing | Chỉnh sửa admin theo feedback | Public website + Customer portal (chủ lực) | SSL + Domain setup |
| **D (W19-21)** | Performance tuning + Security fix | Integration test admin | Integration test customer web | Production deploy |

---

## Tính Năng Đã Cắt / Lùi

| Tính năng | Hành động | Lý do |
|-----------|-----------|-------|
| **Vehicle Transfer** | Cắt khỏi MVP | Nhà xe <50 xe không dùng |
| **SMS Notification** | Cắt | Tốn chi phí, giữ Email |
| **Pricing SeasonMultiplier** | Cắt | Nhà xe tự chỉnh basePrice mùa cao điểm |
| **Pricing Holiday Calendar** | Cắt | Quá phức tạp, maintain hàng năm |
| **Report Nâng cao (xu hướng, top KH)** | Lùi Phase 2 | MVP chỉ cần 3 báo cáo cơ bản |
| **Subscription BASIC/PRO/ENTERPRISE** | Gộp còn FREE + PAID | Phân mảnh gói cước khi có 50+ tenant |
| **Upload ảnh CCCD/GPLX** | Lùi Phase 2 | Giữ AES encrypt, upload ảnh để sau |

---

## Kế Hoạch Sau MVP (Phase 2+)

**Công nghệ chi tiết:** [Post-MVP-Technologies.md](Post-MVP-Technologies.md)

Sau khi ổn định với 50+ tenant trả phí [ƯT]:

| Ưu tiên | Mở rộng | Thời gian dự kiến |
|---------|---------|--------------------|
| 1 | Subscription Plans đa bậc (BASIC/PRO/ENTERPRISE) | 2 tuần |
| 2 | Module Xe Có Lái (Driver + Tour + Dispatch) | 6-8 tuần |
| 3 | Ứng dụng Mobile cho Tenant (iOS/Android) | 8-10 tuần |
| 4 | API tích hợp bên thứ 3 (bảo hiểm, cổng thanh toán) | 4 tuần |
| 5 | Migration Monolith → Microservices (nếu cần) | Liên tục |

---

*Tài liệu được biên soạn dựa trên chuỗi phân tích thị trường & ưu tiên tính năng. Xem chi tiết tại [docs/analysis/](analysis/).*
