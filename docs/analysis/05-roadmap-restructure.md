# Roadmap Tái Cấu Trúc — Từ "Làm Dần" Sang "Ship Từng Phase"

**Ngày:** 24/06/2026
**Mục tiêu:** Tái cấu trúc lộ trình 23 tuần thành các phase có sản phẩm bàn giao rõ ràng. Mỗi phase kết thúc = có sản phẩm demo được đưa ra thị trường, không phải "xong 1 module trong hệ thống dở dang".

---

## 0. Vấn Đề Của Roadmap Cũ

### Cấu trúc cũ:

```
Phase 1: Học (W1-3) → Phase 2: Setup (W4-5) → Phase 3: 8 modules tuần tự (W6-21) → Phase 4: Public (W22-23)
```

### Vấn đề:

| # | Vấn đề | Hệ quả |
|---|--------|--------|
| 1 | **Không có gì chạy được cho đến tuần 21** | 5 tháng dev mà không có sản phẩm để show khách hàng |
| 2 | **8 module xếp tuần tự, không ưu tiên** | Module quan trọng (booking) và module ít quan trọng (transfer) được đối xử như nhau |
| 3 | **Public booking để cuối cùng (tuần 22-23)** | Trong khi đây là thứ khách hàng thấy đầu tiên (chiếm 73% booking) |
| 4 | **Không có mốc release rõ ràng** | "Làm dần bao giờ xong thì xong" — không có áp lực ship |
| 5 | **Không gắn với mục tiêu kinh doanh** | Không trả lời được câu hỏi: "Tháng 2 có gì để bán?" |

---

## 1. Nguyên Tắc Thiết Kế Roadmap Mới

```
NGUYÊN TẮC #1: MỖI PHASE CÓ SẢN PHẨM DEMO ĐƯỢC
────────────────────────────────────────────
Kết thúc bất kỳ phase nào cũng có thể:
  - Demo cho khách hàng tiềm năng
  - Thu thập feedback thật
  - Pivot nếu cần (chưa tốn quá nhiều công)

NGUYÊN TẮC #2: PUBLIC + ADMIN ĐỒNG THỜI
────────────────────────────────────────────
Không tách "admin trước, public sau". Mỗi module làm cả 2 mặt:
  - Admin API (staff quản lý) 
  - Public API (khách đặt xe)
Cùng lúc, trong cùng sprint.

NGUYÊN TẮC #3: TẬP TRUNG VÀO KEY FEATURES
────────────────────────────────────────────
3 key features (Availability, Tenant Iso, Counter Ops) được
đầu tư nhiều thời gian nhất. Tính năng phụ (report, transfer)
bị cắt hoặc lùi.

NGUYÊN TẮC #4: GATE CHẤT LƯỢNG BẮT BUỘC
────────────────────────────────────────────
Mỗi phase có quality gate. Không đạt → không qua phase tiếp.
Các vấn đề phát sinh được xử lý ngay trong phase, không dồn sang phase sau.
```

---

## 2. Roadmap Mới — 4 Phase, 21 Tuần

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         CAR RENTAL SAAS — ROADMAP 2.0                        │
├───────────────┬───────────────┬─────────────────┬─────────────────────────────┤
│   PHASE A     │   PHASE B     │    PHASE C       │      PHASE D               │
│   NỀN MÓNG    │   MVP CORE    │   MVP PUBLIC      │    POLISH & GO-LIVE        │
│   (6 tuần)    │   (8 tuần)    │   (4 tuần)        │    (3 tuần)                │
├───────────────┼───────────────┼─────────────────┼─────────────────────────────┤
│ W1     W6     │ W7     W14    │ W15     W18      │ W19        W21             │
└───────────────┴───────────────┴─────────────────┴─────────────────────────────┘
         │               │               │                    │
         ▼               ▼               ▼                    ▼
    ┌─────────┐    ┌──────────┐    ┌──────────┐        ┌──────────┐
    │Có auth  │    │Nhà xe    │    │Khách đặt │        │Deploy    │
    │+ tenant │    │quản lý   │    │được online│       │production│
    │+ DB     │    │được nội bộ│   │không cần TK│      │có khách  │
    └─────────┘    └──────────┘    └──────────┘        └──────────┘
```

---

## 3. Chi Tiết Từng Phase

### PHASE A: NỀN MÓNG (6 tuần — Week 1→6)

**Mục tiêu kinh doanh:** Có nền tảng kỹ thuật vững chắc, sẵn sàng build nghiệp vụ.

**Câu hỏi trả lời được sau phase này:** "Hệ thống đã cô lập được dữ liệu giữa các nhà xe chưa?"

```
Week 1-3: BOOTCAMP BACKEND
├── Java Core & OOP + Spring Boot Core
├── Spring Data JPA + PostgreSQL
├── Spring Security + JWT + RLS
└── Docker Compose cho toàn team

Week 4-6: BASE SYSTEM
├── Boilerplate BE (Spring Boot 3.x, exception handler, Swagger)
├── Boilerplate FE (Angular 17 admin + Next.js 14 customer)
├── Auth system: Login/Register/Refresh + JWT + RBAC
├── Multi-tenant Engine: TenantContext + Interceptor + RLS
├── DB Schema hoàn chỉnh (tất cả bảng, migration scripts)
└── CI/CD pipeline cơ bản (GitHub Actions build + test)
```

**DELIVERABLE PHASE A:**

| # | Deliverable | Ai kiểm tra? | Tiêu chuẩn đạt |
|---|-------------|-------------|-----------------|
| A1 | Login/Register flow hoàn chỉnh (FE + BE) | Cả team | Đăng nhập thành công, nhận JWT, redirect đúng role |
| A2 | DB Schema diagram + Migration scripts | BE Lead | Tất cả bảng có tenant_id, RLS enabled |
| A3 | Tenant Isolation Test | DevOps | Tenant A không thể đọc dữ liệu Tenant B (có test case tự động) |
| A4 | Docker Compose one-command setup | DevOps | `docker compose up` → chạy được toàn bộ stack |
| A5 | Swagger API docs | BE Lead | Tất cả endpoint có document, FE mock được |

**QUALITY GATE PHASE A:**

```
☐ Tất cả thành viên tự code được 1 API CRUD có JWT bảo vệ
☐ Tenant Isolation Test PASS (có unit test tự động)
☐ Docker Compose hoạt động trên máy của cả 3 thành viên
☐ Swagger docs đầy đủ cho tất cả endpoint Phase A

Nếu GATE FAIL → không sang Phase B.
```

---

### PHASE B: MVP CORE — QUẢN LÝ NỘI BỘ NHÀ XE (8 tuần — Week 7→14)

**Mục tiêu kinh doanh:** Nhà xe dùng được hệ thống để quản lý nội bộ: xe, khách, booking tại quầy, thu tiền.

**Câu hỏi trả lời được sau phase này:** "Một nhà xe có thể bỏ Excel/sổ tay và dùng phần mềm này không?"

```
SPRINT 1 (W7-8): BRANCH + VEHICLE MANAGEMENT
├── BE: CRUD Branch + CRUD Vehicle + Vehicle status
├── FE: Trang quản lý chi nhánh + danh sách xe
├── Public: GET /public/vehicles (danh sách xe trống cho web công cộng)
└── Cache: Redis cache availability theo branch + date

SPRINT 2 (W9-10): CUSTOMER + PRICING CƠ BẢN
├── BE: CRUD Customer + AES encrypt CCCD/GPLX
├── BE: Pricing engine (basePrice × dayMultiplier)
├── FE: Trang quản lý khách hàng + lịch sử thuê
├── Public: GET /public/pricing (xem giá trước khi đặt)
└── FE: Trang cấu hình bảng giá

SPRINT 3 (W11-12): BOOKING + COUNTER OPERATIONS
├── BE: CRUD Booking + status workflow (pending→confirmed→in_progress→completed)
├── BE: Pessimistic locking (SELECT FOR UPDATE) chống double booking
├── BE: Start rental (giao xe) + Complete rental (nhận xe + tính tiền)
├── FE: Kanban/Table quản lý booking + form giao/nhận xe
└── Public: POST /public/bookings (guest checkout)

SPRINT 4 (W13-14): PAYMENT + BASIC REPORT
├── BE: CRUD Payment (cash, bank_transfer) + VietQR generator
├── BE: 3 báo cáo: doanh thu theo ngày, fleet status, booking hôm nay
├── FE: Trang quản lý hóa đơn + màn hình thanh toán
├── FE: Dashboard với biểu đồ doanh thu cơ bản
└── Integration test: full flow từ tạo booking → giao xe → nhận xe → thu tiền
```

**DELIVERABLE PHASE B:**

| # | Deliverable | Mô tả | Tiêu chuẩn đạt |
|---|-------------|-------|-----------------|
| B1 | **Fleet Dashboard** | Nhà xe xem được: xe nào đang trống/đang thuê/bảo dưỡng | Thời gian load < 200ms với 50 xe |
| B2 | **Booking Flow Tại Quầy** | Staff tạo booking → confirm → giao xe → nhận xe → thu tiền | Hoàn thành 1 booking trong < 3 phút |
| B3 | **Public Vehicle Search** | Khách vào web thấy danh sách xe trống theo ngày | Kết quả chính xác 100% (so với DB) |
| B4 | **Guest Booking** | Khách lạ đặt xe không cần tài khoản | Booking xuất hiện trên dashboard staff |
| B5 | **VietQR Payment** | Khách quét mã QR chuyển khoản cọc | Hiển thị đúng số tiền + nội dung CK |
| B6 | **Basic Reports** | Doanh thu hôm nay + số xe đang thuê + danh sách booking | Số liệu khớp với DB 100% |

**QUALITY GATE PHASE B:**

```
☐ Double-booking test: 10 request đồng thời book 1 xe → chỉ 1 thành công
☐ Full flow test: Guest book → Staff confirm → Giao xe → Nhận xe → Thanh toán
☐ Performance: Availability query < 200ms với 100 xe
☐ Cache hit rate > 80% cho availability queries
☐ FE hoạt động trên Chrome, Firefox, Safari (desktop + mobile)

Nếu GATE FAIL → không sang Phase C.
```

---

### PHASE C: MVP PUBLIC — KHÁCH ĐẶT ONLINE HOÀN CHỈNH (4 tuần — Week 15→18)

**Mục tiêu kinh doanh:** Khách hàng cuối có thể tự đặt xe qua web công cộng. Nhà xe có web riêng với domain riêng.

**Câu hỏi trả lời được sau phase này:** "Một khách du lịch ở Đà Nẵng có thể vào web nhà xe, tìm xe, đặt xe, và ra quầy lấy xe không?"

```
SPRINT 5 (W15-16): PUBLIC WEBSITE HOÀN CHỈNH
├── FE Customer Website (Next.js):
│   ├── Trang chủ + tìm kiếm xe (branch, ngày, loại xe)
│   ├── Trang chi tiết xe + bảng giá
│   ├── Form đặt xe (guest checkout) + xác nhận booking
│   └── Responsive mobile (khách du lịch dùng điện thoại là chính)
├── BE: Hoàn thiện public API + rate limiting
├── Multi-tenant domain routing: tenant.rental.vn hoặc domain riêng
└── Email xác nhận booking (template riêng cho từng tenant)

SPRINT 6 (W17-18): CUSTOMER PORTAL
├── Customer login/register (từ guest → member)
├── Customer dashboard: lịch sử booking + trạng thái hiện tại
├── 1-click rebook (tự động điền form từ booking cũ)
├── Cập nhật hồ sơ cá nhân (CCCD, GPLX, SĐT)
└── FE Customer Portal (Next.js protected routes)
```

**DELIVERABLE PHASE C:**

| # | Deliverable | Mô tả | Tiêu chuẩn đạt |
|---|-------------|-------|-----------------|
| C1 | **Public Website Hoàn Chỉnh** | Web đặt xe công cộng, responsive, gắn domain tenant | Khách tìm → đặt → nhận email trong < 5 phút |
| C2 | **Guest → Member Flow** | Sau khi đặt xe, khách được mời tạo tài khoản | Tỷ lệ chuyển đổi > 15% |
| C3 | **Customer Portal** | Khách có TK xem được lịch sử, đặt lại 1 chạm | Load lịch sử < 1s |
| C4 | **Multi-tenant Domain** | Tenant A: rentcar-vn.com, Tenant B: xedien-sg.com | Cả 2 web hoạt động độc lập, đúng brand |

**QUALITY GATE PHASE C:**

```
☐ UAT: 5 người dùng thật hoàn thành booking không cần hướng dẫn
☐ Mobile responsive: Toàn bộ flow hoạt động trên iPhone/Android
☐ Domain routing: 2 tenant khác nhau, 2 web khác nhau, data cô lập
☐ Email deliverability > 95% (không vào spam)

Nếu GATE FAIL → không sang Phase D.
```

---

### PHASE D: POLISH & GO-LIVE (3 tuần — Week 19→21)

**Mục tiêu kinh doanh:** Hệ thống sẵn sàng cho khách hàng thật. Deploy production. Có test coverage + audit.

**Câu hỏi trả lời được sau phase này:** "Có thể mời nhà xe đầu tiên đăng ký dùng thử không?"

```
SPRINT 7 (W19-20): TESTING & HARDENING
├── Unit test BE: coverage > 60%
├── Integration test: Full flow tự động (Selenium/Cypress)
├── Security audit: OWASP Top 10, IDOR, SQL injection
├── Tenant isolation audit: penetration test cross-tenant
├── Performance test: 100 tenant × 1000 xe × 1000 booking
└── Bug fixing (từ test + audit)

SPRINT 8 (W21): DEPLOYMENT & DOCS
├── Deploy production environment (VPS/Cloud)
├── SSL/TLS setup cho từng tenant domain
├── Monitoring setup (health check, error tracking)
├── User documentation (hướng dẫn sử dụng cho nhà xe)
├── Landing page cho SaaS product (bán hàng)
└── Onboarding flow cho tenant đầu tiên
```

**DELIVERABLE PHASE D:**

| # | Deliverable | Mô tả | Tiêu chuẩn đạt |
|---|-------------|-------|-----------------|
| D1 | **Production Deployment** | Hệ thống chạy trên production environment | Uptime 99%+ |
| D2 | **Test Coverage** | Unit test BE + Integration test | Coverage > 60%, tất cả critical path có test |
| D3 | **Security Audit Report** | Kết quả kiểm tra bảo mật | 0 lỗi Critical/High |
| D4 | **Tenant Isolation Certification** | Chứng minh dữ liệu cô lập | 0 cross-tenant data leak |
| D5 | **User Guide** | Tài liệu hướng dẫn nhà xe sử dụng | Nhà xe tự setup được trong 1 giờ |
| D6 | **SaaS Landing Page** | Trang web bán sản phẩm SaaS | Khách hàng có thể đăng ký dùng thử |

**QUALITY GATE PHASE D (GO-LIVE GATE):**

```
☐ Production deploy thành công, không có lỗi critical
☐ All quality gates từ Phase A, B, C vẫn PASS
☐ Load test: 100 concurrent users, response time < 500ms
☐ Security audit: 0 critical, 0 high vulnerabilities
☐ Có ít nhất 1 tenant thật đăng ký dùng thử thành công

ĐẠT → GO LIVE.
```

---

## 4. So Sánh Trước/Sau

| Tiêu chí | Roadmap Cũ | Roadmap Mới |
|----------|-----------|-------------|
| **Tổng thời gian** | 23 tuần | 21 tuần |
| **Phase có sản phẩm chạy** | Tuần 23 (cuối cùng) | Tuần 14 (Phase B xong) |
| **Public booking** | Tuần 22-23 (2 tuần cuối) | Từ tuần 7 (song song admin) |
| **Số module nghiệp vụ** | 8 modules (gồm Transfer) | 6 modules (cắt Transfer) |
| **Quality Gate** | 5 mốc mơ hồ | 4 gate cứng, có test case cụ thể |
| **Demo được cho khách** | Sau 5 tháng | Sau 3 tháng (Phase B xong) |
| **Cơ hội pivot** | Không có (làm 1 mạch) | Sau mỗi phase đều có thể điều chỉnh |

---

## 5. Timeline Trực Quan

```
     PHASE A        │     PHASE B (MVP CORE)      │  PHASE C   │ PHASE D
     (Nền móng)     │                             │ (Public)   │(Polish)
                    │                             │            │
 W1  W2  W3  W4  W5  W6  W7  W8  W9 W10 W11 W12 W13 W14 W15 W16 W17 W18 W19 W20 W21
 │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │
 ├───┴───┴───┤ ├───┴───┴───┤ ├──┴──┤ ├──┴──┤ ├──┴──┤ ├──┴──┤ ├──┴──┤ ├──┴──┴──┤
 │ Bootcamp  │ │Base System│ │Bran │ │Cust │ │Book │ │Pay  │ │Pub  │ │Polish  │
 │ Java+Spring│ │Auth+Tenant│ │Vehicle│ │Pricing│ │Counter│ │Report│ │Portal│ │Deploy │
 │ 3 tuần   │ │3 tuần    │ │2 tuần│ │2 tuần│ │2 tuần│ │2 tuần│ │2 tuần│ │3 tuần │
 ├───────────┤ ├──────────┤ ├──────┤ ├──────┤ ├──────┤ ├──────┤ ├──────┤ ├────────┤
 │          │ │          │ │      │ │      │ │      │ │      │ │      │ │       │
 └──────────┴─┴──────────┴─┴──────┴─┴──────┴─┴──────┴─┴──────┴─┴──────┴─┴───────┘
  ▲                        ▲              ▲        ▲        ▲        ▲        ▲
  │                        │              │        │        │        │        │
  M1: Bootcamp            M2: Base      M3:     M4:      M5:      M6:     M7:
  Complete                Ready         Fleet   Counter  Payment  Public  GO-LIVE
                                        Live    Ops Live Done     Web Live
```

---

## 6. Milestones & Quyết Định Kinh Doanh

| Milestone | Tuần | Sản phẩm demo được | Quyết định kinh doanh |
|-----------|------|---------------------|------------------------|
| **M1: Bootcamp** | W3 | API CRUD có JWT | Team sẵn sàng code nghiệp vụ? |
| **M2: Base Ready** | W6 | Auth + Tenant Isolation | Đã test cô lập dữ liệu thành công? |
| **M3: Fleet Live** | W8 | Quản lý xe + chi nhánh + xem xe trống | **Mời 2-3 nhà xe xem demo, lấy feedback** |
| **M4: Counter Ops** | W12 | Booking tại quầy + giao/nhận xe | **Cho 1 nhà xe dùng thử nội bộ** |
| **M5: Payment Done** | W14 | Thanh toán + báo cáo cơ bản | Đã sẵn sàng thu tiền thật? |
| **M6: Public Web** | W18 | Web công cộng hoàn chỉnh | **Mở đăng ký dùng thử (free trial)** |
| **M7: GO-LIVE** | W21 | Production system | **Bắt đầu thu phí tenant đầu tiên** |

**Khác biệt chính:** Từ M3 (tuần 8) đã có thứ để show. Từ M4 (tuần 12) đã có thứ để test với khách hàng thật. Không phải đợi đến hết 21 tuần.

---

## 7. Phân Bổ Nhân Sự

```
PHASE A (W1-6): Cả team tập trung học + build nền móng
├── BE Lead: Code base Spring Boot + Auth + Tenant
├── FE Angular: Setup base + Auth UI
├── FE Next.js: Setup base + Auth UI
└── DevOps: Docker + DB + RLS

PHASE B (W7-14): BE/FE song song theo sprint
├── BE Lead: API nghiệp vụ (entity → service → controller → test)
├── FE Angular: Admin dashboard (từng module)
├── FE Next.js: Public web (từng module)
└── DevOps: CI/CD + monitoring

PHASE C (W15-18): FE Next.js chủ lực, BE hỗ trợ
├── BE Lead: Hoàn thiện public API + domain routing
├── FE Next.js: Public website + Customer portal
├── FE Angular: Chỉnh sửa admin theo feedback
└── DevOps: SSL + domain setup

PHASE D (W19-21): Cả team test + fix + deploy
├── BE Lead: Performance tuning + security fix
├── FE Angular: Integration test admin
├── FE Next.js: Integration test customer web
└── DevOps: Production deploy + monitoring
```

---

*Đây là tài liệu roadmap tái cấu trúc. Sau khi được phê duyệt, sẽ cập nhật vào Roadmap.md và Mindmap.md chính thức.*

*Xem các phân tích liên quan:*
- [01 - Cắt gọt & Ưu tiên Tính năng](01-feature-prioritization.md)
- [02 - Phân tích Hành vi Khách Thuê Xe](02-customer-booking-behavior.md)
- [03 - So sánh 2 Loại hình Cho thuê](03-business-model-comparison.md)
- [04 - Định hướng Công nghệ](04-technology-direction.md)
