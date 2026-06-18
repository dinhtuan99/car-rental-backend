# Roadmap & Timeline - Car Rental SaaS

## Mục lục
1. [Tổng quan Timeline](#1-tổng-quan-timeline)
2. [Phương pháp Phát triển (Development Strategy)](#2-phương-pháp-phát-triển-development-strategy)
3. [Phase 0: Preparation & Bootcamp (Weeks 1-8)](#3-phase-0-preparation--bootcamp-weeks-1-8)
4. [Phase 1: Foundations & Async Development (Weeks 9-16)](#4-phase-1-foundations--async-development-weeks-9-16)
5. [Phase 2: MVP Features & Core Integration (Weeks 17-24)](#5-phase-2-mvp-features--core-integration-weeks-17-24)
6. [Phase 3: Quality Gates, Testing & Hardening (Weeks 25-27)](#6-phase-3-quality-gates-testing--hardening-weeks-25-27)
7. [Phase 4: Launch & Handover (Week 28)](#7-phase-4-launch--handover-week-28)
8. [Phân bổ vai trò trong Team (Collaborative Model)](#8-phân-bổ-vai-trò-trong-team-collaborative-model)
9. [Milestones & Quality Gates](#9-milestones--quality-gates)
10. [Rủi ro & Biện pháp giảm thiểu (Risks & Mitigations)](#10-rủi-ro--biện-pháp-giảm-thiểu-risks--mitigations)

---

## 1. Tổng quan Timeline

Do đặc thù team 3 người làm việc **part-time (~30-40% thời gian)**, phân bổ cho nhiều dự án song song và nhân sự phụ trách Backend (Spring Boot) chưa có kinh nghiệm thực tế, roadmap được kéo giãn lên **~7 tháng (28 tuần)**. 

Lộ trình này áp dụng mô hình **Học tập cộng tác (Collaborative Learning)** và **Thiết kế API-First** để tối ưu hiệu suất làm việc song song của nhóm.

```
Week 1-2     │ Architecture & Design Documents ✅
Week 3-8     │ Tech Learning & Team Bootcamp (2 tháng - Cả 3 người cùng học) 🔄
Week 9-16    │ Phase 1: Foundations & Async Development (2 tháng)
Week 17-24   │ Phase 2: MVP Features & Core Integration (2 tháng)
Week 25-27   │ Phase 3: Quality Gates, Testing & Hardening (3 tuần)
Week 28      │ Phase 4: Launch & Handover (1 tuần)
─────────────────────────────────────────────────────────────────
Total: ~7 tháng (Part-time, song song các dự án khác)
```

---

## 2. Phương pháp Phát triển (Development Strategy)

Để đảm bảo tính khả thi trong bối cảnh nhân sự part-time và thiếu kinh nghiệm Backend, dự án áp dụng 3 chiến lược phát triển chuyên nghiệp:

1. **API-First Development:** Thống nhất chi tiết API Contract (Request/Response JSON, mã lỗi, kiểu dữ liệu) ngay từ đầu (Week 9). Điều này cho phép Frontend Lead dựng Mock Server (Sử dụng Mock Service Worker hoặc JSON Server) để tự lập trình và chạy thử giao diện một cách độc lập mà không cần chờ Backend Lead viết xong API thực tế.
2. **Collaborative Bootcamp:** Xóa bỏ ranh giới "việc ai nấy làm" trong 2 tháng đầu. FE Lead và DevOps hỗ trợ đắc lực cho BE Lead trong việc nghiên cứu tài liệu, hiểu luồng nghiệp vụ và debug code ở các tầng như Bảo mật (Security) và Database (JPA/SQL).
3. **Phân rã nghiệp vụ (Feature Slicing):** Cắt giảm các luồng tự động hóa phức tạp của các tính năng phụ ở giai đoạn MVP (ví dụ: Thay vì tự động điều phối xe thông minh ở Vehicle Transfer, ta làm luồng yêu cầu và phê duyệt bằng tay đơn giản, lưu vết logs lịch sử). Tập trung tài nguyên cho luồng đặt xe (Booking Flow) và an toàn dữ liệu Tenant (RLS).

---

## 3. Phase 0: Preparation & Bootcamp (Weeks 1-8)

| Tuần | Nội dung | Mục tiêu đào tạo chéo | Deliverables | Status |
|------|----------|-----------------------|--------------|--------|
| **1-2** | Design & Architecture | Cả 3 người nắm rõ kiến trúc chung hệ thống | ✅ SPEC.md, API-Specification.md, Database-Schema.md, Security-Design.md | ✅ Done |
| **3-4** | Java OOP, Spring Boot & Next.js 14 App Router | - BE Lead: Cú pháp Java OOP, Spring Core, DI/IoC.<br>- Cả team: Nắm cấu trúc Next.js App Router (Layouts, Routing) | - Dựng khung ứng dụng trống.<br>- Nắm vững nguyên lý Dependency Injection | 🔄 In Progress |
| **5-6** | Spring Data JPA & DB & Next.js Data Fetching | - BE Lead: Hibernate Entity Mapping, JPA repository queries.<br>- Cả team: Cách gọi API từ Server Component Next.js | - Chạy thử các câu lệnh SQL tự động sinh từ JPA.<br>- Thiết lập DB schema cục bộ | 🔄 In Progress |
| **7** | Spring Security, JWT & Next.js Middleware | - BE Lead: Bộ lọc Filters, JWT token creation/parsing.<br>- Cả team: Cookie handling, Auth checks | - Chạy thử luồng authenticate & authorize bằng Postman | 🔄 In Progress |
| **8** | PostgreSQL RLS & Docker Compose | - BE Lead: Cấu hình context session trong DB.<br>- DevOps: Viết script RLS policy & Super Admin bypass | - Setup hoàn chỉnh Docker Compose môi trường Dev chạy PostgreSQL + Redis | 🔄 In Progress |

---

## 4. Phase 1: Foundations & Async Development (Weeks 9-16)

Trong giai đoạn này, FE Lead làm việc trên API Mock Server, BE Lead viết API thực tế.

| Week | Backend (Spring Boot) | Frontend (Next.js 14) | Infrastructure / DevOps |
|------|-----------------------|-----------------------|-------------------------|
| **9-10** | - Khởi tạo khung dự án Spring Boot.<br>- Viết Filter phân giải Tenant (`tenant_id` resolver) từ subdomain/header | - Khởi tạo khung dự án Next.js 14.<br>- **Thiết lập Mock API Server**.<br>- Dựng luồng Login/Register (UI) | - Setup Git repositories con độc lập.<br>- Cấu hình Docker cho Postgres & Redis |
| **11-12**| - Viết APIs đăng ký/đăng nhập hệ thống.<br>- APIs quản lý Tenant cho Super Admin (`/api/v1/super-admin/tenants`) | - Dựng UI cho **SaaS Admin Portal** (Màn hình dashboard chung, kích hoạt tenant, cấu hình gói cước) | - Migrate DB script cho cột `tenant_id` nullable trên bảng `users`.<br>- Cấu hình chính sách Postgres RLS |
| **13-14**| - Viết REST APIs: Chi nhánh (Branch APIs) & Loại xe/Xe (Vehicle CRUD APIs) | - Tích hợp Mock APIs để hoàn thiện UI **Admin Dashboard** (Màn quản lý chi nhánh, quản lý fleet xe) | - Setup CI/CD build test tự động cơ bản |
| **15-16**| - Viết REST APIs: Quản lý khách hàng (Customer APIs) | - Hoàn thiện các trang: Quản lý khách hàng, Cấu hình bảng giá của Tenant | - Triển khai thuật toán mã hóa AES cho trường CCCD/GPLX của khách hàng |

**Deliverables Phase 1:**
- [ ] Khung code Backend & Frontend hoạt động ổn định.
- [ ] Logic phân tách tenant (Multi-tenant middleware) ở Backend hoạt động.
- [ ] Frontend hoàn thành toàn bộ khung giao diện CRUD (Tenant, Chi nhánh, Xe, Khách hàng) bằng Mock API.

---

## 5. Phase 2: MVP Features & Core Integration (Weeks 17-24)

Giai đoạn tích hợp APIs thực tế giữa Frontend và Backend, tập trung xử lý các nghiệp vụ phức tạp.

| Week | Backend (Spring Boot) | Frontend (Next.js 14) | Integrations / DevOps |
|------|-----------------------|-----------------------|-----------------------|
| **17-18**| - Xây dựng **Booking API**.<br>- Áp dụng cơ chế **Database locking (Pessimistic Lock)** để chống double booking | - Tích hợp API thật.<br>- Hoàn thiện luồng đặt xe (Booking Flow UI) trên Customer Website | - Viết các test case kiểm thử độ chịu tải đặt trùng xe (race condition) |
| **19-20**| - Phát triển **Pricing Engine** (Tính toán giá thuê xe theo ngày thường/cuối tuần/ngày lễ/mùa cao điểm) | - Đồng bộ giao diện tính toán & hiển thị giá chi tiết khi khách đặt xe | - Cấu hình bảng dữ liệu pricing_rules |
| **21-22**| - APIs lưu vết giao dịch thanh toán.<br>- APIs duyệt yêu cầu gia hạn gói của Super Admin | - Giao diện thanh toán cọc.<br>- Giao diện đối soát cước của Super Admin | - **Tích hợp cổng VNPAY Sandbox** (BE kiểm tra chữ ký & IPN callback; FE chuyển hướng) |
| **23-24**| - APIs yêu cầu luân chuyển xe (Vehicle Transfer).<br>- APIs xuất báo cáo doanh thu & giám sát hệ thống | - Trang quản lý yêu cầu transfer xe.<br>- Giao diện biểu đồ doanh thu (Tenant & Super Admin) | - Tích hợp SMTP mail server / SMS gateway mock (Gửi OTP, thông báo đặt xe thành công) |

**Deliverables Phase 2:**
- [ ] Hoàn thành trọn vẹn luồng Đặt xe - Tính giá - Thanh toán VNPAY của khách hàng.
- [ ] Khách hàng đăng nhập cổng Customer Portal xem lại lịch sử thuê xe thành công.
- [ ] Super Admin thực hiện được luồng kiểm duyệt thanh toán cước thuê bao và xem biểu đồ doanh thu toàn sàn.

---

## 6. Phase 3: Quality Gates, Testing & Hardening (Weeks 25-27)

Tập trung kiểm soát chất lượng kỹ càng do code Backend được viết bởi lập trình viên mới.

| Week | Công việc chi tiết | Tiêu chí đạt được (Quality Gate) |
|------|--------------------|----------------------------------|
| **25** | - Viết Unit tests & Integration tests.<br>- Thực hiện kiểm thử thủ công end-to-end các luồng nghiệp vụ lỗi | - Độ bao phủ code Backend đạt **>60% unit test coverage**.<br>- Không còn lỗi crash luồng đặt xe |
| **26** | - Thực hiện **Tenant Isolation Audit**: Chạy thử các kịch bản hack IDOR (dùng token Tenant A để đọc xe Tenant B) | - **100% các bảng dữ liệu có RLS** được kiểm thử cô lập thành công, không có rò rỉ chéo dữ liệu |
| **27** | - Sửa các lỗi bảo mật phát hiện từ audit.<br>- Đo hiệu năng hệ thống, viết index bổ sung cho các truy vấn DB chậm.<br>- Hoàn thiện tài liệu cấu hình vận hành (Setup Guide, API Docs Swagger) | - Tốc độ phản hồi các API CRUD đạt `< 200ms`.<br>- Tài liệu hướng dẫn cài đặt rõ ràng, dễ hiểu |

---

## 7. Phase 4: Launch & Handover (Week 28)

| Công việc | Deliverables |
|-----------|--------------|
| Triển khai lên Staging (Sử dụng Docker Compose hoặc K8s thu nhỏ) | staging.carrental-saas.com |
| Tiến hành UAT (User Acceptance Testing) với 1-2 nhà xe đối tác chạy thử | UAT feedback list |
| Hotfix nhanh các lỗi phát sinh từ UAT | - |
| Triển khai chính thức lên Production, hướng dẫn vận hành | production.carrental-saas.com |

---

## 8. Phân bổ vai trò trong Team (Collaborative Model)

Do làm việc part-time, các thành viên sẽ hỗ trợ chặt chẽ để giảm tải và nâng cao năng lực cho nhau:

### Backend Lead (Lập trình viên BE)
* **Vai trò:** Lập trình chính phần Backend API, kết nối DB, RLS và cấu hình Security.
* **Hỗ trợ nhận được:** 
  * Được DevOps Lead hỗ trợ cấu hình script Docker DB, viết lệnh SQL migrations nâng cao và kiểm soát chính sách RLS.
  * Được FE Lead hỗ trợ thiết kế cấu trúc API Request/Response DTO và hỗ trợ code phụ các API CRUD cơ bản.

### Frontend Lead (Lập trình viên FE)
* **Vai trò:** Thiết kế UI/UX, lập trình Next.js 14 App Router, tích hợp API.
* **Hỗ trợ nhận được:** Được BE Lead hỗ trợ tài liệu đặc tả API chuẩn (Swagger/API-Spec) và cung cấp các kịch bản dữ liệu mock.

### DevOps / Shared
* **Vai trò:** Setup hạ tầng Docker, xây dựng luồng CI/CD, cấu hình cơ sở dữ liệu Postgres RLS, tích hợp các cổng dịch vụ (VNPAY, Mail gateway).
* **Hỗ trợ nhận được:** Được BE Lead hỗ trợ cấu hình Session context trong ứng dụng Java kết nối sang Database.

---

## 9. Milestones & Quality Gates

| Milestone | Target | Tiêu chuẩn chất lượng (Quality Gate) | Status |
|-----------|--------|-------------------------------------|--------|
| **M1: Design Approved** | Week 2 | SPEC, API Spec, DB Schema được duyệt hoàn toàn | ✅ Done |
| **M2: Bootcamp Complete** | Week 8 | Cả 3 người vượt qua các bài học công nghệ của nhau | 🔄 In Progress |
| **M3: Core CRUD Ready** | Week 16 | 100% APIs cơ bản hoạt động trên giao diện Mock và thật | ☐ Pending |
| **M4: MVP Functional** | Week 24 | Luồng Đặt xe - Tính giá - Thanh toán tích hợp thông suốt | ☐ Pending |
| **M5: Quality Gate Passed** | Week 27 | Vượt qua bài kiểm tra Tenant Isolation Audit & Unit test coverage >60% | ☐ Pending |
| **M6: Go-Live** | Week 28 | Hệ thống production chạy ổn định, UAT thành công | ☐ Pending |

---

## 10. Rủi ro & Biện pháp giảm thiểu (Risks & Mitigations)

| Rủi ro | Khả năng | Tác động | Giải pháp giảm thiểu |
|------|------------|--------|------------|
| BE Lead quá tải khi học Spring Boot từ đầu | High | High | - Dành trọn 6 tuần bootcamp chỉ để học, không giao task code.<br>- Áp dụng học tập cộng tác: DevOps và FE hỗ trợ thiết kế cấu trúc dữ liệu và giải quyết bug phức tạp. |
| Rò rỉ dữ liệu chéo giữa các Tenant (IDOR) | Medium | Critical | - Sử dụng **PostgreSQL RLS** ở tầng DB thay vì lọc thủ công ở tầng code Java.<br>- Thiết lập **Tenant Isolation Audit** bắt buộc ở Week 26 trước khi bàn giao hệ thống. |
| Xung đột tiến độ do làm part-time song song dự án khác | High | Medium | - Áp dụng **API-First**: FE và BE hoạt động độc lập qua Mock Server, không bị nghẽn (block) lẫn nhau.<br>- Chia nhỏ task theo tuần để dễ quản lý tiến độ. |
| Trùng lặp đặt xe (Double Booking) | Medium | High | - Nghiên cứu thiết kế khóa cơ sở dữ liệu (Pessimistic Locking / SELECT FOR UPDATE) ngay ở tuần 17. |

---

*Last updated: 2026-06-18*
