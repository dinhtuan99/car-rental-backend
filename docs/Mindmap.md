# Sơ đồ trực quan Lộ trình Dự án (Mindmap & Gantt Chart)

**Cập nhật:** 24/06/2026 — Tái cấu trúc theo phân tích thị trường & ưu tiên tính năng.
**Tham chiếu:** [Phân tích chi tiết](analysis/)

---

## 1. Mindmap — Cấu Trúc Sản Phẩm & Key Features

```mermaid
mindmap
  root((Car Rental SaaS))
    Key Feature 1: Real-time Inventory
      ::icon(fa fa-heart)
      Redis Cache Availability
      Pessimistic Locking
      Auto-release Expired
      0 Double-booking

    Key Feature 2: Tenant Isolation
      ::icon(fa fa-shield)
      Postgres RLS
      5-Min Onboarding
      Domain Riêng Từng Tenant
      Audit Cross-tenant

    Key Feature 3: Counter Operations
      ::icon(fa fa-bolt)
      Giao Xe < 60s
      Nhận Xe < 90s
      Tự động Tính Tiền
      Offline-first Ready

    Phase A: Nền móng
      ::icon(fa fa-book)
      Java Core & Spring Boot
      Spring Security & JWT
      Postgres RLS & Docker
      Boilerplate BE + FE
      Auth & RBAC
      DB Schema + Migrations

    Phase B: MVP Core
      ::icon(fa fa-layer-group)
      Branch & Vehicle CRUD
      Customer & AES Encrypt
      Pricing Engine (đơn giản)
      Booking & Counter Flow
      Payment & VietQR
      Basic Reports

    Phase C: MVP Public
      ::icon(fa fa-globe)
      Public Vehicle Search
      Guest Checkout Flow
      Customer Portal
      1-Click Rebook
      Domain Per Tenant

    Phase D: Go-Live
      ::icon(fa fa-rocket)
      Unit Test > 60%
      Security Audit
      Tenant Iso Audit
      Production Deploy
      User Guide
```

---

## 2. Gantt Chart — Timeline 21 Tuần (4 Phase)

```mermaid
gantt
    title Lộ trình Car Rental SaaS 2.0 (21 Tuần)
    dateFormat  YYYY-MM-DD
    axisFormat  Tuần %V
    todayMarker off

    section Phase A: Nền móng (W1-W6)
    Bootcamp Java & Spring Boot          : active, pA_1, 2026-06-08, 3w
    Base System Auth & Tenant            : active, pA_2, after pA_1, 3w

    section Phase B: MVP Core (W7-W14)
    S1 - Branch & Vehicle Management     : pB_1, after pA_2, 2w
    S2 - Customer & Pricing Engine       : pB_2, after pB_1, 2w
    S3 - Booking & Counter Operations    : pB_3, after pB_2, 2w
    S4 - Payment & Basic Reports         : pB_4, after pB_3, 2w

    section Phase C: MVP Public (W15-W18)
    Public Website & Guest Checkout      : pC_1, after pB_4, 2w
    Customer Portal & Domain Setup       : pC_2, after pC_1, 2w

    section Phase D: Go-Live (W19-W21)
    Testing & Security Audit             : pD_1, after pC_2, 2w
    Deploy Production & Docs             : pD_2, after pD_1, 1w
```

---

## 3. So Sánh Trước / Sau

| Khía cạnh | Roadmap Cũ | Roadmap Mới |
|-----------|-----------|-------------|
| **Tổng thời gian** | 23 tuần | 21 tuần |
| **Có sản phẩm chạy được** | Tuần 23 | Tuần 8 (xe + branch), Tuần 14 (full nội bộ) |
| **Public Booking** | Tuần 22-23 (cuối) | Từ tuần 7, hoàn thiện tuần 18 |
| **Số module nghiệp vụ** | 8 (gồm Vehicle Transfer) | 6 (cắt Transfer, giảm Pricing, giảm Report) |
| **Quality Gate** | 5 mốc chưa rõ ràng | 4 gate cứng, có test case cụ thể |
| **Demo được** | Sau 5 tháng | Sau 2 tháng (Phase A), 3 tháng (Phase B) |

### Tính năng bị cắt / giảm scope

| Tính năng | Hành động | Lý do |
|-----------|-----------|-------|
| **Vehicle Transfer** | CẮT khỏi MVP | Nhà xe <50 xe không dùng, gọi điện thoại là xong |
| **SMS Notification** | CẮT | Tốn chi phí, ROI thấp với nhà xe nhỏ. Giữ Email. |
| **Pricing đa hệ số** | GIẢM còn basePrice × dayMultiplier | Bỏ Season, Holiday. Nhà xe tự chỉnh basePrice. |
| **Report nâng cao** | GIẢM còn 3 báo cáo cơ bản | Doanh thu ngày + Fleet status + Booking hôm nay |
| **Subscription 4 bậc** | GIẢM còn 2 bậc (FREE/PAID) | Phân mảnh gói cước là bài toán scaling |

---

## 4. Milestones Kinh Doanh

| Milestone | Tuần | Demo được gì? | Quyết định |
|-----------|------|---------------|------------|
| **M1: Bootcamp** | W3 | API CRUD có JWT | Team sẵn sàng code? |
| **M2: Base Ready** | W6 | Auth + Tenant Isolation | Đã test cô lập dữ liệu? |
| **M3: Fleet Live** | W8 | Quản lý xe + xem xe trống | **Demo cho 2-3 nhà xe, lấy feedback** |
| **M4: Counter Ops** | W12 | Booking tại quầy + giao/nhận xe | **Cho 1 nhà xe dùng thử** |
| **M5: Payment Done** | W14 | Thanh toán + báo cáo | Sẵn sàng thu tiền thật? |
| **M6: Public Web** | W18 | Web công cộng hoàn chỉnh | **Mở đăng ký dùng thử** |
| **M7: GO-LIVE** | W21 | Production system | **Bắt đầu thu phí** |

---

## Hướng dẫn chèn hình ảnh vào báo cáo

1. Truy cập **[Mermaid Live Editor](https://mermaid.live)**.
2. Dán mã code tương ứng vào ô biên tập.
3. Nhấp vào nút **Download** ở góc dưới cùng bên trái và tải về định dạng **PNG** hoặc **SVG**.
4. Lưu hình ảnh vào dự án của bạn (ví dụ: `docs/assets/mindmap-v2.png` và `docs/assets/gantt-v2.png`).
5. Sử dụng cú pháp sau để hiển thị trong Markdown:

```markdown
.[Sơ đồ tư duy](assets/mindmap-v2.png)
.[Biểu đồ Gantt](assets/gantt-v2.png)
```
