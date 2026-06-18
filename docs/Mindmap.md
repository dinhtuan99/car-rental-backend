# 📊 Sơ đồ Trực quan Lộ trình Dự án (Mindmap & Gantt Chart)

Tài liệu này cung cấp mã nguồn biểu đồ để bạn vẽ tự động các sơ đồ trực quan (Sơ đồ tư duy & Biểu đồ Gantt tiến độ) phục vụ cho việc nhúng vào slide hoặc báo cáo dự án.

Bạn chỉ cần copy mã nguồn tương ứng bên dưới và dán vào trang **[Mermaid Live Editor](https://mermaid.live)** để tạo và tải hình ảnh chất lượng cao (PNG/SVG) về máy.

---

## 🧠 1. Sơ đồ Tư duy Lộ trình (Mindmap Roadmap)

Đoạn mã này hiển thị cấu trúc nội dung cốt lõi của từng giai đoạn phát triển:

```mermaid
mindmap
  root((Car Rental SaaS))
    Phase 1: Bootcamp BE
      ::icon(fa fa-book)
      Java Core & OOP
      Spring Boot Core
      Spring Data JPA
      Spring Security & JWT
      Postgres RLS & Docker
    Phase 2: Setup Base
      ::icon(fa fa-cogs)
      Boilerplate BE & FE
      Auth & Role-Based Authz
      Tenant Resolution Engine
      DB Migrations Setup
      RLS Policy Activation
    Phase 3: Core Features
      ::icon(fa fa-layer-group)
      1. SaaS Admin & Tenant
      2. Branch Management
      3. Fleet & Vehicle Transfer
      4. Customer & AES Encryption
      5. Pricing Engine
      6. Booking & Pessimistic Locking
      7. Payment & VietQR Flow
      8. Report & Dashboard Analytics
    Phase 4: Customer Portal
      ::icon(fa fa-globe)
      Public Search & Booking
      Guest Checkout Flow
      Customer Login & Auth
      Portal Dashboard UI
```

---

## 📅 2. Biểu đồ Gantt Tiến độ (Gantt Chart Timeline)

Đoạn mã này hiển thị chi tiết tiến độ thực hiện theo từng tuần của 4 giai đoạn phát triển trong vòng **6 tháng (24 tuần)**, hiển thị trục thời gian chuẩn từ **Tuần 01 đến Tuần 24**:

```mermaid
gantt
    title Lộ trình Phát triển Car Rental SaaS (6 Tháng - 24 Tuần)
    dateFormat  YYYY-MM-DD
    axisFormat  Tuần %V
    todayMarker off
    
    section Phase 1: Bootcamp (W1-W4)
    Java Core, OOP & Spring Boot Core   : active, p1_1, 2024-01-01, 1w
    Spring Data JPA & PostgreSQL        : active, p1_2, after p1_1, 1w
    Spring Security & JWT               : active, p1_3, after p1_2, 1w
    PostgreSQL RLS & Docker Setup       : active, p1_4, after p1_3, 1w
    
    section Phase 2: Setup Base (W5-W6)
    Khởi tạo Boilerplate BE & FE        : p2_1, after p1_4, 1w
    Auth, Tenant Resolution & RLS       : p2_2, after p2_1, 1w
    
    section Phase 3: Core Features (W7-W22)
    S1 - SaaS Admin & Tenant Onboarding : p3_1, after p2_2, 2w
    S2 - Branch Management              : p3_2, after p3_1, 2w
    S3 - Fleet & Vehicle Transfer       : p3_3, after p3_2, 2w
    S4 - Customer & AES Encryption      : p3_4, after p3_3, 2w
    S5 - Pricing Engine (Tính giá)      : p3_5, after p3_4, 2w
    S6 - Booking & Khóa bi quan         : p3_6, after p3_5, 2w
    S7 - Payment & Quản lý Hóa đơn      : p3_7, after p3_6, 2w
    S8 - Báo cáo Thống kê & Dashboard   : p3_8, after p3_7, 2w
    
    section Phase 4: Customer Portal (W23-W24)
    Public Search & Booking API         : p4_1, after p3_8, 1w
    Guest Checkout & Portal UI          : p4_2, after p4_1, 1w
```

---

## 🖼️ Hướng dẫn chèn hình ảnh vào báo cáo

1. Truy cập **[Mermaid Live Editor](https://mermaid.live)**.
2. Dán mã code tương ứng vào ô biên tập.
3. Nhấp vào nút **Download** ở góc dưới cùng bên trái và tải về định dạng **PNG** hoặc **SVG**.
4. Lưu hình ảnh vào dự án của bạn (ví dụ: `docs/assets/mindmap.png` và `docs/assets/gantt.png`).
5. Sử dụng cú pháp sau để hiển thị trong Markdown:

```markdown
![Sơ đồ tư duy](assets/mindmap.png)
![Biểu đồ Gantt](assets/gantt.png)
```
